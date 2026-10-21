# Aster MCA Nano — CH32

> **Repository moved / 仓库已迁移**
>
> Development and current documentation are maintained at **[Aster-MCA/nano](https://github.com/pulsefoundry/Aster-MCA/tree/main/nano)**.
> [AT32 track](https://github.com/pulsefoundry/Aster-MCA/tree/main/nano/docs/at32/README.en.md) · [CH32 track](https://github.com/pulsefoundry/Aster-MCA/tree/main/nano/docs/ch32) · [中文](https://github.com/pulsefoundry/Aster-MCA/tree/main/nano/README.md)
>
> This repository retains historical files, branches and existing download links. Nano project-authored material uses [MIT](LICENSE); [third-party terms](THIRD_PARTY_NOTICES.md) remain applicable.

[中文](README.md) | English | [All project tracks](https://github.com/pulsefoundry/Aster-MCA-Nano/tree/main)

**CH32X033F8P6 baseline drift and abnormal spikes are still under investigation. Reliable spectroscopy has not been validated.** This branch preserves the CH32 analog-chain diagnostic implementation. A valid USB transfer or waveform checksum does not establish pulse-height accuracy or energy resolution.

## Hardware

![CH32 PCB top](outputs/Aster-MCA-Nano-CH32-v0.3-USB-A-LED/pcb-top.png)

The PCB measures 32 × 18 mm, with the CH32's integrated op amps, USB-A, SMA and two blue power/run LEDs. The built board is 1.6 mm thick.

- [Schematic PDF](outputs/Aster-MCA-Nano-CH32-v0.3-USB-A-LED/Aster-MCA-Nano-CH32-v0.3-USB-A-LED-Schematic.pdf).
- [Single-board Gerber ZIP](outputs/Aster-MCA-Nano-CH32-v0.3-USB-A-LED/Aster-MCA-Nano-CH32-v0.3-USB-A-LED-Gerber.zip).
- [EasyEDA project, BOM and board views](outputs/Aster-MCA-Nano-CH32-v0.3-USB-A-LED).
- [Hand-assembly reference](outputs/Aster-MCA-Nano-CH32-v0.3-焊接对照).

## Diagnostic firmware

[Source](firmware/ch32-selftest-v0.3) · [Modes and protocol](firmware/ch32-selftest-v0.3/README-波形诊断.txt) · [Host capture tool](firmware/ch32-selftest-v0.3/host/capture_wave.py)

v0.3 supports slow multi-node readings, AD-only readings, ADC pause, 4096-sample DMA waveforms and range-triggered diagnostic blocks. The nominal waveform sample rate is approximately 333 kS/s. Sampling pauses during upload, so blocks have gaps. Triggered blocks are not event counts. This is separate from the AT32 4 MS/s peak firmware.

Historical diagnostics used C9 removed and R15 = 100 ohms. The hardware package preserves the original BOM; this was a diagnostic change, not a proven fix. The root cause is unresolved, and no CH32 energy-resolution result is claimed. Do not apply the CH32 C9 change to AT32.

The next gate is a stable baseline with a controlled input condition, followed by pulse-height and spectrum validation. Current archived source spectra, prototype photos, itemized cost and v0.8.2 firmware are documented in the **[AT32 track](docs/at32/README.en.md)**.

## Shared resources and licensing

[Development guide](docs/development.md) · [Detailed CH32 status](docs/ch32/README.md) · [Chinese/English project overview](https://github.com/pulsefoundry/Aster-MCA-Nano/tree/main)

The common host and AT32 archives remain in the repository for reference. They are not validation of CH32 spectroscopy. Nano project-authored material is licensed under [MIT](LICENSE). WCH code retains its original notices and WCH-microcontroller-only restrictions; see [SOURCE-NOTES](firmware/ch32-selftest-v0.3/SOURCE-NOTES.txt).
