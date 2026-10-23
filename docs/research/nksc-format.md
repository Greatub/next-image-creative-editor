# `.nksc` sidecar format — findings

[English](nksc-format.md) | [简体中文](../zh-CN/research/nksc-format.md)

Last updated: 2026-10-23. Status: **research notes**, not a specification. Covers Q-002, Q-003, Q-005; feeds Q-016 and Q-017.

## 1. Samples and method

- Camera: NIKON Z 30, firmware Ver.01.20; lens NIKKOR Z DX 12-28mm f/3.5-5.6 PZ VR; `DSC_3183.NEF` (owner's local `Sample Photos/`, git-ignored).
- Writer: **NX Studio 1.11 (macOS, "1.11 M")**. All conclusions below apply to this version only.
- Read test: five copies of the same NEF, each edited in NX Studio with exactly one change, then diffed field by field.

| Sample | Change made in NX Studio |
|---|---|
| a | Exposure compensation +1.0 |
| b | White balance → Daylight |
| c | Picture Control → Vivid |
| d | Contrast +2 |
| e | No edits; rating 3 stars (baseline) |

- Write test: NICE-generated `.nksc` files (derived from sample e) opened in NX Studio; one file then re-saved by NX Studio.

## 2. File structure

- Location: `<photo folder>/NKSC_PARAM/<file name incl. extension>.nksc` (e.g. `DSC_3183_a.NEF.nksc`).
- Encoding: UTF-8 XMP packet (`<?xpacket …?>`, `x:xmpmeta` / `rdf:RDF`, single `rdf:Description rdf:about="core-sidear-tags/1.0"`).
- **NX Studio did not modify the NEF files** (identical MD5 for all samples), consistent with NICE's read-only rule.

| Element | Content |
|---|---|
| `sdc:about`, `sdc:version`, `sdc:appversion`, `sdc:appname` | `nikon sidecar/1.0`, `1.1.0`, `1.11 M`, `NX Studio` |
| `ast:version` | `11.0.0.3000` |
| `ast:XMLPackets/rdf:value` | **Base64** of an embedded XMP packet (76-char lines, `&#xA;` separators). Holds `xmp:Rating` and `MicrosoftPhoto:Rating` |
| `ast:GPSVersionID` | Base64 binary |
| `nine:version` | `2.0.0` |
| `nine:NineEdits` | **XML-escaped** `<userData>` document with the edit filters (39 in these samples) |
| `nine:Label`, `nine:Rating` | `0` in all samples (rating is *not* stored here — see §4) |
| `nine:Trim` | Base64 binary |

Namespaces: `sdc` = `http://ns.nikon.com/sdc/1.0/`, `ast` = `http://ns.nikon.com/asteroid/1.0/`, `astype` = `http://ns.nikon.com/asteroid/Types/1.0/`, `nine` = `http://ns.nikon.com/nine/1.0/`.

## 3. `NineEdits` filters

Each filter: `<filter id="…"><active>true|false</active><parameters>…</parameters></filter>`. Parameters are mostly plain text (`<integer name=…>`, `<double name=…>`, named elements); some are Base64 binary.

Active by default on this unedited NEF (17): `nikon::PictureControl`, `nikon::QuickFixToneCurve`, `nikon::ColorBalance`, `nikon::Vignette` (intensity 18), `nikon::ChrAb`, `nikon::NXHistory`, `nikon::Diffraction`, `nikon::Distortion` (auto), `nikon::ApplicationData`, `nikon::ExposureSettings`, `nikon::QuickFixContrast`, `nikon::DustOff`, `nikon::transform`, `nikon::SizeRes`, `nikon::ActiveDLighting`, `nikon::NoiseReduction`, `nikon::PixelShiftNoiseReduction`.

**Lens corrections (vignette, distortion, chromatic aberration, diffraction) and Active D-Lighting are on by default.** This explains part of the visual gap between a plain LibRaw render and Nikon's output (Q-016).

## 4. Field mapping (confirmed by single-change diffs)

| Setting | Location | Encoding | Notes |
|---|---|---|---|
| Exposure compensation | `nikon::ExposureSettings` → `ApplyGain` (`true`), `Gain` (EV, e.g. `1`, `-0.7`) | Text | Easy to read/write |
| White balance | `nikon::WhiteBalance` → `active` = `true`; `<binary name="Data">` (57 bytes, `version` 9) | Binary | Decoded so far: bytes 0–15 two little-endian doubles = R/B multipliers (1.863, 1.428 — equal to camera WB); int32 at 16 = mode (**2 = as shot/auto, 3 = Daylight**); uint32 at 24 = color temperature in K (5189 → 5200); a later double changed 0.15 → 0 (likely tint/fine-tune, unconfirmed). Remaining bytes undecoded |
| Picture Control (profile) | `nikon::PictureControl` → `PictureControl` index (**0 = Auto, 4 = Vivid**) + `Export/ExportData` | Text + binary | `ExportData` is a 248-byte blob starting `NCP\0`, containing the profile name as ASCII (`AUTO`, `VIVID`) and fixed-size records |
| Picture Control contrast | `ExportData` byte **192** (value = 128 + adjustment; +2 → 130) and byte **62** set to 1 (modified flag) | Binary | Contrast is a Picture Control parameter, **not** `nikon::QuickFixContrast` (that stayed 0) |
| Other Picture Control items | Further `ExportData` records (bytes 172, 182, 212, 222 changed together in one owner edit) | Binary | Exact mapping still to be mapped one parameter at a time |
| Rating | Embedded XMP in `ast:XMLPackets`: `xmp:Rating` (3) and `MicrosoftPhoto:Rating` (50 for 3 stars) | Base64 → XMP text | `nine:Rating` stays 0. Write both values; keep the packet padding so the packet length is stable |
| Color label | `nine:Label` presumably | Text | Not yet tested |

## 5. Write tests (NICE → NX Studio)

| File | Written by NICE | Result in NX Studio |
|---|---|---|
| w1 | Exposure −0.7 (`ApplyGain` true, `Gain` −0.7) | ✅ Displayed and applied |
| w2 | Contrast −3 (byte 192 = 125, flag = 1), profile Auto | ⚠️ Read, but outside the allowed range: with **Auto**, NX Studio allows only **±2**. After the owner re-saved the file, NX Studio stored **−2** (byte 192 = 126) |
| w3 | Rating 5 (`xmp:Rating` 5, `MicrosoftPhoto:Rating` 99) | ✅ Displayed 5 stars |
| w4 | No parameter change; added a custom element `<nice:Probe>` in a custom namespace inside `rdf:Description` | ✅ Opened normally. After the owner edited a Picture Control item and **re-saved, NX Studio kept the custom element and its namespace** |

## 6. Conclusions

1. **Q-002 — feasible.** The format is an XMP text file; NICE-written files are accepted by NX Studio 1.11, and plain-text fields round-trip. Binary sub-blocks (white balance, Picture Control) are partially decoded and editable at known offsets.
2. **Value ranges are NICE's responsibility.** NX Studio reads out-of-range values without warning and only clamps them when it re-saves. NICE must validate against Nikon's limits per Picture Control profile before writing (Q-017).
3. **Q-003 — unknown data survived one re-save.** A custom-namespace element was preserved by NX Studio 1.11. This is a single test on one version; it does not prove future versions will behave the same.
4. **Q-005 — rating location known.** Ratings live in the embedded XMP packet (`xmp:Rating` + `MicrosoftPhoto:Rating`), not in `nine:Rating`.

## 7. Implementation rules derived from these findings

- Edit `.nksc` surgically: change only the fields NICE owns; keep every other filter, attribute, Base64 line length (76 for `XMLPackets`, 60 for `ExportData`) and `&#xA;` separators exactly as found.
- Keep XMP packet padding when rewriting the embedded packet.
- Never write values outside Nikon's ranges; maintain a per-profile range table (Q-017).
- Re-read the file before writing (the owner may have edited it in NX Studio) and merge rather than overwrite.
- Record the NX Studio version (`sdc:appversion`) seen in a file; treat unknown future versions as read-only until re-validated.

## 8. Still open

- Remaining Picture Control parameters (sharpening, clarity, saturation, hue, brightness, quick sharp, filter effects) and their per-profile ranges (Q-017).
- Remaining white-balance bytes; tint/fine-tune semantics; other WB modes (cloudy, incandescent, Kelvin, preset).
- Color label values (`nine:Label`).
- Behavior with other NX Studio versions, Windows builds, and Capture NX-D.
- High Efficiency RAW samples (Q-004) and rendering parity (Q-016).
