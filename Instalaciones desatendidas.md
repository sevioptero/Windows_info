# Instalación de aplicaciones de forma desatendida
Contenido:
* [Consideraciones](#consideraciones)
* [7-Zip](#7-zip)
* [EViews](#eviews)
* [Mozilla Firefox ESR](#mozilla-firefox-esr)
* [Java JRE](#java-jre)
* [VLC](#vlc)
* [Enlaces](#enlaces)

***

# Consideraciones
Para que funcione la instalación remota el archivo instalador debe estar desbloqueado. Esto pasa por ejemplo con el instalador de Firefox. 
## Desbloqueo del archivo
### De forma manual
1. Botón derecho sobre el archivo instalador.
2. Hacemos clic en Propiedades.
3. Marcamos Desbloquear y pulsamos Aceptar.
### Desde PowerShell
```powershell
$version = "60.1.0"
Unblock-File -Path "$InstallPath\Firefox-ESR\Firefox Setup $($version)esr.exe"
```
# 7-Zip
## Instalación
### Desde CMD
Instalador desatendido para 7-Zip desde paquete MSI.
```batchfile
:: Instalador de 7-ZIP
::  Version: 0.1
::  Fecha: 03/05/2018

@echo off
set AppName=7-Zip
:: Plataforma: -x64 o en blanco para x32
set PLATAFORMA=-x64
set basepath=%~dp0

:: Obtiene la version del instalador
for %%# in ("%basepath%7z*%PLATAFORMA%.msi") do set "filename=%%~nx#"
set installerversion=%filename:~2,4%

:: Instalacion
echo Instalando %AppName% %installerversion% %PLATAFORMA%
%systemroot%\system32\msiexec /i "%basepath%7z%installerversion%%PLATAFORMA%.msi" /qb
```
Instalador desatendido para 7-Zip desde EXE.
```batchfile
:: Instalador de 7-ZIP
::  Version: 0.1
::  Fecha: 03/05/2018

@echo off
set AppName=7-Zip
:: Plataforma: -x64 o en blanco para x32
set PLATAFORMA=-x64
set basepath=%~dp0

:: Obtiene la version del instalador
for %%# in ("%basepath%7z*%PLATAFORMA%.exe") do set "filename=%%~nx#"
set installerversion=%filename:~2,4%

:: Instalacion
echo Instalando %AppName% %installerversion% %PLATAFORMA%
"%basepath%7z%installerversion%%PLATAFORMA%.exe" /S
```
### Desde PowerShell
```powershell
$credenciales = Get-Credential -Credential "Dominio\Usuario"
Invoke-Command -ComputerName PC01 -ScriptBlock { 
$version = "1805"
$InstallPath = "\\server\Shared\Apps"
New-PSDrive -Name "PSDrive" -PSProvider "FileSystem" -Root $InstallPath -Credential $using:credenciales
Unblock-File -Path "$InstallPath\7-ZIP\7z$version-x64.exe"
Start-Process -FilePath "$InstallPath\7-ZIP\7z$version-x64.exe" -ArgumentList "/S" -Wait
}
```
## Desinstalación
### Desde CMD
```batchfile
%ProgramFiles%\7-Zip\Uninstall.exe /S
```
### Desde PowerShell
```powershell
Invoke-Command -ComputerName PC01 -ScriptBlock { 
Start-Process -FilePath "$env:ProgramFiles\7-Zip\Uninstall.exe" -ArgumentList "/S" -Wait
}
```
## Referencia
* [7-Zip FAQ](https://www.7-zip.org/faq.html)


# EViews
## Previo a la instalación
El add-in Excel 2007 requiere Microsoft .NET 3.5. Hay que instalarlo antes de instalar EViews.
### Desde CMD
```Batchfile
Dism /online /enable-feature /featurename:NetFx3 /All /Source:D:\sources\sxs /LimitAccess
```
En D:\sources debe estar el paquete de instalación de .Net 3.5 (microsoft-windows-netfx3-ondemand-package.cab). También podemos copiarlo al directorio donde está el instalador de EViews e instalarlo desde ahí.
### Desde PowerShell
```powershell
Add-WindowsCapability -Online -Name NetFx3~~~~
```

## Creación del script de instalación silenciosa
La instalación desatendida de EViews se realiza creando una primera instalación en la que se crea un archivo de respuestas. Luego este archivo se usa para posteriores instalaciones.
```Batchfile
Setup.exe /r /f1C:\EViews\Install\setup.iss
```
Se realiza una instalación normal y nos pide todos los datos de instalación, que se almacenarán en el archivo de respuestas setup.iss. El archivo de respuestas estará en C:\EViews\Install y se llamará setup.iss.
## Instalación desatendia
### Desde CMD
```Batchfile
Setup.exe /s /f1C:\eviews\install\setup.iss
```
## Referencia
* [Microsoft SMS](http://forums.eviews.com/viewtopic.php?f=1&t=21)
* [Licensing Eview 9 silent install](http://forums.eviews.com/viewtopic.php?t=12117)
* [EViews 8 silent install](http://forums.eviews.com/viewtopic.php?t=7750)
* [Silent install of 7.1 patch & update](http://forums.eviews.com/viewtopic.php?t=2351)

# Mozilla Firefox ESR
## Instalación
### Desde CMD
Instalación.
```Batchfile
"Firefox Setup 60.0.3.exe" /S
```
### Desde PowerShell
Instalación en un equipo remoto.
```powershell
$credenciales = Get-Credential -Credential "Dominio\Usuario"
Invoke-Command -ComputerName PC01 -ScriptBlock { 
$version = "60.1.0"
$HomePage = "https://www.web.com"
$InstallPath = "\\server\Shared\Apps"
New-PSDrive -Name "PSDrive" -PSProvider "FileSystem" -Root "$InstallPath" -Credential $using:credenciales
Unblock-File -Path "$InstallPath\Firefox-ESR\Firefox Setup $($version)esr.exe"
Start-Process -FilePath "$InstallPath\Firefox-ESR\Firefox Setup $($version)esr.exe" -ArgumentList "/S /TaskbarShortcut=true /DesktopShortcut=true" -Wait
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox" -Name "DontCheckDefaultBrowser" -PropertyType "DWord" -Value "1" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox" -Name "DisableTelemetry" -PropertyType "DWord" -Value "1" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox" -Name "OverrideFirstRunPage" -PropertyType "String" -Value $HomePage -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox\Homepage" -Name "URL" -PropertyType "String" -Value $HomePage -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox\Homepage" -Name "Locked" -PropertyType "DWord" -Value "1" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox\PopupBlocking\Allow" -Name "1" -PropertyType "String" -Value $HomePage -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Mozilla\Firefox\PopupBlocking\Allow" -Name "2" -PropertyType "String" -Value $HomePage -Force
}
```
## Desinstalación
### Desde CMD
```Batchfile
%programfiles%\Mozilla Firefox\uninstall\helper.exe /SILENT
```
### Desde PowerShell
Desinstalación en un equipo remoto.
```powershell
Invoke-Command -ComputerName PC01 -ScriptBlock { 
Start-Process -FilePath "$env:ProgramFiles\Mozilla Firefox\uninstall\helper.exe" -ArgumentList "/SILENT" -Wait
}
```
## Referencia
* [Mozilla Firefox - Full Installer Configuration - Command-line Options](https://firefox-source-docs.mozilla.org/browser/installer/windows/installer/FullConfig.html)

# Java JRE
## Instalación
### Desde CMD
Mediante archivo EXE
```Batchfile
jre-8u171-windows-i586.exe INSTALL_SILENT=Enable REMOVEOUTOFDATEJRES=1
```
## Referencia
* [Java Platform, Standard Edition Installation Guide. 17 Windows JRE Installer Options](https://docs.oracle.com/javase/8/docs/technotes/guides/install/config.html#table_config_file_options)
* [Java Platform, Standard Edition Installation Guide. 20 Installing With a Configuration File](https://docs.oracle.com/javase/8/docs/technotes/guides/install/config.html)

# VLC
## Instalación
### Desde CMD
```Batchfile
vlc-3.0.3-win64.exe /S
```
## Desde PowerShell
```powershell
$credenciales = Get-Credential -Credential "Dominio\Usuario"
Invoke-Command -ComputerName PC01 -ScriptBlock { 
$version = "3.0.3"
$InstallPath = "\\server\Shared\Apps"
New-PSDrive -Name "PSDrive" -PSProvider "FileSystem" -Root "$InstallPath" -Credential $using:credenciales
Start-Process -FilePath "$InstallPath\VLC\vlc-$version-win64.exe" -ArgumentList "/S" -Wait
}
```
## Desinstalación
```Batchfile
"%ProgramFiles%\VideoLAN\VLC\uninstall.exe" /S
```
## Desde PowerShell
```powershell
Invoke-Command -ComputerName PC01 -ScriptBlock { 
Start-Process -FilePath "$env:ProgramFiles\VideoLAN\VLC\uninstall.exe" -ArgumentList "/S" -Wait
}
```
### Referencia
* [VLC Documentation:Installing VLC](https://wiki.videolan.org/Documentation:Installing_VLC/#Alternative)

## Enlaces
* [Desktop Central - Software Installation Guide](https://www.manageengine.com/products/desktop-central/software-installation/windows-software.html)
* [SilentInstall.net](https://silent-install.net/start)
