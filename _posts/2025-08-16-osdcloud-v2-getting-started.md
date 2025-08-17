---
title: OSDCloud v2 — Getting Started
date: 2025-08-16 00:00:00 -0600
categories: [OSDCloud]
tags: [OSDCloud, OSD.Workspace, powershell]
image:
  path: /assets/img/headers/OSDCloud_Part1.webp
---

# OSDCloud v2 — Getting Started

I finally spent some time trying out OSDCloud v2 (via the OSD.Workspace project) and wanted to write down the minimal, practical steps I used to get up and running.

This post is a hands-on “get started” guide: The prerequisites.

> Note: the project docs are here: https://github.com/OSDeploy/OSD.Workspace/wiki — I’ll link the specific pages where useful, but you should read the wiki for full context and the latest changes.

## What is OSDCloud v2 

OSDCloud v2 (OSD.Workspace) is the community-driven PowerShell project used to build and deploy Windows opperating systems. It’s designed to help automate creating deployment artifacts you can use with PXE, WDS, or USB drives.

## Prerequisites 

- Windows 11 24H2
- Git for Windows
- PowerShell 7 
- Windows ADK + WinPE Add-on
- Microsoft Deployment Toolkit (MDT) if you use MDT workflows.
- An elevated (Administrator) PowerShell terminal to run install steps.

The OSD.Workspace wiki lists a Recommended Client Configuration — follow that page if you want an exact checklist: https://github.com/OSDeploy/OSD.Workspace/wiki#recommended-client-configuration

## Install / Prerequisite steps 

Open an elevated PowerShell session and run the commands below. 

1. Set the execution policy for this session and ensure TLS 1.2 is enabled (some gallery operations need it):

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned -Force
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

2. Install the NuGet Package Provider

```powershell
if ($(Get-PackageProvider).Name -notcontains 'NuGet') {
    Install-PackageProvider -Name NuGet -Force -Verbose
}
```

3. Tuest the PowerShell Gallery

```powershell
if ((Get-PSRepository -Name 'PSGallery').InstallationPolicy -ne 'Trusted') {
    Set-PSRepository -Name 'PSGallery' -InstallationPolicy Trusted
    Write-Host "PowerShell Gallery (PSGallery) has been set to Trusted."
} else {
    Write-Host "PowerShell Gallery (PSGallery) is already Trusted."
}
```

4. Update PowerShellGet

```powershell
Install-Module -Name PowerShellGet -Force -Scope AllUsers -AllowClobber -SkipPublisherCheck -Verbose
```

5. Update PackageManagement
```powershell
Install-Module -Name PackageManagement -Force -Scope AllUsers -AllowClobber -SkipPublisherCheck -Verbose
```

6. Install PowerShell 7
```powershell
winget install -e --id Microsoft.PowerShell --override '/passive ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL=1 ADD_FILE_CONTEXT_MENU_RUNPOWERSHELL=1 ENABLE_PSREMOTING=1 REGISTER_MANIFEST=1 USE_MU=1 ENABLE_MU=1 ADD_PATH=1'
```

7. Install Git for Windows
```powershell
winget install -e --id Microsoft.PowerShell --override '/passive ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL=1 ADD_FILE_CONTEXT_MENU_RUNPOWERSHELL=1 ENABLE_PSREMOTING=1 REGISTER_MANIFEST=1 USE_MU=1 ENABLE_MU=1 ADD_PATH=1'
```

8. Configure Git for Windows
```powershell
# Set user email
git config --global user.email "you@example.com"
# Set user name
git config --global user.name "Your Name"
```

9. Install VSCode Insiders
```powershell
winget install -e --id Microsoft.VisualStudioCode.Insiders --override '/SILENT /mergetasks="!runcode,addcontextmenufiles,addcontextmenufolders,associatewithfiles,addtopath"'
```

Post-Installation Steps
* Launch Visual Studio Code from the Start Menu or by typing code in a new terminal window.
* Sign in with your Microsoft or GitHub account to sync settings and extensions (optional).
* Install recommended extensions for your workflow (e.g., PowerShell, Python, GitLens, etc.).
* Configure settings by navigating to File > Preferences > Settings or pressing Ctrl+,.
* Update regularly to receive the latest features and security updates.

10. Install the Windows ADK and the WinPE Add-on

```powershell
# Windows ADK Setup
$Url = 'https://go.microsoft.com/fwlink/?linkid=2289980'
Invoke-Expression "& curl.exe --insecure --location --output `"$env:TEMP\adksetup.exe`" --url `"$Url`""
Start-Process -FilePath "$env:TEMP\adksetup.exe" -ArgumentList '/features', 'OptionId.DeploymentTools', 'OptionId.ImagingAndConfigurationDesigner', '/quiet', '/ceip', 'off', '/norestart' -Wait

# Windows PE add-on for the Windows ADK Setup
$Url = 'https://go.microsoft.com/fwlink/?linkid=2289981'
Invoke-Expression "& curl.exe --insecure --location --output `"$env:TEMP\adkwinpesetup.exe`" --url `"$Url`""
Start-Process -FilePath "$env:TEMP\adkwinpesetup.exe" -ArgumentList '/features', 'OptionId.WindowsPreinstallationEnvironment', '/quiet', '/ceip', 'off', '/norestart' -Wait

# MDT Windows PE x86 MMC snap-in error
$path = 'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs'
New-Item -Path $path -ItemType Directory -Force | Out-Null
```
12. Install MDT
```powershell
winget install --id Microsoft.DeploymentToolkit -e --accept-source-agreements --accept-package-agreements
```

13. Install the modules
```powershell
# The OSDWorkspace PowerShell Module
Install-Module -Name OSD.Workspace -SkipPublisherCheck

# Required for creating OSDWorkspace help files
Install-Module -Name platyPS -SkipPublisherCheck

# Used in some of the OSDWorkspace functions
Install-Module -Name OSD -SkipPublisherCheck

# Optionally used in some of the OSDWorkspace Gallery functions
Install-Module -Name OSDCloud -SkipPublisherCheck
```


## Create the OSD Workspace

Open an elevated PopwerShell 7 terminal, and run this command to create the workspace

```powershell
New-OSDWorkspace
```

This will create a new folder on yoru C drive called C:\OSDWorkspace
* Open this folder in VSCode and watch the lower right corner of VSCode.  It will ask you if you would like to open the worksapce associated with the folder.



## Next posts I plan to write in this series

- Building a reproducible `boot.wim` with OSDCloud v2.
- PXE/WDS integration and sample WDS configuration.
- Packaging drivers and third-party tools into the workspace.