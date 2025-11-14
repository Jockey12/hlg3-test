# HLG3 Quick Reference for MLV App

## Essential Settings

### Processing Gamut
```
Rec.2020 (BT.2020)
```
*Select from predefined dropdown - Defines the RGB color space primaries (without transfer function)*

### Transfer Function (Manual Entry)
**IMPORTANT:** Must be entered manually - HLG is not available as a predefined option

**Enter this exact formula:**
```
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```
*Applied after white balance, gamut conversion, and exposure to convert linear data to HLG*

## Quick Setup Steps

1. Open MLV App
2. Load your MLV footage
3. Set **Processing Gamut** to **Rec.2020** (select from predefined dropdown)
4. **Manually enter the Transfer Function** formula (see below - HLG is not available as a preset)
5. Adjust white balance and exposure as needed
6. Export with 10-bit or higher settings

## Transfer Function Formula (Manual Entry)

Since HLG is not available as a predefined tonemap function, copy and paste this exact formula into the Transfer Function field:

```
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

**Note:** Use natural logarithm (log/ln), not log10

## Color Primaries (Rec.2020)

| Color | x | y |
|-------|-------|-------|
| Red | 0.708 | 0.292 |
| Green | 0.170 | 0.797 |
| Blue | 0.131 | 0.046 |
| White (D65) | 0.3127 | 0.3290 |

## Key Characteristics

- **Dynamic Range**: ~15 stops
- **Middle Gray**: 38-42% IRE
- **System Gamma**: 1.2
- **Peak Luminance**: 1000 nits (scalable to 10,000)

## Export Recommendations

- **Codec**: ProRes 422 HQ or H.265 (HEVC)
- **Bit Depth**: 10-bit minimum
- **Container**: MOV or MP4
- **Color Space**: Rec.2020
- **Transfer**: HLG

## Monitoring Tips

- ✓ Use HDR monitor in HLG mode for accurate preview
- ✓ Middle gray should be around 38-42%
- ✓ Keep highlights below 100%
- ⚠ Will appear darker on SDR monitors (this is normal)

---
For detailed information, see [HLG3_COLOR_PROFILE.md](HLG3_COLOR_PROFILE.md)
