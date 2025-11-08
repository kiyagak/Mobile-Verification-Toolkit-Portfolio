# Basic MVT Scan

MVT scans for **public Indicators of Compromise (IOCs)** from known spyware like NSO Group's Pegasus, Predator, NoviSpy, and stalkerware. It analyzes backups or device data but cannot detect unknown or zero-day threats.

## Download Public Indicators of Compromise (IOCs)

1. Open the Terminal.  

2. Add the current user (represented by the `$USER` environment variable) to the `plugdev` supplementary group on Linux systems, without removing them from any existing groups (due to the `-a` for "append" and `-G` for supplementary groups).  This is needed because:

- MVT's iOS and Android modules often need direct USB access to a connected device.
- On Linux, USB device nodes are usually limited to root or specific groups for security.
- The `plugdev` group gives non-root users permission to access and mount removable/pluggable devices, including phones in USB debugging mode or iOS devices for backup/lockdown operations.
- Without this group membership, MVT scans may fail with permission errors.

```bash
sudo usermod -aG plugdev $USER
```

3. **Log out and log back in** (or reboot) for the group change to take effect in your session. 

4. Verify that group membership lists `plugdev`:

`groups`

5. Change to the MVT installation directory:

```bash
cd /home/user/.local/share/pipx/venvs/mvt
```

6. Automatically download the latest public STIX files for known spyware signatures:

```bash
mvt-ios download-iocs
mvt-android download-iocs
```

This pulls from https://github.com/mvt-project/mvt-indicators (includes Pegasus, Predator, NoviSpy, etc.). Files are stored in `~/.mvt/indicators/`.

## Basic Android Scanning (Via ADB)

### Enable USB Debugging

On your Android phone:
- Settings > About phone > Tap Build number 7 times.
- Settings > System > Developer options > Enable USB debugging.

### Connect and Scan
```bash
# Download data via ADB
mvt-android check-adb --iocs ~/.mvt/indicators/*.stix2 -o results/

# Or create a full ADB backup first (requires device unlock)
adb backup -apk -shared -all -f android_backup.ab
# Then convert/scan:
mvt-android check-backup --iocs ~/.mvt/indicators/*.stix2 android_backup.ab
```

This checks apps, processes, network connections, and known malicious files.

For stalkerware-specific IOCs, additional repos like https://github.com/AmnestyTech/investigations may have extras.

## Generate a Report and Timeline

After scanning, MVT creates JSON files in your output folder (`-o results/`).

```bash
# Generate HTML report (optional, install extra deps if needed)
mvt-ios generate-report results/ report.html
```

Or view raw JSON/timeline:
```bash
ls results/*.json
```
## View the Results
- **No detections**: Likely no match to public IOCs (does **not** mean your device is clean—advanced spyware may evade public indicators).
- **Detections**: Shown in `detected.json` or console. Common for Pegasus: suspicious processes, domains, or files (e.g., Bridge.app, mpt.db anomalies).
- **False positives**: Very common (e.g., legit apps matching patterns). Cross-reference with Amnesty's forensic methodology.
- If something is flagged: **Do not panic**. Share results with experts (redact personal data). Upload anonymized logs to Amnesty if appropriate.

Example output snippet:
```
[!] Detected suspicious process: com.nso.pegasus
```
For full details, always refer to the official docs: https://docs.mvt.re/en/latest/.
