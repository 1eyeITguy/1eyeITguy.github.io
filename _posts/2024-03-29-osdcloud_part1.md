---
title: OSDCloud - Part 1
date: 2024-03-29 10:00:00 -0600
categories: [OSDCloud]
tags: [OSDCloud, powershell]
image:
 path: /assets/img/headers/OSDCloud_Part1.webp
---

# OSDCloud - Part 1 - The Prerequisites  

This blog series is about how I use OSDCloud as a solution for my deployment needs. I hope to share what I have learned about this awesome tool and help others who might be interested in it.

My goal that led me to OSDCloud was to deploy Windows 11 and have computers only joined to Entra ID. I wanted to move to modern management away from MDT and GPOs, but Intune and Autopilot.  After attending [MMS](https://mmsmoa.com/) at the Mall of America I gained a lot of knowledge and came up with a great solution based on OSDCloud. It took a lot of experimenting, but it paid off.

This series will not cover everything that OSDCloud can do, but just what I have used. I decided to create this blog to document my process and also to hopefully help the community and others who might benefit from it.

In this first post, I will present a PowerShell script that can help you prepare for using OSDCloud. This script installs all the required components for OSDCloud, including the Windows 11 ADK, WinPE Add-On, and MDT. It also installs the latest OSD PowerShell module from the PowerShell gallery if it is not already installed.

I want to thank David Segura for creating OSDCloud. You can find more information about OSDCloud on the official website, [OSDCloud.com](https://www.osdcloud.com/), and on David Segura's [GitHub](https://github.com/OSDeploy) page. I also want to acknowledge Gary Blok and Ákos Bakos, who have added to the OSDCloud project. You can find their work on their GitHub pages and personal websites, [garytown.com](https://www.garytown.com) and [akosbakos.ch](https://akosbakos.ch/).

My current setup is utilizing a Windows Server 2022 VM.  I also have WDS installed on this VM so I can have clients PXE boot the OSDCloud boot.wim that is created.  I'll have examples of that in an upcoming post.

Now, let's look at the [setup script](https://github.com/1eyeITguy/Cloud_Scipts/blob/main/OSDCloud/OSDCloud_Prereqs.ps1).

You can download the script from my [GitHub page](https://github.com/1eyeITguy/Cloud_Scipts/blob/main/OSDCloud/OSDCloud_Prereqs.ps1), or you can open an administrator PowerShell prompt and run it directly using the command below. ***Before executing any command, especially when it involves downloading and running scripts from the internet, exercise caution and understand the code.***

```powershell
Invoke-Expression (Invoke-RestMethod -Uri "https://raw.githubusercontent.com/1eyeITguy/Cloud_Scipts/main/OSDCloud/OSDCloud_Prereqs.ps1")
```

This first section is defining the executable URLs, and downloads them to c:\temp.  We’ll also check to see if that directory exists, and if not create it.

```powershell
# Define the download URLs
$adkUrl = "https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/adksetup.exe"
$addonUrl = "https://download.microsoft.com/download/5/2/5/525dcde0-c7b8-487a-894d-0952775a78c7/adkwinpeaddons/adkwinpesetup.exe"
$mdtUrl = "https://download.microsoft.com/download/3/3/9/339BE62D-B4B8-4956-B58D-73C4685FC492/MicrosoftDeploymentToolkit_x64.msi"

# Define the installation paths
$adkInstallerPath = "C:\temp\adksetup.exe"
$addonInstallerPath = "C:\temp\adkwinpesetup.exe"
$mdtInstallerPath = "C:\temp\MicrosoftDeploymentToolkit_x64.msi"

# Create C:\temp directory if it doesn't exist
if (-not (Test-Path -Path "C:\temp" -PathType Container)) {
    New-Item -Path "C:\temp" -ItemType Directory | Out-Null
    Write-Host -ForegroundColor Green "[+] Created C:\Temp Directory"
}

# Download the installers using curl 
curl -Uri $adkUrl -OutFile $adkInstallerPath
curl -Uri $addonUrl -OutFile $addonInstallerPath
curl -Uri $mdtUrl -OutFile $mdtInstallerPath
```

Next, we’ll run the silent install commands to install each of the three programs.

```powershell
# Install ADK silently
Write-Host -ForegroundColor Yellow "[-] Installing Windows ADK..."
Start-Process -FilePath $adkInstallerPath -ArgumentList "/quiet" -Wait
Write-Host -ForegroundColor Green "[+] Windows ADK Installed"

# Install WinPE Addon silently
Write-Host -ForegroundColor Yellow "[-] Installing WinPE Add-On..."
Start-Process -FilePath $addonInstallerPath -ArgumentList "/quiet" -Wait
Write-Host -ForegroundColor Green "[+] WinPE Add-On installed"

# Install MDT silently
Write-Host -ForegroundColor Yellow "[-] Installing MDT..."
Start-Process -FilePath msiexec.exe -ArgumentList "/i $mdtInstallerPath /quiet" -Wait
New-Item -Path 'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs' -ItemType Directory -Force | Out-Null
Write-Host -ForegroundColor Green "[+] MDT Installed"
```

Once setup, you won’t need the installers anymore so why don’t we just delete them.

```powershell
# Clean up: Delete the downloaded installers
Remove-Item -Path $adkInstallerPath, $addonInstallerPath, $mdtInstallerPath -Force | Out-Null
Write-Host -ForegroundColor Green "[+] Windows ADK, WinPE Addon, and MDT have been installed silently."
```

Lastly we’ll install the OSDCloud PowerShell module, and import it.   We’ll also go ahead and utilize our first OSDCloud functions of installing PackageManagement and trusting the PowerShell Gallery.

```powershell
# Check if the OSD module is installed
if (-not (Get-Module -ListAvailable -Name OSD)) {
    Write-Host -ForegroundColor Yellow "[!] OSD module is not installed. Installing the latest version..."

    Write-Host -ForegroundColor Green "[+] Transport Layer Security (TLS) 1.2"
    [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

    # Pre-Load some OSDCloud functions
    Invoke-Expression (Invoke-RestMethod -Uri 'https://raw.githubusercontent.com/OSDeploy/OSD/master/cloud/modules/_anywhere.psm1')
    osdcloud-InstallPackageManagement
    osdcloud-TrustPSGallery

    # Install the OSD module from the PowerShell Gallery
    Install-Module -Name OSD -Force

    # Import the newly installed module
    Import-Module -Name OSD
    Write-Host -ForegroundColor Green "[+] OSD module has been installed and imported."
} else {
    Write-Host -ForegroundColor Green "[+} OSD module is already installed."
}
```

Hopefully you find this setup script helpful, stay tuned for more in this series on OSDCloud and how it can help you streamline your deployment process.

# Full Script

```powershell
#Requires -RunAsAdministrator
<#
.DESCRIPTION
Installs WinGet, Microsoft ADK and the Windows PE add-on for Windows 11, version 22H2, and MDT.  ALso installs the OSDCloud 
PowerShell module if not intalled. 
.LINK
https://www.osdcloud.com/osdcloud/setup
#>
[CmdletBinding()]
param()

# Define the download URLs
$adkUrl = "https://download.microsoft.com/download/6/7/4/674ec7db-7c89-4f2b-8363-689055c2b430/adk/adksetup.exe"
$addonUrl = "https://download.microsoft.com/download/5/2/5/525dcde0-c7b8-487a-894d-0952775a78c7/adkwinpeaddons/adkwinpesetup.exe"
$mdtUrl = "https://download.microsoft.com/download/3/3/9/339BE62D-B4B8-4956-B58D-73C4685FC492/MicrosoftDeploymentToolkit_x64.msi"

# Define the installation paths
$adkInstallerPath = "C:\temp\adksetup.exe"
$addonInstallerPath = "C:\temp\adkwinpesetup.exe"
$mdtInstallerPath = "C:\temp\MicrosoftDeploymentToolkit_x64.msi"

# Create C:\temp directory if it doesn't exist
if (-not (Test-Path -Path "C:\temp" -PathType Container)) {
    New-Item -Path "C:\temp" -ItemType Directory | Out-Null
    Write-Host -ForegroundColor Green "[+] Created C:\Temp Directory"
}

# Download the installers using curl 
curl -Uri $adkUrl -OutFile $adkInstallerPath
curl -Uri $addonUrl -OutFile $addonInstallerPath
curl -Uri $mdtUrl -OutFile $mdtInstallerPath

# Install ADK silently
Write-Host -ForegroundColor Yellow "[-] Installing Windows ADK..."
Start-Process -FilePath $adkInstallerPath -ArgumentList "/quiet" -Wait
Write-Host -ForegroundColor Green "[+] Windows ADK Installed"

# Install WinPE Addon silently
Write-Host -ForegroundColor Yellow "[-] Installing WinPE Add-On..."
Start-Process -FilePath $addonInstallerPath -ArgumentList "/quiet" -Wait
Write-Host -ForegroundColor Green "[+] WinPE Add-On installed"

# Install MDT silently
Write-Host -ForegroundColor Yellow "[-] Installing MDT..."
Start-Process -FilePath msiexec.exe -ArgumentList "/i $mdtInstallerPath /quiet" -Wait
New-Item -Path 'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs' -ItemType Directory -Force | Out-Null
Write-Host -ForegroundColor Green "[+] MDT Installed"

# Clean up: Delete the downloaded installers
Remove-Item -Path $adkInstallerPath, $addonInstallerPath, $mdtInstallerPath -Force | Out-Null
Write-Host -ForegroundColor Green "[+] Windows ADK, WinPE Addon, and MDT have been installed silently."

# Check if the OSD module is installed
if (-not (Get-Module -ListAvailable -Name OSD)) {
    Write-Host -ForegroundColor Yellow "[!] OSD module is not installed. Installing the latest version..."

    Write-Host -ForegroundColor Green "[+] Transport Layer Security (TLS) 1.2"
    [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12

    # Pre-Load some OSDCloud functions
    Invoke-Expression (Invoke-RestMethod -Uri 'https://raw.githubusercontent.com/OSDeploy/OSD/master/cloud/modules/_anywhere.psm1')
    osdcloud-InstallPackageManagement
    osdcloud-TrustPSGallery

    # Install the OSD module from the PowerShell Gallery
    Install-Module -Name OSD -Force

    # Import the newly installed module
    Import-Module -Name OSD
    Write-Host -ForegroundColor Green "[+] OSD module has been installed and imported."
} else {
    Write-Host -ForegroundColor Green "[+] OSD module is already installed."
}
```
