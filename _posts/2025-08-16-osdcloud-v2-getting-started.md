---
title: "OSDCloud v2 Setup: From Zero to Deployment Ready in One Script"
date: 2025-08-16 00:00:00 -0600
categories: [OSDCloud, Windows Deployment, Automation]
tags: [OSDCloud, OSD.Workspace, powershell, windows-deployment, automation, winpe, scripting]
image:
  path: /assets/img/headers/OSDCloud_Part1.webp
  alt: OSDCloud v2 automated workspace setup and configuration
---

# OSDCloud Setup: One Script Does It All

Setting up OSDCloud v2 involves multiple components and dependencies that need to work together. Rather than walking through each manual installation step, I've created an automation script that handles the entire workspace setup process.

This is an automation solution that takes you from a fresh Windows system to a fully functional OSD.Workspace development environment. A complete step-by-step guide can be found on the official Wiki - [https://github.com/OSDeploy/OSD.Workspace/wiki](https://github.com/OSDeploy/OSD.Workspace/wiki)

## What Exactly Is OSDCloud v2 (OSD.Workspace)?

**OSDCloud v2 (OSD.Workspace) is a community-driven PowerShell framework** that revolutionizes Windows deployment automation. Think of it as your complete deployment toolkit - from creating custom boot images to managing drivers, all through clean PowerShell workflows.

## Why Automate the Setup Process?

Manual OSDCloud setup involves coordinating multiple components and dependencies across different installation methods. While the manual process is well-documented on the official wiki, automation offers several advantages:

- **Consistency**: Every installation follows the same steps and configurations
- **Time Savings**: Complete setup in minutes rather than following multiple manual steps
- **Error Prevention**: Automated dependency checking and version compatibility
- **Reproducibility**: Easy to replicate the same environment across team members
- **Smart Detection**: Skips components that are already installed correctly

The automated approach doesn't replace the manual method - it simply streamlines it for faster, more reliable deployments.

## The Complete Prerequisites List

Before running any automation, here's what we need to install and configure:

### Core Requirements:
- **Windows 11 24H2** (your deployment workstation)
- **PowerShell 7** with proper execution policies
- **Git for Windows** with user configuration
- **Visual Studio Code Insiders** (required for workspace management)
- **Windows ADK + WinPE Add-on** (Microsoft's deployment tools)
- **Administrator privileges** throughout the process

### PowerShell Modules:
- **OSD.Workspace** - The main OSDCloud framework
- **platyPS** - Documentation generation
- **OSD** - Core OSD functions  
- **OSDCloud** - Gallery and cloud functions

### Optional Components:
- **Microsoft Deployment Toolkit (MDT)** - If intergrating DART

> **Reference**: The OSD.Workspace wiki maintains a [Recommended Client Configuration](https://github.com/OSDeploy/OSD.Workspace/wiki#recommended-client-configuration) with the official requirements.

## The Automated Solution

Rather than walking you through manual steps, I've created a PowerShell script that handles everything automatically. This script is based on the official documentation 

### Download the Complete Setup Script

You can download the full automation script from my GitHub repository:

**[Setup-OSDWorkspace.ps1](https://github.com/1eyeITguy/learningITtogether/blob/main/Scripts/OSDCloud/Setup-OSDWorkspace.ps1)**

### What the Script Does

The script operates in two phases for maximum transparency and control:

#### Phase 1: System Analysis
1. **Comprehensive Prerequisite Scan** - Analyzes your entire system to identify what's already installed
2. **Detailed Status Report** - Shows exactly what's missing, what needs configuration, and what's already complete
3. **Interactive Summary** - Presents a clear breakdown before making any changes
4. **User Confirmation** - Allows you to review and approve the installation plan

#### Phase 2: Automated Installation
1. **System Prerequisites Check** - Validates admin privileges and PowerShell version
2. **PowerShell Security Configuration** - Sets execution policies and TLS protocols
3. **Package Provider Setup** - Installs NuGet and configures PowerShell Gallery
4. **Core Module Updates** - PowerShellGet and PackageManagement
5. **Git Installation & Configuration** - Includes interactive user setup
6. **Visual Studio Code Insiders** - With proper context menu integration
7. **Windows ADK Installation** - Both main toolkit and WinPE add-on
8. **Compatibility Fixes** - MDT WinPE x86 directory creation
9. **OSD Module Installation** - All required OSDCloud PowerShell modules
10. **Post-Installation Verification** - Confirms all components installed correctly

### Key Script Features

- **Two-Phase Approach**: First scans your system, then shows you exactly what will be installed before making changes
- **Comprehensive Analysis**: Detailed prerequisite scanning with clear status reporting
- **Interactive Confirmation**: Review the installation plan and choose optional components
- **Automatic PowerShell 7 Installation**: Installs PowerShell 7 automatically if not present
- **Intelligent Module Management**: Installs modules to PowerShell 7 specific location for better compatibility
- **Interactive Configuration**: Prompts for Git user details if not already configured
- **Smart Detection**: Skips components that are already installed
- **Error Handling**: Comprehensive error checking with clear status messages
- **Progress Tracking**: Visual progress indicators throughout installation
- **Post-Installation Verification**: Confirms all components are working correctly
- **MDT Support**: Includes optional Microsoft Deployment Toolkit installation

## Running the Setup Script

### Step 1: Prepare Your Environment

First, manually set the PowerShell execution policy (this can't be automated for security reasons):

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
```

### Step 2: Download and Execute

```powershell
# Download the script
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/1eyeITguy/learningITtogether/main/Scripts/OSDCloud/Setup-OSDWorkspace.ps1" -OutFile "$env:TEMP\Setup-OSDWorkspace.ps1"

# Run as Administrator
Start-Process PowerShell -Verb RunAs -ArgumentList "-File", "$env:TEMP\Setup-OSDWorkspace.ps1"
```

### Step 3: Follow Interactive Prompts

The script will ask for:
- **Git email address** (if not already configured)
- **Git user name** (if not already configured)

**Note**: If PowerShell 7 is not installed, the script will automatically install it with full configuration before proceeding with the rest of the setup. 

## What Happens During Installation

Here's what you'll see during the automated setup process:

### Phase 1: System Analysis
```
+=======================================================================+
|                 OSD.Workspace Prerequisites Installer               |
|                             Version 3.0                             |
+=======================================================================+

+- Scanning System Prerequisites ------------------------------------

[i] Scanning system for OSDCloud Workspace prerequisites...

  Checking Administrator...
  Checking PowerShell7...
  Checking ExecutionPolicy...
  Checking TLS12...
  Checking NuGet...
  Checking PSGallery...
  Checking PowerShellGet...
  Checking PackageManagement...
  Checking Git...
  Checking VSCodeInsiders...
  Checking WindowsADK...
  Checking WindowsPE...
  Checking MDT...
  Checking WinPEx86Fix...
  Checking OSDModules...

+=======================================================================+
|                       PREREQUISITE SCAN RESULTS                     |
+=======================================================================+

ALREADY INSTALLED:
-----------------
  [OK] Administrator - Running as Administrator
  [OK] PowerShell7 - Installed version: 7.4.0 at C:\Program Files\PowerShell\7

NEEDS INSTALLATION:
------------------
  [--] Git - Not installed
       Action: Install Git for Windows
  [--] VSCodeInsiders - Not installed
       Action: Install Visual Studio Code Insiders

OPTIONAL COMPONENTS:
-------------------
  [i] MDT - Not installed - Optional for DaRT capabilities
      Action: Install Microsoft Deployment Toolkit (Optional)

SUMMARY:
-------
[+] Prerequisites already satisfied: 8
[!] Prerequisites needing configuration: 0
[X] Prerequisites needing installation: 6
[i] Optional components available: 1
[i] Total required actions: 6
```

### Phase 2: Installation Process
After you confirm the installation, you'll see detailed progress for each component:

```
+=======================================================================+
|                        INSTALLATION CONFIRMATION                      |
+=======================================================================+

This script will now install and configure the missing prerequisites.
The installation may take several minutes depending on your internet connection.

Do you want to continue with the installation? (Y/N): Y

+=======================================================================+
|                         STARTING INSTALLATION                         |
+=======================================================================+

[1/6] Processing: Git
+- Installing and Configuring Git ----------------------------------
  [i] Installing Git for Windows...
  [+] Git for Windows installed successfully
  [+] Git user email configured successfully
  [+] Git user name configured successfully
  [+] Git processed successfully

[2/6] Processing: VSCodeInsiders
+- Installing Visual Studio Code Insiders --------------------------
  [i] Installing Visual Studio Code Insiders...
  [+] Visual Studio Code Insiders installed successfully
  [+] VSCodeInsiders processed successfully

... (continues with each component)

+=======================================================================+
|                    INSTALLATION COMPLETED SUCCESSFULLY!               |
+=======================================================================+

VERIFICATION RESULTS:
--------------------
[+] Administrator - VERIFIED
[+] PowerShell7 - VERIFIED
[+] ExecutionPolicy - VERIFIED
[+] TLS12 - VERIFIED
[+] NuGet - VERIFIED
[+] PSGallery - VERIFIED
[+] PowerShellGet - VERIFIED
[+] PackageManagement - VERIFIED
[+] Git - VERIFIED
[+] VSCodeInsiders - VERIFIED
[+] WindowsADK - VERIFIED
[+] WindowsPE - VERIFIED
[+] OSDModules - VERIFIED

[i] Verification Summary: 13/13 prerequisites successfully installed
[+] All prerequisites verified successfully!
```

## Enabling MDT Support (Optional)

The script now includes **interactive prompts** for optional components like Microsoft Deployment Toolkit (MDT). During the installation process, you'll be asked:

```
OPTIONAL: Microsoft Deployment Toolkit (MDT)
MDT provides advanced deployment capabilities including DaRT (Diagnostics and Recovery Toolset).
Note: DaRT capabilities require Software Assurance (SA) licensing to utilize.

Would you like to install MDT for adding DaRT capabilities to your deployment? (Y/N):
```

Simply respond **Y** if you want MDT installed, or **N** to skip it. This is much more user-friendly than the previous version that required manual script editing.

## Post-Installation Steps

After the script completes successfully, you'll need to:

### 1. Open PowerShell 7
Open a new PowerShell 7 session and run:

```powershell
Install-OSDWorkspace
```

This creates your workspace at `C:\OSDWorkspace`.

### 2. Open in Visual Studio Code
```powershell
cd C:\OSDWorkspace
code-insiders .
```

When VSCode opens, accept the workspace prompt in the lower-right corner.

## What's Coming Next

This automated setup is just the foundation for your OSDCloud journey. In upcoming posts, I'll cover:

- **Building Custom Boot Images**: Creating reproducible `boot.wim` files with your drivers and tools
- **PXE/WDS Integration**: Complete network deployment setup with sample configurations  
- **Driver Management**: Automated driver packaging and workspace integration
- **Advanced Automation**: Real-world deployment scenarios and troubleshooting

## The Complete Setup Script

Here's the full automation script for reference. The latest version is always available at the GitHub link above.

```powershell
#Requires -RunAsAdministrator

<#
.SYNOPSIS
    OSD.Workspace Prerequisites Installation Script
    
.DESCRIPTION
    This script checks and installs all prerequisites for OSD.Workspace development.
    Based on the official OSDeploy documentation: https://github.com/OSDeploy/OSD.Workspace/wiki
    
    The script performs a comprehensive scan first, then presents a summary of what
    is already installed and what needs to be installed before proceeding.
    
    IMPORTANT: Before running this script, you must set the execution policy manually:
    Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
    
    Components checked and installed:
    - PowerShell execution policy and security protocols
    - NuGet package provider
    - Trusted PowerShell Gallery repository
    - PowerShellGet and PackageManagement modules
    - PowerShell 7 with full configuration
    - Git for Windows with user configuration
    - Visual Studio Code Insiders
    - Windows ADK and Windows PE add-on
    - Microsoft Deployment Toolkit
    - OSD PowerShell modules (OSD.Workspace, platyPS, OSD, OSDCloud)
    
.NOTES
    Author: Matthew Miles
    Version: 3.0
    Requires: Administrator privileges
    
.LINK
    https://github.com/OSDeploy/OSD.Workspace/wiki
#>

# ===============================================================================
# SCRIPT INITIALIZATION
# ===============================================================================

Write-Host ""
Write-Host ""
Write-Host "+=======================================================================+" -ForegroundColor Cyan
Write-Host "|                 OSD.Workspace Prerequisites Installer                 |" -ForegroundColor Cyan
Write-Host "|                             Version 3.0                               |" -ForegroundColor Cyan
Write-Host "+=======================================================================+" -ForegroundColor Cyan
Write-Host ""

$ErrorActionPreference = 'Stop'
$ProgressPreference = 'SilentlyContinue'

# Global variables for tracking (these will be populated during execution)
$AllPrerequisites = @()
$PrerequisiteNames = @()

# Function to write section headers
function Write-SectionHeader {
    param([string]$Title)
    Write-Host ""
    Write-Host "+- $Title " -ForegroundColor Yellow -NoNewline
    Write-Host ("-" * (70 - $Title.Length)) -ForegroundColor Yellow
    Write-Host ""
}

# Function to write status messages
function Write-Status {
    param(
        [string]$Message,
        [string]$Status = "INFO"
    )
    
    $color = switch ($Status) {
        "SUCCESS" { "Green" }
        "WARNING" { "Yellow" }
        "ERROR" { "Red" }
        "INFO" { "White" }
        "SKIP" { "Gray" }
        "INSTALLED" { "Green" }
        "NOT_INSTALLED" { "Red" }
        "NEEDS_CONFIG" { "Yellow" }
    }
    
    $icon = switch ($Status) {
        "SUCCESS" { "[+]" }
        "WARNING" { "[!]" }
        "ERROR" { "[X]" }
        "INFO" { "[i]" }
        "SKIP" { "[-]" }
        "INSTALLED" { "[OK]" }
        "NOT_INSTALLED" { "[--]" }
        "NEEDS_CONFIG" { "[!]" }
    }
    
    Write-Host "  $icon " -ForegroundColor $color -NoNewline
    Write-Host $Message -ForegroundColor $color
}

# Function to check if a command exists
function Test-CommandExists {
    param([string]$Command)
    
    $cmd = Get-Command $Command -ErrorAction SilentlyContinue
    return $null -ne $cmd
}

# Function to get detailed prerequisite status
function Get-PrerequisiteStatus {
    param([string]$Name)
    
    $status = @{
        Name = $Name
        IsInstalled = $false
        NeedsConfiguration = $false
        CurrentVersion = $null
        Details = ""
        Action = "None"
        Priority = 0
        IsOptional = $false
    }
    
    switch ($Name) {
        "Administrator" {
            $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())
            $status.IsInstalled = $currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
            $status.Details = if ($status.IsInstalled) { "Running as Administrator" } else { "Must run as Administrator" }
            $status.Priority = 1
        }
        
        "PowerShell7" {
            # Check if PowerShell 7 is actually installed on the system (not just the current session)
            $ps7InstallPath = "${env:ProgramFiles}\PowerShell\7"
            $ps7Executable = Join-Path $ps7InstallPath "pwsh.exe"
            $status.IsInstalled = (Test-Path $ps7InstallPath) -and (Test-Path $ps7Executable)
            
            if ($status.IsInstalled) {
                # Get the installed version if PowerShell 7 is installed
                try {
                    $ps7Version = & $ps7Executable --version 2>$null
                    if ($ps7Version) {
                        $status.CurrentVersion = $ps7Version.Replace("PowerShell ", "")
                        $status.Details = "Installed version: $($status.CurrentVersion) at $ps7InstallPath"
                    } else {
                        $status.CurrentVersion = "Unknown"
                        $status.Details = "Installed at: $ps7InstallPath"
                    }
                } catch {
                    $status.CurrentVersion = "Unknown"
                    $status.Details = "Installed at: $ps7InstallPath"
                }
            } else {
                $currentVersion = $PSVersionTable.PSVersion.ToString()
                $status.Details = "Not installed - Currently running PowerShell $currentVersion"
                $status.Action = "Install PowerShell 7"
                $status.Priority = 2
            }
        }
        
        # ... (Additional prerequisite checks truncated for brevity)
    }
    
    return $status
}

# ===============================================================================
# PREREQUISITE SCANNING PHASE
# ===============================================================================

Write-SectionHeader "Scanning System Prerequisites"

# Check administrator privileges first
$adminStatus = Get-PrerequisiteStatus "Administrator"
if (-not $adminStatus.IsInstalled) {
    Write-Status "ERROR: This script must be run as Administrator!" "ERROR"
    Write-Status "Please run PowerShell as Administrator and try again." "ERROR"
    Write-Host ""
    Read-Host "Press Enter to exit"
    exit 1
}

Write-Status "Scanning system for OSDCloud Workspace prerequisites..." "INFO"
Write-Host ""

# Define all prerequisites to check
$PrerequisiteNames = @(
    "Administrator",
    "PowerShell7", 
    "ExecutionPolicy",
    "TLS12",
    "NuGet",
    "PSGallery", 
    "PowerShellGet",
    "PackageManagement",
    "Git",
    "VSCodeInsiders",
    "WindowsADK",
    "WindowsPE",
    "MDT",
    "WinPEx86Fix",
    "OSDModules"
)

# Scan all prerequisites
$AllPrerequisites = @()
foreach ($prereqName in $PrerequisiteNames) {
    Write-Host "  Checking $prereqName..." -ForegroundColor Gray
    $status = Get-PrerequisiteStatus $prereqName
    $AllPrerequisites += $status
}

# ... (Rest of the script continues with installation logic)

# Note: The complete script is available at the GitHub repository link
# This is a simplified version showing the key structure and improvements
```

> **Note**: Due to the script's length (over 1000 lines), I'm showing the key structural improvements here. The complete, fully functional script is available at the GitHub repository link above.