# Data Center Cooling System Analysis

基于 Weka 的数据中心冷源系统关联规则挖掘与决策树分类分析。

## 项目概述

利用 **Apriori 算法**挖掘冷却策略与运行参数之间的关联规则，使用 **J48 决策树**对冷却策略进行分类预测，并对比两种建模方法（训练集+独立测试集 vs 10 折交叉检验）的性能。

### 数据集特征

| 特征 | 说明 |
|---|---|
| Server_Workload(%) | 服务器负载 |
| Inlet_Temperature(°C) | 进风温度 |
| Outlet_Temperature(°C) | 出风温度 |
| Ambient_Temperature(°C) | 环境温度 |
| Cooling_Unit_Power_Consumption(kW) | 冷却单元功耗 |
| Chiller_Usage(%) | 冷水机使用率 |
| AHU_Usage(%) | 空调箱使用率 |
| Temperature_Deviation(°C) | 温度偏差 |
| Cooling_Strategy_Action | 冷却策略动作（目标变量） |

- **实例数**：700
- **目标类别**：Reduce AHU、Eco Mode、Boost All、Maintain、Increase Chiller

## 目录结构

```
├── code/                 # 代码
│   └── analysis.ipynb    # Jupyter 分析脚本（可视化图表）
├── models/               # 训练好的模型
│   └── 决策树模型结果.model
├── results/              # 运行结果（文本）
│   ├── Apriori算法结果.txt
│   └── 决策树两种方法的运行结果.txt
├── docs/                 # 文档
│   ├── report.docx
│   ├── Apriori算法分析参考.doc
│   ├── 决策树分析参考.doc
│   └── 模型选定.doc
├── images/               # 图表（按实验阶段分类）
│   ├── apriori/          # Apriori 参数配置与结果
│   ├── decision-tree/    # J48 配置与原始数据分布
│   ├── cross-validation/ # 10 折交叉检验结果
│   ├── data-split/       # 训练/测试集分割
│   ├── evaluation/       # 独立测试集评估
│   └── tables/           # 输出数据表
└── archive/              # 归档
    └── report.zip
```

## 主要结果

### Apriori 关联规则（Top 10，最小支持度 0.25，最小置信度 0.9）

关键发现：
- 环境温度 22.73-25.24°C 时，温度偏差稳定在 1.255°C 以内（**置信度 99%，Lift 2.97**）
- 高负载+高功耗场景下冷水机使用率显著上升（置信度 95-98%）
- 低负载+低功耗场景下冷水机需求降低（置信度 94-97%）

### 决策树分类

| 方法 | 正确率 | Kappa |
|---|---|---|
| 训练集训练 + 独立测试集评估 | 21.29% | 0.016 |
| 10 折交叉检验 | 24.57% | 0.053 |

决策树分类准确率较低，表明冷却策略与数值型运行参数之间存在较强的非线性关系，关联规则分析在实际业务洞察方面更具价值。

## 工具与环境

- **Weka 3.x**：数据挖掘与机器学习
- **Python 3** + Jupyter Notebook：可视化分析（matplotlib, seaborn, pandas, numpy）
- 使用离散化预处理（3-bin, precision 6）
