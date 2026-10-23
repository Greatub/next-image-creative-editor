# NICE · Next Image Creative Editor

面向尼康（Nikon）用户的桌面照片软件：浏览选片 → 编辑 → 相册编排 → 打印输出。
核心承诺：**编辑参数独立于原文件保存，原始 NEF/JPEG 永不改动；尼康兼容的那部分参数写入 `NKSC_PARAM/*.nksc`，可被 NX Studio / Capture NX-D 读取。**

- 状态：界面构想阶段，**待定稿**（最后更新 2026-10-23）
- 平台：macOS 与 Windows 桌面（图标资产已同时提供 `.icns` 与 `.ico`）；技术选型未定，见 `docs/open-questions.md`

## 文件夹地图

| 路径 | 内容 | 谁来维护 |
|---|---|---|
| `AGENTS.md` | **所有参与开发的 agent 必读**：工作规则、真相来源、修改流程 | 项目负责人 |
| `CLAUDE.md` | 指向 `AGENTS.md`（供 Claude Code 自动加载） | — |
| `docs/product.md` | 产品需求：定位、用户、v1 范围、四个页面的功能清单 | 产品 |
| `docs/ui-spec.md` | 界面规范：统一骨架、尺寸、显示方式、防抖规则、各页布局、交互与快捷键 | 设计 |
| `docs/architecture.md` | 参数分层（L1/L2/L3）、边车文件、处理管线、负片转换、待验证技术点 | 架构 |
| `docs/data-model.md` | 数据模型：照片、NEF+JPEG 对、相册（含智能/临时/嵌套）、相册书、证件照规格、导入导出 | 架构 |
| `docs/decisions.md` | **已定决策记录**（按编号，不可随意推翻） | 项目负责人 |
| `docs/open-questions.md` | 待决策 / 待验证事项 | 全体 |
| `docs/roadmap.md` | 版本范围：v1（P0）、v1.x（P1）、以后 | 产品 |
| `docs/glossary.md` | 术语表（中英对照、代码标识符） | 全体 |
| `design/tokens.json` | 设计令牌：两套主题颜色、字号、尺寸、动效时长 | 设计 |
| `design/prototype/` | 可交互设计稿的本地副本（参考用）与说明 | 设计 |
| `App Icon/` | 应用图标成品（svg / icns / ico / 各尺寸 png） | 品牌，**勿改** |
| `SVG Icon/` | 图标设计备选稿 | 品牌，**勿改** |

## 代码仓库与协作

- GitHub 仓库：[Greatub/next-image-creative-editor](https://github.com/Greatub/next-image-creative-editor)（私有，访问需授权）。
- `main` 用于保存同步后的项目基线；后续修改在独立功能分支完成，再通过 Pull Request 合入。
- 提交前检查 `git status` 与暂存区内容；不提交本地环境配置、凭据或真实用户照片。`.gitignore` 已排除系统文件及常见本地配置。
- 当前只有文档、设计令牌、原型和图标资产，尚无 `package.json`，因此暂不适用 `pnpm lint`、`pnpm typecheck`、`pnpm build`。技术栈确认后再补充开发与验证命令。

## 建议阅读顺序

1. `AGENTS.md`
2. `docs/decisions.md` —— 先知道哪些已经定了
3. `docs/product.md` → `docs/ui-spec.md` → `docs/architecture.md` → `docs/data-model.md`
4. `docs/open-questions.md` 与 `docs/roadmap.md`
5. 需要看界面时：`design/prototype/README.md`

## 一句话原则

一个功能只有一个位置 · 四个页面骨架相同、切换无抖动 · 照片区保持干净 · 导航即工作流（浏览 → 编辑 → 相册 → 打印）· 原始文件只读。
