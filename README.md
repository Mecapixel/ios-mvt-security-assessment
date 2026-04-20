# 📱 iOS Spyware Detection Using MVT (Mobile Verification Toolkit)

**Author:** Mecapixel  
**Date:** April 2026  
**Tool:** MVT 2.7.0 by Amnesty International's Security Lab  
**Platform:** Windows 11 → iOS Device  
**Result:** ✅ No indicators of compromise detected

---

## 🔍 Overview

This project documents a hands-on mobile security assessment performed on a personal iOS device using [Amnesty International's Mobile Verification Toolkit (MVT)](https://mvt.re). The goal was to detect any indicators of compromise (IOCs) from known commercial spyware including **Pegasus, Predator, RCS Lab, Stalkerware, KingSpawn, DarkSword**, and others.

This is a real-world security hygiene exercise and part of an active cybersecurity portfolio documenting practical skills on the path toward digital forensics and ICAC investigations.

---

## 🦠 Spyware Families Checked

| Spyware | Developer | Notes |
|---|---|---|
| Pegasus | NSO Group (Israel) | Nation-state spyware, zero-click iOS exploits |
| Predator | Intellexa | Mercenary spyware targeting politicians/journalists |
| RCS Lab | RCS Lab (Italy) | Commercial surveillance, Europe/Asia targets |
| KingSpawn | Quadream (Israel) | iOS-targeting commercial spyware |
| NoviSpy | Serbian state | Documented journalist targeting campaign (2024) |
| DarkSword | Unknown | IOCs from 2026 campaign |
| Stalkerware | Various | Consumer tracking apps, domestic abuse vector |
| Cellebrite | Cellebrite (Israel) | Forensic extraction tool indicators |
| Wintego Helios | Wintego | Commercial surveillance platform |
| Coruna | Cryptowaters | Mercenary spyware campaign (2026) |

---

## 💻 Requirements

- Windows 10 or Windows 11
- Python 3.8 or higher
- iTunes (Microsoft Store version or Apple website)
- Microsoft Visual C++ Build Tools
- iOS device with USB cable

---

## ⚙️ Installation

### Step 1 — Install Python

1. Go to [python.org/downloads](https://python.org/downloads)
2. Download the latest version
3. Run the installer
4. ⚠️ **CRITICAL:** On the first screen check **"Add Python to PATH"** before clicking anything else

Verify install:
```bash
python --version
```

---

### Step 2 — Install Visual C++ Build Tools

MVT requires this to compile the `pyahocorasick` dependency (C extension for high-performance string matching).

1. Go to [visualstudio.microsoft.com/visual-cpp-build-tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
2. Click **Download Build Tools**
3. In the installer select **"Desktop development with C++"** workload only
4. Click **Install** (~129MB)
5. Restart your terminal after install completes

---

### Step 3 — Install MVT

Open Command Prompt or PowerShell **as Administrator** and run:

```bash
pip install mvt
```

Verify install:
```bash
mvt-ios --version
```

---

### Step 4 — Download Latest IOC Indicators

```bash
mvt-ios download-iocs
```

This downloads the latest spyware indicators from Amnesty International's GitHub. MVT will load them automatically on next run. Re-run this command periodically to stay current.

---

## 📲 Usage

### Step 1 — Create an Encrypted iTunes Backup

> ⚠️ Encrypted backup is **required** — unencrypted backups exclude sensitive data domains needed for comprehensive scanning.

1. Open iTunes and connect your iPhone via USB
2. Trust the computer on your phone if prompted
3. Click the iPhone icon in iTunes
4. Under **Backups** select **This Computer**
5. Check **Encrypt local backup**
6. Set a strong password — **do not forget this, you cannot recover it**
7. Click **Back Up Now**
8. Wait for completion — check **Edit → Preferences → Devices** to confirm

---

### Step 2 — Find Your Backup Folder

Paste this into PowerShell:

```powershell
cd "$env:USERPROFILE\Apple\MobileSync\Backup\"
dir
```

> If that path doesn't exist, iTunes was installed from Apple's website. Try:
> ```powershell
> cd "$env:APPDATA\Apple Computer\MobileSync\Backup\"
> dir
> ```

You'll see a folder with a long alphanumeric name — that's your backup. Copy the full name.

---

### Step 3 — Decrypt the Backup

```bash
mvt-ios decrypt-backup --password YOUR_BACKUP_PASSWORD --destination "C:\Users\[USERNAME]\Desktop\decrypted_backup" "C:\Users\[USERNAME]\Apple\MobileSync\Backup\[BACKUP_FOLDER_NAME]"
```

Replace:
- `YOUR_BACKUP_PASSWORD` — the password you set in iTunes
- `[USERNAME]` — your Windows username
- `[BACKUP_FOLDER_NAME]` — the folder name from Step 2

Wait for completion. You'll see INFO lines scrolling — this is normal. Took approximately 15-20 minutes on a heavily used device.

---

### Step 4 — Run the MVT Scan

```bash
mvt-ios check-backup --iocs "C:\Users\[USERNAME]\AppData\Local\mvt\mvt\indicators" --output "C:\Users\[USERNAME]\Desktop\mvt_results" "C:\Users\[USERNAME]\Desktop\decrypted_backup"
```

MVT will scan all modules against 11,000+ spyware indicators. Allow 10-20 minutes to complete.

---

## 📊 Reading Results

### What to look for

| Output | Meaning |
|---|---|
| `INFO ... produced no detections!` | ✅ Clean — no IOC matches |
| `WARNING` | ⚠️ Flag for review — may or may not be malicious |
| `DETECTED` in any filename in results folder | 🚨 Potential compromise — investigate further |

### Result files

MVT writes JSON output files to your specified `--output` folder. Check for any files with **DETECTED** in the filename — these require immediate attention.

### Common benign warnings

- **HTTP redirect warnings** — normal website redirects (e.g. ebike.com → bosch-ebike.com) are flagged but not malicious
- **Lockdown Mode disabled** — this is the default iOS state, not a threat indicator
- **WhatsApp no data** — appears if WhatsApp is not installed

---

## 📋 My Results Summary

| Module | Status | Detail |
|---|---|---|
| BackupInfo | ✅ No Detections | 174 apps, device info extracted |
| DataUsage | ✅ No Detections | 327,578 processes analyzed |
| SafariBrowserState | ✅ No Detections | Browser state clean |
| SafariHistory | ✅ No Detections | 3,621 history records — 1 benign redirect |
| TCC Permissions | ✅ No Detections | All permissions from known legitimate apps |
| Shortcuts | ✅ No Detections | 8 shortcuts checked |
| Applications | ✅ No Detections | 174 apps verified |
| Calendar | ✅ No Detections | 179 calendar items clean |
| GlobalPreferences | ✅ No Detections | Lockdown Mode disabled (expected) |
| WhatsApp | N/A | Not installed |

**Total indicators checked: 11,209**  
**Detections: 0**

---

## 🔒 Security Recommendations

Based on this assessment and general mobile security best practices:

1. **Keep iOS updated** — most known spyware exploits unpatched vulnerabilities
2. **Restart your phone weekly** — some spyware cannot survive reboots
3. **Consider Lockdown Mode** — Settings → Privacy & Security → Lockdown Mode (recommended for elevated threat profiles)
4. **Audit app permissions** — review microphone, camera, and location access regularly
5. **Use Signal** for sensitive communications
6. **Monitor background data usage** — unexpected spikes can indicate exfiltration
7. **Re-run MVT quarterly** — new IOCs are added as new campaigns are discovered

---

## 📁 Repository Contents

```
├── README.md                          # This file
├── MVT_Security_Assessment_Report.docx  # Full technical report
└── sample_output/                     # Example of clean scan output (redacted)
```

---

## ⚠️ Disclaimer

This assessment was performed on a personally owned device for security hygiene purposes. Results reflect known, publicly documented IOCs only. MVT itself notes that public indicators will not detect the most advanced nation-state attacks using unknown infrastructure.

> "Using MVT with public indicators of compromise (IOCs) WILL NOT automatically detect advanced attacks." — MVT Project

If you have serious concerns about a possible spyware attack, contact Amnesty International's Security Lab at [securitylab.amnesty.org/get-help](https://securitylab.amnesty.org/get-help/?c=mvt).

---

## 🔗 Resources

- [MVT Official Site](https://mvt.re)
- [MVT GitHub Repository](https://github.com/mvt-project/mvt)
- [Amnesty International Security Lab](https://securitylab.amnesty.org)
- [iOS Lockdown Mode](https://support.apple.com/lockdown-mode)
- [NSO Group Pegasus Investigation — Amnesty Tech](https://www.amnesty.org/en/latest/research/2021/07/forensic-methodology-report-how-to-catch-nso-groups-pegasus/)

---

*Part of the Mecapixel cybersecurity portfolio — OSCP track | Digital Forensics | Python*
