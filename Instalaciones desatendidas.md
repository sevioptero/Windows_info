# Instalación de aplicaciones de forma desatendida
Contenido:
* [Instalación de 7-Zip](#instalación-de-7-Zip)
* [Instalación de EViews](#instalación-de-eviews)
* [Instalación de Mozilla Firefox](#instalación-de-mozilla-firefox)
* [Instalación de Java JRE](#instalación-de-java-jre)
* [Instalación de VLC](#instalación-de-vlc)

***


## Instalación de 7-Zip
### Instalación mediante paquete MSI
```Batchfile
msiexec.exe /i 7z1805-x64.msi /qb
```
### Instalación mediante archivo EXE
```Batchfile
7z1805-x64.exe /S
```
### Referencia
* [7-Zip FAQ](https://www.7-zip.org/faq.html)

## Instalación de EViews
### Previo a la instalación
El add-in Excel 2007 requiere Microsoft .NET 3.5. Hay que instalarlo antes de instalar EViews.
```Batchfile
Dism /online /enable-feature /featurename:NetFx3 /All /Source:D:\sources\sxs /LimitAccess
```
En D:\sources debe estar el paquete de instalación de .Net 3.5 (microsoft-windows-netfx3-ondemand-package.cab). También podemos copiarlo al directorio donde está el instalador de EViews e instalarlo desde ahí.
### Creación del script de instalación silenciosa
La instalación desatendida de EViews se realiza creando una primera instalación en la que se crea un archivo de respuestas. Luego este archivo se usa para posteriores instalaciones.
```Batchfile
Setup.exe /r /f1C:\EViews\Install\setup.iss
```
Se realiza una instalación normal y nos pide todos los datos de instalación, que se almacenarán el archivo de respuestas setup.iss. El archivo de respuestas estará en C:\EViews\Install y se llamará setup.iss.
### Instalación desatendia
```Batchfile
Setup.exe /s /f1C:\eviews\install\setup.iss
```
### Referencia
* [Microsoft SMS](http://forums.eviews.com/viewtopic.php?f=1&t=21)
* [Licensing Eview 9 silent install](http://forums.eviews.com/viewtopic.php?t=12117)
* [EViews 8 silent install](http://forums.eviews.com/viewtopic.php?t=7750)
* [Silent install of 7.1 patch & update](http://forums.eviews.com/viewtopic.php?t=2351)

## Instalación de Mozilla Firefox
```Batchfile
Firefox Setup 60.0.3.exe /S
```
### Referencia
* [Mozilla Firefox - Full Installer Configuration
 - Command-line Options
](https://firefox-source-docs.mozilla.org/browser/installer/windows/installer/FullConfig.html)

## Instalación de Java JRE
### Instalación mediante archivo EXE
```Batchfile
jre-8u171-windows-i586.exe INSTALL_SILENT=Enable REMOVEOUTOFDATEJRES=1
```
### Referencia
* [Java Platform, Standard Edition Installation Guide. 17 Windows JRE Installer Options](https://docs.oracle.com/javase/8/docs/technotes/guides/install/config.html#table_config_file_options)
* [Java Platform, Standard Edition Installation Guide. 20 Installing With a Configuration File](https://docs.oracle.com/javase/8/docs/technotes/guides/install/config.html)

## Instalación de VLC
```Batchfile
vlc-3.0.3-win64.exe /S
```
### Referencia
* [VLC Documentation:Installing VLC](https://wiki.videolan.org/Documentation:Installing_VLC/#Alternative)

