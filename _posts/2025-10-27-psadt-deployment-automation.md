---
title: "PSADT Deployment Creator: Automate Your Application Packaging Workflow"
date: 2025-10-27 00:00:00 -0600
categories: [PowerShell, Automation, Application Deployment]
tags: [powershell, psadt, automation, application-packaging, intune, deployment, scripting]
image:
  path: /assets/img/headers/psadt-header-1200x630.png
  alt: PowerShell App Deployment Toolkit automation script
---

# Stop Building PSADT Packages Manually

**How many times have you created a new PSADT deployment package?** If you're an application packager or deployment engineer, you know the routine: download the toolkit, create the folder structure, update the deployment script parameters, modify the config file, replace default assets... rinse and repeat for every single application.

Today, we're automating that entire workflow into a single script that takes you from zero to a fully configured PSADT deployment in minutes.

## What Is PSADT (PowerShell App Deployment Toolkit)?

**PSAppDeployToolkit (PSADT) is the de facto standard framework for deploying Windows software at scale.** It's a comprehensive PowerShell-based toolkit that wraps around your application installers (MSI, EXE, etc.) to provide:

### Core Capabilities:
- ✅ **Standardized deployment workflow** - Consistent install/uninstall/repair operations
- 🎨 **Professional user experience** - Branded dialogs and progress indicators  
- 🔄 **Application handling** - Close running apps, prevent reopening during install
- 📝 **Automated logging** - Detailed logs for troubleshooting and compliance
- 🌍 **Localization support** - Multi-language UI components
- 🏢 **Enterprise-ready** - Used by Fortune 500 companies and government agencies
- 🔌 **3rd-party integration** - Works with SCCM, Intune, and other deployment tools

**PSADT v4** brings modern capabilities including PowerShell 7 support, ARM platform compatibility, and improved module architecture. Organizations worldwide use PSADT to deploy applications on millions of Windows endpoints every day.

> **Learn More**: Visit the official [PSAppDeployToolkit website](https://psappdeploytoolkit.com/) for comprehensive documentation and resources.

## The Manual PSADT Setup Problem

Creating a PSADT deployment package manually involves numerous repetitive steps:

1. **Install/Update the PSAppDeployToolkit module** from PowerShell Gallery
2. **Run New-ADTTemplate** to create the folder structure
3. **Navigate to the deployment folder** and open the deployment script
4. **Update application details** (vendor, name, version) in multiple locations
5. **Modify the config.psd1 file** with company name and log paths
6. **Replace default asset files** (AppIcon.png, Banner.Classic.png) with custom branding
7. **Update the completion message** to show proper application details
8. **Set the default architecture** (x86, x64) if needed
9. **Add your installation files** to the Files directory

**For every single application you package.** That's a lot of repetitive manual work that's prone to inconsistencies and typos.

## The Automated Solution

I've created a comprehensive PowerShell script that automates the entire PSADT deployment creation workflow. This script handles everything from module management to final configuration in a single, streamlined process.

### Download the Complete Script

You can download the automation script from my GitHub repository:

**[New-PSADTDeployment.ps1](https://github.com/1eyeITguy/learningITtogether/blob/main/Scripts/PSADT/New-PSADTDeployment.ps1)**

### What the Script Does

The script provides a complete end-to-end automation solution:

#### 1. Module Management
- **Checks for PSAppDeployToolkit module** installation
- **Compares installed version** with latest available
- **Automatically updates** to the newest version if outdated
- **Imports the module** and displays version information

#### 2. Interactive Configuration
- **Prompts for application details** (vendor, name, version)
- **Validates all required inputs** to prevent empty values
- **Suggests default locations** with option for custom paths
- **Creates the deployment folder structure** using New-ADTTemplate

#### 3. Asset Customization
- **Downloads custom AppIcon.png** if URL configured
- **Downloads custom Banner.Classic.png** if URL configured
- **Replaces default template assets** automatically
- **Provides clear status** for each asset operation

#### 4. Script Configuration
- **Updates deployment script parameters** (vendor, name, version)
- **Sets script author** and date automatically
- **Configures default architecture** (x86/x64) if specified
- **Customizes completion message** with application details

#### 5. Configuration File Updates
- **Modifies config.psd1** with company name
- **Sets custom log paths** (e.g., Intune log location)
- **Preserves template defaults** for unconfigured values

#### 6. Post-Creation Actions
- **Opens deployment folder** in Windows Explorer
- **Launches VS Code** for immediate editing
- **Displays comprehensive summary** of all configurations

## Key Script Features

### User-Configurable Settings
At the top of the script, you'll find optional configuration variables:

```powershell
# URLs for asset downloads (leave empty for template defaults)
$AppIconUrl = ""
$BannerClassicUrl = ""

# Company name to write into config.psd1
$CompanyName = ""

# Script author name (leave empty for template default)
$Author = ""

# Log path for Intune (leave empty for template default)
$LogPath = "C:\ProgramData\Microsoft\IntuneManagementExtension\Logs"

# Default application architecture (x86, x64, or leave empty)
$DefaultAppArch = "x64"
```

**All variables are optional** - leave them empty to use PSADT template defaults. Configure only what you need for your environment.

### Intelligent Processing
The script includes smart handling for common scenarios:

- **Version checking** - Compares installed vs. available module versions
- **Path validation** - Creates parent directories if they don't exist
- **Asset management** - Only downloads if URLs are configured
- **Error handling** - Comprehensive try/catch blocks with status messages
- **Progress indicators** - Visual feedback with colored icons and status messages

### Enhanced User Experience
The script provides excellent visibility throughout the process:

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                                                                           ║
║                     PSADT Deployment Creator Script                       ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝

================================================================================

🔍 Checking PSAppDeployToolkit module installation...
ℹ️ Found installed version: 4.1.4
ℹ️ Latest available version: 4.1.4
✅ PSAppDeployToolkit is already up to date
✅ Module loaded successfully - Version: 4.1.4
```

## Running the Script

### Prerequisites
- **PowerShell 7.0 or later** (required)
- **Internet connection** for module updates and asset downloads
- **Administrator privileges** 

### Basic Usage

1. **Download the script** from GitHub
2. **Edit configuration variables** at the top (optional)
3. **Run the script** in PowerShell 7:

```powershell
.\New-PSADTDeployment.ps1
```

4. **Provide application details** when prompted:
   - Application vendor (e.g., Microsoft, Adobe, Cisco)
   - Application name
   - Application version

5. **Choose deployment location** (or press Enter for default)

6. **Let the script work** - it handles everything automatically

### Interactive Prompts

The script validates all input to ensure you don't miss required information:

```powershell
Enter the application vendor (e.g., Microsoft, Adobe, Cisco): Microsoft
Enter the application name: Edge
Enter the application version: 130.0.2849.46
```

### Example Output

Here's what you'll see during execution:

```
================================================================================

ℹ️ Gathering deployment information...

Enter the application vendor (e.g., Microsoft, Adobe, Cisco): Microsoft
Enter the application name: Edge  
Enter the application version: 130.0.2849.46

ℹ️ Default deployment parent location: C:\PSADT\v4.1.4\Microsoft
Press Enter to use default location, or specify custom parent path: 

✅ Deployment will be created at: C:\PSADT\v4.1.4\Microsoft\Edge 130.0.2849.46

================================================================================

🔄 Creating PSADT deployment template...
🔄 Template creation command completed. Verifying folder structure...
✅ Folder structure verification complete
✅ Successfully created deployment template: 'Edge 130.0.2849.46'
ℹ️ Deployment created at: C:\PSADT\v4.1.4\Microsoft\Edge 130.0.2849.46

================================================================================

⬇️ Checking asset file configuration...
ℹ️ No custom asset URLs configured - using template defaults

================================================================================

⚙️ Updating deployment script configuration...
⚙️ Found deployment script, updating app details...
✅ Updated AppVendor to 'Microsoft'
✅ Updated AppName to 'Edge'
✅ Updated AppVersion to '130.0.2849.46'
✅ Updated AppScriptDate to '2025-10-27'
✅ Updated AppScriptAuthor to 'Matthew Miles'
✅ Updated AppArch to 'x64'
✅ Deployment script updated successfully

⚙️ Updating installation completion message...
✅ Installation completion message updated successfully
ℹ️ New message: $(adtSession.AppVendor) $(adtSession.AppName) $(adtSession.AppVersion) was successfully installed.

================================================================================

⚙️ Updating configuration file...
⚙️ Found config file, updating settings...
✅ Updated CompanyName to 'Your Company'
⚙️ Updating log paths to custom location...
✅ Updated LogPath to 'C:\ProgramData\Microsoft\IntuneManagementExtension\Logs'
✅ Updated LogPathnoAdminRights to 'C:\ProgramData\Microsoft\IntuneManagementExtension\Logs'
✅ Configuration file updated successfully

================================================================================

✅ Deployment creation completed!

📋 Deployment Summary:
   • Vendor: Microsoft
   • Application: Edge
   • Version: 130.0.2849.46
   • Location: C:\PSADT\v4.1.4\Microsoft\Edge 130.0.2849.46
   • PSADT Module Version: v4.1.4
   • Company: Your Company
   • Log Path: C:\ProgramData\Microsoft\IntuneManagementExtension\Logs
   • Assets: Template defaults
   • Completion Message: Custom message with app details

🎯 Next Steps:
   1. Navigate to: C:\PSADT\v4.1.4\Microsoft\Edge 130.0.2849.46
   2. Customize the deployment script as needed
   3. Add your installation files to the Files directory
   4. Test the deployment

ℹ️ Opening deployment folder and VS Code...

Would you like to open the deployment folder in Explorer and VS Code? (Y/N): Y

🔄 Opening deployment folder in Explorer...
✅ Explorer opened successfully
🔄 Opening deployment folder in VS Code...
✅ VS Code opened successfully

🎉 Script completed successfully! Happy deploying! 🚀
```

## Customization Options

### Asset Branding
Replace the default PSADT assets with your organization's branding:

```powershell
# Add your custom asset URLs
$AppIconUrl = "https://yourcompany.com/assets/AppIcon.png"
$BannerClassicUrl = "https://yourcompany.com/assets/Banner.png"
```

The script will automatically download and replace the default template assets.

### Company Configuration
Set your organization details once in the script:

```powershell
# Configure once, applies to all deployments
$CompanyName = "Contoso Corporation"
$Author = "Your Name"
```

### Log Path for Intune
If deploying through Microsoft Intune, configure the appropriate log location:

```powershell
# Intune logging location
$LogPath = "C:\ProgramData\Microsoft\IntuneManagementExtension\Logs"
```

### Architecture Defaults
Set the default architecture for your environment:

```powershell
# Set default to x64 for modern endpoints
$DefaultAppArch = "x64"

# Or leave empty for template default
$DefaultAppArch = ""
```

## Real-World Workflow Integration

### For Application Packagers
1. **Configure the script once** with your organization's settings
2. **Run it for every new application** you need to package
3. **Focus on testing and validation** instead of manual setup

### For Deployment Engineers
1. **Standardize your deployment structure** across all applications
2. **Ensure consistent logging** and configuration
3. **Speed up package creation** for urgent deployments

### For Teams
1. **Share the configured script** with your team
2. **Everyone uses the same settings** and structure
3. **Reduce onboarding time** for new team members

## Advanced Usage Tips

### Quick Deployment for Testing
Leave all configuration variables empty for the fastest setup using all template defaults:

```powershell
# All variables empty = quickest setup
$AppIconUrl = ""
$BannerClassicUrl = ""
$CompanyName = ""
$Author = ""
$LogPath = ""
$DefaultAppArch = ""
```

### Organization-Wide Standards
Configure once and save as your team's standard:

```powershell
# Organization standard settings
$CompanyName = "Contoso Corporation"
$Author = "IT Deployment Team"
$LogPath = "C:\ProgramData\Microsoft\IntuneManagementExtension\Logs"
$DefaultAppArch = "x64"
```

### Custom Deployment Locations
The script suggests a default location but accepts custom paths:

```
Default: C:\PSADT\v4.1.4\<Vendor>\<App> <Version>
Custom: D:\Deployments\Applications\<App> <Version>
```

## The Complete Script

The full script with all features is available on GitHub. Here's the structure:

```powershell
#Requires -Version 7.0

<#
.SYNOPSIS
    Creates new PowerShell Application Deployment Toolkit (PSADT) v4 
    deployments with automated setup.

.DESCRIPTION
    This script automates the creation of PSADT v4 deployment packages by:
    - Installing/updating the PSAppDeployToolkit module
    - Creating a new deployment template
    - Downloading and replacing default asset files
    - Updating deployment script parameters
    - Configuring deployment settings
    - Optionally opening in Explorer and VS Code

.NOTES
    File Name      : New-PSADTDeployment.ps1
    Author         : Matthew Miles
    Prerequisite   : PowerShell 7.0 or later
    
.LINK
    https://github.com/PSAppDeployToolkit/PSAppDeployToolkit
#>

# User-configurable settings at top
# All optional - leave empty for defaults

# Function definitions for modular operations
# - Write-StepOutput: Colored status messages
# - Write-Separator: Visual section dividers
# - Update-ScriptParameter: Efficient parameter updates
# - Get-ValidatedInput: Input validation
# - Wait-ForFolderStructure: Folder verification
# - Update-AssetFiles: Asset downloads
# - Update-DeploymentScript: Script configuration
# - Update-CompletionMessage: Custom messages
# - Update-ConfigFile: Config file updates

# Main script execution flow:
# 1. Check/update PSAppDeployToolkit module
# 2. Gather application details interactively
# 3. Create deployment template
# 4. Download custom assets if configured
# 5. Update deployment script
# 6. Update configuration file
# 7. Display summary and open in Explorer/VS Code
```

**Download the complete script**: [New-PSADTDeployment.ps1](https://github.com/1eyeITguy/learningITtogether/blob/main/Scripts/PSADT/New-PSADTDeployment.ps1)

## Additional Resources

### Official PSADT Resources
- **Website**: [PSAppDeployToolkit.com](https://psappdeploytoolkit.com/)
- **Documentation**: [PSADT Docs](https://psappdeploytoolkit.com/docs)
- **Reference**: [Function Reference](https://psappdeploytoolkit.com/docs/reference)
- **GitHub**: [PSAppDeployToolkit Repository](https://github.com/PSAppDeployToolkit/PSAppDeployToolkit)

### Community Support
- **Discourse Forum**: [Official PSADT Forum](https://discourse.psappdeploytoolkit.com/)
- **Discord**: [WinAdmins Discord #psadt Channel](https://discord.com/channels/618712310185197588/627204361545842688)
- **Reddit**: [r/PSADT Subreddit](https://reddit.com/r/psadt)

### Training and Learning
- **ViaMonstra Academy**: [PSADT v4 Essentials Course](https://academy.viamonstra.com/courses/psadt-v4-essentials)
- **YouTube**: Search for "PSADT v4" for community tutorials

## Final Thoughts

The script is designed to be flexible - use it with full configuration for organization-wide standardization, or with minimal configuration for quick one-off packages. Either way, you'll save time and reduce errors.

**Happy packaging!** 🚀
