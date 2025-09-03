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

Setting up OSDCloud v2 involves multiple components and dependencies that need to work together seamlessly. Rather than walking through each manual installation step, I've created a comprehensive automation script that handles the entire workspace setup process.

This is a complete, tested automation solution that takes you from a fresh Windows system to a fully functional OSD.Workspace development environment. A complete step-by-step guide can be found on the official Wiki - https://github.com/OSDeploy/OSD.Workspace/wiki

## What Exactly Is OSDCloud v2 (OSD.Workspace)?

**OSDCloud v2 (OSD.Workspace) is a community-driven PowerShell framework** that revolutionizes Windows deployment automation. Think of it as your complete deployment toolkit - from creating custom boot images to managing drivers, all through clean PowerShell workflows.

### Why OSDCloud v2 Matters:

- **Zero-Touch Deployments**: Fully automated Windows installations
- **Multiple Deployment Methods**: Works with USB, or WDS for PXE booting
- **PowerShell Native**: No proprietary tools or complex GUIs to learn  
- **Community Driven**: Active development with enterprise-grade features
- **Extensible**: Easy to customize for your specific requirements

> **Full Documentation**: Complete project documentation is available at the [OSD.Workspace Wiki](https://github.com/OSDeploy/OSD.Workspace/wiki). I'll reference specific pages throughout this guide.

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

**[Setup-OSDWorkspace.ps1](https://github.com/1eyeITguy/learningITtogether/blob/main/Scripts/OSDCloud/Setup-OSDWorksapce.ps1)**

### What the Script Does

The script handles these major installation phases:

1. **System Prerequisites Check** - Validates admin privileges and PowerShell version
2. **PowerShell Security Configuration** - Sets execution policies and TLS protocols
3. **Package Provider Setup** - Installs NuGet and configures PowerShell Gallery
4. **Core Module Updates** - PowerShellGet and PackageManagement
5. **Git Installation & Configuration** - Includes interactive user setup
6. **Visual Studio Code Insiders** - With proper context menu integration
7. **Windows ADK Installation** - Both main toolkit and WinPE add-on
8. **Compatibility Fixes** - MDT WinPE x86 directory creation
9. **OSD Module Installation** - All required OSDCloud PowerShell modules
10. **Post-Installation Guidance** - Clear next steps for workspace creation

### Key Script Features

- **Interactive Configuration**: Prompts for Git user details if not already configured
- **Smart Detection**: Skips components that are already installed
- **Error Handling**: Comprehensive error checking with clear status messages
- **Progress Tracking**: Visual progress indicators throughout installation
- **Validation**: Verifies each component before proceeding
- **MDT Support**: Includes commented MDT installation (uncomment if needed)

## Running the Setup Script

### Step 1: Prepare Your Environment

First, manually set the PowerShell execution policy (this can't be automated for security reasons):

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
```

### Step 2: Download and Execute

```powershell
# Download the script
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/1eyeITguy/learningITtogether/main/Scripts/OSDCloud/Setup-OSDWorksapce.ps1" -OutFile "$env:TEMP\Setup-OSDWorkspace.ps1"

# Run as Administrator
Start-Process PowerShell -Verb RunAs -ArgumentList "-File", "$env:TEMP\Setup-OSDWorkspace.ps1"
```

### Step 3: Follow Interactive Prompts

The script will ask for:
- **Git email address** (if not already configured)
- **Git user name** (if not already configured)
- **Confirmation to continue** 

## What Happens During Installation

Here's what you'll see during the automated setup:

```
+=======================================================================+
|              OSDCloud Workspace Prerequisites Installer               |
|                             Version 2.2                               |
+=======================================================================+

+- System Prerequisites Check ----------------------------------------

  [+] Running as Administrator - OK
  [+] Running PowerShell 7.4.0 - OK
  [i] Current execution policy (CurrentUser scope): RemoteSigned

+- PowerShell Security Configuration ---------------------------------

  [i] Current execution policy (CurrentUser scope): RemoteSigned
  [+] TLS 1.2 protocol configured successfully

+- NuGet Package Provider --------------------------------------------

  [+] NuGet package provider installed successfully

... (continues with each component)
```

## Enabling MDT Support (Optional)

If you need Microsoft Deployment Toolkit integration, simply uncomment this section in the script:

```powershell
# ===============================================================================
# MICROSOFT DEPLOYMENT TOOLKIT INSTALLATION
# ===============================================================================

Write-SectionHeader "Microsoft Deployment Toolkit (MDT)"

try {
    # Check if MDT is already installed
    $mdtPath = "${env:ProgramFiles}\Microsoft Deployment Toolkit"
    if (Test-Path $mdtPath) {
        Write-Status "Microsoft Deployment Toolkit already installed" "SKIP"
    } else {
        Write-Status "Installing Microsoft Deployment Toolkit..." "INFO"
        winget install --id Microsoft.DeploymentToolkit -e --accept-source-agreements --accept-package-agreements
        Write-Status "Microsoft Deployment Toolkit installed successfully" "SUCCESS"
    }
} catch {
    Write-Status "Failed to install Microsoft Deployment Toolkit: $($_.Exception.Message)" "ERROR"
    throw
}
```

## Post-Installation Steps

After the script completes successfully, you'll need to:

### 1. Restart Your Terminal Session
This is critical - many components require a fresh terminal session to work properly.

### 2. Create Your OSD Workspace
Open a new PowerShell 7 session and run:

```powershell
New-OSDWorkspace
```

This creates your workspace at `C:\OSDWorkspace`.

### 3. Open in Visual Studio Code
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

Here's the full automation script for reference:

```powershell
#Requires -RunAsAdministrator

<#
.SYNOPSIS
    OSDCloud Workspace Prerequisites Installation Script
    
.DESCRIPTION
    This script installs and configures all prerequisites for OSDCloud.Workspace development.
    Based on the official OSDeploy documentation: https://github.com/OSDeploy/OSD.Workspace/wiki
    
    IMPORTANT: Before running this script, you must set the execution policy manually:
    Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
    
    Components installed:
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
    Version: 2.2
    Requires: Administrator privileges
    
.LINK
    https://github.com/OSDeploy/OSD.Workspace/wiki
#>

# ===============================================================================
# SCRIPT INITIALIZATION
# ===============================================================================

Write-Host "+=======================================================================+" -ForegroundColor Cyan
Write-Host "|              OSDCloud Workspace Prerequisites Installer               |" -ForegroundColor Cyan
Write-Host "|                             Version 2.2                               |" -ForegroundColor Cyan
Write-Host "+=======================================================================+" -ForegroundColor Cyan
Write-Host ""

$ErrorActionPreference = 'Stop'
$ProgressPreference = 'SilentlyContinue'

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
    }
    
    $icon = switch ($Status) {
        "SUCCESS" { "[+]" }
        "WARNING" { "[!]" }
        "ERROR" { "[X]" }
        "INFO" { "[i]" }
        "SKIP" { "[-]" }
    }
    
    Write-Host "  $icon " -ForegroundColor $color -NoNewline
    Write-Host $Message -ForegroundColor $color
}

# ===============================================================================
# PREREQUISITE CHECKS
# ===============================================================================

Write-SectionHeader "System Prerequisites Check"

# Check if running as Administrator
$currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())
$isAdmin = $currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)

if (-not $isAdmin) {
    Write-Status "ERROR: This script must be run as Administrator!" "ERROR"
    Write-Status "Please run PowerShell as Administrator and try again." "ERROR"
    Write-Host ""
    Read-Host "Press Enter to exit"
    exit 1
} else {
    Write-Status "Running as Administrator - OK" "SUCCESS"
}

# Check PowerShell version
$psVersion = $PSVersionTable.PSVersion
if ($psVersion.Major -lt 7) {
    Write-Status "WARNING: Running PowerShell $($psVersion.ToString())" "WARNING"
    Write-Status "This script is optimized for PowerShell 7" "WARNING"
    Write-Host ""
    Write-Host "  To install PowerShell 7, run this command:" -ForegroundColor Yellow
    Write-Host "  winget install -e --id Microsoft.PowerShell --override '/passive ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL=1 ADD_FILE_CONTEXT_MENU_RUNPOWERSHELL=1 ENABLE_PSREMOTING=1 REGISTER_MANIFEST=1 USE_MU=1 ENABLE_MU=1 ADD_PATH=1'" -ForegroundColor Cyan
    Write-Host ""
    $continue = Read-Host "Do you want to continue with PowerShell $($psVersion.ToString())? (y/n)"
    if ($continue -ne 'y' -and $continue -ne 'yes') {
        Write-Status "Script execution cancelled by user" "INFO"
        exit 0
    }
} else {
    Write-Status "Running PowerShell $($psVersion.ToString()) - OK" "SUCCESS"
}

# Check execution policy
$currentPolicy = Get-ExecutionPolicy -Scope CurrentUser
Write-Status "Current execution policy (CurrentUser scope): $currentPolicy" "INFO"

# Collect Git user information upfront if Git is not installed or not configured
$gitUserEmail = $null
$gitUserName = $null

$gitInstalled = Get-Command git -ErrorAction SilentlyContinue
if ($gitInstalled) {
    $currentEmail = git config --global user.email 2>$null
    $currentName = git config --global user.name 2>$null
    
    # Only ask for email if it's not configured or has default values
    if ([string]::IsNullOrWhiteSpace($currentEmail) -or $currentEmail -eq "you@example.com") {
        Write-Host ""
        Write-Host "Git is installed but email is not configured." -ForegroundColor Yellow
        Write-Host "Please enter your Git email address: " -ForegroundColor Yellow -NoNewline
        $gitUserEmail = Read-Host
    } else {
        Write-Status "Git email already configured: $currentEmail" "SKIP"
    }
    
    # Only ask for name if it's not configured or has default values
    if ([string]::IsNullOrWhiteSpace($currentName) -or $currentName -eq "Your Name") {
        Write-Host "Please enter your Git user name: " -ForegroundColor Yellow -NoNewline
        $gitUserName = Read-Host
    } else {
        Write-Status "Git user name already configured: $currentName" "SKIP"
    }
} else {
    Write-Host ""
    Write-Host "Git is not installed. Please provide your Git configuration:" -ForegroundColor Yellow
    Write-Host "Enter your Git email address: " -ForegroundColor Yellow -NoNewline
    $gitUserEmail = Read-Host
    Write-Host "Enter your Git user name: " -ForegroundColor Yellow -NoNewline
    $gitUserName = Read-Host
}

# ===============================================================================
# POWERSHELL EXECUTION POLICY AND SECURITY SETUP
# ===============================================================================

Write-SectionHeader "PowerShell Security Configuration"

try {
    # Note: The execution policy should already be set by the user before running this script
    $currentPolicy = Get-ExecutionPolicy -Scope CurrentUser
    Write-Status "Current execution policy (CurrentUser scope): $currentPolicy" "INFO"
    
    # Check and set security protocol
    $currentProtocol = [Net.ServicePointManager]::SecurityProtocol
    if ($currentProtocol -notmatch 'Tls12') {
        Write-Status "Configuring TLS 1.2 security protocol..." "INFO"
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Write-Status "TLS 1.2 protocol configured successfully" "SUCCESS"
    } else {
        Write-Status "TLS 1.2 protocol already configured" "SKIP"
    }
} catch {
    Write-Status "Failed to configure PowerShell security: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# NUGET PACKAGE PROVIDER INSTALLATION
# ===============================================================================

Write-SectionHeader "NuGet Package Provider"

try {
    $nugetProvider = Get-PackageProvider -Name 'NuGet' -ErrorAction SilentlyContinue
    if (-not $nugetProvider) {
        Write-Status "Installing NuGet package provider..." "INFO"
        Install-PackageProvider -Name NuGet -Force -Verbose
        Write-Status "NuGet package provider installed successfully" "SUCCESS"
    } else {
        Write-Status "NuGet package provider already installed (Version: $($nugetProvider.Version))" "SKIP"
    }
} catch {
    Write-Status "Failed to install NuGet package provider: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# POWERSHELL GALLERY REPOSITORY CONFIGURATION
# ===============================================================================

Write-SectionHeader "PowerShell Gallery Repository"

try {
    $psGallery = Get-PSRepository -Name 'PSGallery' -ErrorAction SilentlyContinue
    if ($psGallery -and $psGallery.InstallationPolicy -ne 'Trusted') {
        Write-Status "Setting PowerShell Gallery as trusted repository..." "INFO"
        Set-PSRepository -Name 'PSGallery' -InstallationPolicy Trusted
        Write-Status "PowerShell Gallery (PSGallery) has been set to Trusted" "SUCCESS"
    } elseif ($psGallery) {
        Write-Status "PowerShell Gallery (PSGallery) is already Trusted" "SKIP"
    } else {
        Write-Status "PowerShell Gallery repository not found" "WARNING"
    }
} catch {
    Write-Status "Failed to configure PowerShell Gallery: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# POWERSHELLGET AND PACKAGEMANAGEMENT MODULES
# ===============================================================================

Write-SectionHeader "PowerShell Core Modules"

try {
    # Check and install PowerShellGet
    $psGetModule = Get-Module -Name PowerShellGet -ListAvailable | Sort-Object Version -Descending | Select-Object -First 1
    if (-not $psGetModule -or $psGetModule.Version -lt [Version]"2.2.5") {
        Write-Status "Installing/updating PowerShellGet module..." "INFO"
        Install-Module -Name PowerShellGet -Force -Scope AllUsers -AllowClobber -SkipPublisherCheck -Verbose
        Write-Status "PowerShellGet module installed successfully" "SUCCESS"
    } else {
        Write-Status "PowerShellGet module already up to date (Version: $($psGetModule.Version))" "SKIP"
    }
    
    # Check and install PackageManagement
    $pkgMgmtModule = Get-Module -Name PackageManagement -ListAvailable | Sort-Object Version -Descending | Select-Object -First 1
    if (-not $pkgMgmtModule -or $pkgMgmtModule.Version -lt [Version]"1.4.7") {
        Write-Status "Installing/updating PackageManagement module..." "INFO"
        Install-Module -Name PackageManagement -Force -Scope AllUsers -AllowClobber -SkipPublisherCheck -Verbose
        Write-Status "PackageManagement module installed successfully" "SUCCESS"
    } else {
        Write-Status "PackageManagement module already up to date (Version: $($pkgMgmtModule.Version))" "SKIP"
    }
} catch {
    Write-Status "Failed to install PowerShell core modules: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# GIT FOR WINDOWS INSTALLATION AND CONFIGURATION
# ===============================================================================

Write-SectionHeader "Git for Windows Installation and Configuration"

try {
    # Check if Git is already installed
    $gitInstalled = Get-Command git -ErrorAction SilentlyContinue
    if ($gitInstalled) {
        $gitVersion = git --version
        Write-Status "Git already installed: $gitVersion" "SKIP"
    } else {
        Write-Status "Installing Git for Windows..." "INFO"
        winget install --id Git.Git -e -h
        Write-Status "Git for Windows installed successfully" "SUCCESS"
        
        # Refresh environment variables
        $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
    }
    
    # Configure Git user identity using collected information
    if ($gitUserEmail) {
        Write-Status "Configuring Git user email: $gitUserEmail" "INFO"
        git config --global user.email "$gitUserEmail"
        Write-Status "Git user email configured successfully" "SUCCESS"
    } else {
        $currentEmail = git config --global user.email 2>$null
        if ($currentEmail) {
            Write-Status "Git user email already configured: $currentEmail" "SKIP"
        }
    }
    
    if ($gitUserName) {
        Write-Status "Configuring Git user name: $gitUserName" "INFO"
        git config --global user.name "$gitUserName"
        Write-Status "Git user name configured successfully" "SUCCESS"
    } else {
        $currentName = git config --global user.name 2>$null
        if ($currentName) {
            Write-Status "Git user name already configured: $currentName" "SKIP"
        }
    }
} catch {
    Write-Status "Failed to install/configure Git: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# VISUAL STUDIO CODE INSIDERS INSTALLATION
# ===============================================================================

Write-SectionHeader "Visual Studio Code Insiders Installation"

try {
    # Check if VS Code Insiders is already installed
    $vscodeInsiders = Get-Command "code-insiders" -ErrorAction SilentlyContinue
    if ($vscodeInsiders) {
        Write-Status "Visual Studio Code Insiders already installed" "SKIP"
    } else {
        Write-Status "Installing Visual Studio Code Insiders..." "INFO"
        $vscodeArgs = '/SILENT /mergetasks="!runcode,addcontextmenufiles,addcontextmenufolders,associatewithfiles,addtopath"'
        winget install -e --id Microsoft.VisualStudioCode.Insiders --override $vscodeArgs
        Write-Status "Visual Studio Code Insiders installed successfully" "SUCCESS"
    }
} catch {
    Write-Status "Failed to install Visual Studio Code Insiders: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# WINDOWS ADK INSTALLATION
# ===============================================================================

Write-SectionHeader "Windows Assessment and Deployment Kit (ADK)"

try {
    # Check if Windows ADK is already installed
    $adkPath = "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit"
    if (Test-Path $adkPath) {
        Write-Status "Windows ADK already installed" "SKIP"
    } else {
        Write-Status "Downloading and installing Windows ADK..." "INFO"
        $adkUrl = 'https://go.microsoft.com/fwlink/?linkid=2289980'
        $adkSetupPath = "$env:TEMP\adksetup.exe"
        
        # Download ADK setup
        Invoke-WebRequest -Uri $adkUrl -OutFile $adkSetupPath -UseBasicParsing
        
        # Install ADK with required features
        $adkArgs = @('/features', 'OptionId.DeploymentTools', 'OptionId.ImagingAndConfigurationDesigner', '/quiet', '/ceip', 'off', '/norestart')
        $adkProcess = Start-Process -FilePath $adkSetupPath -ArgumentList $adkArgs -Wait -PassThru
        
        if ($adkProcess.ExitCode -eq 0) {
            Write-Status "Windows ADK installed successfully" "SUCCESS"
        } else {
            Write-Status "Windows ADK installation completed with exit code: $($adkProcess.ExitCode)" "WARNING"
        }
        
        # Clean up
        Remove-Item $adkSetupPath -Force -ErrorAction SilentlyContinue
    }
} catch {
    Write-Status "Failed to install Windows ADK: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# WINDOWS PE ADD-ON INSTALLATION
# ===============================================================================

Write-SectionHeader "Windows PE Add-on for Windows ADK"

try {
    # Check if Windows PE add-on is already installed
    $winpePath = "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment"
    if (Test-Path $winpePath) {
        Write-Status "Windows PE add-on already installed" "SKIP"
    } else {
        Write-Status "Downloading and installing Windows PE add-on..." "INFO"
        $winpeUrl = 'https://go.microsoft.com/fwlink/?linkid=2289981'
        $winpeSetupPath = "$env:TEMP\adkwinpesetup.exe"
        
        # Download Windows PE add-on setup
        Invoke-WebRequest -Uri $winpeUrl -OutFile $winpeSetupPath -UseBasicParsing
        
        # Install Windows PE add-on
        $winpeArgs = @('/features', 'OptionId.WindowsPreinstallationEnvironment', '/quiet', '/ceip', 'off', '/norestart')
        $winpeProcess = Start-Process -FilePath $winpeSetupPath -ArgumentList $winpeArgs -Wait -PassThru
        
        if ($winpeProcess.ExitCode -eq 0) {
            Write-Status "Windows PE add-on installed successfully" "SUCCESS"
        } else {
            Write-Status "Windows PE add-on installation completed with exit code: $($winpeProcess.ExitCode)" "WARNING"
        }
        
        # Clean up
        Remove-Item $winpeSetupPath -Force -ErrorAction SilentlyContinue
    }
} catch {
    Write-Status "Failed to install Windows PE add-on: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# MDT WINPE X86 BUGFIX
# ===============================================================================

Write-SectionHeader "MDT WinPE x86 Bugfix"

try {
    $winpeOCsPath = 'C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs'
    if (Test-Path $winpeOCsPath) {
        Write-Status "WinPE x86 OCs directory already exists" "SKIP"
    } else {
        Write-Status "Creating WinPE x86 OCs directory for MDT compatibility..." "INFO"
        New-Item -Path $winpeOCsPath -ItemType Directory -Force | Out-Null
        Write-Status "WinPE x86 OCs directory created successfully" "SUCCESS"
    }
} catch {
    Write-Status "Failed to create WinPE x86 OCs directory: $($_.Exception.Message)" "ERROR"
    throw
}

# ===============================================================================
# MICROSOFT DEPLOYMENT TOOLKIT INSTALLATION
# ===============================================================================

<##
Write-SectionHeader "Microsoft Deployment Toolkit (MDT)"

try {
    # Check if MDT is already installed
    $mdtPath = "${env:ProgramFiles}\Microsoft Deployment Toolkit"
    if (Test-Path $mdtPath) {
        Write-Status "Microsoft Deployment Toolkit already installed" "SKIP"
    } else {
        Write-Status "Installing Microsoft Deployment Toolkit..." "INFO"
        winget install --id Microsoft.DeploymentToolkit -e --accept-source-agreements --accept-package-agreements
        Write-Status "Microsoft Deployment Toolkit installed successfully" "SUCCESS"
    }
} catch {
    Write-Status "Failed to install Microsoft Deployment Toolkit: $($_.Exception.Message)" "ERROR"
    throw
}
#>

# ===============================================================================
# OSD POWERSHELL MODULES INSTALLATION
# ===============================================================================

Write-SectionHeader "OSD PowerShell Modules"

$modules = @(
    @{ Name = "OSD.Workspace"; Description = "The main OSDWorkspace PowerShell Module" },
    @{ Name = "platyPS"; Description = "Required for creating OSDWorkspace help files" },
    @{ Name = "OSD"; Description = "Used in some of the OSDWorkspace functions" },
    @{ Name = "OSDCloud"; Description = "Optionally used in some of the OSDWorkspace Gallery functions" }
)

foreach ($module in $modules) {
    try {
        # Check if module is already installed
        $installedModule = Get-Module -Name $module.Name -ListAvailable -ErrorAction SilentlyContinue
        if ($installedModule) {
            $latestVersion = $installedModule | Sort-Object Version -Descending | Select-Object -First 1
            Write-Status "$($module.Name) already installed (Version: $($latestVersion.Version))" "SKIP"
        } else {
            Write-Status "Installing $($module.Name) module - $($module.Description)..." "INFO"
            Install-Module -Name $module.Name -SkipPublisherCheck -Force -AllowClobber
            Write-Status "$($module.Name) module installed successfully" "SUCCESS"
        }
    } catch {
        Write-Status "Failed to install $($module.Name) module: $($_.Exception.Message)" "ERROR"
        throw
    }
}

# ===============================================================================
# SCRIPT COMPLETION
# ===============================================================================

Write-Host ""
Write-Host "+=======================================================================+" -ForegroundColor Green
Write-Host "|                    INSTALLATION COMPLETED SUCCESSFULLY!               |" -ForegroundColor Green
Write-Host "+=======================================================================+" -ForegroundColor Green
Write-Host ""

Write-Status "All OSDCloud Workspace prerequisites have been installed successfully!" "SUCCESS"
Write-Status "TERMINAL RESTART REQUIRED: You must restart your terminal session for all changes to take effect." "WARNING"
Write-Host ""

Write-Host "NEXT STEPS:" -ForegroundColor Cyan
Write-Host "----------" -ForegroundColor Cyan
Write-Host ""
Write-Host "1. RESTART YOUR TERMINAL SESSION" -ForegroundColor Yellow
Write-Host "   This is required to complete the installation of all components." -ForegroundColor White
Write-Host ""
Write-Host "2. After restart, open PowerShell 7 and run:" -ForegroundColor Yellow
Write-Host "   New-OSDWorkspace" -ForegroundColor Cyan
Write-Host "   This will create your OSD workspace directory." -ForegroundColor White
Write-Host ""
Write-Host "3. Open the workspace in VS Code Insiders:" -ForegroundColor Yellow
Write-Host "   cd C:\OSDWorkspace" -ForegroundColor Cyan
Write-Host "   code-
```