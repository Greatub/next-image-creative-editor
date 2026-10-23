# `.nksc` 参数文件格式 —— 研究记录

最后更新：2026-10-23

[English](../../research/nksc-format.md) | [简体中文](nksc-format.md)

状态：**研究记录**，不是规范。涉及 Q-002、Q-003、Q-005，并为 Q-016、Q-017 提供依据。

## 1. 样本与方法

- 相机：尼康 Z 30，固件 Ver.01.20；镜头 NIKKOR Z DX 12-28mm f/3.5-5.6 PZ VR；`DSC_3183.NEF`（负责人本地的 `Sample Photos/`，已被 git 忽略）。
- 写入软件：**NX Studio 1.11（macOS，"1.11 M"）**。以下结论只适用于该版本。
- 读取测试：同一张 NEF 复制 5 份，在 NX Studio 中每份只改一项，然后逐字段对比。

| 样本 | 在 NX Studio 中的修改 |
|---|---|
| a | 曝光补偿 +1.0 |
| b | 白平衡 → 日光 |
| c | 优化校准 → 鲜艳 |
| d | 对比度 +2 |
| e | 不改参数，评分 3 星（基准） |

- 写入测试：由 NICE 生成 `.nksc`（基于样本 e），在 NX Studio 中打开；其中一个文件再由 NX Studio 重新保存。

## 2. 文件结构

- 位置：`<照片目录>/NKSC_PARAM/<含扩展名的文件名>.nksc`（例如 `DSC_3183_a.NEF.nksc`）。
- 编码：UTF-8 的 XMP 包（`<?xpacket …?>`、`x:xmpmeta` / `rdf:RDF`，只有一个 `rdf:Description rdf:about="core-sidear-tags/1.0"`）。
- **NX Studio 没有修改 NEF**（所有样本的 MD5 相同），与 NICE 的"原文件只读"原则一致。

| 元素 | 内容 |
|---|---|
| `sdc:about`、`sdc:version`、`sdc:appversion`、`sdc:appname` | `nikon sidecar/1.0`、`1.1.0`、`1.11 M`、`NX Studio` |
| `ast:version` | `11.0.0.3000` |
| `ast:XMLPackets/rdf:value` | 内嵌 XMP 包的 **Base64**（每行 76 字符，以 `&#xA;` 分隔），包含 `xmp:Rating` 与 `MicrosoftPhoto:Rating` |
| `ast:GPSVersionID` | Base64 二进制 |
| `nine:version` | `2.0.0` |
| `nine:NineEdits` | **经 XML 转义**的 `<userData>` 文档，包含编辑滤镜（本样本中 39 个） |
| `nine:Label`、`nine:Rating` | 所有样本中均为 `0`（评分**不**存在这里，见 §4） |
| `nine:Trim` | Base64 二进制 |

命名空间：`sdc` = `http://ns.nikon.com/sdc/1.0/`，`ast` = `http://ns.nikon.com/asteroid/1.0/`，`astype` = `http://ns.nikon.com/asteroid/Types/1.0/`，`nine` = `http://ns.nikon.com/nine/1.0/`。

## 3. `NineEdits` 滤镜

每个滤镜：`<filter id="…"><active>true|false</active><parameters>…</parameters></filter>`。参数大多是纯文本（`<integer name=…>`、`<double name=…>` 或具名元素），部分是 Base64 二进制。

未编辑的 NEF 默认启用 17 个：`nikon::PictureControl`、`nikon::QuickFixToneCurve`、`nikon::ColorBalance`、`nikon::Vignette`（强度 18）、`nikon::ChrAb`、`nikon::NXHistory`、`nikon::Diffraction`、`nikon::Distortion`（自动）、`nikon::ApplicationData`、`nikon::ExposureSettings`、`nikon::QuickFixContrast`、`nikon::DustOff`、`nikon::transform`、`nikon::SizeRes`、`nikon::ActiveDLighting`、`nikon::NoiseReduction`、`nikon::PixelShiftNoiseReduction`。

**镜头校正（暗角、畸变、色差、衍射）和动态 D-Lighting 默认开启。** 这解释了 LibRaw 直接渲染与尼康效果之间的部分差距（Q-016）。

## 4. 字段对应（经单项修改对比确认）

| 设置 | 位置 | 编码 | 说明 |
|---|---|---|---|
| 曝光补偿 | `nikon::ExposureSettings` → `ApplyGain`（`true`）、`Gain`（EV，例如 `1`、`-0.7`） | 文本 | 易读易写 |
| 白平衡 | `nikon::WhiteBalance` → `active` = `true`；`<binary name="Data">`（57 字节，`version` 9） | 二进制 | 已解出：0–15 字节为两个小端 double，即 R/B 增益（1.863、1.428，与相机白平衡一致）；第 16 字节起的 int32 为模式（**2 = 拍摄时设定/自动，3 = 日光**）；第 24 字节起的 uint32 为色温 K（5189 → 5200）；后面一个 double 从 0.15 变为 0（可能是色调/微调，未确认）。其余字节未解出 |
| 优化校准（类型） | `nikon::PictureControl` → `PictureControl` 编号（**0 = 自动，4 = 鲜艳**）+ `Export/ExportData` | 文本 + 二进制 | `ExportData` 是以 `NCP\0` 开头的 248 字节数据块，含 ASCII 名称（`AUTO`、`VIVID`）和定长记录 |
| 优化校准中的对比度 | `ExportData` 第 **192** 字节（值 = 128 + 调整量；+2 → 130），第 **62** 字节置 1（已修改标记） | 二进制 | 对比度属于优化校准参数，**不是** `nikon::QuickFixContrast`（该值仍为 0） |
| 其他优化校准项 | `ExportData` 中的其他记录（负责人一次修改中第 172、182、212、222 字节同时变化） | 二进制 | 具体对应关系还需逐项测定 |
| 评分 | `ast:XMLPackets` 内嵌的 XMP：`xmp:Rating`（3）与 `MicrosoftPhoto:Rating`（3 星为 50） | Base64 → XMP 文本 | `nine:Rating` 保持 0。两个值都要写；保留包内填充，使包长度稳定 |
| 色标 | 推测为 `nine:Label` | 文本 | 尚未测试 |

## 5. 写入测试（NICE → NX Studio）

| 文件 | NICE 写入的内容 | NX Studio 中的结果 |
|---|---|---|
| w1 | 曝光 −0.7（`ApplyGain` true，`Gain` −0.7） | ✅ 正确显示并生效 |
| w2 | 对比度 −3（第 192 字节 = 125，标记 = 1），类型为自动 | ⚠️ 能读取，但超出范围：**自动**类型下 NX Studio 只允许 **±2**。负责人重新保存后，NX Studio 写入的是 **−2**（第 192 字节 = 126） |
| w3 | 评分 5（`xmp:Rating` 5，`MicrosoftPhoto:Rating` 99） | ✅ 显示 5 星 |
| w4 | 不改参数；在 `rdf:Description` 中加入自定义命名空间元素 `<nice:Probe>` | ✅ 正常打开。负责人修改了一项优化校准参数并**重新保存后，NX Studio 保留了这个自定义元素和命名空间** |

## 6. 结论

1. **Q-002 —— 可行。** 格式是 XMP 文本；NICE 写入的文件被 NX Studio 1.11 接受，纯文本字段可以往返读写。二进制子块（白平衡、优化校准）已部分解出，可按已知偏移修改。
2. **取值范围由 NICE 负责。** NX Studio 读取超出范围的值时不提示，只在重新保存时截断。NICE 写入前必须按各优化校准类型的尼康取值范围校验（Q-017）。
3. **Q-003 —— 未知数据在一次重新保存后被保留。** NX Studio 1.11 保留了自定义命名空间元素。这只是一个版本上的一次测试，不能保证以后的版本行为相同。
4. **Q-005 —— 评分位置已明确。** 评分存放在内嵌 XMP 包中（`xmp:Rating` + `MicrosoftPhoto:Rating`），而不是 `nine:Rating`。

## 7. 由此得出的实现规则

- 以"外科手术"方式修改 `.nksc`：只改 NICE 负责的字段；其他滤镜、属性、Base64 行宽（`XMLPackets` 76、`ExportData` 60）和 `&#xA;` 分隔符保持原样。
- 重写内嵌 XMP 包时保留包内填充。
- 不写入超出尼康范围的值；维护按优化校准类型划分的范围表（Q-017）。
- 写入前重新读取文件（负责人可能在 NX Studio 中改过），合并而不是覆盖。
- 记录文件中的 NX Studio 版本（`sdc:appversion`）；遇到未验证的新版本时先按只读处理，重新验证后再写入。

## 8. 尚待研究

- 其余优化校准参数（锐化、清晰度、饱和度、色相、亮度、快速锐化、滤镜效果）及各类型的取值范围（Q-017）。
- 白平衡其余字节；色调/微调的含义；其他白平衡模式（阴天、白炽灯、色温 K、预设）。
- 色标取值（`nine:Label`）。
- 其他 NX Studio 版本、Windows 版和 Capture NX-D 的行为。
- 高效率 RAW 样本（Q-004）与渲染一致性（Q-016）。
