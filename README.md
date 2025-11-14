# Sony HLG3 Color Profile for MLV App

This repository contains research and documentation for Sony's HLG3 (Hybrid Log-Gamma 3) color profile preset, specifically for use with MLV App (Magic Lantern Video Application).

## Quick Answer

For using Sony's HLG3 color profile in MLV App:

- **Processing Gamut**: `Rec.2020` (BT.2020) - *Defines color primaries only*
- **Tonemap Function**: `HLG` (Hybrid Log-Gamma) - *Transfer function applied to linear data*

## Documentation

- **[Summary](SUMMARY.md)** - Complete overview and quick reference card
- **[Quick Reference](QUICK_REFERENCE.md)** - Essential settings and quick setup guide
- **[Complete Documentation](HLG3_COLOR_PROFILE.md)** - Detailed technical specifications and usage guide
- **[MLV App Configuration](MLVAPP_CONFIGURATION.md)** - Step-by-step configuration examples
- **[Transfer Function Code](HLG_TRANSFER_FUNCTION_CODE.md)** - HLG formula in MLV App compatible format
- **[Technical Reference](TECHNICAL_REFERENCE.md)** - Mathematical specifications and color science details

## What is HLG3?

HLG3 is Sony's implementation of the Hybrid Log-Gamma HDR standard (ITU-R BT.2100). It provides:
- Wide color gamut (Rec.2020)
- High dynamic range (~15 stops)
- Backward compatibility with SDR displays
- Minimal grading requirements

## Why This Matters for MLV App

MLV App is used to process raw video files from Magic Lantern modified cameras. To properly interpret and export footage that matches Sony's HLG3 look, you need to configure:

1. The correct **color primaries** (Processing Gamut) - defines the RGB color space without transfer function
2. The correct **tonemap function** - the transfer/gamma function applied after linear processing

MLV App's processing pipeline:
1. White balance (linear)
2. Gamut conversion using Processing Gamut (linear)
3. Exposure adjustment (linear)
4. Tonemap Function applied (converts from linear to desired output)

This documentation provides those exact parameters based on the HLG standard and Sony's implementation.

## Usage

See [QUICK_REFERENCE.md](QUICK_REFERENCE.md) for setup instructions and [HLG3_COLOR_PROFILE.md](HLG3_COLOR_PROFILE.md) for detailed technical information.

## References

- ITU-R BT.2100: HLG specification
- ITU-R BT.2020: Wide color gamut specification
- ARIB STD-B67: Original HLG standard

## License

This documentation is provided for educational purposes.