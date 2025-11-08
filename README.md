# Mobile Verification Toolkit (MVT) Scan on Android

MVT (Mobile Verification Toolkit) is a **forensic research tool for Android and iOS nobile devices**. It requires command-line knowledge and understanding of digital forensics. False positives are common, and results can be misleading without expertise.

## Installation

**Mobile Verification Toolkit (MVT)** is a Python-based tool that will be installed on Debian Linux using `pipx` for an isolated installation. 

### Prerequisites

Update your system and install required packages:
```bash
sudo apt update
sudo apt install python3 python3-venv python3-pip sqlite3 libusb-1.0-0-dev
```

- `libusb-1.0-0-dev` is needed for `mvt-android` (Android support via USB/ADB). Skip it if you'll only use `mvt-ios`.
- Python 3.9+ is recommended.

For Android support, also install ADB (from Android SDK Platform Tools):
- Download the latest from https://developer.android.com/tools/releases/platform-tools.

<img width="1080" height="929" alt="image" src="https://github.com/user-attachments/assets/9b8873b1-c8fb-49fe-aea5-ee199ff69358" />

<img width="1024" height="839" alt="image" src="https://github.com/user-attachments/assets/5d59e63b-45ec-4b74-b3e1-e1458e47ec41" />

- Extract it (e.g., to `~/platform-tools-latest-linux`) and add to your PATH:
  ```bash
  export PATH="$PATH:~/platform-tools-latest-linux"
  ```

### Recommended Method: Install with `pipx` (Isolated Environment)
1. Install `pipx`:
   - On Debian 12+ or Ubuntu 23.04+:
     ```bash
     sudo apt install pipx
     ```
   - On older Debian/Ubuntu:
     ```bash
     python3 -m pip install --user pipx
     ```
   Then ensure PATH:
   ```bash
   pipx ensurepath
   ```

2. **Restart your terminal** (or source `~/.bashrc`) for PATH changes to take effect.

3. Install MVT:
   ```bash
   pipx install mvt
   ```
   This installs the latest stable version from PyPI and provides `mvt-ios` and `mvt-android` commands globally.

### Verify Installation
```bash
mvt-ios --help
mvt-android --help
```

- `mvt-ios --help`

```
mvt-android --help
Usage: mvt-android [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  check-adb        Check an Android device over ADB
  check-androidqf  Check data collected with AndroidQF
  check-backup     Check an Android Backup
  check-bugreport  Check an Android Bug Report
  check-iocs       Compare stored JSON results to provided indicators
  download-apks    Download all or only non-system installed APKs
  download-iocs    Download public STIX2 indicators
  version          Show the currently installed version of MVT
```

- `mvt-android --help`

```
 mvt-ios --help
Usage: mvt-ios [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  check-backup    Extract artifacts from an iTunes backup
  check-fs        Extract artifacts from a full filesystem dump
  check-iocs      Compare stored JSON results to provided indicators
  decrypt-backup  Decrypt an encrypted iTunes backup
  download-iocs   Download public STIX2 indicators
  extract-key     Extract decryption key from an iTunes backup
  version         Show the currently installed version of MVT
```
