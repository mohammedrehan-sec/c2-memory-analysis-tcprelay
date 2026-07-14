# Command & Control Memory Analysis

## Objective
To analyze a memory image to identify a suspicious executable associated with malware activity and determine its full file path within the system.

## Scenario
A workstation exhibited unusual performance issues while the installed antivirus software reported no infections. To investigate further, a memory image of the workstation was captured. The objective was to analyze the memory dump and identify any suspicious executable files that could be linked to malware or Command & Control (C2) activity.

## Tools Used
- **Volatility 3** – Memory analysis framework
- **strings** – Linux utility to extract readable text from binary files
- **grep** – Command-line search tool for filtering output
- **Kali Linux Terminal** – Analysis environment
- **Memory Dump File:** `Command & Control.dmp` (renamed to `c2mem.dmp`)

## Methodology

### Step 1 – Rename the memory dump for easier usage
```bash
mv "Command & Control.dmp" c2mem.dmp
```

### Step 2 – Extract readable strings from the memory dump
```bash
strings ~/Downloads/c2mem.dmp > c2_strings.txt
```

### Step 3 – Search extracted strings for executable files
```bash
grep -i ".exe" c2_strings.txt
```

### Step 4 – Analyze results
Multiple executable paths were returned. The output included legitimate Windows system executables as well as third-party application binaries. One executable stood out due to its location in a temporary directory.

## Analysis & Findings
Among the many executables identified in the memory dump, the majority were legitimate Windows system files or known applications. However, one executable was flagged as suspicious:

| Attribute | Value |
|-----------|-------|
| **Executable Name** | `tcprelay.exe` |
| **Full Path** | `C:\Users\John Doe\AppData\Local\Temp\TEMP23\tcprelay.exe` |
| **Reason for Suspicion** | Located in a temporary directory — a common technique used by malware to evade detection |

Temporary directories such as `AppData\Local\Temp` are frequently exploited by malware as staging locations for payloads. The combination of the filename (`tcprelay.exe` — suggestive of TCP traffic relay/proxying) and its location in a randomly-named temp subfolder (`TEMP23`) are strong indicators of malicious activity consistent with C2 communication capability.

## Screenshots
> <img width="697" height="456" alt="Screenshot 2026-04-14 153434" src="https://github.com/user-attachments/assets/3451d504-4380-4d0b-bfb3-3c54469e06f6" />
> <img width="576" height="690" alt="image" src="https://github.com/user-attachments/assets/e02142d1-10e2-4594-baf5-db8b5c3bc260" />

## Conclusion
The memory analysis successfully identified a suspicious executable linked to potential malware activity. The file `tcprelay.exe`, found at `C:\Users\John Doe\AppData\Local\Temp\TEMP23\tcprelay.exe`, exhibits behavioral indicators consistent with a Command & Control implant. Its presence in a temporary directory, combined with its TCP relay functionality, suggests the system may have been compromised and used as part of a C2 infrastructure.

## Recommendations
- Monitor temporary directories (`AppData\Local\Temp`) for unauthorized or unexpected executable files
- Deploy endpoint detection and response (EDR) tools capable of detecting memory-resident threats
- Implement application whitelisting to prevent unauthorized executables from running
- Conduct regular memory forensics as part of incident response procedures

*Part of the Cyber50 Defense Blue Team Internship Program – Digital Forensics module.*
