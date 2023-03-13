# KQL : Controlled Folder Access
Controlled Folder Access is a security feature in Microsoft Defender for Endpoint that helps protect important files from ransomware and other malicious software. It blocks unauthorized changes to designated folders and allows only trusted applications to access them, which helps prevent malware from accessing or modifying sensitive files.

> Note : **Windows system folders are protected by default**
> ```
> c:\Users\<username>\Documents
> c:\Users\Public\Documents
> c:\Users\<username>\Pictures
> c:\Users\Public\Pictures
> c:\Users\Public\Videos
> c:\Users\<username>\Videos
> c:\Users\<username>\Music
> c:\Users\Public\Music
> c:\Users\<username>\Favorites
> ```
> [Protect important folders from ransomware from encrypting your files with controlled folder access | Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/controlled-folders?view=o365-worldwide)


## PowerShell Cmdlet
**PowerShell**
```powershell
# Enable/Disable controlled folder access
Set-MpPreference -EnableControlledFolderAccess AuditMode
Set-MpPreference -EnableControlledFolderAccess Enabled
Set-MpPreference -EnableControlledFolderAccess Disabled

# Customize controlled folder access
Add-MpPreference -ControlledFolderAccessProtectedFolders "c:\apps\"
Add-MpPreference -ControlledFolderAccessAllowedApplications "c:\apps\test.exe"
Remove-MpPreference -ControlledFolderAccessProtectedFolders  "c:\apps\"
Remove-MpPreference -ControlledFolderAccessAllowedApplications "c:\apps\test.exe"
```

## KQL Hunting queries
```kql
DeviceEvents
| where Timestamp > ago(7d)
| where ActionType contains "ControlledFolder"
| summarize TargetFolderPath = make_list(FolderPath) by DeviceId, DeviceName
| extend Num = array_length(TargetFolderPath)
| project DeviceId, DeviceName, Num, TargetFolderPath
```