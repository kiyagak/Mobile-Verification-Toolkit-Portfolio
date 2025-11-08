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

<img width="459" height="1024" alt="image" src="https://github.com/user-attachments/assets/2c236b0d-764c-481e-9fd9-1a56ed2cbc53" />

<img width="500" height="1036" alt="image" src="https://github.com/user-attachments/assets/7891a386-c68d-4060-8844-a1b944591108" />

<img width="500" height="426" alt="image" src="https://github.com/user-attachments/assets/853437a1-20de-4b44-a919-ed915a56260d" />


### Connect and Scan
```bash
# Download data via ADB

mvt-android check-adb \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_AmnestyTech_investigations_master_2023-03-29_android_campaign_malware.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_AmnestyTech_investigations_master_2024-05-02_wintego_helios_wintego_helios.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_AmnestyTech_investigations_master_2024-12-16_serbia_novispy_novispy.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_AssoEchap_stalkerware-indicators_master_generated_stalkerware.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_main_2022-06-23_rcs_lab_rcs.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_main_2023-04-11_quadream_kingspawn.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_main_2023-06_01_operation_triangulation_operation_triangulation.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_main_2023-07-25_wyrmspy_dragonegg_wyrmspy_dragonegg.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_main_intellexa_predator_predator.stix2 \
--iocs /home/user/.local/share/mvt/indicators/raw.githubusercontent.com_mvt-project_mvt-indicators_master_candiru_candiru.stix2 \
-o results/
```

This checks apps, processes, network connections, and known malicious files.

For stalkerware-specific IOCs, additional repos like https://github.com/AmnestyTech/investigations may have extras.

## Generate a Report and Timeline

After scanning, MVT creates JSON files in your output folder (`-o results/`).

```bash
cd /home/user/.local/share/mvt/indicators/results
dir
```
```
command.log		      dumpsys_dbinfo.json     packages.json
dumpsys_accessibility.json    dumpsys_receivers.json  processes.json
dumpsys_activities.json       dumpsys.txt	      selinux_status.json
dumpsys_adb_state.json	      files.json	      settings.json
dumpsys_appops_detected.json  getprop.json	      sms.json
dumpsys_appops.json	      info.json		      timeline.csv
dumpsys_battery_daily.json    logcat_last.txt	      timeline_detected.csv
dumpsys_battery_history.json  logcat.txt
```

View raw JSON/timeline:
```bash
ls results/*.json
```
## View the Results
- **No detections**: Likely no match to public IOCs (does **not** mean your device is clean—advanced spyware may evade public indicators).
- **Detections**: Shown in `detected.json` or console. Common for Pegasus: suspicious processes, domains, or files (e.g., Bridge.app, mpt.db anomalies).
- **False positives**: Very common (e.g., legit apps matching patterns). Cross-reference with Amnesty's forensic methodology.
- If something is flagged: **Do not panic**. Share results with experts (redact personal data). Upload anonymized logs to Amnesty if appropriate.

## What I Learned

I learned
- how to enable an Android device to allow MVT to scan it
- how to use MVT to scan an Android mobile device for any indicators of compromise (IoC)
- how to find the detected IoCs that were output in the `results` folder
