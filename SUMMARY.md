# Summary: Sony HLG3 for MLV App

## Direct Answer

To use Sony's HLG3 color profile in MLV App, configure these two settings:

### 1. Processing Gamut
**Set to:** `Rec.2020` (or `BT.2020`)

**What it does:** Defines the RGB color space primaries (without transfer function). Converts camera sensor data to wide color gamut during the gamut conversion stage.

**Requirements:** "Use Camera Matrix" must be enabled for this setting to take effect.

**How:** Select from the predefined Processing Gamut dropdown options.

### 2. Transfer Function (Manual Entry Required)
**IMPORTANT:** HLG is **NOT** available as a predefined tonemap function. You must manually enter the transfer function formula.

**Enter this formula:**
```c
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

**What it does:** Applies the HLG transfer function to convert linear RGB data to HLG-encoded output. This happens AFTER white balance, gamut conversion, and exposure adjustments.

## HLG Transfer Function Formula

For MLV App source code implementation (following the format of Sony S-Log3):

```c
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

Where:
- `x` is normalized linear scene light (0.0 to 1.0)
- `log` is natural logarithm (ln), not log10
- Output is HLG signal (0.0 to 1.0)

## Processing Pipeline

MLV App processes footage in this order:

```
Raw Bayer Data
    ↓
1. White Balance (linear)
    ↓
2. Gamut Conversion (linear) ← Processing Gamut setting used here
    ↓
3. Exposure Adjustment (linear)
    ↓
4. Tonemap Function ← HLG transfer function applied here
    ↓
HLG-encoded Output
```

## Key Points

- **Processing Gamut = Rec.2020**: This defines ONLY the color primaries (red, green, blue coordinates), NOT the transfer function
- **Tonemap Function = HLG**: This is the actual transfer/gamma curve that converts from linear to HLG
- HLG is applied to linear data, not to already-encoded data
- HLG uses natural logarithm (ln), unlike S-Log3 which uses log10
- Output should be 10-bit minimum for proper HLG encoding

## Rec.2020 Color Primaries

| Primary | CIE x | CIE y |
|---------|-------|-------|
| Red | 0.708 | 0.292 |
| Green | 0.170 | 0.797 |
| Blue | 0.131 | 0.046 |
| White (D65) | 0.3127 | 0.3290 |

## HLG Transfer Function Details

**Lower range** (0 ≤ x ≤ 1/12):
- Formula: `E' = √(3 × E)`
- Gamma-like curve (approximately gamma 0.5)

**Upper range** (1/12 < x ≤ 1):
- Formula: `E' = a × ln(12 × E - b) + c`
- Logarithmic curve for high dynamic range
- Constants: a=0.17883277, b=0.28466892, c=0.55991073

**Transition point:** x = 1/12 ≈ 0.08333333

## Export Settings

When exporting from MLV App with HLG:

- **Container**: MOV or MP4
- **Codec**: ProRes 422 HQ or H.265 (HEVC)
- **Bit Depth**: 10-bit minimum, 12-bit preferred
- **Color Space**: Rec.2020 / BT.2020
- **Transfer Characteristic**: HLG
- **Color Range**: Full range

## Comparison with S-Log3

| Aspect | Sony S-Log3 | HLG |
|--------|-------------|-----|
| **Processing Gamut** | S-Gamut3 / S-Gamut3.Cine | Rec.2020 |
| **Lower Range** | Linear | Square root (gamma ~0.5) |
| **Upper Range** | log10 based | Natural log (ln) based |
| **Threshold** | 0.01125 | 0.08333333 (1/12) |
| **Purpose** | Log grading workflow | Direct HDR display |
| **Grading Required** | Yes, needs LUT/grading | Minimal, display-ready |

## Documentation Index

1. **[README.md](README.md)** - Project overview and MLV App pipeline explanation
2. **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Quick settings and setup steps
3. **[HLG3_COLOR_PROFILE.md](HLG3_COLOR_PROFILE.md)** - Complete guide with usage examples
4. **[MLVAPP_CONFIGURATION.md](MLVAPP_CONFIGURATION.md)** - Configuration steps and troubleshooting
5. **[HLG_TRANSFER_FUNCTION_CODE.md](HLG_TRANSFER_FUNCTION_CODE.md)** - Code implementation details
6. **[TECHNICAL_REFERENCE.md](TECHNICAL_REFERENCE.md)** - Mathematical specifications
7. **[SUMMARY.md](SUMMARY.md)** - This document (quick overview)

## Common Questions

**Q: What's the difference between Processing Gamut and Tonemap Function?**  
A: Processing Gamut defines the color space primaries (coordinates of red, green, blue) without any transfer function. Tonemap Function is the gamma/transfer curve applied to linear data as the final processing stage.

**Q: Can I use HLG with Rec.709 gamut?**  
A: Technically yes, but it defeats the purpose. HLG is designed for wide color gamut (Rec.2020). Using Rec.709 will severely limit the color range and won't be true HDR.

**Q: Do I need an HDR monitor to use HLG?**  
A: For accurate preview, yes. HLG will appear darker on SDR monitors (this is normal). However, you can still export HLG content for viewing on HDR displays.

**Q: Is this the same as standard HLG?**  
A: HLG3 is Sony's implementation of the standard HLG (ITU-R BT.2100) with minor optimizations for Sony sensors. The base transfer function is the same standard HLG.

**Q: Why natural log instead of log10?**  
A: The HLG standard (ITU-R BT.2100) specifies natural logarithm. This is different from log-based formats like S-Log3 or Cineon which use log10.

## Standards References

- **ITU-R BT.2100-2**: Image parameter values for high dynamic range television
- **ITU-R BT.2020-2**: Ultra-high definition television color space
- **ARIB STD-B67**: Original HLG specification (BBC/NHK)
- **SMPTE ST 2084**: PQ (alternative HDR format for comparison)
- **SMPTE ST 2086**: Static HDR metadata

## Version

- Documentation Version: 1.0
- Last Updated: 2025-11-14
- Based on: ITU-R BT.2100-2 (July 2018)

---

**Quick Reference Card:**

```
┌─────────────────────────────────────────────────────────────┐
│  Sony HLG3 for MLV App - Quick Setup                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Processing Gamut:    Rec.2020 (select from dropdown)      │
│                                                             │
│  Transfer Function:   MANUAL ENTRY REQUIRED                │
│                                                             │
│  Formula to Enter:                                          │
│  (x >= 0.08333333) ?                                        │
│    (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : │
│    sqrt(3.0 * x)                                            │
│                                                             │
│  Export Bit Depth:    10-bit minimum                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```
