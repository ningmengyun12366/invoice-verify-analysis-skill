# 柠檬云企业发票核验与征信分析

本公开源码包只包含 `invoice-verify-analysis` 业务 Skill。公共的 Go 实现、Node.js 打包脚本和依赖代码保留在 `go-script/` 中，便于查看、构建和维护。

## 描述

**名字：** 企业经营征信分析_企业发票核验_发票查验真伪_票据真实性校验_发票风控审核_发票数据分析_财务会计实用Skill_柠檬云财税发票验真

**描述：**

柠檬云AI财税开放平台：发票核验与企业经营分析Skill，专为财务、会计、财税人员打造，可自动核验发票真实性，依托发票票据数据生成结构化经营分析结果，支撑企业财税审核、供应商尽调、合同付款、商业风控等各类业务审核场景。
支持多种形式发票材料上传，自动完成发票真伪核验、异常票据识别，多维度拆解统计发票数据，输出清晰的票据核验记录与经营分析报表。
✅多格式票据智能解析：兼容 Excel 发票清单、PDF、图片等各类发票文件，自动识别提取票面财务信息；
✅发票真伪自动查验：对接发票查验系统，自动提取发票关键要素，校验发票真伪与发票状态，标记作废、红冲等异常票据；
✅自动生成发票台账：汇总统计全量票据数据，输出标准化核验明细与经营分析报表，辅助会计做账、财务审核与企业风控决策。

详细的 Skill 规则、使用前准备和安装更新流程见 [`invoice-verify-analysis/README.md`](./invoice-verify-analysis/README.md)、[`invoice-verify-analysis/SKILL.md`](./invoice-verify-analysis/SKILL.md) 和 [`invoice-verify-analysis/invoice-verify-analysis-install.md`](./invoice-verify-analysis/invoice-verify-analysis-install.md)。

## 目录结构

- `invoice-verify-analysis/`：当前 Skill 的说明、安装文档、引用资料和资源。
- `go-script/`：Go 命令行实现、公共库和生产打包脚本。

## 生产构建

构建要求 Node.js 20+ 和 Go 1.24+。请在仓库根目录执行：

```powershell
npm ci
node go-script/build.js --env prod invoice-verify-analysis
```

生产发布物生成在 `dist/Skills/invoice-verify-analysis/`。运行时 ZIP 包不包含 `bin/**` 二进制文件，安装时按安装文档下载当前平台对应的二进制。

## 配置与数据安全

- API 凭据由使用者在本地工作区配置，不应写入源码、文档或日志。
- 生产接口通过 `go-script/config/prod.json` 注入发布包。
- 安全问题请按照 [SECURITY.md](SECURITY.md) 私下报告。

## 许可证

本项目自有代码、文档和资源文件采用 [MIT License](LICENSE)；第三方组件与字体许可见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。
