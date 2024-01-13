We treat out software as a part of a larger pipeline made out of third party components. It allows our program to:
- be console-friendly and dockerable
- be correctly split into easy-to-use components
- support batch (mass) processing
- be a GUI wrapper over a bunch of non-interactive console programs

It prolongs a life of the application my making it an infrastructure together with maintining community around it.

## Debugging

https://www.nirsoft.net/utils/shexview.html

## Multimedia

https://web.archive.org/web/20211130093522/https://shounen.ru/docs/ogm/index.html

## README content and directory structure

<https://github.com/xiph/vorbis/tree/84c023699cdf023a32fa4ded32019f194afcdad0>

## Performance

Build optimization tips:
- build with [ThinLTO](https://clang.llvm.org/docs/ThinLTO.html)

Из комментариев к статье "[Главный секрет разработки хороших Electron-приложений](https://habr.com/ru/companies/ruvds/articles/458502/)":
- Ну и как можно относится к приложениям, которые лагают на топовом железе
- Справедливости ради, продукты JetBrains потребляют ещё больше ресурсов

## Shell Extensions

- https://learn.microsoft.com/en-us/windows/win32/shell/app-registration (file extension processing)
- https://learn.microsoft.com/en-us/windows/win32/lwef/search-handlers
- https://learn.microsoft.com/en-us/windows/win32/lwef/column-handlers
- https://learn.microsoft.com/en-us/windows/win32/shell/how-to-create-drop-handlers (for archives)

Создание подпунктов контекстного меню и назначение им скриптов, из [«Полезняшки» или «Реестр Windows как платформа»](https://habr.com/ru/articles/317802/):

```toml
Windows Registry Editor Version 5.00 

;Copyright 2016 Trottle 
;This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License version 3. 
;This program is distributed WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. 
;See <http://www.gnu.org/licenses/> for more details. 

[-HKEY_CLASSES_ROOT\exefile\shell\Bpc] 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc] 
"HasLUAShield"="" 
"MUIVerb"="Set base priority" 
"subcommands"=""

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\01low] 
"MUIVerb"="Idle CPU, lowest IO, low paging" 
"Icon"="comres.dll,9" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\01low\command] 
@="cmd /q /c echo Windows Registry Editor Version 5.00>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & for /f \"delims=<\" %%i in (\"%1\") do echo [HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions]>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"CpuPriorityClass\"=dword:00000001>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"IoPriority\"=dword:00000000>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"PagePriority\"=dword:00000001>>%%TEMP%%\\pr.reg & regedit /s %%TEMP%%\\pr.reg & del %%TEMP%%\\pr.reg & msg * %~ni priority is set to IDLE" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\02below] 
"MUIVerb"="Below normal CPU, low IO" 
"Icon"="comres.dll,12" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\02below\command] 
@="cmd /q /c echo Windows Registry Editor Version 5.00>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & for /f \"delims=<\" %%i in (\"%1\") do echo [HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions]>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"CpuPriorityClass\"=dword:00000005>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"IoPriority\"=dword:00000001>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"PagePriority\"=->>%%TEMP%%\\pr.reg & regedit /s %%TEMP%%\\pr.reg & del %%TEMP%%\\pr.reg & msg * %~ni priority is set to BELOW NORMAL" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\03above] 
"Icon"="comres.dll,8" 
"MUIVerb"="Above normal CPU" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\03above\command] 
@="cmd /q /c echo Windows Registry Editor Version 5.00>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & for /f \"delims=<\" %%i in (\"%1\") do echo [HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions]>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"CpuPriorityClass\"=dword:00000006>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"IoPriority\"=->>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"PagePriority\"=->>%%TEMP%%\\pr.reg & regedit /s %%TEMP%%\\pr.reg & del %%TEMP%%\\pr.reg & msg * %~ni priority is set to ABOVE NORMAL" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\04high] 
"MUIVerb"="High CPU" 
"Icon"="comres.dll,16" 
"CommandFlags"=dword:00000040

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\04high\command] 
@="cmd /q /c echo Windows Registry Editor Version 5.00>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & for /f \"delims=<\" %%i in (\"%1\") do echo [HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions]>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"CpuPriorityClass\"=dword:00000003>>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"IoPriority\"=->>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & echo \"PagePriority\"=->>%%TEMP%%\\pr.reg & regedit /s %%TEMP%%\\pr.reg & del %%TEMP%%\\pr.reg & msg * %~ni priority is set to HIGH" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\06ask] 
"MUIVerb"="Show current priorities" 
"Icon"="shell32.dll,23" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\06ask\command] 
@="cmd /q /c for /f \"delims=<\" %%i in (\"%1\") do reg query \"HKLM\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions\" /s | msg *" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\07default] 
"MUIVerb"="Restore to default" 
"Icon"="comres.dll,4" 

[HKEY_CLASSES_ROOT\exefile\shell\Bpc\shell\07default\command] 
@="cmd /q /c echo Windows Registry Editor Version 5.00>%%TEMP%%\\pr.reg & echo.>>%%TEMP%%\\pr.reg & for /f \"delims=<\" %%i in (\"%1\") do echo [-HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\%%~nxi\\PerfOptions]>>%%TEMP%%\\pr.reg & regedit /s %%TEMP%%\\pr.reg & del %%TEMP%%\\pr.reg & msg * %~ni priority is restored to default" 

; If you do not want to have uninstaller you can skip next part: 

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\BpcSubMenu] 
"DisplayName"="'Set base priority' submenu" 
"DisplayIcon"="imageres.dll,73" 
"UninstallString"="cmd /q /c echo Windows Registry Editor Version 5.00>%TEMP%\\pr.reg & echo.>>%TEMP%\\pr.reg & echo [-HKEY_CLASSES_ROOT\\exefile\\shell\\Bpc]>>%TEMP%\\pr.reg & echo.>>%TEMP%\\pr.reg & echo [-HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Uninstall\\BpcSubMenu]>>%TEMP%\\pr.reg & regedit /s %TEMP%\\pr.reg & del %TEMP%\\pr.reg" 
"DisplayVersion"="1.1" 
"URLInfoAbout"="http://habrahabr.ru/post/317802/" 
"NoModify"=dword:00000001 
"NoRepair"=dword:00000001
```

```toml
Windows Registry Editor Version 5.00 

;Copyright 2016 Trottle 
;This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License version 3. 
;This program is distributed WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. 
;See <http://www.gnu.org/licenses/> for more details. 

[-HKEY_CLASSES_ROOT\exefile\shell\FWc] 

[HKEY_CLASSES_ROOT\exefile\shell\FWc] 
"MUIVerb"="Set firewall rules" 
"Icon"="imageres.dll,102" 
"subcommands"=""

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\01] 
"MUIVerb"="block inbound" 
"Icon"="imageres.dll,100" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\01\command] 
@="cmd /q /c echo CreateObject(\"Shell.Application\").ShellExecute \"cmd\", \"/q /c chcp 1251 & netsh advfirewall firewall add rule name=\"\"%1\"\" dir=in action=block program=\"\"%1\"\" enable=yes | msg * \", \"\", \"runas\" > %%temp%%\\ev.vbs & cscript %%temp%%\\ev.vbs & del %%temp%%\\ev.vbs" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\02] 
"MUIVerb"="allow inbound" 
"Icon"="imageres.dll,101" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\02\command] 
@="cmd /q /c echo CreateObject(\"Shell.Application\").ShellExecute \"cmd\", \"/q /c chcp 1251 & netsh advfirewall firewall add rule name=\"\"%1\"\" dir=in action=allow program=\"\"%1\"\" enable=yes | msg * \", \"\", \"runas\" > %%temp%%\\ev.vbs & cscript %%temp%%\\ev.vbs & del %%temp%%\\ev.vbs" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\03] 
"Icon"="imageres.dll,100" 
"MUIVerb"="block outbound" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\03\command] 
@="cmd /q /c echo CreateObject(\"Shell.Application\").ShellExecute \"cmd\", \"/q /c chcp 1251 & netsh advfirewall firewall add rule name=\"\"%1\"\" dir=out action=block program=\"\"%1\"\" enable=yes | msg * \", \"\", \"runas\" > %%temp%%\\ev.vbs & cscript %%temp%%\\ev.vbs & del %%temp%%\\ev.vbs" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\04] 
"MUIVerb"="allow outbound" 
"Icon"="imageres.dll,101" 
"CommandFlags"=dword:00000040

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\04\command] 
@="cmd /q /c echo CreateObject(\"Shell.Application\").ShellExecute \"cmd\", \"/q /c chcp 1251 & netsh advfirewall firewall add rule name=\"\"%1\"\" dir=out action=allow program=\"\"%1\"\" enable=yes | msg * \", \"\", \"runas\" > %%temp%%\\ev.vbs & cscript %%temp%%\\ev.vbs & del %%temp%%\\ev.vbs" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\06] 
"MUIVerb"="Show firewall panel" 
"Icon"="imageres.dll,109" 

[HKEY_CLASSES_ROOT\exefile\shell\FWc\shell\06\command] 
@="mmc.exe wf.msc" 

; If you do not want to have uninstaller you can skip next part: 

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\FWcSubMenu] 
"DisplayName"="'Set firewall rules' submenu" 
"DisplayIcon"="imageres.dll,102" 
"UninstallString"="cmd /q /c echo Windows Registry Editor Version 5.00>%TEMP%\\pr.reg & echo.>>%TEMP%\\pr.reg & echo [-HKEY_CLASSES_ROOT\\exefile\\shell\\FWc]>>%TEMP%\\pr.reg & echo.>>%TEMP%\\pr.reg & echo [-HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Uninstall\\FWcSubMenu]>>%TEMP%\\pr.reg & regedit /s %TEMP%\\pr.reg & del %TEMP%\\pr.reg" 
"DisplayVersion"="1.1" 
"URLInfoAbout"="http://habrahabr.ru/post/317802/" 
"NoModify"=dword:00000001 
"NoRepair"=dword:00000001 
```

> Это прекрасно. Вы используете винду как линукс. Поясню свою мысль. На линуксе можно по-быстрому нахакать какой-нибудь шел-скрипт, который будет генерить скрипт на седе, который будет генерить какой-нибудь юнит-файл для systemd, который ещё чё-нибудь куда-нибудь пропишет и так далее и тому подобное. Теперь выясняется, что в винде так тоже можно.

[How can I specify icons for my app to use on the Start menu in high contrast mode?](https://devblogs.microsoft.com/oldnewthing/20240104-00/?p=109239)

### Version assignment with Git

### version.h.in

```cpp
/* version.h.in - a configuration file processed by tools/update_version.cmake
 *
 * Copyright (c) 2021 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

#ifndef GRAMMAR2CODE_VERSION_H
#define GRAMMAR2CODE_VERSION_H

/** A string ID of a Git commit used to build the project */
#cmakedefine GRAMMAR2CODE_BUILD_COMMIT_ID "@GRAMMAR2CODE_BUILD_COMMIT_ID@"

/** A flag of GRAMMAR2CODE_BUILD_COMMIT_ID being a development prerelease */
#cmakedefine01 GRAMMAR2CODE_BUILD_IS_PRERELEASE

/** A flag of uncommited changes ahead of GRAMMAR2CODE_BUILD_COMMIT_ID */
#cmakedefine01 GRAMMAR2CODE_BUILD_IS_PATCHED

/**
 * A string time when the project was built, in %Y-%m-%d.%H-%M-%S format
 *
 * Defined for out-of-git builds (if GRAMMAR2CODE_BUILD_COMMIT_ID is undefined)
 * and builds from uncommited changes (if GRAMMAR2CODE_BUILD_IS_PATCHED is
 * defined).
 */
#cmakedefine GRAMMAR2CODE_BUILD_TIME "@GRAMMAR2CODE_BUILD_TIME@"

/**
 * @def GRAMMAR2CODE_VERSION
 * A string version in semver format
 */
#if !defined(GRAMMAR2CODE_BUILD_COMMIT_ID)
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev.private.modified." GRAMMAR2CODE_BUILD_TIME
#elif GRAMMAR2CODE_BUILD_IS_PATCHED
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev." GRAMMAR2CODE_BUILD_COMMIT_ID \
                             ".modified." GRAMMAR2CODE_BUILD_TIME
#elif GRAMMAR2CODE_BUILD_IS_PRERELEASE
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev." GRAMMAR2CODE_BUILD_COMMIT_ID
#else
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@"
#endif

#endif // GRAMMAR2CODE_VERSION_H
```

## GUI

- [Don’t keep state in your XAML item templates; put the state in the items](https://devblogs.microsoft.com/oldnewthing/20231226-00/?p=109187)
## ATL

- [How do I prevent my ATL class from participating in COM aggregation? `DECLARE_NOT_AGGREGATABLE` didn’t work](https://devblogs.microsoft.com/oldnewthing/20240101-00/?p=109214)

## C++ Hacks

- [If a pointer to `T` is convertible to a pointer to the detector, then we know that the detector appears only once among the base classes of `T`.](https://devblogs.microsoft.com/oldnewthing/20230816-00/?p=108608):
	- `std::is_convertible<T *, typename T::esft_detector *>::type`