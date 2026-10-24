# Render parity: LibRaw vs NX Studio — findings

[English](render-parity.md) | [简体中文](../zh-CN/research/render-parity.md)

Last updated: 2026-10-24. Status: **research notes**, not a specification. Covers Q-016; feeds Q-001, Q-004 and the "Restorable in NX Studio" wording.

## 1. Question

`.nksc` research (see [nksc-format.md](nksc-format.md)) showed that NX Studio can *read* NICE's parameters. This note asks a different question: if NICE renders the same NEF with an open-source decoder, how close does it look to NX Studio, and what causes the gap?

## 2. Samples and method

- Same NEF as the `.nksc` research: NIKON Z 30, NIKKOR Z DX 12-28mm f/3.5-5.6 PZ VR at 12 mm, `DSC_3183`.
- Ground truth: NX Studio 1.11 (macOS) JPEG exports. Sample **e** (no edits, Picture Control Auto) and sample **a** (exposure +1.0 EV). Both are tagged "Nikon sRGB 4.0.0.3002" and are 5568×3712.
- Nikon-to-Nikon reference: the in-camera JPEG `DSC_3183.JPG` and the full-size preview JPEG embedded in the NEF.
- NICE stand-in: LibRaw 0.22 (rawpy) with camera white balance, sRGB primaries, linear output, no auto-brightening, half-size decode.
- Everything was compared at 2784×1856 in linear light. The metric is **CIEDE2000 (ΔE)**, given as mean / 95th percentile.
  - Rough guide: below 1 is invisible; 1–2 is visible only side by side; above 5 is obviously different.
- The pipeline was rebuilt one stage at a time to show which stage accounts for which share of the gap.
  - Geometry: SIFT matches fitted with a radial model; 3,362 inliers, median residual 0.22 px.
  - Vignetting: a radial gain.
  - Tone: a monotone luminance curve.
  - Color: a 3×3 matrix fitted in linear light before the curve, with shadows weighted up.
  - Bound check: a 25³ 3D LUT as an upper bound for *any* global color/tone mapping.
- "Low-frequency" figures blur both images (Gaussian σ = 2 px) before comparing. This removes sharpening, noise-reduction and demosaicing differences and leaves tone and color.

## 3. Results

### 3.1 Stage by stage (sample e)

| Pipeline | ΔE pixel (mean / p95) | ΔE low-frequency σ2 (mean / p95) |
|---|---|---|
| LibRaw plain | 13.99 / 31.88 | 12.62 / 30.98 |
| + lens geometry | 11.33 / 27.10 | 10.60 / 26.39 |
| + tone curve (no vignette) | 3.65 / 9.01 | — |
| + vignette + tone curve | 3.21 / 7.70 | — |
| + 3×3 color matrix ("model A") | 3.20 / 8.92 | **1.80 / 4.79** (σ6: 1.50 / 3.80) |
| 3D LUT fitted on left half, tested on right half | 3.09 / 8.87 | 2.55 / 7.90 |
| 3D LUT fitted on the whole frame (best any global mapping can do) | 2.16 / 6.12 | — |
| *Reference: in-camera JPEG vs NX Studio* | *1.29 / 3.20* | ***0.58 / 1.18*** |
| *Reference: NEF embedded preview vs NX Studio* | *1.39 / 3.47* | ***0.66 / 1.42*** |

All model figures except the LUT holdout row are **in-sample**: they were fitted on this image. Real-world parity will be worse.

### 3.2 What each component contributes

- **Tone is the largest gap.** NX Studio's default render is much brighter in the midtones than a linear camera-WB render. Fitted luminance mapping (linear in → linear out):

  | Input | Output |
  |---|---|
  | 0.10 | 0.26 |
  | 0.18 | 0.48 |
  | 0.30 | 0.70 |

  Highlights roll off above that. This mixes the Picture Control tone curve, Nikon's baseline exposure and Active D-Lighting; one image cannot separate them.
- **Lens geometry is large on this lens.** NX Studio's auto distortion correction scales the frame by 1.063 with strong barrel correction (k1 ≈ −0.28, k2 ≈ +0.15). Edge features move by up to ≈115 px at half size (≈230 px at full size).
- **Vignetting correction is strong.** Gain relative to the center:

  | Normalized radius | Gain | ≈ EV |
  |---|---|---|
  | 0.43 | 1.09 | |
  | 0.62 | 1.24 | |
  | 0.83 | 1.40 | |
  | 0.93 | 1.64 | +0.7 EV in the corners |

- **Color after tone is a small step.** The 3×3 matrix is close to identity except the blue row, so most remaining color difference is not a simple matrix.
- **The remaining pixel-level gap is mostly detail, not color.** The ΔE heat map concentrates on edges and texture: sharpening, noise reduction and demosaicing. Blurring cuts model A from 3.20 to 1.80. Even the best global 3D LUT stays at 2.16 pixel-level.
- **NX Studio's exposure slider is not a linear gain.**

  | Comparison | ΔE (mean / p95) |
  |---|---|
  | NX Studio +1 EV vs NX Studio 0 EV × 2 (linear) | 4.31 / 8.59 |
  | Model A with a ×2 linear gain vs NX Studio +1 EV | 5.43 / 14.14 |

  Measured NX response, output luminance at 0 EV → at +1 EV:

  | 0 EV | +1 EV |
  |---|---|
  | 0.18 | 0.41 |
  | 0.50 | 0.77 |
  | 0.90 | 0.97 |

  NX Studio compresses highlights, so matching its exposure behavior needs its curve model, not just a gain.
- **The NEF carries a Nikon render.** Its embedded preview is a full-size 5568×3712 JPEG (≈2.1 MB) within ΔE 0.66 (low-frequency) of the NX Studio export.

### 3.3 Follow-up (2026-10-24): Active D-Lighting and Picture Control profiles

The owner exported sample **f** (Active D-Lighting set to Off) and samples **g / h / i** (Picture Control Standard / Neutral / Flat). Sample **c** (Vivid) is from the first round. Lens corrections cannot be turned off in NX Studio for this lens, so test 2 in §7 is not possible.

- **Active D-Lighting is not part of the gap on this sample.** The maker notes show the camera shot with Active D-Lighting **Off**, so "same as shot" already meant Off. Sample f is practically identical to e (ΔE 0.20 / 0.53 low-frequency). The fitted tone curves match to three decimals. The bright default tone is therefore the Picture Control curve plus Nikon's baseline exposure.
- **Each profile has its own tone and color.** Results are low-frequency ΔE (mean / p95). Fitted curve values are linear luminance out for inputs 0.03 / 0.18 / 0.50:

  | Profile | vs Auto | Fitted curve | Mean chroma C* | Auto's model applied | Model refitted for this profile |
  |---|---|---|---|---|---|
  | Auto (e) | — | 0.036 / 0.482 / 0.822 | 13.7 | 1.80 / 4.79 | 1.80 / 4.79 |
  | Standard (g) | 0.74 / 1.91 | 0.036 / 0.485 / 0.831 | 12.1 | 1.97 / 4.79 | 1.68 / 4.37 |
  | Vivid (c) | 1.80 / 3.60 | 0.024 / 0.458 / 0.807 | 14.7 | 2.68 / 5.94 | 1.95 / 5.07 |
  | Neutral (h) | 2.52 / 4.44 | 0.051 / 0.404 / 0.763 | 11.3 | 2.96 / 5.66 | 1.55 / 3.96 |
  | Flat (i) | 4.62 / 6.41 | 0.062 / 0.376 / 0.720 | 10.5 | 4.93 / 7.09 | 1.67 / 4.13 |

  - On this scene, Auto behaves almost like Standard.
  - Vivid deepens shadows and adds saturation. Neutral and Flat lift shadows, lower midtones and reduce saturation.
- **One global model per profile fits every profile about equally well (≈ 1.6–2.0 mean).** Reusing Auto's model for another profile does not (up to 4.9 for Flat). Option B therefore needs **calibration data per camera × per Picture Control**, not one shared "Nikon look".

## 4. Conclusions for Q-016

1. **Pixel-identical parity with NX Studio is not achievable** with LibRaw plus a hand-built pipeline. Nikon's own two engines (camera and NX Studio) differ by only ΔE ≈ 0.5–0.6 low-frequency. The best global model here reaches ≈1.5–1.8 in-sample, and worse out of sample.
2. **"Close by eye" is plausible** if NICE models, in order of impact:
   1. the default tone: Picture Control curve + baseline exposure, per profile (§3.3). Active D-Lighting also matters when the camera had it on; it was off for this sample
   2. lens distortion
   3. vignetting
   4. per-profile color
   5. Nikon-like sharpening and noise reduction
3. **"Restorable in NX Studio" must mean parameters, not pixels.** The label is accurate for L1 parameters. It must not promise that NX Studio shows the same image as NICE.
4. **The embedded preview is a free Nikon-accurate view** of unedited photos. It is useful for Browse and as a reference. It also creates an anti-jitter risk: an unedited photo would change look when Edit swaps in NICE's own render (see §5).

## 5. Implications and options (for decision, not decided)

| Option | Parity | Cost / risk |
|---|---|---|
| A. NICE look: LibRaw + own pipeline, no parity claim | Low (ΔE ≫ 5 vs NX by default) | Simplest; users see a different default look from NX Studio and camera JPEGs |
| B. Nikon-like default: LibRaw + lens corrections + per-camera / per-Picture-Control calibrated tone and color, Active D-Lighting approximation | Close by eye (target ΔE ≤ 2 mean, ≤ 5 p95 low-frequency) | Calibration data per camera and profile; needs lens correction data (see below); never exact |
| C. Nikon SDK rendering, if a license allows it | Potentially exact | Depends on Nikon SDK availability, terms and platforms (Q-004); vendor lock-in |

Recommendation: plan for **B**, keep **C** under investigation in Q-004, and word the product as in conclusion 3.

The Browse/Edit look mismatch is a separate UI question, and needs an owner decision. Either:

- always show NICE's render, consistent but not Nikon-identical; or
- show the embedded preview until a photo is edited, faster and Nikon-accurate, but the look changes on first edit.

Lens correction data: the fitted coefficients above are from one image only. An implementation needs real per-lens data. Candidates, all unverified:

- correction data stored in the NEF maker notes (Z-mount bodies embed it for in-camera correction);
- the lensfun database;
- NICE's own calibration.

## 6. Limits of this study

- One scene, one lens, one camera, one Picture Control (Auto). Models were fitted on the image they were tested on, except the LUT holdout.
- 8-bit sRGB JPEGs as ground truth; half-resolution comparison.
- Picture Control curve and baseline exposure were not separated. Active D-Lighting was off in camera, so its effect when on is unmeasured.

## 7. Next tests (owner, NX Studio)

| # | Test | Status |
|---|---|---|
| 1 | Active D-Lighting Off | Done (sample f): no effect, because the camera already had it off |
| 2 | Lens corrections Off | Not possible: NX Studio does not allow it for this lens. Geometry and vignetting must be checked another way, e.g. against data read from the NEF maker notes |
| 3 | Picture Control Standard / Neutral / Flat | Done (samples g / h / i) |
| 4 | A second, different scene, ideally with a color checker or a colorful subject | Open: out-of-sample check |
| 5 | A NEF shot with Active D-Lighting **on** in camera, exported once as shot and once with ADL Off | New: measures ADL itself |
