# Sony HLG3 Color Profile for MLV App

This repository contains research and documentation for Sony's HLG3 (Hybrid Log-Gamma 3) color profile preset, specifically for use with MLV App (Magic Lantern Video Application).

## Quick Answer

For using Sony's HLG3 color profile in MLV App:

- **Processing Gamut**: `Rec.2020` (BT.2020)
- **Transfer Function**: `HLG` (Hybrid Log-Gamma)

## Documentation

- **[Quick Reference](QUICK_REFERENCE.md)** - Essential settings and quick setup guide
- **[Complete Documentation](HLG3_COLOR_PROFILE.md)** - Detailed technical specifications and usage guide

## What is HLG3?

HLG3 is Sony's implementation of the Hybrid Log-Gamma HDR standard (ITU-R BT.2100). It provides:
- Wide color gamut (Rec.2020)
- High dynamic range (~15 stops)
- Backward compatibility with SDR displays
- Minimal grading requirements

## Why This Matters for MLV App

MLV App is used to process raw video files from Magic Lantern modified cameras. To properly interpret and export footage that matches Sony's HLG3 look, you need to configure:

1. The correct **color space** (Processing Gamut)
2. The correct **gamma curve** (Transfer Function)

This documentation provides those exact parameters based on the HLG standard and Sony's implementation.

## Usage

See [QUICK_REFERENCE.md](QUICK_REFERENCE.md) for setup instructions and [HLG3_COLOR_PROFILE.md](HLG3_COLOR_PROFILE.md) for detailed technical information.

## References

- ITU-R BT.2100: HLG specification
- ITU-R BT.2020: Wide color gamut specification
- ARIB STD-B67: Original HLG standard

## License

This documentation is provided for educational purposes.