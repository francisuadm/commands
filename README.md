# commands



## The script below will backup your windows profile data to box.com account. Save the script called it "BackupBOX.bat"

```
@echo off

call :isAdmin

if %errorlevel% == 0 (
    goto :run
) else (
    echo Requesting administrative privileges...
    goto :UACPrompt
)

exit /b

:isAdmin
    fsutil dirty query %systemdrive% >nul
exit /b




:run
@Echo Off
cls

:: Set variable for both source and destination.
SET "sUser=%UserProfile%"
SET "UserDSTPath=%UserProfile%\BOX\-Backup.BOX"


:: Change the color back to default.
Color 17


:: Set default local directory folder & network
@echo off
cls
pushd "%~dp0"



:: Check if the username doesn't exit to the network folder then create the username folder. 
:: Then set current network folder as the new user network folder. Open the user network folder.
@ECHO OFF
IF NOT EXIST "%UserDSTPath%" md "%UserDSTPath%"
popd
pushd %UserDSTPath%
explorer %UserDSTPath%

::
CLS
@echo.
@echo.
@ECHO Backup user profile [%UserProfile%] to BOX backup folder [%UserDSTPath%]
@echo. &
:: Robocopy command options breakdown
:: /S — Copy subdirectories, but not empty ones.
:: /E — Copy Subdirectories, including empty ones.
:: /Z — Copy files in restartable mode.
:: /ZB — Uses restartable mode. If access is denied, use backup mode.
:: /R:5 — Retry 5 times (you can specify a different number, the default is 1 million).
:: /W:0 — Wait 0 seconds before retrying (you can specify a different number, the default is 30 seconds).
:: /TBD — Wait for share names To Be Defined (retry error 67).
:: /NFL — No file list - don't log file names.
:: /V — Produce verbose output, showing skipped files.
:: /MT:32 — Do multithreaded copies with n threads (default is 8).
:: /LOG - Log file save on network folder under userprofile.
:: /XD - Exclude selected directories; space delimited.
:: /XF - Exclude select files; space delimited.
:: /TEE - Outputs information on the screen as well as in the log file
:: 
robocopy.exe %UserProfile% %UserDSTPath% /S /E /ZB /R:5 /W:0 /LOG:%UserProfile%\Backup-Box_robocopy_log.txt /XJ /NFL /XD Appdata Box Downloads OneDrive Searches "Saved Games" "3D Objects" “Temporary Internet Files” "VirtualBox VMs" ".*" OfficeFileCache Temp *cache* Spotify WER Box /XF *cache* NTUSER.* /MT:32 /TEE
:end

@echo.
@echo. &
@Echo Your profile folders is now backing up to Box cache folder via Box Drive.
@echo. &
@echo The window -Backup.BOX folder shows all of the files/folders that was copied.
@echo If all of the files/folders icons are showing with blue icon cloud then it's completely sync up to the BOX.COM, you can close this window.
@echo All tasks are completed....
@echo But if all of the files/folders icons are still have a red icon that means they are not completely sync up to the BOX.COM,
@echo Please wait until all files/folders icons are showing with blue icon cloud or else your data are not backup.

pause
endlocal
exit /B



:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    echo UAC.ShellExecute "cmd.exe", "/c %~s0 %~1", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
exit /B
```

#


###
Manual way backing up to box.com, with box drive already installed.
Make sure that folder called Transfer on your box.com under My Personal Folder 
have been created first.
###
```
robocopy c:\transfer "C:\Users\username\Box\01. My Personal Folder\Transfer" /S /E /ZB /R:5 /W:0 /LOG:%UserProfile%\Backup-Box_robocopy_log.txt /XJ /NFL /MT:32 /TEE
```


#

## Save it and called "backupTerm.bat"

```
@echo off

call :isAdmin

if %errorlevel% == 0 (
    goto :run
) else (
    echo Requesting administrative privileges...
    goto :UACPrompt
)

exit /b

:isAdmin
    fsutil dirty query %systemdrive% >nul
exit /b

:run
@Echo Off
cls

:: Set variable for both source and destination.
SET "User1=%1"
::SET "dUser=%Username%"
SET "UserDSTPath="
SET "NETWORKDSTPath=\\NetworkSMB\files\"

:: Change color
Color 17

:: Get list of users from the profile folder.
SET "Value="
setlocal enableDelayedExpansion
FOR /F "tokens=1*" %%A IN ('dir C:\Users\ /B') DO (
    SET "Name=%%A"
    IF /I "!NAME!" NEQ "Administrator" (
        IF /I "!NAME!" NEQ "Public" (
            IF /I "!NAME!" NEQ !USERNAME! (
				SET "Value=!Value! "
                SET "Value=!Value![%%A]"

            )
        )
    )
)

:: Display list of users from the local drive.
@echo Here are the list of users for this machine: %Value%
@echo.
@echo. &


:: Display the current user name, request user name to be backup.
:Input
if "%User1%"=="" set /p "User1=What's the username that need to backup (Default [%Username%]): "
	
:Continue
Set "UserPath=C:\Users\%User1%"
:: Set "currUser=%User1%"

:: Change the color back to default.
Color 7

:: Set default local directory folder & network
@echo off
cls
pushd "%~dp0"
pushd %NETWORKDSTPath%


:: Check if the username doesn't exit to the network folder then create the username folder. 
:: Then set current network folder as the new user network folder. Open the user network folder.
@ECHO OFF
IF NOT EXIST "%User1%" md "%User1%"
popd
SET "CurrNDP=\\NetworkSMB\files\terms\%User1%"
pushd %CurrNDP%
explorer %CurrNDP%

::
CLS
@echo.
@echo.
@ECHO Copying profile name %UserPath% to network folder %CurrNDP%
@echo. &
:: Robocopy command options breakdown
:: /S — Copy subdirectories, but not empty ones.
:: /E — Copy Subdirectories, including empty ones.
:: /Z — Copy files in restartable mode.
:: /ZB — Uses restartable mode. If access is denied, use backup mode.
:: /R:5 — Retry 5 times (you can specify a different number, the default is 1 million).
:: /W:0 — Wait 0 seconds before retrying (you can specify a different number, the default is 30 seconds).
:: /TBD — Wait for share names To Be Defined (retry error 67).
:: /NFL — No file list - don't log file names.
:: /V — Produce verbose output, showing skipped files.
:: /MT:32 — Do multithreaded copies with n threads (default is 8).
:: /LOG - Log file save on network folder under userprofile.
:: /XD - Exclude selected directories; space delimited.
:: /XF - Exclude select files; space delimited.
:: /TEE - Outputs information on the screen as well as in the log file

robocopy.exe %UserPath% %CurrNDP% /S /E /ZB /R:5 /W:0 /LOG:%CurrNDP%\%User1%_robocopy_log.txt /XJ /NFL /XD OneDrive Searches "Saved Games" "3D Objects" “Temporary Internet Files” OfficeFileCache Temp *cache* Spotify WER Box /XF *cache* /MT:32 /TEE
:end
@echo.
@echo. &
@Echo All tasks completed...
pause
endlocal
exit /B


:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    echo UAC.ShellExecute "cmd.exe", "/c %~s0 %~1", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
exit /B
```

