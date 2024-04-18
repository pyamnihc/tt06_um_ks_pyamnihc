![](../../workflows/gds/badge.svg) ![](../../workflows/docs/badge.svg) ![](../../workflows/test/badge.svg)

## Karplus-Strong String Synthesis for Tiny Tapeout
This is simplified implementation of Karplus-Strong (KS) string synthesis based on papers, [Digital Synthesis of Plucked-String and Drum Timbres](https://doi.org/10.2307/3680062) and [Extensions of the Karplus-Strong Plucked-String Algorithm](https://doi.org/10.2307/3680063). 

A register map controls and configures the KS synthesis module. This register map is accessed through a SPI interface. Synthesized sound samples are sent out through the I2S transmitter interface.

### SPI Frame
SPI Mode: CPOL = 0, CPHA = 1

The 16-bit SPI frame is defined as,

|     |     |     |
|:---:|:---:|:---:|
| Read=1/Write=0 | Address[6:0] | Data[7:0] |


### Register Map
The Register Map has 16 Registers of 8-bits each. It is divided into configuration and status registers,

|     |     |
|:--- |:--- |
| Register[7:0] | Configuration Registers |
| Register[11:8 ]| Status Registers |

Each register is mapped as follows,

| Register/Bit | 7                  | 6                   | 5             | 4              | 3             | 2                     | 1             | 0              |
|:------------:|:------------------:|:-------------------:|:-------------:|:--------------:|:-------------:|:---------------------:|:-------------:|:--------------:|
| 0            | i2s_noise_sel      | ks_freeze           | freeze_prbs_7 | freeze_prbs_15 |               | ~rst_n_ks_string      | ~rst_n_prbs_7 | ~rst_n_prbs_15 |
| 1            | ~lfsr_init_15[7:0] |                     |               |                |               |                       |               |                |
| 2            | load_prbs_15       | ~lfsr_init_15[14:8] |               |                |               |                       |               |                |
| 3            | load_prbs_7        | lfsr_init_7[6:0]    |               |                |               |                       |               |                |
| 4            |                    | clip_noise          | dynamics_en   | fine_tune_n    | drum_string_n | toggle_pattern_prbs_n | round_en      | pluck          |
| 5            | fine_tune_C[7:0]   |                     |               |                |               |                       |               |                |
| 6            | dynamics_R[7:0]    |                     |               |                |               |                       |               |                |
| 7            | ~ks_period[7:0]    |                     |               |                |               |                       |               |                |
| 9            | 1                  | 1                   | 0             | 0              | 0             | 0                     | 0             | 0              |
| 10           | 0                  | 0                   | 0             | 0              | 0             | 0                     | 0             | 1              |
| 11           | ui_in[7]           | ui_in[6]            | ui_in[5]      | ui_in[4]       | ui_in[3]      | ui_in[2]              | ui_in[1]      | ui_in[0]       |
| 12           |                    |                     |               |                |               |                       |               |                |

### I2S Transmitter
The 8-bit signed sound samples are sent out at `f_sck = 256 kHz` through this interface.

### How to use
Connect a clock with frequency `f_clk = 256 kHz` and apply a reset cycle to initialize the design, this sets the audio sample rate at `fs = 16 kHz`. Use the spi register map or the `ui_in` to futher configure the design. The synthesized samples are sent continuously through the I2S transmitter interface.

#### A description of what the inputs do (e.g. red button, SPI CLK, SPI MOSI, etc).
  inputs:               
  - ~rst_n_prbs_15, ~rst_n_prbs_7
  - load_prbs_15, load_prbs_7 
  - freeze_prbs_15
  - freeze_prbs_7
  - i2s_noise_sel
  - ~rst_n_ks_string
  - pluck
  - NOT CONNECTED
#### A description of what the outputs do (e.g. status LED, SPI MISO, etc)
  outputs:
  - segment a: rstn_n
  - segment b: rst_n_prbs_15
  - segment c: rst_n_prbs_7
  - segment d: rst_n_ks_string
  - segment e: freeze_prbs_15
  - segment f: freeze_prbs_15
  - segment g: i2s_noise_sel
  - dot: pluck
#### A description of what the bidirectional I/O pins do (e.g. I2C SDA, I2C SCL, etc)
  bidirectional:
  - sck_i
  - sdi_i
  - sdo_o
  - cs_ni
  - i2s_sck_o
  - i2s_ws_o
  - i2s_sd_o 
  - prbs_15


# Tiny Tapeout Verilog Project Template

- [Read the documentation for project](docs/info.md)

## What is Tiny Tapeout?

TinyTapeout is an educational project that aims to make it easier and cheaper than ever to get your digital designs manufactured on a real chip.

To learn more and get started, visit https://tinytapeout.com.

## Verilog Projects

1. Add your Verilog files to the `src` folder.
2. Edit the [info.yaml](info.yaml) and update information about your project, paying special attention to the `source_files` and `top_module` properties. If you are upgrading an existing Tiny Tapeout project, check out our [online info.yaml migration tool](https://tinytapeout.github.io/tt-yaml-upgrade-tool/).
3. Edit [docs/info.md](docs/info.md) and add a description of your project.
4. Optionally, add a testbench to the `test` folder. See [test/README.md](test/README.md) for more information.

The GitHub action will automatically build the ASIC files using [OpenLane](https://www.zerotoasiccourse.com/terminology/openlane/).

## Enable GitHub actions to build the results page

- [Enabling GitHub Pages](https://tinytapeout.com/faq/#my-github-action-is-failing-on-the-pages-part)

## Resources

- [FAQ](https://tinytapeout.com/faq/)
- [Digital design lessons](https://tinytapeout.com/digital_design/)
- [Learn how semiconductors work](https://tinytapeout.com/siliwiz/)
- [Join the community](https://tinytapeout.com/discord)
- [Build your design locally](https://docs.google.com/document/d/1aUUZ1jthRpg4QURIIyzlOaPWlmQzr-jBn3wZipVUPt4)

## What next?

- [Submit your design to the next shuttle](https://app.tinytapeout.com/).
- Edit [this README](README.md) and explain your design, how it works, and how to test it.
- Share your project on your social network of choice:
  - LinkedIn [#tinytapeout](https://www.linkedin.com/search/results/content/?keywords=%23tinytapeout) [@TinyTapeout](https://www.linkedin.com/company/100708654/)
  - Mastodon [#tinytapeout](https://chaos.social/tags/tinytapeout) [@matthewvenn](https://chaos.social/@matthewvenn)
  - X (formerly Twitter) [#tinytapeout](https://twitter.com/hashtag/tinytapeout) [@matthewvenn](https://twitter.com/matthewvenn)
