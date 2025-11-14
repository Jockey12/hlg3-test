# HLG Technical Specifications

## Mathematical Definition of HLG Transfer Function

### OETF (Opto-Electronic Transfer Function)

The HLG OETF converts scene linear light (E) to non-linear signal (E'):

For normalized scene light E (where 1.0 = reference white):

```
If 0 ≤ E ≤ 1/12:
    E' = √(3 × E)

If 1/12 < E ≤ 1:
    E' = a × ln(12 × E - b) + c
```

Where constants are:
```
a = 0.17883277
b = 0.28466892  
c = 0.55991073
```

### EOTF (Electro-Optical Transfer Function)

The inverse function to convert from signal (E') back to display light:

```
If 0 ≤ E' ≤ 0.5:
    E = E'² / 3

If 0.5 < E' ≤ 1:
    E = (exp((E' - c) / a) + b) / 12
```

### Reference White and Black

- **Reference White**: 203 cd/m² (nominal)
- **Peak White**: 1000 cd/m² (typical), scalable to 10,000 cd/m²
- **Reference Black**: 0 cd/m² (absolute)
- **System Gamma**: 1.2 (in reference viewing environment)

## Rec.2020 Color Space Specifications

### Color Primaries (CIE 1931)

| Primary | x | y |
|---------|-----------|-----------|
| Red | 0.708 | 0.292 |
| Green | 0.170 | 0.797 |
| Blue | 0.131 | 0.046 |
| White (D65) | 0.3127 | 0.3290 |

### RGB to XYZ Transformation Matrix

From Rec.2020 RGB to CIE XYZ:

```
[ X ]   [ 0.6370  0.1446  0.1689 ]   [ R ]
[ Y ] = [ 0.2627  0.6780  0.0593 ] × [ G ]
[ Z ]   [ 0.0000  0.0281  1.0610 ]   [ B ]
```

### XYZ to RGB Transformation Matrix

From CIE XYZ to Rec.2020 RGB:

```
[ R ]   [  1.7167 -0.3557 -0.2534 ]   [ X ]
[ G ] = [ -0.6667  1.6165  0.0158 ] × [ Y ]
[ B ]   [  0.0176 -0.0428  0.9421 ]   [ Z ]
```

## Sony HLG3 Specific Parameters

### Characteristics

Sony's HLG3 implementation follows ITU-R BT.2100 HLG with these typical characteristics:

- **Black Level**: 0-3% (slight lift in some implementations)
- **Middle Gray**: 38-42% signal (IRE)
- **Knee Point**: Modified shoulder around 90-95%
- **Bit Depth**: 10-bit minimum encoding
- **Color Subsampling**: 4:2:2 or 4:2:0

### Signal Range

| Element | Value (10-bit) | Value (%) | Notes |
|---------|----------------|-----------|-------|
| Black | 64 | 0% | Legal black |
| Middle Gray | 394-430 | 38-42% | 18% reflectance |
| White | 940 | 90% | Reference white |
| Peak | 1023 | 100% | Maximum signal |

### Dynamic Range Mapping

HLG encodes approximately 15 stops of dynamic range:

| Stop | IRE % | Description |
|------|-------|-------------|
| -7.5 | 0% | Black level |
| -3 | 10% | Deep shadows |
| 0 | 38-42% | Middle gray (18% card) |
| +3 | 68% | Skin tones |
| +4.5 | 82% | Bright objects |
| +6 | 95% | Specular highlights |
| +7.5 | 100% | Peak white |

## Color Volume

### Rec.2020 Coverage

Rec.2020 covers approximately:
- **vs. Rec.709**: 70% larger color gamut
- **vs. DCI-P3**: 40% larger color gamut  
- **vs. Adobe RGB**: Similar to slightly larger
- **vs. Human Vision**: ~75% of visible colors

### Gamut Boundary Points

In CIE 1931 xy chromaticity space:

```
Red vertices:   (0.708, 0.292)
Green vertices: (0.170, 0.797)
Blue vertices:  (0.131, 0.046)
Triangle area:  0.3457 (vs 0.1582 for Rec.709)
```

## HLG System Gamma

### Display Referred Conversion

HLG uses a system gamma of 1.2 for typical viewing:

```
Display Light = (Signal)^1.2 × Peak Luminance
```

For different viewing conditions:
- **Bright Room**: γ = 1.0-1.1
- **Normal Room**: γ = 1.2 (reference)
- **Dark Room**: γ = 1.3-1.4

### OOTF (Opto-Optical Transfer Function)

The complete scene-to-display transformation:

```
Display = Scene^γ × (Peak_Luminance / Reference_White)

Where:
γ = 1.2 (typical)
Peak_Luminance = 1000 cd/m² (typical)
Reference_White = 203 cd/m²
```

## Quantization

### 10-bit Encoding

HLG is typically encoded in 10-bit:
```
Signal levels: 0-1023 (integer)
Legal range: 64-940 (limited) or 4-1019 (full)
Step size: ~0.1% per level
```

### Banding Prevention

To prevent visible banding:
- **Minimum**: 10-bit encoding
- **Recommended**: 12-bit for grading
- **Dithering**: May be applied in 10-bit

## Color Difference Signals

### Y'C'BC'R Encoding

For broadcast, HLG uses Y'C'BC'R:

```
Y'  = 0.2627×R' + 0.6780×G' + 0.0593×B'
C'B = (B' - Y') / 1.8814
C'R = (R' - Y') / 1.4746
```

Where R', G', B' are HLG non-linear signals in Rec.2020.

### Signal Ranges (10-bit, limited)

```
Y':  64-940 (876 levels)
C'B: 64-960 (896 levels)
C'R: 64-960 (896 levels)
```

## Metadata

### SMPTE ST 2086 (Static HDR Metadata)

For HLG content, optional metadata:

```
Display Primaries: Rec.2020 coordinates
White Point: D65
Max Display Luminance: 1000 nits (or as appropriate)
Min Display Luminance: 0.0001 nits
Max Content Light Level: Variable
Max Frame Average Light Level: Variable
```

### SMPTE ST 2094 (Dynamic Metadata)

HLG typically does NOT use dynamic metadata, but can optionally include:
- Scene-by-scene brightness adjustments
- Color volume optimization

## Compatibility Modes

### SDR Display Fallback

When displayed on SDR screen:
- HLG signal is interpreted as ~Gamma 2.4
- Image appears darker but viewable
- No metadata required for basic compatibility

### Conversion to Other HDR Formats

#### HLG to PQ (Perceptual Quantizer):
```
1. Apply HLG EOTF to get linear
2. Apply system gamma (1.2)
3. Apply PQ OETF
```

#### HLG to SDR:
```
1. Apply HLG EOTF to get linear  
2. Tone map to SDR range
3. Apply Rec.709 gamma (2.4)
```

## Verification Test Patterns

### Recommended Test Signals

1. **Ramp**: 0-100% in 10% steps
2. **Gray Scale**: 5%, 10%, 18%, 30%, 50%, 70%, 90%, 95%
3. **Color Bars**: 75% and 100% saturation
4. **Pluge**: Black level verification

### Expected Measurements

Using HLG mode on calibrated monitor:

```
Signal 0%:   0 cd/m²
Signal 38%:  ~50 cd/m² (middle gray)
Signal 75%:  ~400 cd/m²
Signal 100%: ~1000 cd/m² (peak)
```

## Reference Standards

### Primary Standards

1. **ITU-R BT.2100-2** (07/2018)
   - "Image parameter values for high dynamic range television"
   - Defines HLG OETF and EOTF

2. **ITU-R BT.2020-2** (10/2015)
   - "Parameter values for ultra-high definition television"
   - Defines Rec.2020 color space

3. **ARIB STD-B67** (07/2015)
   - "Essential Parameter Values for EIDRTV"
   - Original HLG specification

### Implementation Guidelines

1. **ITU-R BT.2390** 
   - "High dynamic range television for production"
   - Operational guidelines

2. **EBU Tech 3320**
   - "User Requirements for Video Monitors"
   - Display calibration

## Practical Formulas for MLV App

### Signal to Stops Above Middle Gray

```
stops = log₂(signal / middle_gray)

Where:
signal = HLG signal value (0-1)
middle_gray ≈ 0.38-0.42
```

### Exposure Compensation

```
new_signal = old_signal × 2^(stops_compensation)
```

### White Balance Adjustment

```
R_corrected = R × (target_temp / source_temp)_red_coefficient
G_corrected = G  
B_corrected = B × (target_temp / source_temp)_blue_coefficient
```

## Implementation Notes for MLV App

When implementing HLG3 in MLV App:

1. **Debayer First**: Convert raw Bayer to RGB
2. **Apply White Balance**: In linear space
3. **Exposure Adjustment**: In linear space
4. **Transform to Rec.2020**: Apply color matrix
5. **Apply HLG OETF**: Convert to HLG signal
6. **Output**: 10-bit or 12-bit encoded file

This workflow ensures proper color science is maintained throughout the processing pipeline.

---
Last Updated: 2025-11-14
