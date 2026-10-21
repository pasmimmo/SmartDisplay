## Description

Please provide a clear and concise summary of the changes introduced by this pull request.

Fixes #(issue)

---

## Type of Change

Please mark the relevant option(s) with an `x`:

- [ ] 🐛 **Bug fix** (non-breaking change which fixes an issue)
- [ ] ✨ **New feature** (non-breaking change which adds functionality)
- [ ] 💥 **Breaking change** (fix or feature that would cause existing functionality to not work as expected)
- [ ] ⚡ **Performance improvement** (optimizations to rendering, DMA, SPI, or memory usage)
- [ ] 📝 **Documentation update** (README, Wiki, ADRs, or inline comments)
- [ ] ⚙️ **CI/CD or Build system** (changes to GitHub Actions, workflows, or linting)

---

## Architectural Parity Verification

> [!IMPORTANT]
> If changes affect ESPHome YAML configuration, verify that both modular packages and standalone configurations are updated in sync:

- [ ] Changes applied to modular files in `packages/*.yaml`
- [ ] Identical changes mirrored into `smartdisplay_standalone.yaml`

---

## Checklist

- [ ] My code adheres to the style guidelines of this project
- [ ] I have executed `yamllint .` locally and resolved any warnings/errors
- [ ] I have executed `esphome config smartdisplay.yaml` and verified clean compilation
- [ ] I have executed `esphome config smartdisplay_standalone.yaml` and verified clean compilation
- [ ] My commits follow the [Conventional Commits](https://www.conventionalcommits.org/) convention
- [ ] I have updated relevant documentation / Wiki / ADRs where applicable
- [ ] (If applicable) Tested physically on `SP-ESP32-S3-1.28-BOX` hardware
