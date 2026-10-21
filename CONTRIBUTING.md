# Contributing to SmartDisplay

Thank you for your interest in contributing to **SmartDisplay**! We welcome bug reports, feature enhancements, documentation improvements, and architectural suggestions.

Please read this document before submitting contributions to ensure a smooth review process.

---

## Code of Conduct

This project adheres to the [Contributor Covenant](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code of conduct.

---

## Branching Strategy

We follow a Gitflow-inspired branching model:

* **`main`**: Production and stable release branch. Direct commits to `main` are restricted. All official releases (`v1.x.y`) are tagged from here.
* **`develop`**: Primary integration branch. All feature branches, bugfixes, and documentation updates should branch off and merge into `develop`.
* **Feature Branches**: Name your branches descriptively:
  * `feat/your-feature-name`
  * `fix/issue-description`
  * `docs/documentation-update`

```
main       ●───────────────● (v1.0.0 Tagged Release)
            \             /
develop      ●───●───●───●
              \     /
feature/xyz    ●───●
```

---

## Architecture Parity Guarantee

> [!IMPORTANT]
> SmartDisplay offers two installation modes:
> 1. **Modular Packages** (`smartdisplay.yaml` + `packages/*.yaml`)
> 2. **Single-File Standalone** (`smartdisplay_standalone.yaml`)
>
> Any pull request that modifies hardware pins, LVGL pages, sensors, or automations **must update both** configurations to maintain 100% parity.

---

## Local Development Setup

### 1. Prerequisites
* Python 3.10+
* Git
* (Optional) Hardware device `SP-ESP32-S3-1.28-BOX`

### 2. Setup Virtual Environment
```bash
git clone https://github.com/pasmimmo/SmartDisplay.git
cd SmartDisplay
git checkout develop

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: .\venv\Scripts\Activate.ps1

# Install dependencies
pip install -r requirements-ci.txt
```

### 3. Local Secrets Configuration
Copy the sample secrets file to test configurations without exposing your private credentials:
```bash
cp secrets.yaml.example secrets.yaml
```

### 4. Running Local Validation
Before submitting your code, verify that all configurations pass validation and linting:

```bash
# 1. Lint YAML formatting
yamllint .

# 2. Validate modular package configuration
esphome config smartdisplay.yaml

# 3. Validate single-file standalone configuration
esphome config smartdisplay_standalone.yaml
```

---

## Commit Guidelines (Conventional Commits)

We enforce the [Conventional Commits v1.0.0](https://www.conventionalcommits.org/) standard. Commit messages must use the following format:

```
<type>(<scope>): <short description>

[optional body]

[optional footer(s)]
```

### Allowed Types
* `feat`: A new feature (e.g., adding an LVGL widget or sensor)
* `fix`: A bug fix (e.g., resolving a touch gesture issue or backlight state)
* `docs`: Documentation updates only (e.g., README, Wiki, ADRs)
* `style`: Code style/formatting changes that do not affect logic
* `refactor`: Code restructuring without adding features or fixing bugs
* `perf`: Performance optimizations (e.g., SPI bus timing, DMA buffer tuning)
* `ci`: Changes to CI/CD workflows and automation scripts
* `chore`: Maintenance tasks, dependency updates, or repository housekeeping

### Examples
* `feat(display): add weather forecast page with daily high/low cards`
* `fix(audio): gate GPIO46 low during I2S idle state to eliminate hiss`
* `docs(adr): document 80MHz SPI bus throughput calculations`

---

## Pull Request Checklist

When submitting a Pull Request to `develop`:

1. [ ] Target branch is set to `develop` (not `main`).
2. [ ] `yamllint .` passes without errors.
3. [ ] `esphome config smartdisplay.yaml` passes validation.
4. [ ] `esphome config smartdisplay_standalone.yaml` passes validation and matches modular changes.
5. [ ] Commit history follows Conventional Commits.
6. [ ] Documentation or ADRs updated if introducing breaking changes or new architectural choices.
