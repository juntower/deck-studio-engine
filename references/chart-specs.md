# 图表类型规范

## 16 种图表类型详细规范

### 1. bar — 条形图
**用途：** 分类数据对比
**数据格式：**
```yaml
data:
  categories: ["产品A", "产品B", "产品C", "产品D"]
  series:
    - name: "销售额"
      values: [120, 85, 200, 150]
```
**适用场景：** 市场份额、业绩对比、分类排名
**最佳主题：** consulting, pitch-vc, corporate

### 2. column — 柱状图
**用途：** 时间序列对比
**数据格式：**
```yaml
data:
  categories: ["2020", "2021", "2022", "2023"]
  series:
    - name: "营收(亿)"
      values: [12.5, 15.3, 18.7, 22.1]
```
**适用场景：** 年度数据对比、季度业绩
**最佳主题：** consulting, policy-analysis, financial

### 3. line — 折线图
**用途：** 趋势展示
**数据格式：**
```yaml
data:
  categories: ["Jan", "Feb", "Mar", "Apr", "May", "Jun"]
  series:
    - name: "用户增长"
      values: [1000, 1500, 2100, 2800, 3500, 4200]
    - name: "活跃率"
      values: [0.6, 0.62, 0.65, 0.63, 0.68, 0.72]
```
**适用场景：** 增长率、价格走势、趋势分析
**最佳主题：** consulting, financial, policy-analysis

### 4. area — 面积图
**用途：** 累积趋势
**数据格式：** 同折线图
**适用场景：** 市场规模累积、用户总量增长
**最佳主题：** consulting, financial, sustainability

### 5. stacked_bar — 堆叠条形图
**用途：** 组成对比
**数据格式：**
```yaml
data:
  categories: ["2020", "2021", "2022"]
  series:
    - name: "产品线A"
      values: [40, 45, 55]
    - name: "产品线B"
      values: [30, 35, 40]
    - name: "产品线C"
      values: [20, 25, 30]
```
**适用场景：** 产品线构成对比、收入来源拆解
**最佳主题：** consulting, financial, corporate

### 6. stacked_column — 堆叠柱状图
**用途：** 年度组成对比
**数据格式：** 同堆叠条形图
**适用场景：** 收入构成、成本拆解
**最佳主题：** consulting, financial, policy-analysis

### 7. pie — 饼图
**用途：** 占比展示
**数据格式：**
```yaml
data:
  series:
    - name: "市场份额"
      values:
        - { label: "公司A", value: 35 }
        - { label: "公司B", value: 25 }
        - { label: "公司C", value: 20 }
        - { label: "其他", value: 20 }
```
**注意：** 切片不宜超过 6 个
**适用场景：** 简单占比展示
**约束：** consulting 和 financial 主题中应避免

### 8. donut — 环形图
**用途：** 占比展示（更现代）
**数据格式：** 同饼图
**适用场景：** 市场份额、预算分配
**最佳主题：** pitch-vc, product-launch, minimal

### 9. radar — 雷达图
**用途：** 多维度对比
**数据格式：**
```yaml
data:
  categories: ["性能", "价格", "易用性", "安全", "扩展"]
  series:
    - name: "产品A"
      values: [4.5, 3.0, 4.0, 3.5, 4.2]
    - name: "产品B"
      values: [3.0, 4.5, 3.5, 4.0, 3.0]
```
**适用场景：** 能力评估、产品对比、综合素质
**最佳主题：** tech-launch, product-launch, academic

### 10. scatter — 散点图
**用途：** 相关性展示
**数据格式：**
```yaml
data:
  series:
    - name: "样本"
      points:
        - { x: 10, y: 20 }
        - { x: 15, y: 35 }
        - { x: 20, y: 32 }
```
**适用场景：** 投入产出、相关性分析
**最佳主题：** academic, tech-launch, financial

### 11. bubble — 气泡图
**用途：** 三维数据展示
**数据格式：**
```yaml
data:
  series:
    - name: "市场"
      bubbles:
        - { x: 10, y: 20, r: 15, label: "市场A" }
        - { x: 15, y: 35, r: 25, label: "市场B" }
```
**适用场景：** 市场规模+增长率+利润三维展示
**最佳主题：** consulting, product-launch

### 12. funnel — 漏斗图
**用途：** 漏斗转化
**数据格式：**
```yaml
data:
  stages:
    - { label: "曝光", value: 10000 }
    - { label: "点击", value: 2000 }
    - { label: "注册", value: 500 }
    - { label: "付费", value: 100 }
```
**适用场景：** 销售漏斗、用户转化、招聘流程
**最佳主题：** pitch-vc, corporate, education

### 13. waterfall — 瀑布图
**用途：** 累积变化拆解
**数据格式：**
```yaml
data:
  items:
    - { label: "期初利润", value: 100, type: "total" }
    - { label: "收入增长", value: +30, type: "positive" }
    - { label: "成本上升", value: -15, type: "negative" }
    - { label: "期末利润", value: 115, type: "total" }
```
**适用场景：** 利润拆解、成本分析、预算说明
**最佳主题：** financial, consulting

### 14. heatmap — 热力图
**用途：** 矩阵数据可视化
**数据格式：**
```yaml
data:
  rows: ["能力A", "能力B", "能力C"]
  columns: ["部门X", "部门Y", "部门Z"]
  values:
    - [3, 4, 2]
    - [2, 5, 3]
    - [4, 3, 4]
```
**适用场景：** 风险矩阵、关联分析、技能矩阵
**最佳主题：** academic, financial

### 15. gauge — 仪表盘
**用途：** 达成率/KPI
**数据格式：**
```yaml
data:
  label: "完成率"
  value: 78
  min: 0
  max: 100
  thresholds:
    - { color: "#E53935", range: [0, 50] }
    - { color: "#FFB300", range: [50, 80] }
    - { color: "#43A047", range: [80, 100] }
```
**适用场景：** KPI 完成度、健康度指标
**最佳主题：** tech-launch, corporate

### 16. treemap — 矩形树图
**用途：** 层级占比
**数据格式：**
```yaml
data:
  items:
    - { label: "业务A", value: 40, children: [...] }
    - { label: "业务B", value: 30 }
    - { label: "业务C", value: 20 }
```
**适用场景：** 业务板块构成、资产配置
**最佳主题：** consulting, financial

---

## 图表选择速查

| 数据目的 | 推荐图表 | 备选 |
|---------|---------|------|
| 对比大小 | bar / column | stacked_bar |
| 展示趋势 | line / area | column |
| 展示占比 | donut | pie |
| 展示关联 | scatter / bubble | — |
| 展示分布 | radar / heatmap | — |
| 展示转化 | funnel | — |
| 展示拆解 | waterfall | stacked_bar |
| 展示进度 | gauge | — |
| 展示层级 | treemap | stacked_bar |
