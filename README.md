# Malware IOC Scanner

A PowerShell-based Windows malware detection and incident response scanner designed to identify indicators commonly associated with **RATs (Remote Access Trojans), clipboard stealers (clippers), persistence malware, and fake system-process malware**.

This project was created from real-world malware investigation patterns and focuses on finding suspicious behavior rather than relying only on file hashes or antivirus signatures. For windows automation scripts follow the project: https://github.com/noble3212/WindowsAutomation.git

> ⚠️ This tool is a security research / incident response utility. It is not a replacement for Windows Defender, EDR solutions, or professional malware analysis tools. 

---

# Features

The scanner checks for multiple malware persistence and execution techniques:

## 1. Known Malware File Indicators

Detects executables matching known suspicious malware characteristics:

- Known malicious file sizes
- Suspicious executable locations
- Malware remnants left after removal

Example detections:

```

AppData\Local\Health\HealthService.exe
Temp\service_main.exe
Temp\bootstrap_helper.exe

```

---

## 2. Fake System Process Detection

Many malware families attempt to disguise themselves as legitimate Windows components.

The scanner looks for Windows-named executables running from abnormal locations.

Examples:

```

RuntimeBroker.exe
SecurityHealth.exe
HealthService.exe
svchost.exe
csrss.exe
lsass.exe
services.exe

```

Legitimate:

```

C:\Windows\System32\RuntimeBroker.exe

```

Suspicious:

```

C:\Users\User\AppData\Roaming\RuntimeBroker.exe

```

---

## 3. Registry Persistence Detection

Checks common Windows startup locations:

```

HKCU:\Software\Microsoft\Windows\CurrentVersion\Run

HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKLM:\Software\Microsoft\Windows\CurrentVersion\Run

HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce

```

Detects:

- Malware launching after reboot
- Startup entries pointing to AppData
- Suspicious deleted-malware registry leftovers

---

## 4. Scheduled Task Analysis

Checks for malicious persistence through Windows Task Scheduler.

Detects:

- Known malware task names
- Tasks launching from user folders
- Suspicious hidden persistence mechanisms

---

## 5. Startup Folder Analysis

Checks:

```

%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup

%ProgramData%\Microsoft\Windows\Start Menu\Programs\Startup

```

Flags:

- Executables
- Scripts
- Command files
- Suspicious startup persistence

---

## 6. COM Hijacking Detection

Searches for COM persistence techniques commonly abused by malware.

Checks:

- Known abused CLSIDs
- COM registrations pointing to:
  - AppData
  - Temp
  - Downloads

---

## 7. Running Process Investigation

Analyzes active processes for:

- System processes running outside Windows directories
- Unsigned executables
- Programs running from:
  - Downloads
  - Temp
  - AppData

---

## 8. Remote Access Tool Detection

Looks for suspicious remote-control software often abused by attackers.

Examples:

```

AnyDesk
TeamViewer
UltraViewer
Ammyy Admin
DWAgent

```

Flags:

- Remote tools running from Downloads
- Portable remote access executables
- Suspicious execution paths

---

## 9. Clipper / Clipboard Malware Indicators

Checks for indicators associated with cryptocurrency clipboard stealers.

Looks for:

- Hidden AppData processes
- Background executables without visible windows
- Suspicious user-folder execution

---

## 10. Recent File Activity

Checks recent Windows activity for suspicious files.

Looks for:

- Recently created shortcuts
- Malware-like filenames
- Installer impersonation patterns

---

# How This Was Built

This scanner was created using PowerShell with a focus on real incident response workflows.

The detection logic was based on:

- Malware persistence analysis
- Windows forensic investigation
- IOC (Indicator of Compromise) hunting
- Suspicious process behavior analysis
- Registry investigation
- Malware cleanup validation

The development process involved:

1. Identifying malware artifacts from real infections
2. Extracting common attacker behaviors
3. Building detection rules around those behaviors
4. Testing against infected environments and malware remnants
5. Adjusting rules to detect persistence techniques

The goal was not to create another antivirus engine.

The goal was:

> "Find the things malware leaves behind."

---

# Testing

The scanner was tested against:

- Real malware indicators
- RAT-style persistence methods
- Clipboard stealer indicators
- Fake Windows process naming techniques
- Suspicious AppData execution
- Registry persistence artifacts
- Scheduled task persistence

Testing focused on whether the scanner could detect:

- Malware pretending to be Windows components
- Persistence after reboot
- Suspicious execution locations
- Common attacker mistakes

---

# False Positive Warning

Because this scanner uses aggressive behavioral detection, it **will detect legitimate software in some situations.**

Examples of possible false positives:

## Legitimate Applications Using AppData

Many normal applications run from:

```

C:\Users\User\AppData\

```

Examples:

- Discord
- Steam
- VS Code extensions
- Electron applications
- Game launchers
- Developer tools

---

## Legitimate Remote Access Software

Remote administration tools may trigger alerts:

Examples:

- IT support tools
- Remote desktop utilities
- Company management software

---

## Unsigned Applications

Many independent developers distribute applications without code signing.

The scanner may flag:

- Personal scripts
- Open-source tools
- Small utilities
- Internal company software

---

## System-Like Names

Some applications may intentionally or accidentally use names similar to Windows components.

Example:

```

RuntimeBroker.exe
HealthService.exe
SecurityHealth.exe

````

The location matters more than the filename.

---

# Usage

Run normally:

```powershell
.\MalwareScanner.ps1
````

Run deeper scanning:

```powershell
.\MalwareScanner.ps1 -DeepScan
```

Silent mode:

```powershell
.\MalwareScanner.ps1 -Silent
```

---

# Output

The scanner provides severity ratings:

| Severity | Meaning                  |
| -------- | ------------------------ |
| CRITICAL | Strong malware indicator |
| HIGH     | Very suspicious behavior |
| MEDIUM   | Requires investigation   |
| LOW      | Informational            |
| INFO     | General observation      |

Results are exported automatically:

```
Downloads\malware-scan-YYYY-MM-DD_HH-MM-SS.csv
```

---

# Requirements

* Windows 10 / Windows 11
* PowerShell 5+
* Administrator privileges recommended

Some checks may be limited without administrator access.

---

# Limitations

This tool does not:

* Perform memory analysis
* Reverse engineer malware
* Scan encrypted files
* Replace antivirus software
* Guarantee a clean system

A clean scan means:

"No known indicators detected."

It does not mean:

"Impossible to have malware."

---

# Future Improvements

Potential improvements:

* YARA rule integration
* Hash reputation checking
* VirusTotal API integration
* Event log analysis
* Network connection inspection
* PE header analysis
* Digital certificate reputation scoring
* JSON reporting
* Automated remediation mode

---

# Disclaimer

This project is intended for:

* Security research
* Malware analysis education
* Incident response assistance
* Personal system auditing

Use responsibly. Always verify findings before deleting files or modifying system configuration.

A detection is a lead, not a verdict

