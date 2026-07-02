# 五维度智能主题推荐引擎

## 概述

根据 Blueprint 的内容特征，自动推荐最合适的品牌主题。五个维度综合打分，满分 100 分。

---

## 评分公式

```
总分 = 场景匹配(0-25) + 模板亲和(0-20) + 图表适配(0-25) + 信息密度(0-15) + 视觉平衡(0-15)
```

---

## 维度详解

### 1. 场景匹配 (0-25 分)

从用户输入中提取场景标签，与品牌预设中的 `scene_scores` 匹配。

**场景类型映射：**
| 用户输入关键词 | 映射场景 | 示例内容 |
|--------------|---------|---------|
| 路演、融资、BP、投资人、A轮 | pitch | AI 创业 BP |
| 咨询、报告、战略、董事会、行业 | consulting | AI 行业研究报告 |
| 技术、发布、GPU、算力、开发者 | tech_launch | AI 技术发布会 |
| 政策、宏观、解读、政府、监管 | policy | AI 政策解读 |
| 产品、消费者、发布、营销 | product | AI 产品发布 |

**实现逻辑：**
```python
def score_scene_match(blueprint, theme):
    """场景匹配评分"""
    detected_scenes = detect_scenes_from_content(blueprint)
    score = 0
    for scene in detected_scenes:
        score += theme.get("scene_scores", {}).get(scene, 0)
    return min(score, 25)
```

### 2. 模板亲和 (0-20 分)

评估所选模板与品牌主题的亲和度。

**规则：**
- 数据密集型报告（模板以 TPL-DATA/TPL-CHART/TPL-TABLE 为主）→ 不适合叙事型/简约型主题
- 叙事型内容（模板以 TPL-STORY/TPL-HERO 为主）→ 不适合数据型主题
- 混合内容 → 适合通用型主题（consulting, corporate）

**实现逻辑：**
```python
def score_template_affinity(blueprint, theme):
    """模板亲和度评分"""
    templates = extract_templates(blueprint)
    data_templates_ratio = count_data_templates(templates) / len(templates)
    theme_density = theme.get("density_preference", "medium")

    if theme_density == "high" and data_templates_ratio > 0.5:
        return 20  # 数据密集 + 高密度主题 = 完美匹配
    elif theme_density == "low" and data_templates_ratio < 0.3:
        return 20  # 叙事型 + 低密度主题 = 完美匹配
    elif theme_density == "medium":
        return 15  # 中密度主题通用性好
    else:
        return 5   # 不匹配
```

### 3. 图表适配 (0-25 分)

从 YAML 品牌预设中提取每种主题对图表类型的偏好，与 Blueprint 中的图表需求匹配。

**评分逻辑：**
```python
def score_chart_adaptation(blueprint, theme):
    """图表适配评分"""
    required_charts = extract_chart_types(blueprint)
    preferences = theme.get("chart_preferences", {})

    score = 0
    max_score = 25

    for chart in required_charts:
        if chart in preferences.get("strongly_preferred", []):
            score += 5
        elif chart in preferences.get("preferred", []):
            score += 3
        elif chart in preferences.get("neutral", []):
            score += 1
        elif chart in preferences.get("avoid", []):
            score -= 2

    return max(0, min(score, max_score))
```

### 4. 信息密度 (0-15 分)

评估 Blueprint 的信息密度与主题密度倾向的匹配度。

⚠️ **关键注意：** 使用 `math.log1p()` 进行对数空间转换，避免纯数据报告场景下叙事内容占比为零导致的除零错误。

```python
import math

def score_information_density(blueprint, theme):
    """信息密度评分"""
    # 统计内容类型
    data_pages = count_data_pages(blueprint)
    narrative_pages = count_narrative_pages(blueprint)
    total = data_pages + narrative_pages

    if total == 0:
        return 7  # 默认中等分

    # 使用 log1p 避免零除
    density_ratio = math.log1p(data_pages) / math.log1p(total + 1)

    theme_density = theme.get("density_preference", "medium")
    density_score = 0

    if theme_density == "high":
        density_score = density_ratio * 15
    elif theme_density == "medium":
        density_score = (1 - abs(density_ratio - 0.5) * 2) * 15
    else:  # low
        density_score = (1 - density_ratio) * 15

    return max(0, min(round(density_score), 15))
```

### 5. 视觉平衡 (0-15 分)

评估主题的颜色对比和视觉平衡。

**使用对数空间比较避免零除问题：**

```python
import math

def score_visual_balance(blueprint, theme):
    """视觉平衡评分"""
    colors = theme.get("colors", {})
    primary = colors.get("primary", "#000000")
    background = colors.get("background", "#FFFFFF")

    # 计算亮度对比（使用对数空间）
    primary_luminance = calculate_luminance(primary)
    bg_luminance = calculate_luminance(background)

    # 用 log1p 避免零除
    contrast_ratio = (math.log1p(primary_luminance + 0.05) /
                      math.log1p(bg_luminance + 0.05))

    # 理想对比度在 4.5:1 到 7:1 之间
    if 4.5 <= contrast_ratio <= 7:
        return 15
    elif contrast_ratio >= 3:
        return 10
    else:
        return 5
```

---

## 完整评分流程

```python
def recommend_theme(blueprint, themes):
    """为 Blueprint 推荐最佳品牌主题"""
    results = []

    for theme in themes:
        score = (
            score_scene_match(blueprint, theme) +
            score_template_affinity(blueprint, theme) +
            score_chart_adaptation(blueprint, theme) +
            score_information_density(blueprint, theme) +
            score_visual_balance(blueprint, theme)
        )

        results.append({
            "theme_id": theme["id"],
            "theme_name": theme["name"],
            "score": score,
            "breakdown": {
                "scene_match": score_scene_match(blueprint, theme),
                "template_affinity": score_template_affinity(blueprint, theme),
                "chart_adaptation": score_chart_adaptation(blueprint, theme),
                "information_density": score_information_density(blueprint, theme),
                "visual_balance": score_visual_balance(blueprint, theme),
            }
        })

    # 按分数从高到低排序
    results.sort(key=lambda x: x["score"], reverse=True)

    return results
```

---

## 常见问题排查

| 问题 | 原因 | 修复 |
|------|------|------|
| `AttributeError: 'Theme' object has no attribute 'get'` | Theme 对象被当做 dict 访问 | 使用 `getattr(theme, "inverse", False)` 而非 `theme.get("inverse")` |
| 模板分类匹配为空 | 分类列表中有不存在的模板名 | 同步分类列表与 12 种布局家族 |
| 信息密度计算除零 | 纯数据报告无叙事内容 | 使用 `math.log1p()` 替换线性计算 |
| 评分结果全部为 0 | 场景/模板/图表均未识别到类型 | 检查 theme 配置中的 scene_scores 和 chart_preferences 是否正确 |
