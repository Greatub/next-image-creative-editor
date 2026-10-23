# 数据模型（概念）

最后更新：2026-10-23 · 字段名为建议的代码标识符，最终以实现为准

## Photo（照片）

| 字段 | 说明 |
|---|---|
| `id` | 资料库内唯一 ID |
| `path` | 主文件路径（NEF 优先） |
| `pairPath` | 同名 JPEG 路径（NEF+JPEG 对），可为空 |
| `captureTime`, `camera`, `lens`, `exif` | 只读拍摄信息 |
| `rating` | 0–5 |
| `colorLabel` | `none \| red \| yellow \| green \| blue \| purple` |
| `rejected` | 排除标记（L2） |
| `isFilmScan` | 是否为底片翻拍（L2） |
| `rollId` | 所属胶卷（底片翻拍时） |
| `params.l1` | L1 参数（映射到 `.nksc`） |
| `params.l2` | L2 参数（映射到 `.nice`） |

显示规则：「NEF + JPEG 叠加为一张」打开时，一对文件在照片条中显示为一个条目（类型 `NEF+JPG`，带层叠效果），信息面板可切换查看 NEF / JPEG。

## Album（相册）

相册 = 照片的归类。同一张照片可属于多个相册；加入相册不移动、不复制原文件。

| 字段 | 说明 |
|---|---|
| `id`, `name` | — |
| `kind` | `normal \| smart \| temp` |
| `parentId` | 嵌套；选择上级相册时包含所有下级相册的照片 |
| `members` | 有序的照片 ID 列表（`normal`、`temp`） |
| `rule` | 智能相册规则（例如 `rating >= 4`、所属相册、相机、日期、已编辑） |
| `writeKeywords` | 是否写入照片关键字；`normal` 默认 `true`，`temp` 与 `smart` 默认 `false` |
| `book` | 可选，相册书数据（见下） |

排序：`normal` 相册使用自身顺序；上级相册与智能相册按拍摄时间。

## Roll（胶卷）

| 字段 | 说明 |
|---|---|
| `id`, `name` | — |
| `filmType` | `c41 \| bw \| e6` |
| `baseSample` | 片基取样颜色 |
| `negParams` | 密度、青红、品绿、黄蓝、除尘、裁片边 |
| `profile` | 胶片档案 |

## Book（相册书）

| 字段 | 说明 |
|---|---|
| `size` | `sq20 (20×20) \| p21 (21×28) \| l28 (28×21) \| sq30 (30×30)`，单位 cm |
| `bleedMm` | 默认 3 |
| `safeMm` | 默认 5 |
| `binding` | 装订方式与书脊宽度 |
| `vendorPreset` | 印厂预设 |
| `spreads` | 有序跨页列表；第一个为封面，最后一个为封底 |

**Spread（跨页）**：`template`、`placed`（照片框 → 照片 ID）、`text`（图注文字）。
**Template（模板）**：照片框与文字框的几何以「毫米 + 相对位置」描述；贴边照片框自动延伸到出血线；换尺寸时的行为：按比例缩放 / 保持毫米重排 / 锁定尺寸。项目中嵌入模板副本，模板库修改不影响已有相册书。

页码规则：封面 → 2–3 → 4–5 … → 封底。

## IdPhotoSpec（证件照规格）

| 字段 | 说明 |
|---|---|
| `id`, `name` | 例如「申根 / 英国 / 日本」 |
| `widthMm`, `heightMm` | 照片尺寸 |
| `headTopPct`, `chinPct`, `eyeBandPct` | 参考线位置（占照片高度的百分比） |
| `background` | 允许的背景色 |
| `digital` | 电子版像素与文件大小要求（P1） |
| `source`, `checkedAt` | 官方来源链接与核对日期（必须填写） |

设计稿中的参考线数值为示意，**正式数据必须逐条对照官方要求**（Q-008）。

## PrintJob（打印任务）

纸张、方向、布局（`1 \| 2 \| 4 \| 9 \| id`）、适合方式、页边距、打印信息字段、打印机（含 PDF）、ICC、渲染方式、软打样、份数；打印内容 = 底栏选中的照片。

## Preferences（偏好）

主题（`dark \| gray`）、显示方式（`std \| focus \| immersive`）、窗口 / 全屏、上次来源、显示器 ICC、默认目录、快捷键。启动时恢复上次的显示方式与窗口状态。
