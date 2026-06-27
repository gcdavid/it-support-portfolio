# Group Policy - Corporate Workstation Management Lab

## Project Overview
This lab demonstrates the implementation of centralized workstation management using Group Policy in a Windows Active Directory environment. The goal was to enforce security standards, standardize the user experience, and restrict unnecessary access on corporate workstations.

## Objectives
- Create and apply Group Policies for security hardening
- Standardize desktop environment (wallpaper, mapped drives)
- Restrict user access to system settings (Control Panel, USB storage)
- Deploy software centrally
- Troubleshoot and resolve Group Policy application issues

## Environment
- **Domain Controller**: Windows Server 2022
- **Client Machine**: Windows 11 joined to the domain
- **Group Policy Management**: Corporate Workstation Policy
- **Shared Folder**: Used for corporate wallpaper and mapped drives

## Configurations Implemented

### Security Policies
- Enabled password complexity requirements
- Enforced password history (remember last 1 password)
- Set account lockout threshold after 3 invalid attempts
- Disabled access to Control Panel and PC Settings
- Blocked all removable storage devices (USB)

### User Experience Standardization
- Mapped network drive (Z:) to a shared folder using Group Policy Preferences
- Deployed corporate wallpaper to all workstations
- Deployed Google Chrome via Group Policy Software Installation

## Challenges & Troubleshooting

### 1. Wallpaper Not Displaying on Client
**Problem**: Corporate wallpaper was not appearing on the Windows 11 client machine.

**Root Cause**: Incorrect file path in the Group Policy setting.

**Solution**: Used Command Prompt on the client to verify the UNC path (`\\servername\Shared\CorporateImage.jpg`). Confirmed the path was correct and re-applied the policy. Wallpaper appeared successfully after `gpupdate /force`.

### 2. Client VM Receiving APIPA Address (169.254.x.x)
**Problem**: Client virtual machine was not getting an IP address from DHCP.

**Root Cause**: 
- Virtual network adapter was set to NAT instead of Internal Network.
- Internal network DHCP server was disabled.

**Solution**: Changed the network adapter to **Internal Network** and enabled DHCP on the internal virtual switch. Client then received a proper IP address from the Domain Controller.

### 3. GPO Naming Conflict After Deletion
**Problem**: After deleting a GPO, creating a new GPO with the same name threw an error.

**Root Cause**: Lingering Group Policy object in Active Directory.

**Solution**: Used PowerShell to identify and remove the orphaned GPO:
```powershell
Get-GPO -Name "Corporate Workstation Policy" | Remove-GPO