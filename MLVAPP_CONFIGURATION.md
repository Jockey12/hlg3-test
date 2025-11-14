# MLV App Configuration Examples

## Comparison: Different Gamma Curves

This document shows how to configure MLV App for different gamma curves, with HLG3 highlighted.

### Configuration Matrix

| Profile | Processing Gamut | Transfer Function | Notes |
|---------|------------------|-------------------|-------|
| **HLG3 (Sony)** | **Rec.2020** | **HLG** | HDR, ~15 stops, broadcast ready |
| Standard Rec.709 | Rec.709 | Gamma 2.4 | SDR, ~6-8 stops, web/broadcast |
| Cineon/DPX | Rec.709 | Cineon | Film scanning, ~10 stops |
| Linear | Rec.709 / Rec.2020 | Linear | VFX work, requires grading |

### HLG3 Step-by-Step Configuration

#### Step 1: Processing Gamut
```
Location: Color Processing Settings
Setting: Processing Gamut
Value: Rec.2020
```

**Why Rec.2020?**
- HLG standard requires wide color gamut
- Covers more colors than Rec.709
- Essential for HDR content
- Matches Sony camera implementation

#### Step 2: Transfer Function
```
Location: Color Processing Settings
Setting: Transfer Function / Gamma
Value: HLG (or Hybrid Log-Gamma)
```

**Why HLG?**
- Matches Sony's HLG3 gamma curve
- Preserves HDR information
- Scene-referred encoding
- Compatible with broadcast standards

### Common Mistakes to Avoid

❌ **Wrong**: Processing Gamut = Rec.709, Transfer = HLG
- Result: Colors will be clipped and undersaturated

❌ **Wrong**: Processing Gamut = Rec.2020, Transfer = Gamma 2.4
- Result: Incorrect tonal distribution, not HDR

❌ **Wrong**: Processing Gamut = sRGB, Transfer = HLG
- Result: Severely limited color range

✅ **Correct**: Processing Gamut = Rec.2020, Transfer = HLG
- Result: Proper HLG3 color and tonal reproduction

### Alternative Workflows

#### If Rec.2020 is not available:

1. **Option 1**: Use widest available gamut (P3 if available)
   - Will limit color range but maintain structure

2. **Option 2**: Use Rec.709 with HLG transfer
   - Maintains tonal curve but limits color
   - Not recommended for HDR delivery

#### If HLG transfer is not available:

Unfortunately, there's no good substitute for HLG transfer function. You would need to:
1. Export in Linear or Log
2. Apply HLG LUT in post-production software
3. This is less ideal than native HLG support

### Verification

After setting up HLG3 in MLV App:

1. **Check Waveform**
   - Middle gray should be around 38-42%
   - Highlights should not clip at 100%

2. **Check Vectorscope**
   - Colors should extend to outer limits (Rec.2020 is wide)
   - No severe clipping on color vectors

3. **Check Preview**
   - On SDR monitor: Image will look darker than normal (expected)
   - On HDR monitor (HLG mode): Should look natural with extended highlights

### Export Settings for HLG3

```
Container: MOV or MP4
Video Codec: ProRes 422 HQ (MOV) or H.265/HEVC (MP4)
Color Space: Rec.2020 / BT.2020
Transfer Characteristics: HLG
Bit Depth: 10-bit minimum (12-bit preferred)
Color Range: Full
```

### Workflow Integration

#### For Editing:
1. Export from MLV App with HLG settings
2. Import into NLE (Premiere, DaVinci Resolve, Final Cut)
3. Set project to Rec.2020 / HLG
4. Minimal grading required

#### For Delivery:
- **Broadcast**: HLG is broadcast-ready (BBC, NHK standard)
- **Streaming**: YouTube and Vimeo support HLG HDR
- **Direct Display**: Compatible TVs can display directly

#### For Archival:
- HLG is a scene-referred format
- Future-proof for HDR workflows
- No destructive transformations needed

## Technical Validation

### How to verify HLG3 is working correctly:

1. **Export a test frame** with known values
2. **Analyze in color software** (DaVinci Resolve, etc.)
3. **Check EOTF curve** matches HLG specification
4. **Verify color primaries** match Rec.2020

### Expected Results:

- **18% Gray Card**: Should read ~38-42% in HLG
- **90% White**: Should read ~75% in HLG  
- **Peak White**: Should read 100% with soft rolloff
- **Color Checker**: All patches within Rec.2020 gamut

## Additional Resources

- MLV App Documentation: Check for latest HLG support
- DaVinci Resolve: Use Color Space Transform for HLG
- Adobe Premiere: Use Lumetri Color with Rec.2020/HLG preset

---
Last Updated: 2025-11-14
