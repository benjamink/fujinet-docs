# Printer Emulation

FujiNet emulates several classic Atari printers, generating output available for download from the FujiNet web interface. Print output is rendered as PDF or SVG files that faithfully reproduce the look of the original printer hardware.

## Emulated Printers

| Printer | Output Format | Notes |
|---------|--------------|-------|
| Atari 820 | PDF | Dot-matrix style, supports normal and sideways printing |
| Atari 822 | PDF | Thermal printer emulation |
| Atari 825 | PDF | Centronics-compatible |
| Atari 1020 | SVG | Color plotter with vector graphics |
| Atari 1025 | PDF | Dot-matrix printer |
| Atari 1027 | PDF | Letter-quality printer |
| Atari 1029 | PDF | Dot-matrix printer |
| Atari XMM801 | PDF | Epson-compatible |
| Atari XDM121 | PDF | Daisy-wheel letter-quality |
| Okimate 10 | PDF | Color thermal printer |
| Epson 80 | PDF | Epson MX/FX-80 compatible |

## Accessing Print Output

Print output files are served from FujiNet's built-in web server. After printing from your Atari:

1. Open a web browser on any device connected to the same network.
2. Navigate to your FujiNet's IP address.
3. Download the generated PDF or SVG file.

## PDF Output

Most emulated printers produce PDF files. FujiNet uses a compact, custom PDF generation library designed specifically for the constraints of the embedded platform. The library handles:

- Starting new pages
- Outputting lines of text
- Setting fonts and font sizes
- Building the PDF cross-reference table

The generated PDFs are standard-compliant and can be opened with any PDF viewer.

## SVG Output

The Atari 1020 color plotter produces SVG (Scalable Vector Graphics) output. SVG files can be viewed in any modern web browser and scale to any size without loss of quality, making them well-suited to reproducing plotter output.

## Printer Fonts

FujiNet uses carefully selected and custom-made fonts to reproduce the authentic look of each printer:

| Printer | Font | Details |
|---------|------|---------|
| Atari 820 | Atari 820 Normal / Atari 820 Sideways | Custom fonts based on real printer output |
| Atari 822 | Atari 822 Thermal | Custom font based on real printer output |
| Atari 1020 | FifteenTwenty | Vector-style font matching the plotter character set |
| Atari 1027 | Prestige Elite | Near-perfect match for the original letter-quality output |
| Epson 80 | FX Matrix | Comprehensive Epson dot-matrix font family |

## File Printer Mode

In addition to emulating specific printers, FujiNet provides a file printer mode for capturing raw SIO print data to a single binary file. This is useful for debugging or for processing print data with external tools.

The file printer supports three modes:

| Mode | Description |
|------|-------------|
| Raw | Captures the entire SIO buffer as-is |
| Trim | Captures the buffer up to the first EOL character (0x9B) |
| Trim + Convert | Trims at EOL and converts the Atari EOL (0x9B) to a standard newline (`\n`) |

## CP/M Printer Compatibility

When running CP/M on FujiNet, the currently selected FujiNet printer is passed through to the CP/M LST: device. For recommended CP/M printer mappings, see the [CP/M Support guide](../cpm.md).
