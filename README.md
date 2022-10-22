# install-win

## Clenup of uncessary services
run elevated `cmd`
```bat
sc delete "CCleanerBrowserElevationService"
sc delete "ccleaner"
sc delete "ccleanerm"
```

# source https://www.groovypost.com/howto/12-windows-10-services-that-are-safe-to-disable/
Windows Image Acquisition (WIA)
Fax
TabletInputService
Windows Mobile Hotspot Service
Connected User Experiences and Telemetry
```bat
sc delete stisvc
sc delete fax
sc delete TabletInputService
sc delete icssvc
sc delete DiagTrack
```

## Remove gaming services
```powershell
Get-Appxpackage Microsoft.GamingServices | remove-AppxPackage -allusers
Get-AppxPackage Microsoft.XboxGamingOverlay | remove-AppxPackage -allusers
Get-AppxPackage Microsoft.GamingApp | remove-AppxPackage -allusers
```

## Regedit: remove unwanted start apps
Current user
```
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```
All users
```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```
## Enable multi RDP

Run elevated PowerShell

Change the PowerShell execution policy settings for the current session:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
```

and then execute

```
# PowerShell script used to patch termsrv.dll file and allow multiple RDP connections on Windows 10 (1809 and never) and Windows 11 
# Details here http://woshub.com/how-to-allow-multiple-rdp-sessions-in-windows-10/


# Stop RDP service, make a backup of the termsrv.dllfile and change the permissions 
Stop-Service UmRdpService -Force
Stop-Service TermService -Force
$termsrv_dll_acl = Get-Acl c:\windows\system32\termsrv.dll
Copy-Item c:\windows\system32\termsrv.dll c:\windows\system32\termsrv.dll.copy
takeown /f c:\windows\system32\termsrv.dll
$new_termsrv_dll_owner = (Get-Acl c:\windows\system32\termsrv.dll).owner
cmd /c "icacls c:\windows\system32\termsrv.dll /Grant $($new_termsrv_dll_owner):F /C"
# search for a pattern in termsrv.dll file 
$dll_as_bytes = Get-Content c:\windows\system32\termsrv.dll -Raw -Encoding byte
$dll_as_text = $dll_as_bytes.forEach('ToString', 'X2') -join ' '
$patternregex = ([regex]'39 81 3C 06 00 00(\s\S\S){6}')
$patch = 'B8 00 01 00 00 89 81 38 06 00 00 90'
$checkPattern=Select-String -Pattern $patternregex -InputObject $dll_as_text
If ($checkPattern -ne $null) {
    $dll_as_text_replaced = $dll_as_text -replace $patternregex, $patch
}
Elseif (Select-String -Pattern $patch -InputObject $dll_as_text) {
    Write-Output 'The termsrv.dll file is already patch, exitting'
    Exit
}
else { 
    Write-Output "Pattern not found "
}
# patching termsrv.dll
[byte[]] $dll_as_bytes_replaced = -split $dll_as_text_replaced -replace '^', '0x'
Set-Content c:\windows\system32\termsrv.dll.patched -Encoding Byte -Value $dll_as_bytes_replaced
# comparing two files 
fc.exe /b c:\windows\system32\termsrv.dll.patched c:\windows\system32\termsrv.dll
# replacing the original termsrv.dll file 
Copy-Item c:\windows\system32\termsrv.dll.patched c:\windows\system32\termsrv.dll -Force
Set-Acl c:\windows\system32\termsrv.dll $termsrv_dll_acl
Start-Service UmRdpService
Start-Service TermService
```

In addition you may need to run `gpedit.msc`

An in the policy “Limit number of connections” under Computer Configuration -> Administrative Templates -> Windows Components -> Remote Desktop Services -> Remote Desktop Session Host -> Connections section. Change its value to 999999.

# Can’t pin folders to Quick Access (The parameter is incorrect.) ASUS
```
del /F /Q %APPDATA%\Microsoft\Windows\Recent\AutomaticDestinations
```
https://www.jdhodges.com/blog/solved-cant-pin-folders-quick-access-parameter-incorrect/

