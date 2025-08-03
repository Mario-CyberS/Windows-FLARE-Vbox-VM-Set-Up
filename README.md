# Windows FLARE Vbox VM Set-Up  
This project documents how to install and configure a Windows 10 VirtualBox VM with the FLARE-VM toolkit for reverse engineering and malware analysis. It includes clean snapshotting, Group Policy patching, Defender disablement, and final toolkit installation in a safe, isolated virtual environment.

---

## 🎯 Objective  
To build a fully functional FLARE-VM malware analysis system using Windows 10, configured in VirtualBox with Defender disabled, Guest Additions installed, and internet access secured. Snapshots are taken before and after FLARE install to support safe, revertible analysis.

---

## 🔍 Why Use FLARE-VM in a Vbox Lab?  
This setup provides:
- A dedicated reverse engineering environment  
- Dozens of malware analysis tools pre-configured  
- Snapshot safety for rollback during dangerous samples  
- Optional isolation via host-only networking  
- A repeatable build for clean and fast testing  

---

## 📚 Skills Learned  
- Windows 10 ISO installation and configuration  
- Group Policy manipulation on Windows Home  
- Disabling Microsoft Defender with gpedit.msc  
- Installing FLARE-VM via PowerShell scripts  
- VirtualBox snapshotting and display tuning  

---

## 🛠️ Tools Used  
<div>
  <a href="https://www.virtualbox.org/" target="_blank">
    <img src="https://img.shields.io/badge/-VirtualBox-183A61?style=for-the-badge&logo=VirtualBox&logoColor=white"/>
  </a>
  <a href="https://www.microsoft.com/en-us/software-download/windows10" target="_blank">
    <img src="https://img.shields.io/badge/-Windows%2010-0078D6?style=for-the-badge&logo=Windows&logoColor=white"/>
  </a>
  <a href="https://github.com/mandiant/flare-vm" target="_blank">
    <img src="https://img.shields.io/badge/-FLARE--VM-FC4C02?style=for-the-badge"/>
  </a>
  <a href="https://learn.microsoft.com/en-us/powershell/" target="_blank">
    <img src="https://img.shields.io/badge/-PowerShell-012456?style=for-the-badge&logo=PowerShell&logoColor=white"/>
  </a>
  <a href="https://x64dbg.com/" target="_blank">
    <img src="https://img.shields.io/badge/-x64dbg-4A4A4A?style=for-the-badge"/>
  </a>
</div>

---

## 📝 Deployment Steps  

### 1. Install VirtualBox on Windows Host  
Go to: https://www.virtualbox.org/wiki/Downloads  
Download for Windows hosts and install with default settings.  
(Optional) Also install the Extension Pack.

---

### 2. Download Windows 10 ISO  
Visit: [Windows 10 ISO Download](https://www.microsoft.com/software-download/windows10)  
Choose “Create Installation Media” → select Windows 10 64-bit → save ISO file.

---

### 3. Create the Windows 10 VM  
- Open VirtualBox → Click **New**  
- Name: `PMAT-FlareVM`  
- Assign RAM: 4 GB minimum  
- CPUs: 2+  
- Skip unattended install  
- Add ISO as boot media  
- Create VDI disk (60 GB+)  
- Install Windows manually: choose custom install, apply partition

---

### 4. Get Internet + Install Chrome  
Use Edge to download and install Chrome for improved browsing.  
Set Chrome as default.

---

### 5. Install VirtualBox Guest Additions  
- Devices → Insert Guest Additions CD image…  
- Open the CD and run `VBoxWindowsAdditions-amd64.exe`  
- Reboot the VM  
- Enable shared clipboard (Settings > General > Advanced > Bidirectional)

---

### 6. Take Clean Snapshot  
Shutdown the VM → Snapshots → Take snapshot  
Name: `base-install`  

---

### 7. Enable Group Policy on Windows Home  
Open PowerShell as Administrator and run:  
```powershell
Get-ChildItem @(
    "C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientTools-Package*.mum",
    "C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientExtensions-Package*.mum"
) | ForEach-Object {
    dism.exe /online /norestart /add-package:"$_"
}
```
This sequence installs the built-in Group Policy components that are packaged but disabled by default in Windows 10 Home stellarinfo.co.in+11gist.github.com+11answers.microsoft.com+11.

Reboot afterward.

---

### 8. Disable Microsoft Defender via Group Policy
Open gpedit.msc
Navigate to:
Computer Configuration → Administrative Templates → Windows Components → Microsoft Defender Antivirus

Enable:
- “Turn off Microsoft Defender Antivirus”
Then under Real-time Protection:
- “Turn off real-time protection”
- “Turn off behavior monitoring”
- “Turn off on-access protection”
- “Turn off scanning of downloaded files”

Reboot again.

---

### 9. Install FLARE-VM Toolkit
Open PowerShell as Admin, then run:
```powershell
(New-Object net.webclient).DownloadFile('https://raw.githubusercontent.com/mandiant/flare-vm/main/install.ps1',"$([Environment]::GetFolderPath("Desktop"))\install.ps1")
Unblock-File .\install.ps1
Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
.\install.ps1
```
⚠️ Keep internet on but don’t browse or open other apps.
Install takes time. Let it complete.

---

### 10. Final Snapshot & Isolation
- Take new snapshot: Flare VM Installed
- Switch networking to host-only adapter
- Disable Windows Updates in gpedit.msc

After a secure Vbox network set-up (in next sub-project) you will be ready to begin safe malware analysis using FLARE-VM.

---

### 👨‍💻 Author
Mario Tagaras | Cyber Security Specialist
















