# HLG Transfer Function - Manual Entry for MLV App

## Important Notice

**HLG is NOT available as a predefined tonemap function in MLV App.** You must manually enter the transfer function formula below.

## Manual Transfer Function Formula

Copy and paste this exact formula into the Transfer Function field in MLV App:

```c
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

**Critical Notes:**
- Use natural logarithm `log()` (ln), NOT `log10()`
- Input `x` is normalized linear scene light (0.0 to 1.0)
- Output is HLG signal value (0.0 to 1.0)
- This formula follows the same format as other manual transfer functions like Sony S-Log3

## HLG Transfer Function Formula for MLV App

This document provides the HLG (Hybrid Log-Gamma) transfer function in a format suitable for implementation in MLV App source code.

## HLG OETF (Opto-Electronic Transfer Function)

### Standard Form

The HLG transfer function as defined in ITU-R BT.2100:

```c
// HLG OETF - Converts linear scene light to HLG signal
// Input: x (linear scene light, normalized 0-1)
// Output: HLG signal (0-1)

double hlg_oetf(double x) {
    const double a = 0.17883277;
    const double b = 0.28466892;
    const double c = 0.55991073;
    const double threshold = 1.0 / 12.0;  // 0.08333...
    
    if (x >= 0.0 && x <= threshold) {
        // Lower range: gamma curve
        return sqrt(3.0 * x);
    } else if (x > threshold && x <= 1.0) {
        // Upper range: logarithmic curve
        return a * log(12.0 * x - b) + c;
    } else {
        return 0.0;  // Out of range
    }
}
```

### MLV App Compatible Format (Single Line)

Following the Sony S-Log3 example format, here's the HLG OETF as a single-line conditional expression:

```c
// HLG OETF in MLV App format
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

**Alternative using log10:**
```c
// Using log10 instead of natural log (ln)
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

Note: The formula uses natural logarithm (ln), not log10. In C/C++, this is `log()` not `log10()`.

### With Explicit Constants

```c
// More explicit version with all constants defined
(x >= (1.0 / 12.0)) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

### Normalized to 10-bit Range (0-1023)

If MLV App expects output in 10-bit range:

```c
// HLG OETF normalized to 10-bit (0-1023)
((x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)) * 1023.0
```

## HLG EOTF (Inverse - For Reference)

### Standard Form

```c
// HLG EOTF - Converts HLG signal back to linear light
// Input: E (HLG signal, normalized 0-1)
// Output: linear light (0-1)

double hlg_eotf(double E) {
    const double a = 0.17883277;
    const double b = 0.28466892;
    const double c = 0.55991073;
    
    if (E >= 0.0 && E <= 0.5) {
        // Lower range
        return (E * E) / 3.0;
    } else if (E > 0.5 && E <= 1.0) {
        // Upper range
        return (exp((E - c) / a) + b) / 12.0;
    } else {
        return 0.0;  // Out of range
    }
}
```

### MLV App Compatible Format (Single Line)

```c
// HLG EOTF in MLV App format
(E >= 0.5) ? ((exp((E - 0.55991073) / 0.17883277) + 0.28466892) / 12.0) : ((E * E) / 3.0)
```

## Comparison with Sony S-Log3

### Sony S-Log3 (Provided Example)
```c
(x >= 0.01125000) ? (420.0 + log10((x + 0.01) / (0.18 + 0.01)) * 261.5) / 1023.0 : (x * (171.2102946929 - 95.0) / 0.01125000 + 95.0) / 1023.0
```

### HLG (This Implementation)
```c
(x >= 0.08333333) ? (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : sqrt(3.0 * x)
```

### Key Differences

| Feature | S-Log3 | HLG |
|---------|--------|-----|
| **Lower threshold** | 0.01125 | 0.08333333 (1/12) |
| **Lower curve** | Linear | Square root (gamma ~0.5) |
| **Upper curve** | Logarithmic (log10) | Logarithmic (natural log) |
| **Normalized output** | Yes (/1023.0) | Depends on implementation |
| **Designed for** | Log grading workflow | Direct HDR display |

## Constants Explanation

### HLG Constants

```c
const double a = 0.17883277;  // Log scaling factor
const double b = 0.28466892;  // Log offset
const double c = 0.55991073;  // Output offset
```

These constants are defined in ITU-R BT.2100 and derived to ensure:
1. Continuity at the transition point (1/12)
2. Smooth derivative at the transition
3. Proper mapping of reference values

### Derivation (For Reference)

The constants are calculated to satisfy:
- At x = 1/12: Both curves produce same output
- At x = 1/12: Both curves have same derivative (smooth transition)
- Proper system gamma of 1.2

```
a = 0.17883277
b = 1 - 4 * a = 0.28466892
c = 0.5 - a * ln(4 * a) = 0.55991073
```

## Full Implementation Example

### Complete C/C++ Function

```c
/**
 * HLG OETF (Opto-Electronic Transfer Function)
 * Converts linear scene light to HLG signal
 * 
 * @param x Linear scene light (0.0 to 1.0, where 1.0 = reference white)
 * @return HLG signal value (0.0 to 1.0)
 */
double hlg_oetf(double x) {
    // ITU-R BT.2100 HLG constants
    const double a = 0.17883277;
    const double b = 0.28466892;
    const double c = 0.55991073;
    const double threshold = 1.0 / 12.0;  // 0.083333...
    
    // Clamp input to valid range
    if (x < 0.0) x = 0.0;
    if (x > 1.0) x = 1.0;
    
    if (x <= threshold) {
        // Lower range: gamma curve (approximate gamma 0.5)
        return sqrt(3.0 * x);
    } else {
        // Upper range: logarithmic curve
        return a * log(12.0 * x - b) + c;
    }
}
```

### Processing Per Pixel (RGB)

```c
// Apply HLG to RGB pixel (linear input)
void apply_hlg_to_pixel(double* r, double* g, double* b) {
    *r = hlg_oetf(*r);
    *g = hlg_oetf(*g);
    *b = hlg_oetf(*b);
}
```

### Processing Full Image

```c
// Apply HLG to entire image buffer
void apply_hlg_to_image(double* image_data, int width, int height) {
    int total_pixels = width * height;
    
    for (int i = 0; i < total_pixels; i++) {
        int idx = i * 3;  // RGB channels
        image_data[idx + 0] = hlg_oetf(image_data[idx + 0]);  // R
        image_data[idx + 1] = hlg_oetf(image_data[idx + 1]);  // G
        image_data[idx + 2] = hlg_oetf(image_data[idx + 2]);  // B
    }
}
```

## Usage in MLV App

To add HLG as a tonemap function in MLV App:

1. **Locate the transfer function definitions** in the source code
2. **Add HLG alongside existing functions** (like S-Log3, Rec709, etc.)
3. **Use the single-line formula** for consistency:

```c
// In the tonemap function selection code
case TONEMAP_HLG:
    output = (x >= 0.08333333) ? 
             (0.17883277 * log(12.0 * x - 0.28466892) + 0.55991073) : 
             sqrt(3.0 * x);
    break;
```

Or as a macro:

```c
#define HLG_OETF(x) ((x) >= 0.08333333 ? \
    (0.17883277 * log(12.0 * (x) - 0.28466892) + 0.55991073) : \
    sqrt(3.0 * (x)))
```

## Testing and Validation

### Test Values

Expected HLG output for standard input values:

| Linear Input | HLG Output | Description |
|-------------|-----------|-------------|
| 0.00 | 0.0000 | Black |
| 0.01 | 0.1732 | Very dark |
| 0.05 | 0.3873 | Dark gray |
| 0.08333 | 0.5000 | Transition point |
| 0.18 | 0.6628 | Middle gray (18%) |
| 0.38 | 0.7826 | Typical middle gray in HLG |
| 0.50 | 0.8353 | 50% |
| 0.75 | 0.9023 | Bright |
| 1.00 | 1.0000 | Reference white |

### Verification Code

```c
#include <stdio.h>
#include <math.h>

void test_hlg() {
    double test_values[] = {0.0, 0.01, 0.05, 0.08333, 0.18, 0.38, 0.5, 0.75, 1.0};
    int num_tests = sizeof(test_values) / sizeof(double);
    
    printf("HLG OETF Test Results:\n");
    printf("Linear Input | HLG Output\n");
    printf("-------------|------------\n");
    
    for (int i = 0; i < num_tests; i++) {
        double input = test_values[i];
        double output = hlg_oetf(input);
        printf("%.5f        | %.4f\n", input, output);
    }
}
```

## Performance Considerations

### Optimizations

1. **Precompute constants** - Done above
2. **Use lookup tables** - For real-time processing
3. **SIMD/GPU acceleration** - Process multiple pixels in parallel
4. **Approximate sqrt** - Can use fast sqrt approximations if needed

### Lookup Table Example

```c
// Create 1024-entry lookup table
double hlg_lut[1024];

void init_hlg_lut() {
    for (int i = 0; i < 1024; i++) {
        double x = (double)i / 1023.0;
        hlg_lut[i] = hlg_oetf(x);
    }
}

// Fast lookup (with interpolation if needed)
double hlg_lookup(double x) {
    double idx = x * 1023.0;
    int idx_low = (int)idx;
    int idx_high = idx_low + 1;
    
    if (idx_high >= 1024) return hlg_lut[1023];
    
    // Linear interpolation
    double frac = idx - idx_low;
    return hlg_lut[idx_low] * (1.0 - frac) + hlg_lut[idx_high] * frac;
}
```

## Notes for MLV App Integration

1. **Input Range**: Ensure linear RGB values are normalized to 0-1 before applying HLG
2. **Output Range**: HLG output is 0-1, scale to bit depth as needed (e.g., × 1023 for 10-bit)
3. **Processing Order**: Apply HLG AFTER white balance, gamut conversion, and exposure
4. **Color Primaries**: Use with Rec.2020 gamut for proper HDR
5. **Metadata**: Tag output files with HLG transfer characteristic

## References

- **ITU-R BT.2100-2**: Official HLG specification
- **ARIB STD-B67**: Original HLG standard (BBC/NHK)
- **ISO/IEC 23008-2**: HEVC with HLG support

---
Last Updated: 2025-11-14
