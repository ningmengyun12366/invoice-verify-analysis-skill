# invoice-verify-analysis · 柠檬云企业发票核验与征信分析 Skill

> **给开发者**：把「发票真伪核验 + 重复风险识别 + 结构化经营分析 + 可交付台账 / 报告」这块能力，免费接进你自己的 AI Agent、SaaS 或工作流。不用自己对接税局查验通道、不用维护图片 / PDF / OFD 版式解析、也不用让大模型凭印象编统计结论。

**Open-source Chinese invoice verification & business-analysis Skill for AI agents** — MIT licensed · structured JSON / XLSX / PDF output · real-time tax bureau verification · free API key · Go static binary.

![License: MIT](https://img.shields.io/badge/license-MIT-green) ![Open Source](https://img.shields.io/badge/open%20source-Skill%20layer-blue) ![Go](https://img.shields.io/badge/Go-1.24%2B-00ADD8) ![Node](https://img.shields.io/badge/Node-20%2B-339933) ![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey) ![Open Platform](https://img.shields.io/badge/%E6%9F%A0%E6%AA%AC%E4%BA%91%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0-%E5%85%8D%E8%B4%B9%E6%8E%A5%E5%85%A5-orange)

[开放平台首页](https://www.nmy.cn) · [Skill 列表](https://www.nmy.cn/skills) · [开放 API 文档](https://opendocs.nmy.cn) · [总集合仓库 lemon_skill](https://github.com/ningmengyun12366/lemon_skill) · [能力矩阵](#这个仓库背后是一整套免费开放能力) · [30 秒跑起来](#30-秒跑起来)

---

## TL;DR：这个仓库解决什么问题

做财税相关的 AI 产品，最难受的不是"让模型总结"，而是让结论**站得住、交得出**：

| 你要做的事     | 自己从零做                                | 用 `invoice-verify-analysis`                                                       |
| --------- | ------------------------------------ | --------------------------------------------------------------------------------- |
| 拿到票面数据    | 适配图片 / PDF / OFD 版式、二维码、Excel 清单字段映射 | 一条 `verify` 命令扫描文件夹，或直接读标准列清单                                                     |
| 判断票是不是真的  | 对接查验通道、处理限频与状态轮询、识别作废 / 红冲 / 失控发票    | 调用查验接口，产物保留「税局查验状态」「查验时间」字段                                                       |
| 识别重复与异常   | 自建查重规则与票据状态分布逻辑                      | `analysis` 补充「是否重复风险」列并输出风险预警                                                     |
| 出一份能交付的分析 | 自己搭统计维度、表格和报告排版                      | 七个统计维度 JSON + 增强 XLSX + PDF 分析报告                                                  |
| 防止模型改写结论  | 靠 prompt 约束，效果不稳定                    | 硬流程门禁：禁止跳过 `verify` 直接 `analysis`；最终文本必须逐字复用 `report-pdf` 返回的 `final_report_text` |
| 接进已有系统    | 自己设计接口、鉴权、错误码                        | JSON / XLSX / PDF 产物可直接入库归档；stdout 纯 JSON，可直接管道接进服务                               |

**它是一个 Skill，也是一个 CLI。** 既可以挂到遵循 `SKILL.md` 规范的 Agent 上，也可以当作命令行工具嵌进 ERP、OA、风控系统或你自己的 SaaS。

---

## 这个仓库开源了什么

MIT 协议，可商用、可修改、可二次分发。仓库里是**完整可读的 Skill 层实现**，不是只放一个安装包：

| 路径                                                                                                                           | 内容                                                 | 你能拿它做什么                                |
| ---------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- | -------------------------------------- |
| [`invoice-verify-analysis/SKILL.md`](./invoice-verify-analysis/SKILL.md)                                                     | 给模型看的能力定义与执行规范：参数规格、强制执行顺序、输出模板与字段映射、统计分析维度、客户沟通口径 | 读懂一个"可审计的财税分析流程"应该怎么被约束；照着改成你自己的 Skill |
| [`invoice-verify-analysis/README.md`](./invoice-verify-analysis/README.md)                                                   | 发布包内的使用者说明（前置要求、API Key 获取与配置）                     | 直接复用为你的产品文档                            |
| [`invoice-verify-analysis/invoice-verify-analysis-install.md`](./invoice-verify-analysis/invoice-verify-analysis-install.md) | 安装 / 更新 / 回滚流程、多平台二进制与 PDF 字体下载约定                  | 参考它的自动更新与失败回滚设计                        |
| [`go-script/lib/invoice-verify-common/`](./go-script/lib/invoice-verify-common/)                                             | 发票解析、批量查验与并发、Excel、OFD、PDF 提取、二维码、报表渲染、运行时脱敏       | 查看、复用或改写底层实现                           |
| `go-script/lib/invoice-assistant-go/` · `go-script/lib/tax-analysis/`                                                        | 开票、发票获取与风险分析、税务分析报告等同平台能力实现                        | 了解同一套构建链如何支撑多个 Skill                   |
| [`go-script/build.js`](./go-script/build.js) · `go-script/config/prod.json`                                                  | 生产打包脚本与接口端点注入                                      | 从源码构建你自己的发布包                           |
| [`SECURITY.md`](./SECURITY.md)                                                                                               | 私下报告安全问题的通道                                        | —                                      |
| [`THIRD_PARTY_NOTICES.md`](./THIRD_PARTY_NOTICES.md) · [`LICENSE`](./LICENSE)                                                | 第三方组件与字体许可、MIT 许可                                  | —                                      |

**边界说清楚**：本仓库开源的是 Skill 定义、文档与客户端实现；发票查验服务端接口与税局通道由[柠檬云 AI 财税能力开放平台](https://www.nmy.cn)提供，运行时需要一个 API Key。这样设计是为了让查验口径与票据状态始终由服务端对齐税局，而不是让每个集成方各自维护一份会过期的副本。

运行环境与发布约定：Node.js 20+ / Go 1.24+ 用于本地构建；发布物生成在 `dist/Skills/invoice-verify-analysis/`；运行时 ZIP 不含 `bin/**` 二进制，也不含 PDF 字体，安装时按当前平台单独下载二进制并补齐两份 Noto Sans SC 字体。支持 Windows amd64、Linux amd64/arm64、macOS amd64/arm64。

---

## 这个仓库背后是一整套免费开放能力

`invoice-verify-analysis` 是[柠檬云 AI 财税能力开放平台](https://www.nmy.cn)开源出来的其中一个 Skill。开发者免费注册后，可以按需要单独接入某一项能力，也可以把整条财税链路补齐到自己的产品里——**不用换系统、不用让用户跳到另一个平台**。

| 能力                               | 状态         | 典型集成场景                     | 源码 / 入口                                                                                                              |
| -------------------------------- | ---------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **企业发票核验与征信分析**（本仓库）             | 已上线        | 供应链风控、供应商准入、贷前尽调           | [`invoice-verify-analysis-skill`](https://github.com/ningmengyun12366/invoice-verify-analysis-skill)                 |
| 全场景开票                            | 已上线        | 电商、ERP、CRM 的自动开票模块         | [`invoice-issue-skill`](https://github.com/ningmengyun12366/invoice-issue-skill)                                     |
| 发票查验查重及台账                        | 已上线        | 报销系统、差旅平台、财务共享中心           | [`batch-invoice-verify-skill`](https://github.com/ningmengyun12366/batch-invoice-verify-skill)                       |
| 发票整理、查验及报销助手                     | 已上线        | 员工报销、票据中台、邮箱收票自动化          | [`invoice-reimbursement-assistant-skill`](https://github.com/ningmengyun12366/invoice-reimbursement-assistant-skill) |
| 发票获取及风险分析                        | 已上线        | 代账机构批量体检、财税健康度看板           | [`invoice-fetch-and-risk-analysis-skill`](https://github.com/ningmengyun12366/invoice-fetch-and-risk-analysis-skill) |
| 企业税务风险分析                         | 已上线        | 财税顾问交付、税务自查、尽调报告           | [`tax-analysis-skill`](https://github.com/ningmengyun12366/tax-analysis-skill)                                       |
| AI 财税专家咨询 · 财税知识库                | 已上线        | 财税问答机器人、Agent 的政策依据兜底、合规留痕 | [`tax-knowledge-skill`](https://github.com/ningmengyun12366/tax-knowledge-skill)                                     |
| 财务软件账套连接器                        | 已上线        | 在 AI 里直接查账、出表、做经营分析        | [Skill 列表](https://www.nmy.cn/skills)                                                                                |
| 资金全流程管理（银行流水 / 回单 / 自动对账 / 资金报表） | 官方路线图，即将上线 | 资金看板、现金流分析                 | [开放平台](https://www.nmy.cn)                                                                                           |

> 想要一次性拿到全部 Skill 的源码与能力矩阵，看总集合仓库 [`lemon_skill`](https://github.com/ningmengyun12366/lemon_skill)。每个子仓库可独立使用，不需要整套引入。

### 需要做系统集成？还有标准化 REST API

除了 Skill，开放平台还把税局侧的高频操作开放成了 REST 接口，文档在 **[opendocs.nmy.cn](https://opendocs.nmy.cn)**：

- **登录业务**：企业登录 API、代理企业登录 API（QYYW / ZRRYW / DLYW 三类登录，异步任务 + 验证码流程）
- **发票同步**：发票数据同步、勾选数据同步、发票附件下载（PDF / OFD 版式文件）
- **发票查验**：与全国增值税查验平台口径一致的单张查验，同步返回查验结论及全票面结构化数据
- **发票开具**：蓝字数电发票开具、红字发票全流程（确认单同步 / 创建 / 确认 / 拒绝 / 撤销）
- **开票基础能力**：基础信息与授信查询、客户与商品信息维护、人脸认证、税率与票种字典

两种接入方式的凭据是不同的，别混用：

| 接入方式            | 凭据                                       | 适合谁                        |
| --------------- | ---------------------------------------- | -------------------------- |
| 调用 Skill / CLI  | 控制台创建的 **API Key**，创建时绑定对应能力（如「发票查验」）及企业 | 个人开发者、AI Agent、SaaS 产品快速验证 |
| 直连开放平台 REST API | 接入申请后由平台提供 **appId / appSecret** 与正式接口地址 | ISV、ERP/OA 厂商做产品级集成        |

注册即享**月度免费调用额度，月月重置**，具体额度、有效期与计费规则以开放平台控制台显示为准；产品级用量、商用授权与更高配额可在控制台查看或联系平台客服。

---

## 30 秒跑起来

### 1. 拿一个免费 API Key

到 [柠檬云 AI 财税能力开放平台](https://www.nmy.cn) 注册登录 → 进入控制台 → **API Key 管理** → 新建 Key，**绑定权限时勾选「发票查验」**（按发布包内 README 要求绑定对应能力及企业）→ 复制 Key。

> API Key 只在你本地运行时通过 `--api-key` 传入，不要写进源码、配置样例、日志或 Issue。

### 2. 安装 Skill

**方式 A · 交给 Agent 自动装**：在 [Skill 列表](https://www.nmy.cn/skills) 找到「企业发票核验与征信分析」，复制安装链接，粘贴到你的 AI 客户端对话框即可。

**方式 B · 当作 CLI 直接用**（推荐给产品集成）：按 [`invoice-verify-analysis-install.md`](./invoice-verify-analysis/invoice-verify-analysis-install.md) 安装发布包、当前平台二进制与两份 PDF 字体；也可以从本仓库源码自行构建。

### 3. 调一次

```bash
# 1) 解析并批量查验真伪，输出中间 XLSX
invoice-verify-analysis verify \
  -i ./invoices/ \
  -o ./verify_result.xlsx \
  --api-key "$LEMON_API_KEY"

# 2) 七个维度统计分析，输出增强 XLSX + JSON
invoice-verify-analysis analysis \
  -i ./verify_result.xlsx \
  -c "示例科技有限公司"

# 3) 生成 PDF 分析报告
invoice-verify-analysis report-pdf \
  --input-text-file ./final_report.txt \
  --output ./分析报告.pdf
```

Windows PowerShell 里首次调用本地二进制前，把 UTF-8 初始化和命令放在同一次终端调用中：

```powershell
$Utf8NoBom = New-Object System.Text.UTF8Encoding($false)
[Console]::InputEncoding = $Utf8NoBom
[Console]::OutputEncoding = $Utf8NoBom
$OutputEncoding = $Utf8NoBom
.\bin\windows-amd64\invoice-verify-analysis.exe verify -i .\invoices\ -o .\verify_result.xlsx --api-key "$env:LEMON_API_KEY"
```

`analysis` 的 stdout 是可解析 JSON，结构示意（字段以实际返回为准）：

```json
{
  "input_file": "...",
  "output_file": "...",
  "company_name": "...",
  "record_count": 0,
  "analysis": {
    "verify_stats": {},
    "status_distribution": {},
    "overview": {},
    "time_stats": {},
    "product_stats": {},
    "counterparty_stats": {},
    "risk_alerts": []
  }
}
```

七个统计维度：核验统计、状态分布、总览、时间分布、商品分布、往来方分布、风险预警。最终 XLSX 保留「税局查验状态」「查验时间」，并补充「是否重复风险」列。

失败时 stdout 仍返回 JSON 错误对象，诊断与进度信息一律走 stderr——**可以直接用管道接进你自己的服务**。输入有两种模式：文件夹模式扫描图片、PDF、OFD；清单模式读取 `.xlsx` / `.csv`，标准列为 `发票号码`、`开票日期`、`价税合计`（必填），`发票代码`、`校验码`（可选），不做自动字段映射，列名不同需先转换。中间 XLSX 必须保留，它是 `reverify` 与 `analysis` 的唯一合法输入。

---

## 它是怎么工作的

```mermaid
flowchart LR
    A["你的产品<br/>Agent / SaaS / ERP / 脚本"] --> B["SKILL.md<br/>能力声明 + 强制执行顺序"]
    B --> C["本地 CLI<br/>Go 静态二进制"]
    C -->|"HTTPS + API Key"| D["柠檬云 AI 财税开放平台"]
    D --> E["税局查验通道<br/>全票面结构化数据"]
    E --> C
    C -->|"中间 XLSX → 增强 XLSX<br/>JSON · PDF"| A
```

几个为了让集成省事的取舍：

- **薄 Agent、厚 CLI**：鉴权、版式解析、批量查验并发、轮询、结果归档收敛在 CLI 里，模型只负责理解意图和按规则组织答案。换个模型版本不会把链路跑断。
- **流程硬门禁**：必须按「参数收集 → 清单文件预处理 → `verify` → 核验结果校验 → 可选 `reverify` → `analysis` → 输出模板与字段映射 → `report-pdf` → 展示结果」顺序执行，禁止跳步；禁止跳过 `verify` 直接调用 `analysis`，禁止复用旧批次核验结果。
- **结论不可被模型改写**：最终用户可见文本必须使用 `report-pdf` 返回的 `final_report_text` 逐字原样输出，AI 不得在 PDF 转换后重新生成、改写或重新排版统计文本。
- **第一阶段职责清晰**：`verify` 只做发票解析与查验，不做查重、不做抬头校验、不做报销分析；重复风险、企业方向、状态分布与经营分析只在 `analysis` 阶段产生。
- **静态二进制**：Go 单文件编译，不依赖 Python / Node 运行时，容器和沙箱里都干净。
- **凭据只在运行时**：`--api-key` 传入，不落源码、不落日志；生产接口端点在构建期由 `go-script/config/prod.json` 注入发布包。

### 兼容哪些 AI 客户端

同一套 `SKILL.md` 规范，无需改动即可用于 [WorkBuddy](https://copilot.tencent.com/work/)、[豆包工作](https://www.doubao.com/work)、[千问办公](https://qwenwork.cn/)、AutoClaw（智谱）、ArkClaw（字节跳动）、MaxClaw（MiniMax）、[OpenClaw](https://openclaw.ai/) 等已适配平台。只要你的 Agent 会读 `SKILL.md` 的 `name` / `description` 决定是否调用，就能挂上；不通过 Agent、纯 CLI 或 REST API 集成也完全可以。

---

## 给 AI 博主 / 内容创作者：可直接引用的事实

做评测、写选型对比或录教程时，下面这几条可以直接用，都附了可复现的验证方式：

**一句话速览（可复制）**

> 柠檬云把「企业发票真伪核验 + 经营分析」做成了 MIT 开源的 Agent Skill：一个免费 API Key、三条命令，输出带税局查验状态的 XLSX 台账、结构化 JSON 与 PDF 报告，并用硬流程禁止模型跳过核验或改写统计结论。同平台共开源了 7 个财税 Skill 仓库，覆盖开票、查验、报销、税务风险分析与政策知识库等场景。

**可核验事实**

| 事实                                                     | 怎么验证                                                                                                 |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Skill 层以 MIT 协议开源，允许商用与二次分发                            | 根目录 [`LICENSE`](./LICENSE)                                                                           |
| 禁止跳过 `verify` 直接 `analysis`；`analysis` 只读取本次核验生成的 XLSX | [`SKILL.md` 的「强制执行规则」](./invoice-verify-analysis/SKILL.md)                                           |
| 最终文本必须逐字复用 `report-pdf` 返回的 `final_report_text`        | [`SKILL.md` 的「展示结果」](./invoice-verify-analysis/SKILL.md)                                             |
| 七个统计维度与上述 JSON 字段结构                                    | [`SKILL.md` 的「统计分析维度说明」](./invoice-verify-analysis/SKILL.md)                                         |
| 最终 XLSX 保留「税局查验状态」「查验时间」                               | [`SKILL.md` 命令调用说明](./invoice-verify-analysis/SKILL.md)                                              |
| API Key 仅通过 `--api-key` 运行时传入，不写入磁盘、配置、日志或输出文件         | [`SKILL.md` 的「发票查验 API Key（强制）」](./invoice-verify-analysis/SKILL.md)                                 |
| 业务命令 stdout 纯 JSON、失败也是 JSON，诊断走 stderr                | [`invoice-verify-analysis-install.md`](./invoice-verify-analysis/invoice-verify-analysis-install.md) |
| 平台已上线 7 个开源 Skill 仓库 + 账套连接器，资金能力在路线图中                 | [GitHub 组织](https://github.com/ningmengyun12366) · [Skill 列表](https://www.nmy.cn/skills)             |
| 税局侧高频操作另有标准化 REST API 文档                               | [opendocs.nmy.cn](https://opendocs.nmy.cn)                                                           |

**30 秒可复现的 demo**：注册 → 控制台创建绑定「发票查验」的 API Key → 准备 2–3 张发票或一份标准清单 → 跑 `verify`，观察中间 XLSX 里的「税局查验状态」「查验时间」→ 跑 `analysis` 看 JSON 七个维度与「是否重复风险」列 → 跑 `report-pdf` 拿到 PDF 报告。

**几个容易写错的地方，先说清楚**：

- 它**不是离线工具**，运行时需要联网调用开放平台查验接口；
- 开源范围是 **Skill 定义、文档与客户端实现**，服务端查验接口与税局通道不在仓库内；
- 第一阶段 `verify` **不做查重、不做抬头校验、不做报销分析**，重复风险与经营分析只在 `analysis` 阶段产生；
- **资金全流程管理**官方标注为"即将上线"，请勿写成已可用；
- Skill 用的 **API Key** 与 ISV 直连 REST API 用的 **appId / appSecret** 是两套凭据；
- 免费额度按月重置，具体数值以控制台实时显示为准，不建议在稿子里写死数字。

欢迎实测、评测与转载。涉及能力状态、额度与计费的表述，请以开放平台控制台与本文档为准。需要接入支持可走开放平台控制台，或服务热线 400-004-0696。

---

## FAQ

**Q：没用 Agent，只有后端服务，能用吗？**
能。CLI 是独立二进制，stdout 纯 JSON；也可以直接走 [开放 API 文档](https://opendocs.nmy.cn) 里的 REST 接口做系统集成。

**Q：丢一个发票文件夹就能直接出报告吗？**
可以，但仍要走完整流程：文件夹模式由 `verify` 解析图片 / PDF / OFD 并查验，再 `analysis` 统计，最后 `report-pdf` 出报告。清单模式只接受 `.xlsx` / `.csv` 且需符合标准列名，脚本不做自动字段映射。

**Q：这个 Skill 会不会自己编统计数字？**
设计上就是为了避免这件事：`analysis` 只允许读取本次 `verify` 刚生成或 `reverify` 刷新后的核验 XLSX，文件不存在、为空或来源不明会中止；最终用户可见文本必须逐字复用 `report-pdf` 返回的 `final_report_text`。规则在 [`SKILL.md`](./invoice-verify-analysis/SKILL.md) 里逐条可查。

**Q：开源范围包含查验接口和税局通道吗？**
不包含。MIT 覆盖本仓库自有代码、文档与资源；查验服务端与税局对接能力由开放平台提供，需要 API Key。这样查验口径与票据状态由服务端统一维护。

**Q：能商用吗？**
MIT 允许商用与二次分发。具体调用额度、产品级用量与商用授权以开放平台控制台与平台说明为准。

**Q：支持哪些平台和 Agent？**
Windows amd64、Linux amd64/arm64、macOS amd64/arm64；遵循 `SKILL.md` 规范的 Agent 客户端均可挂载，已适配的主流平台见上文。

**Q：报告安全问题走哪个渠道？**
请勿开公开 Issue，按 [`SECURITY.md`](./SECURITY.md) 使用 Private vulnerability reporting 私下提交；报告里不要附 API Key、真实企业数据或未脱敏日志。

---

## 自己构建

```bash
npm ci
node go-script/build.js --env prod invoice-verify-analysis
```

生产发布物落在 `dist/Skills/invoice-verify-analysis/`。ZIP 包不含 `bin/**` 二进制，也不含 PDF 字体，安装时按 [`invoice-verify-analysis-install.md`](./invoice-verify-analysis/invoice-verify-analysis-install.md) 的下载表只拉取当前平台对应的一个二进制，并补齐两份 Noto Sans SC 字体（缺字体会导致 PDF 生成失败）。

## 配置与数据安全

- API 凭据由使用者在本地工作区配置，不应写入源码、文档、日志或提交记录；本 Skill 只在运行时通过 `--api-key` 接收，不落盘。
- 生产接口端点通过 `go-script/config/prod.json` 在构建期注入发布包。
- 日志与输出经过脱敏处理（`go-script/lib/invoice-verify-common/runtimeio/redact.go`）。
- 发票、申报等属于敏感业务数据，请按你所在场景的合规要求进行存储与留痕。
- 第三方组件与字体许可见 [`THIRD_PARTY_NOTICES.md`](./THIRD_PARTY_NOTICES.md)。

## 反馈与贡献

集成问题、适配需求与功能建议，欢迎通过仓库 Issue 反馈；若你写了文章、录了教程或基于本仓库做了二次开发，也欢迎贴链接，我们会收录并协助传播。安全问题的私下报告渠道见 [`SECURITY.md`](./SECURITY.md)。

## 许可证

本项目自有代码、文档与资源文件采用 [MIT License](./LICENSE)，允许商用与二次分发。

---

## English / 搜索索引

`invoice-verify-analysis` is an MIT-licensed, open-source Agent Skill for **Chinese enterprise invoice verification and business analysis**. It parses invoice folders (image / PDF / OFD) or standard XLSX/CSV lists, verifies invoices against the tax bureau channel, detects duplicate-reimbursement risks, and produces a structured seven-dimension analysis as JSON, an enhanced XLSX ledger and a PDF report. A hard-gated workflow forbids skipping verification or rewriting the generated statistics. It is one of seven Skills open-sourced by the [Ningmengyun AI Finance & Tax Open Platform](https://www.nmy.cn), which also provides standardized REST APIs for invoice issuance, verification, synchronization and tax login. A free API key with a monthly quota is available after registration.

**Search terms / 检索关键词**：发票查验 API · 发票验真 · 数电发票 · 发票台账 · 重复报销识别 · 供应商尽调 · 企业征信分析 · 财税 AI · 税务风险 · Agent Skill · SKILL.md · MCP tool-use · AI Agent 财税能力 · 开源财税 Skill · 柠檬云开放平台 API Key · Go CLI structured JSON · invoice verification API China · fapiao · fully digitalized e-invoice · duplicate invoice detection · supplier due diligence · corporate credit analysis · Chinese VAT invoice · OpenClaw / WorkBuddy skill · tax risk analysis

---

**给你的 AI 产品补一块「中国财税」的专业能力，从这里开始。**

[免费获取 API Key](https://www.nmy.cn) · [浏览全部 Skill](https://www.nmy.cn/skills) · [开放 API 文档](https://opendocs.nmy.cn) · [总集合仓库 lemon_skill](https://github.com/ningmengyun12366/lemon_skill)

<sub>本仓库由深圳易财信息技术有限公司（柠檬云）维护。
