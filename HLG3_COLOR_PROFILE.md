# Sony HLG3 Color Profile for MLV App

## Overview

Sony's HLG3 (Hybrid Log-Gamma 3) is a gamma curve preset designed for HDR video production. HLG (Hybrid Log-Gamma) is an HDR standard (ITU-R BT.2100) that combines traditional gamma curves with logarithmic encoding to provide high dynamic range content with backward compatibility to SDR displays.

## HLG3 Specifications

### What is HLG3?

HLG3 is Sony's implementation of the Hybrid Log-Gamma standard with specific adjustments:
- **Base Standard**: ITU-R BT.2100 HLG
- **Purpose**: HDR video capture and production
- **Dynamic Range**: ~15 stops
- **Characteristics**: Smoother roll-off in highlights compared to standard HLG, optimized for Sony cameras

### Key Differences from Standard HLG

Sony's HLG3 variant provides:
1. Modified toe and shoulder characteristics
2. Optimized middle gray point
3. Better highlight rolloff for Sony sensor characteristics
4. Compatibility with BT.2020 color space

## MLV App Settings

### Processing Gamut

For Sony HLG3 color profile in MLV App, use:

**Processing Gamut: `Rec.2020` (BT.2020)**

**Explanation:**
- HLG is designed to work with the wide color gamut Rec.2020 (ITU-R BT.2020)
- Rec.2020 covers a much wider color space than Rec.709
- This is the standardized color space for HDR content
- Alternative: If Rec.2020 is not available, use the widest available gamut

**Color Primaries (BT.2020):**
- Red: x=0.708, y=0.292
- Green: x=0.170, y=0.797
- Blue: x=0.131, y=0.046
- White Point: D65 (x=0.3127, y=0.3290)

### Transfer Function

For Sony HLG3 color profile in MLV App, use:

**Transfer Function: `HLG` or `Hybrid Log-Gamma`**

**Explanation:**
- HLG uses a hybrid transfer function combining gamma and logarithmic curves
- The transfer function is defined in ITU-R BT.2100
- System gamma: 1.2 for typical viewing conditions

**HLG Transfer Function Characteristics:**
- **Lower Range (0-0.5)**: Uses a gamma curve (approximately 0.5^γ where γ ≈ 1.2)
- **Upper Range (0.5-1.0)**: Uses a logarithmic curve
- **Transition Point**: 0.5 (50% signal level)
- **Peak Luminance**: Typically 1000 nits, scalable up to 10,000 nits
- **Scene-referred**: Yes, with relative scene luminance encoding

**Mathematical Definition:**

For normalized signal E (0-1):
```
If 0 ≤ E ≤ 1/12:
    E' = √(3 × E)

If 1/12 < E ≤ 1:
    E' = a × ln(12 × E - b) + c
    where:
    a = 0.17883277
    b = 0.28466892
    c = 0.55991073
```

### Sony HLG3 Specific Adjustments

While standard HLG is the base, Sony's HLG3 includes:
1. **Black Level**: Slightly lifted blacks for Sony sensor characteristics
2. **Highlight Compression**: Smoother roll-off in highlights (>90% IRE)
3. **Middle Gray**: Approximately 38-42% IRE (varies by implementation)

## Practical Usage Guide

### Recommended Workflow in MLV App

1. **Import MLV footage**
   - Load your raw MLV files into MLV App

2. **Set Processing Gamut**
   - Navigate to color processing settings
   - Select: **Rec.2020** or **BT.2020**

3. **Set Transfer Function**
   - In the transfer function dropdown
   - Select: **HLG** or **Hybrid Log-Gamma**

4. **White Balance**
   - Set to daylight (5600K) or appropriate for your scene
   - HLG works best with proper white balance

5. **Exposure**
   - Adjust exposure to protect highlights
   - HLG has excellent highlight rolloff
   - Aim for middle gray at 38-42%

6. **Export Settings**
   - Container: MOV or MP4
   - Codec: ProRes 422 HQ or H.265 (HEVC)
   - Color Space: Rec.2020
   - Transfer Function: HLG
   - Bit Depth: 10-bit minimum

### Monitoring

When working with HLG3:
- **SDR Monitor**: Will show dimmer image (HLG is scene-referred)
- **HDR Monitor**: Set to HLG mode for accurate preview
- **Waveform**: Middle gray should be around 38-42%
- **Highlight Protection**: Keep peaks below 100% for proper HDR

## Technical References

### Standards

1. **ITU-R BT.2100**: "Image parameter values for high dynamic range television for use in production and international programme exchange"
   - Defines HLG transfer function
   - Specifies Rec.2020 color space

2. **ITU-R BT.2020**: "Parameter values for ultra-high definition television systems for production and international programme exchange"
   - Defines the wide color gamut

3. **ARIB STD-B67**: "Essential Parameter Values for the Extended Image Dynamic Range Television (EIDRTV) System for Programme Production"
   - Original HLG specification by BBC and NHK

### Sony Documentation

- Sony cameras using HLG3: A7S III, FX3, FX6, FX9, Venice
- HLG3 is found in Picture Profile settings (typically PP9 or PP10)
- Provides approximately 15 stops of dynamic range

## Troubleshooting

### If colors look incorrect:

1. **Check Processing Gamut**: Ensure it's set to Rec.2020, not Rec.709
2. **Verify Transfer Function**: Must be HLG, not standard Gamma or Log
3. **Monitor Setup**: Ensure your display is properly configured for HLG

### If image looks too dark:

1. **Normal Behavior**: HLG appears darker on SDR monitors
2. **Solution**: Use HDR-capable monitor in HLG mode
3. **Alternative**: Apply display transform for SDR preview

### If highlights are clipped:

1. **Check Exposure**: Reduce exposure in-camera or in MLV App
2. **Verify Levels**: Ensure signal doesn't exceed 100%
3. **Export Settings**: Confirm 10-bit or higher output

## Comparison with Other Gamma Curves

| Feature | HLG3 | S-Log3 | Log-C | Rec.709 |
|---------|------|--------|-------|---------|
| Dynamic Range | ~15 stops | ~14 stops | ~14 stops | ~6-8 stops |
| Color Space | Rec.2020 | S-Gamut3.Cine | Alexa Wide Gamut | Rec.709 |
| Grading Required | Minimal | Yes | Yes | No |
| SDR Compatible | Yes | No | No | Yes |
| HDR Native | Yes | No | No | No |

## Additional Notes

### Advantages of HLG3

- **No LUT Required**: HLG can be viewed directly on compatible displays
- **Backward Compatible**: Can be viewed on SDR displays (with reduced DR)
- **Live Broadcast Ready**: Suitable for direct transmission
- **Efficient Workflow**: Less grading required compared to Log formats

### Limitations

- **Camera Specific**: Sony's HLG3 characteristics may differ slightly from other manufacturers
- **Monitor Dependent**: Best results require HLG-capable display
- **Limited Grading Range**: Less flexible than Log formats for heavy color grading

## Conclusion

For using Sony's HLG3 color profile in MLV App:
- **Processing Gamut**: Rec.2020 (BT.2020)
- **Transfer Function**: HLG (Hybrid Log-Gamma)

This configuration will provide the most accurate representation of HLG3 footage, maintaining the wide color gamut and HDR characteristics that Sony's HLG3 is designed to deliver.

## Version History

- v1.0 (2025-11-14): Initial documentation of HLG3 color profile parameters for MLV App
