# NICE · Next Image Creative Editor

最后更新：2026-10-23

[English](../../README.md) | [简体中文](README.md)

面向尼康（Nikon）用户的桌面照片软件：浏览选片 → 编辑 → 相册编排 → 打印输出。
核心承诺：**编辑参数独立于原文件保存，原始 NEF/JPEG 永不改动；尼康兼容的那部分参数写入 `NKSC_PARAM/*.nksc`，目标为可被 NX Studio / Capture NX-D 读取，第三方读写兼容性仍需验证（Q-002）。**

- 状态：界面构想阶段，**待定稿**（最后更新 2026-10-23）
- 平台：macOS 与 Windows 桌面（图标资产已同时提供 `.icns` 与 `.ico`）；技术选型未定，见 [open-questions.md](../../docs/open-questions.md)

目前没有应用实现或可独立运行的演示程序。

## 文档语言

仓库以英文为主、简体中文为辅。默认英文文档为维护基准，本目录提供完整中文译文，更新时同步两种语言并提供双向链接。此规则不决定产品界面语言或改变产品行为。现有中文设计原型保留为参考。

## 文件夹地图

| 路径 | 内容 | 谁来维护 |
|---|---|---|
| [AGENTS.md](../../AGENTS.md) | **所有参与开发的 agent 必读**：工作规则、真相来源、修改流程 | 项目负责人 |
| [CLAUDE.md](../../CLAUDE.md) | 指向 [AGENTS.md](AGENTS.md)（供 Claude Code 自动加载） | — |
| [product.md](../../docs/product.md) | 产品需求：定位、用户、v1 范围、四个页面的功能清单 | 产品 |
| [ui-spec.md](../../docs/ui-spec.md) | 界面规范：统一骨架、尺寸、显示方式、防抖规则、各页布局、交互与快捷键 | 设计 |
| [architecture.md](../../docs/architecture.md) | 参数分层（L1/L2/L3）、边车文件、处理管线、负片转换、待验证技术点 | 架构 |
| [data-model.md](../../docs/data-model.md) | 数据模型：照片、NEF+JPEG 对、相册（含智能/临时/嵌套）、相册书、证件照规格、导入导出 | 架构 |
| [decisions.md](../../docs/decisions.md) | **已定决策记录**（按编号，不可随意推翻） | 项目负责人 |
| [open-questions.md](../../docs/open-questions.md) | 待决策 / 待验证事项 | 全体 |
| [research/](research/nksc-format.md) | 验证工作的研究记录（例如 `.nksc` 格式分析）；不是规范 | 全体 |
| [roadmap.md](../../docs/roadmap.md) | 版本范围：v1（P0）、v1.x（P1）、以后 | 产品 |
| [glossary.md](../../docs/glossary.md) | 术语表（中英对照、代码标识符） | 全体 |
| `../../design/tokens.json` | 设计令牌：两套主题颜色、字号、尺寸、动效时长 | 设计 |
| `../../design/prototype/` | 可交互设计稿的本地副本（参考用）与说明 | 设计 |
| `../../App Icon/` | 应用图标成品（svg / icns / ico / 各尺寸 png） | 品牌，**勿改** |
| `../../SVG Icon/` | 图标设计备选稿 | 品牌，**勿改** |
| `../../Sample Photos/` | 验证用的本地测试照片（NEF / JPEG / NX Studio 参数文件）；已被 git 忽略，只存在于负责人的本地副本 | 负责人 |

## 代码仓库与协作

- GitHub 仓库：[Greatub/next-image-creative-editor](https://github.com/Greatub/next-image-creative-editor)（公共仓库）。
- `main` 用于保存同步后的项目基线；后续修改在独立功能分支完成，再通过 Pull Request 合入。
- 提交前检查 `git status` 与暂存区内容；不提交本地环境配置、凭据或真实用户照片。`.gitignore` 已排除系统文件及常见本地配置。
- 当前只有文档、设计令牌、原型和图标资产，尚无 `package.json`，因此暂不适用 `pnpm lint`、`pnpm typecheck`、`pnpm build`。技术栈确认后再补充开发与验证命令。

## 建议阅读顺序

1. [AGENTS.md](AGENTS.md)
2. [decisions.md](../../docs/decisions.md) —— 先知道哪些已经定了
3. [product.md](../../docs/product.md) → [ui-spec.md](../../docs/ui-spec.md) → [architecture.md](../../docs/architecture.md) → [data-model.md](../../docs/data-model.md)
4. [open-questions.md](../../docs/open-questions.md) 与 [roadmap.md](../../docs/roadmap.md)
5. 需要看界面时：[README.md](../../design/prototype/README.md)

## 一句话原则

一个功能只有一个位置 · 四个页面骨架相同、切换无抖动 · 照片区保持干净 · 导航即工作流（浏览 → 编辑 → 相册 → 打印）· 原始文件只读。
