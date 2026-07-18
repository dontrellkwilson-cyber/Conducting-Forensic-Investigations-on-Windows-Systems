# Conducting Forensic Investigations on Windows Systems

<p align="center">
  <strong>Live Windows Analysis, NTFS Examination, Registry Forensics, and Windows Artifact Investigation</strong>
</p>

---

## Objective

The objective of this lab was to collect and analyze forensic evidence from both a live Windows system and a Windows drive image.

The investigation included examining running processes, listening network ports, NTFS volume information, the USN change journal, Windows Registry artifacts, shortcut files, installed applications, browser activity, and files that may have been intentionally concealed.

## Skills Demonstrated

- Live forensic analysis on Windows Server 2019
- Running-process and executable-path examination
- Network listening-port analysis
- NTFS volume inspection with `fsutil`
- USN change journal examination
- File identification through NTFS file IDs
- Windows Registry analysis with Registry Editor
- Windows installation timestamp conversion
- Network-interface Registry analysis
- Winlogon, ShellBag, and RecentDocs examination
- Windows drive-image analysis with Paraben E3
- File sorting and extension-mismatch detection
- Windows shortcut (`.lnk`) analysis
- Installed-application and startup-entry examination
- Browser history and search-keyword analysis
- Advanced keyword searching with a custom word list
- Identification of Tor and Firefox-related Registry evidence

## Lab Environment

| Component | Details |
|---|---|
| Workstation | `vWorkstation` |
| Operating System | Windows Server 2019 |
| Evidence Image | `BG_evidence.001` |
| Evidence Folder | `C:\Beverly Gates Evidence` |
| Suspect | Beverly Gates |
| Investigation Type | Windows system and drive-image forensics |

## Tools Used

| Tool | Purpose |
|---|---|
| **Task Manager** | Examined running processes, executable paths, and process properties |
| **Resource Monitor** | Reviewed active network connections and listening ports |
| **Fsutil** | Examined NTFS volume data, the USN journal, and file IDs |
| **Registry Editor** | Examined Windows installation, interface, login, ShellBag, and RecentDocs artifacts |
| **Paraben E3** | Imported and analyzed the Windows evidence image |
| **Epoch Converter** | Converted the Windows installation timestamp into a readable date |
| **Command Prompt** | Executed NTFS and USN journal commands |

## Investigation Scenario

In this lab, I first performed live analysis on a Windows Server 2019 system. I gathered information about active processes, network activity, the NTFS file system, and Registry artifacts.

I then examined a forensic image of Beverly Gates's Windows laptop. Beverly Gates, the HR manager at Intricate Solutions, was suspected of participating in a drug-trafficking operation with connections to the Russian mafia. The drive image was analyzed for concealed files, suspicious applications, Registry evidence, shortcut files, browser records, and other indicators of unauthorized activity.

---

# Section 1: Hands-On Demonstration

## Part 1: Gather Basic System Information

### Examining Running Processes

I opened Task Manager and enabled the **Command line** column. This displayed the executable path and launch arguments associated with each running process.

I selected a core Windows process and opened its Properties window to document details such as its filename, location, and digital-signature information.

<p align="center">
  <img src="https://github.com/user-attachments/assets/55bb1db1-f6a7-4c6d-b498-cc2d7b8bdb9e"
       alt="FTK Imager forensic evidence verification"
       width="450">
</p>

<p align="center"><em>Figure 1: Properties for a core Windows process selected during live system analysis.</em></p>

### Reviewing Listening Ports

I opened Resource Monitor, selected the **Network** tab, and expanded **Listening Ports**. This view displayed the processes, process IDs, local addresses, ports, protocols, and firewall status associated with active listeners.

<p align="center">
  <img src="https://github.com/user-attachments/assets/091bec50-f1bf-415f-8cab-005b13385046"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 2: Active listening ports and their associated Windows processes.</em></p>

### Examining the NTFS Volume

I used the following command to display NTFS information for the system drive:

```cmd
fsutil fsinfo ntfsinfo C:
```

The results included the NTFS version, volume serial number, sector and cluster information, Master File Table locations, and free-space data.

<p align="center">
  <img src="https://github.com/user-attachments/assets/560e0b30-7a03-4947-8be4-128a936d25f8"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 3: NTFS volume information collected from the `C:` drive.</em></p>

### Examining the USN Change Journal

I queried the USN change journal with:

```cmd
fsutil usn queryjournal C:
```

The USN journal records file-system changes such as file creation, deletion, and modification.

<p align="center">
  <img src="https://github.com/user-attachments/assets/5a6ff321-7432-46f0-af61-0c4f3c27dfc6"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 4: USN change journal information for the Windows system drive.</em></p>

I also reviewed journal records with:

```cmd
fsutil usn readjournal C:
```

To create a searchable CSV copy of the journal, I used:

```cmd
cd Desktop
fsutil usn readjournal C: csv > usn.log
```

### Locating a File Through Its NTFS File ID

I created a text file named:

```text
Dontrell Wilson.txt
```

After locating its record in `usn.log`, I copied the NTFS file ID and queried its path with:

```cmd
 fsutil file queryFileNameById C:\ 0x0000000000000000000a00000001c2e2
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/05168a6b-8d93-4cff-8a44-a4d4fdd440b1"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 5: File path retrieved by querying the NTFS file ID for `Dontrell Wilson.txt`.</em></p>

---

## Part 2: Explore the Windows Registry

### Windows Installation Information

I opened Registry Editor and navigated to:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion
```

The `CurrentVersion` key contained operating-system information such as the Windows edition, build, installation type, registered owner, and installation timestamp.

I copied the hexadecimal `InstallDate` value and converted it into a human-readable date.

<p align="center">
  <img src="https://github.com/user-attachments/assets/72de9f42-33a8-41ec-be7e-a6ec42578a01"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 6: Windows installation timestamp converted from hexadecimal to a readable date.</em></p>

### Default Network Interface

I examined the Registry key for the default network interface:

```text
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{39007c8a-d2a1-4c34-bb9d-e9c1bce829b3}
```

The values included network configuration information such as the assigned IP address, DHCP details, and default gateway.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b5ea7028-7ffd-42df-94cf-4b273e2380c3"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 7: Registry values associated with the default network interface.</em></p>

### Winlogon Registry Key

I navigated to:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
```

The Winlogon key stores information related to user logon behavior, profile loading, and the Windows shell.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9a88b2a6-bece-4560-a5bb-1c5e6eb1172e"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 8: Values stored in the Windows Winlogon Registry key.</em></p>

### ShellBag Artifacts

I navigated to:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\Shell\Bags
```

ShellBag artifacts can show that a user accessed a folder, including folders that may no longer exist on the system.

<p align="center">
  <img src="https://github.com/user-attachments/assets/30aead73-39b1-47b5-8771-4f814e1b62de"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 9: ShellBag values documenting folders accessed by the current user.</em></p>

### RecentDocs Artifacts

I examined:

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
```

The RecentDocs key contained records associated with files recently accessed through File Explorer.

<p align="center">
  <img src="https://github.com/user-attachments/assets/173a79af-f6af-42c7-94c4-9263157afbfb"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 10: RecentDocs values showing evidence of recently accessed files.</em></p>

---

# Section 2: Applied Learning

## Part 1: Create and Sort a New E3 Case

I created an E3 case named:

```text
Dontrell Wilson Windows Case File
```

I then imported the first segment of the Windows forensic image:

```text
C:\Beverly Gates Evidence\BG_evidence.001
```

After the image was added, I ran E3 Content Analysis to categorize the files and artifacts found on the drive.

### Sorted Files

E3 organized the analyzed evidence into categories such as archives, documents, images, applications, and other file types.

<p align="center">
  <img src="https://github.com/user-attachments/assets/22711403-f8e0-45a4-b3f0-f62a6e5e7d6a"
       alt="FTK Imager forensic evidence verification"
       width="800">
</p>

<p align="center"><em>Figure 11: Files categorized by E3 after Content Analysis completed.</em></p>

---

## Part 2: Perform Forensic Analysis on the Windows Drive Image

### Detecting an Extension Mismatch

I searched the sorted files for `.jpg` files with mismatched extensions. The query identified `777.jpg` as a file whose actual content did not match its displayed extension.

The file was located in:

```text
Root\Users\BeverlyGates\AppData\Local\VirtualStore
```

Although the file used a `.jpg` extension, E3 identified it as a compressed package. The Document View showed that it was actually an Excel spreadsheet containing customer names, amounts owed, and drug-related information.

<p align="center">
  <img src="https://github.com/user-attachments/assets/281b8f40-b012-4c39-8f08-e8665d1fcae9"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 12: The disguised `777.jpg` file displayed as an incriminating spreadsheet in E3.</em></p>

### Analyzing the 777.lnk Shortcut

I navigated to:

```text
Data Triage
└── Link Files
    └── Recent
```

I selected `777.lnk` and reviewed it in Text View. The shortcut contained the original path to `777.jpg`, connecting the suspicious spreadsheet to recent user activity.

<p align="center">
  <img src="https://github.com/user-attachments/assets/5f35dcc6-129f-4ee7-937d-ae84510bc845"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 13: `777.lnk` showing the system path associated with the suspicious file.</em></p>

### Suspicious Downloaded Applications

I reviewed:

```text
Data Triage
└── Downloads
```

The Downloads category contained installation files associated with suspicious applications, including:

- Speedify VPN
- Tor Browser

The presence of anonymizing software was relevant because such tools can be used to conceal network identity and browsing activity.

<p align="center">
  <img src="https://github.com/user-attachments/assets/7cf04909-a793-4418-a6d8-5686e618304e"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 14: Installation files for suspicious anonymizing applications found in Downloads.</em></p>

### Installed Speedify VPN Application

I navigated to:

```text
Data Triage
└── Registry
    └── Windows 10 Enterprise
        └── Parsed Registry Data
            └── Programs
                └── Uninstall
```

The Uninstall results confirmed that Speedify VPN had not only been downloaded but installed on the suspect's computer.

<p align="center">
  <img src="https://github.com/user-attachments/assets/5c3d0bc8-4b00-4fcd-bb7c-479165e0af97"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 15: Speedify VPN installation evidence found in the Windows Registry.</em></p>

### User Accounts

I opened the **Users Info** category to identify accounts found in the drive image.

<p align="center">
  <img src="https://github.com/user-attachments/assets/99bed914-bed2-4e77-a10b-594c72130529"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 16: Windows user accounts identified in the Beverly Gates evidence image.</em></p>

### Beverly Gates Startup Entries

I opened the Beverly Gates user record and reviewed the `Run` folder. This folder contained applications configured to start automatically when the user logged on.

<p align="center">
  <img src="https://github.com/user-attachments/assets/6ed2a0fc-2d83-4b7a-9ef0-2147248a4ddc"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>


<p align="center"><em>Figure 17: Startup applications found in the Beverly Gates `Run` Registry folder.</em></p>

### Suspicious Browser History

I reviewed:

```text
Data Triage
└── Internet Browser Data
    └── Default
        └── History
```

The History records contained suspicious browsing activity relevant to the investigation.

<p align="center">
  <img src="https://github.com/user-attachments/assets/fb72bca1-04ac-4f7a-9ef2-6984297b76d9"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 18: Suspicious browser activity identified in the History records.</em></p>

### Suspicious Search Keywords

I reviewed the **Keywords** sub-node and identified search terms associated with the suspected activity.

<p align="center">
  <img src="https://github.com/user-attachments/assets/f2f1e60d-ea1d-4b82-b270-279aa1305687"
       alt="Paraben's E3 forensic evidence analysis"
       width="800">
</p>

<p align="center"><em>Figure 19: Suspicious search keyword recovered from browser artifacts.</em></p>


---

# Section 3: Challenge and Analysis

## Part 1: Use Advanced Search to Locate Additional Evidence

I used E3 Advanced Search on:

```text
Users\Beverly Gates\AppData\Local\Packages
```

I loaded the custom search list:

```text
Drug Search Term List1.txt
```

I enabled the **Whole word** option and searched for terms commonly associated with drug-trafficking investigations.

The search identified a suspicious file containing addresses relevant to the investigation.

<p align="center">
  <img width="900" alt="Suspicious file containing addresses located through E3 Advanced Search" src="PASTE-ADVANCED-SEARCH-SUSPICIOUS-FILE-IMAGE-URL-HERE" />
</p>

<p align="center"><em>Figure 20: Suspicious file containing addresses located with E3 Advanced Search.</em></p>

---

## Part 2: Identify Suspicious Browser Activity

I returned to the parsed Registry artifacts and navigated to:

```text
Data Triage
└── Registry
    └── Windows 10 Enterprise
        └── Parsed Registry Data
            └── Users Info
                └── Beverly Gates
                    └── Applications
```

I ran an Advanced Search for the whole word:

```text
Tor
```

The results included Registry evidence connecting Tor-related activity with Firefox, indicating that Tor had been used or configured as a Firefox extension.

<p align="center">
  <img width="900" alt="Registry key showing information associated with Tor and Firefox" src="PASTE-TOR-FIREFOX-REGISTRY-IMAGE-URL-HERE" />
</p>

<p align="center"><em>Figure 21: Registry evidence associating Tor-related activity with Firefox.</em></p>

---

# Key Findings

- Task Manager and Resource Monitor provided live evidence of running processes and active listening ports.
- `fsutil` exposed NTFS volume details, USN journal information, and file-path data based on NTFS file IDs.
- The Windows Registry contained installation, network, login, folder-access, and recent-document artifacts.
- E3 detected a file-extension mismatch involving `777.jpg`.
- The disguised file was an Excel spreadsheet containing customer, payment, and drug-related information.
- The `777.lnk` shortcut connected the spreadsheet to a path on the suspect's computer and supported evidence of recent access.
- Tor Browser and Speedify VPN installation files were present in the Downloads category.
- Registry artifacts confirmed that Speedify VPN had been installed.
- The Beverly Gates user profile contained startup entries and relevant application artifacts.
- Browser History and Keywords records contained suspicious browsing evidence.
- Advanced Search located an additional file containing suspicious addresses.
- Registry search results connected Tor-related activity with Firefox.

# Artifact Summary

| Artifact | Tool or Location | Forensic Value |
|---|---|---|
| Process properties | Task Manager | Identified executable information for a running Windows process |
| Listening ports | Resource Monitor | Associated network listeners with processes and PIDs |
| NTFS information | `fsutil fsinfo` | Documented volume and file-system structure |
| USN journal | `fsutil usn` | Provided a record of file-system changes |
| InstallDate | Windows Registry | Established the Windows installation timestamp |
| Interface Registry key | Windows Registry | Documented IP and gateway configuration |
| Winlogon | Windows Registry | Showed system login and shell configuration |
| ShellBags | Windows Registry | Documented folders accessed by the user |
| RecentDocs | Windows Registry | Identified recently accessed files |
| `777.jpg` | Paraben E3 | Revealed a concealed incriminating spreadsheet |
| `777.lnk` | Paraben E3 | Connected the file to a system path and recent access |
| Speedify and Tor installers | E3 Downloads | Showed acquisition of anonymizing tools |
| Speedify Uninstall key | E3 Registry analysis | Confirmed installation of the VPN application |
| Browser History and Keywords | E3 browser analysis | Identified suspicious browsing and searches |
| Custom word-list result | E3 Advanced Search | Located additional suspicious address evidence |
| Tor/Firefox Registry evidence | E3 Registry analysis | Connected Tor-related activity to Firefox |

# Conclusion

This lab demonstrated how Windows forensic investigations combine live system analysis with offline drive-image examination.

Live analysis provided volatile and system-level information about running processes, network activity, NTFS structures, and Registry configuration. Examination of the Beverly Gates drive image then revealed a disguised spreadsheet, shortcut-file evidence, anonymizing applications, startup entries, browser artifacts, suspicious addresses, and Tor-related Registry activity.

The lab reinforced the importance of correlating multiple Windows artifacts rather than relying on a single file or record. Together, the file system, Registry, shortcut, application, and browser evidence created a stronger and more complete account of the suspect's activity.

---

> **Note:** This repository documents work completed in an authorized educational lab environment. The evidence, users, devices, and investigative scenario are fictional and were provided only for cybersecurity and digital forensics training.
