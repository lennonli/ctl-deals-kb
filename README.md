# ctl-deals-kb · A股控制权变更案例数据库（2022–2026）

上市公司控制权变更案例的结构化数据库，共 **1223 单**，覆盖 2022–2026 五个年度。
每单为一个 JSON，核心是**股份转让协议六大条款**的结构化抽取（含原文引证），
可用于协议起草、条款审查、交易结构与市场实践研究。

| 年度 | 数量 |
| ---- | ---- |
| 2022 | 308  |
| 2023 | 147  |
| 2024 | 270  |
| 2025 | 227  |
| 2026 | 271  |
| 合计 | 1223 |

## 目录结构

```
2022/
  002576.json        # 单个案例（文件名 = 证券代码.json，同代码多笔时加 _2 后缀）
  ...
  index.json         # 年度索引：{year, count, deals:[{code,name,board,status,change_type,first_announce_date,file}]}
2023/
  ...
```

## 字段字典（顶层）

| 字段 | 说明 |
| ---- | ---- |
| code / name / board | 证券代码 / 证券简称 / 上市板块（null 表示原文未披露） |
| status / status_date | 交易状态（过户完成 / 进行中 / 已终止）/ 状态日期 |
| plan_date / sign_date / first_announce_date | 筹划 / 签署 / 首次公告日期 |
| transferors / transferees | 转让方 / 受让方（数组，含持股比例等） |
| change_type | 变更方式：直接协议转让 / 间接转让 / 表决权委托 / 一致行动协议 / 司法拍卖等 |
| structure | 交易结构标签数组（如 ["协议转让", "表决权放弃"]） |
| shares / pct / price_per_share / total_amount / premium_pct | 股数 / 比例 / 单价 / 总价 / 溢价率 |
| vote_rights | 表决权安排 |
| **payment** | **付款节奏**（字符串，null=原文未披露） |
| **conditions** | **先决条件/生效条件**（字符串数组） |
| **breach** | **违约责任**（字符串） |
| **reps_warranties** | **陈述与保证**（字符串） |
| **transition** | **过渡期安排**（字符串） |
| **tax** | **税费承担**（字符串） |
| governance / termination / offer | 治理安排 / 终止条款 / 要约收购 |
| completeness | 协议披露完整性：协议全文已披露 / 仅摘要条款 / 未披露协议条款 |
| notes | 备注（含资料异常说明，如源文件错配、仅为更名不构成实质变更等） |
| evidence | 通用引证：{字段名: {file, quote}}，conditions 为数组 |
| reps_warranties_evidence / transition_evidence / tax_evidence | 三个新字段的引证 {file, quote} |

**null 约定**：字段值为 null 表示已核查原文但未披露，**不是**数据缺失；
quote 引文 ≤60 字，均可在 file 标注的公告原文中找到。

## 数据来源与质控

- 来源：巨潮资讯 / 交易所披露的上市公司公告原文（提示性公告、协议全文、过户公告等）。
- 抽取：AI 多模型并行抽取（2022–2024 年度），每字段附原文引证。
- 质控：`validate.py --deep` 全量校验（JSON 合法性 + 必填字段 + 引文回原文比对），
  2022 / 2023 / 2024 三年度 ERROR 0；2025 / 2026 同口径校验通过。
- 已知异常：约 15 单存在源文件错配（如目录名与正文证券代码不符），
  已在 notes 中如实标注、字段填 null，未编造。

## 使用

- MCP 查询：见 [lennonli/ctl-deals-mcp](https://github.com/lennonli/ctl-deals-mcp)
- 本地直读：`data = json.load(open('2022/002576.json'))`

## 许可

数据来源于上市公司公开披露公告，仅供研究学习使用。
