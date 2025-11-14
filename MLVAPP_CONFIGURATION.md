# MLV App Configuration Examples

## Understanding MLV App Processing Pipeline

MLV App processes raw footage in these stages:
1. **White Balance** - Applied to linear raw data
2. **Gamut Conversion** - Using Processing Gamut (defines color primaries)
3. **Exposure** - Adjustment in linear space
4. **Tonemap Function** - Transfer function converts linear to output encoding

## Comparison: Different Processing Settings

This document shows how to configure MLV App for different profiles, with HLG3 highlighted.

### Configuration Matrix

| Profile | Processing Gamut | Tonemap Function | Notes |
|---------|------------------|------------------|-------|
| **HLG3 (Sony)** | **Rec.2020** | **HLG** | HDR, ~15 stops, broadcast ready |
| Standard Rec.709 | Rec.709 | Gamma 2.4 | SDR, ~6-8 stops, web/broadcast |
| Cineon/DPX | Rec.709 | Cineon | Film scanning, ~10 stops |
| Linear | Rec.709 / Rec.2020 | Linear | VFX work, requires grading |

### HLG3 Step-by-Step Configuration

#### Step 1: Processing Gamut (Predefined Selection)
```
Location: Color Processing Settings
Setting: Processing Gamut
Value: Rec.2020 (select from dropdown)
Processing Stage: 2 (Gamut Conversion)
```

**What it does:**
- Defines RGB color space primaries only (no transfer function)
- Converts camera sensor data to Rec.2020 primaries
- Applied to linear data before tonemap function

**Why Rec.2020?**
- HLG standard requires wide color gamut
- Covers more colors than Rec.709
- Essential for HDR content
- Matches Sony camera implementation

**Important:**
- This setting requires "Use Camera Matrix" to be enabled
- If disabled, Processing Gamut has no effect

#### Step 2: Transfer Function (Manual Entry Required)

**CRITICAL: HLG is NOT available as a predefined option.** You must manually enter the formula.

```
Location: Color Processing Settings
Setting: Tonemap Function / Transfer Function
Value: Manual entry (see formula below)
Processing Stage: 4 (Final conversion from linear)
```

**Manual Formula to Enter:**
```
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

**How to Enter:**
1. Locate the Transfer Function or Tonemap Function field
2. Clear any existing formula
3. Copy and paste the formula exactly as shown above
4. Ensure you use `log()` (natural logarithm), not `log10()`
5. Verify the formula was entered correctly (check parentheses and constants)

**What it does:**
- Applied AFTER white balance, gamut conversion, and exposure
- Converts linear RGB data to HLG-encoded output
- This is the transfer function (gamma curve)

**Why Manual Entry?**
- MLV App does not include HLG as a predefined tonemap function
- Only Processing Gamut (Rec.2020) can be selected from presets
- Users must provide the transfer function formula themselves

### Common Mistakes to Avoid

❌ **Wrong**: Trying to select "HLG" from the tonemap function dropdown
- Result: HLG is not available as a preset option

❌ **Wrong**: Processing Gamut = Rec.709, Manual Transfer = HLG formula
- Result: Colors will be clipped and undersaturated

❌ **Wrong**: Processing Gamut = Rec.2020, Tonemap = Gamma 2.4
- Result: Incorrect tonal distribution, not HDR

❌ **Wrong**: Using log10 instead of log in the formula
- Result: Incorrect transfer curve calculation

✅ **Correct**: Processing Gamut = Rec.2020 (from dropdown), Manual Transfer = HLG formula
- Result: Proper HLG3 color and tonal reproduction

### Verification of Manual Entry

After entering the HLG formula manually:

1. **Double-check the formula** - Ensure no typos or missing characters
2. **Verify parentheses** - All opening parentheses must have matching closing ones
3. **Confirm log function** - Must use `log()` not `log10()`
4. **Test with sample footage** - Export a test frame and verify it looks correct

### Alternative Workflows

#### MLV App Limitation:

**Important:** In MLV App, only the Processing Gamut can be selected from predefined options. The transfer function must be entered manually.

1. **What's Available as Presets:**
   - Processing Gamut: Rec.2020, Rec.709, DCI-P3, etc. (select from dropdown)

2. **What Must Be Entered Manually:**
   - Transfer Function: HLG formula (copy and paste the formula)

#### If you cannot enter custom transfer functions:

Unfortunately, without the ability to manually enter transfer functions, you cannot achieve true HLG output in MLV App. Alternatives:
1. Export in Linear or Log
2. Apply HLG LUT in post-production software (DaVinci Resolve, Adobe Premiere)
3. This is less ideal than direct HLG encoding

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
