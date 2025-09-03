---
title: "Automate PowerShell Module Updates: Never Manually Update Again"
date: 2025-05-1 19:10:04 -0600
categories: [PowerShell, Automation]
tags: [powershell, automation, modules, scripting, maintenance]
image:
 path: /assets/img/headers/update-modules.webp
 alt: PowerShell automation for module updates
---

# Stop the Manual PowerShell Module Update Madness

**How many times have you opened PowerShell, run a command, and been greeted with that familiar yellow warning about outdated modules?** If you're like most IT professionals, you've probably ignored it more often than you'd care to admit. Today, we're fixing that with a comprehensive automation solution.

## The Problem We're Solving

Managing PowerShell modules manually is a pain. Here's what typically happens:

- ⚠️ **Outdated modules** cause compatibility issues
- 🐌 **Manual updates** are time-consuming and error-prone  
- 🗄️ **Multiple versions** clutter your system
- 😤 **Inconsistent environments** across your team

**Sound familiar?** Let's automate this away forever.

## The Solution: Smart Module Management

I've created a PowerShell function that handles all of this automatically. It's not just another update script – it's a complete module lifecycle manager that:

- ✅ Updates all modules to the latest versions
- 🧹 Cleans up old versions automatically
- 🚀 Supports both stable and pre-release versions
- 🛡️ Includes proper error handling and logging
- 👨‍💼 Requires admin privileges (as it should)

## Quick Start Examples

### Basic Usage (Recommended)
```powershell
.\Update-ModuleVersions.ps1
```
*Updates all modules to latest stable versions*

### Power User Mode
```powershell
.\Update-ModuleVersions.ps1 -AllowPrerelease -ShowErrors
```
*Gets bleeding-edge versions with detailed error reporting*

## How It Works

The script follows a methodical approach:

1. **Security Check** - Verifies admin privileges (required for system-wide updates)
2. **Discovery** - Catalogs all currently installed modules
3. **Update Loop** - Processes each module with progress tracking
4. **Cleanup** - Removes outdated versions to prevent conflicts
5. **Reporting** - Shows exactly what was updated

## The Complete Script

Here's the full script that handles all the heavy lifting:

```powershell
<#
.SYNOPSIS
Update installed modules to the latest production or pre-release version.

.DESCRIPTION
This function updates all installed modules to the latest production or pre-release version (based on the AllowPrerelease switch).

.PARAMETER AllowPrerelease
If specified, updates to the latest pre-release versions; otherwise, updates to the latest production versions.

.PARAMETER ShowErrors
If specified, shows specific PowerShell error messages when an error occurs during the module update process.

.EXAMPLE
Update-ModuleVersions -AllowPrerelease -ShowErrors
# Updates all installed modules to the latest pre-release versions and shows specific error messages when an error occurs.

.EXAMPLE
Update-ModuleVersions
# Updates all installed modules to the latest production versions without showing specific error messages.
#>

function Update-ModuleVersions {
    [CmdletBinding()]
    param (
        [switch]$AllowPrerelease,
        [switch]$ShowErrors
    )

    # Check admin privileges
    if (-not ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]"Administrator")) {
        Throw "This function requires administrator privileges."
    }

    # Get all installed modules
    Write-Host "Retrieving all installed modules ..." -ForegroundColor Green
    $CurrentModules = Get-InstalledModule | Select-Object Name, Version | Sort-Object Name

    if (-not $CurrentModules) {
        Write-Host "No modules found." -ForegroundColor Gray
        return
    }
    else {
        $ModulesCount = $CurrentModules.Count
        Write-Host "$ModulesCount modules found." -ForegroundColor Gray
    }

    # Show status of AllowPrerelease Switch
    if ($AllowPrerelease) {
        Write-Host "Updating installed modules to the latest PreRelease version ..." -ForegroundColor Green
    }
    else {
        Write-Host "Updating installed modules to the latest Production version ..." -ForegroundColor Green
    }

    # Loop through the installed modules and update them if a newer version is available
    $i = 0
    foreach ($Module in $CurrentModules) {
        $i++
        $Counter = "[{0,-$($ModulesCount.ToString().Length)}/{1,-$($ModulesCount.ToString().Length)}]" -f $i, $ModulesCount
        Write-Host ("{0} Checking for updated version of module {1} ..." -f $Counter, $Module.Name) -ForegroundColor Green
        try {
            Update-Module -Name $Module.Name -AllowPrerelease:$AllowPrerelease -AcceptLicense -Scope:AllUsers -Force -ErrorAction Stop
        }
        catch {
            $errorMessage = "Error updating module {0}!" -f $Module.Name
            if ($ShowErrors) {
                $errorMessage += " Error message: {0}" -f $_.Exception.Message
            }
            Write-Host $errorMessage -ForegroundColor Red
        }

        # Retrieve newest version number and remove old(er) version(s) if any
        $AllVersions = Get-InstalledModule -Name $Module.Name -AllVersions | Sort-Object PublishedDate -Descending
        $MostRecentVersion = $AllVersions[0].Version
        if ($AllVersions.Count -gt 1) {
            Foreach ($Version in $AllVersions) {
                if ($Version.Version -ne $MostRecentVersion) {
                    try {
                        Write-Host ("{0} Uninstalling previous version {1} of module {2} ..." -f (' ' * $Counter.Length), $Version.Version, $Module.Name) -ForegroundColor Gray
                        Uninstall-Module -Name $Module.Name -RequiredVersion $Version.Version -Force -ErrorAction Stop
                    }
                    catch {
                        $errorMessage = "Error uninstalling previous version {0} of module {1}!" -f $Version.Version, $Module.Name
                        if ($ShowErrors) {
                            $errorMessage += " Error message: {0}" -f $_.Exception.Message
                        }
                        Write-Warning $errorMessage
                    }
                }
            }
        }
    }

    # Get the new module versions for comparing them to the previous one if updated
    $NewModules = Get-InstalledModule | Select-Object Name, Version | Sort-Object Name
    if ($NewModules) {
        Write-Host "`nList of updated modules:" -ForegroundColor Green
        $NoUpdatesFound = $true
        foreach ($Module in $NewModules) {
            $CurrentVersion = $CurrentModules | Where-Object Name -EQ $Module.Name
            if ($CurrentVersion.Version -notlike $Module.Version) {
                $NoUpdatesFound = $false
                Write-Host ("- Updated module {0} from version {1} to {2}" -f $Module.Name, $CurrentVersion.Version, $Module.Version) -ForegroundColor Green
            }
        }

        if ($NoUpdatesFound) {
            Write-Host "No modules were updated." -ForegroundColor Gray
        }
    }
}

# Execute the function
Update-ModuleVersions
```

## Run This Monthly
Add this to your monthly maintenance routine. Consider creating a scheduled task:

```powershell
# Create a scheduled task to run monthly
Register-ScheduledTask -TaskName "PowerShell Module Updates" -Trigger (New-ScheduledTaskTrigger -Monthly -At "2:00 AM") -Action (New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-File C:\Scripts\Update-ModuleVersions.ps1")
```

## Test Before Production
Always test module updates in a development environment first, especially when using `-AllowPrerelease`.

## Document Your Dependencies
Keep track of which modules your scripts depend on. Consider using a `requirements.psd1` file for your projects.