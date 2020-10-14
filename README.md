# Multiple Teams Instances
Guide and PowerShell Scripts for running multiple MS Teams instances

![Example of two Teams Instances running side-by-side](Multiple.png)

## Why not use Teams in the browser? 
The desktop app gives you full functionality in Teams - like calling and meeting and desktop sharing. Performance is better in the desktop app due to data caching.

## UPDATE 14-10-2020 ##

Running a seccond Teams instance via a spoofed user profile can cause issues with Teams Addin in Outlook not allowing you to add Teams events.

Suggested workarround is to use the orginal apoorch of a  [2 account method](#2AccountMethod)

## UPDATE 1-4-2019 ##

Teams.BAT now runs Teams client in one Click, with no seccond account required!


Use this snipit to get it going!

```
@ECHO OFF
 
REM Uses the file name as the profile name
SET MSTEAMS_PROFILE=%~n0
ECHO - Using profile "%MSTEAMS_PROFILE%"
 
SET "OLD_USERPROFILE=%USERPROFILE%"
SET "USERPROFILE=%LOCALAPPDATA%\Microsoft\Teams\CustomProfiles\%MSTEAMS_PROFILE%"
 
ECHO - Launching MS Teams with profile %MSTEAMS_PROFILE%
cd "%OLD_USERPROFILE%\AppData\Local\Microsoft\Teams"
"%OLD_USERPROFILE%\AppData\Local\Microsoft\Teams\Update.exe" --processStart "Teams.exe"

```


[## 2 Account Mehod](#2AccountMethod) 

###


## Let's get it working!

The guide assumes you are using Windows 10. But similar steps apply to Windows 8 and 7.

- Press Win key and type `user`. Select "Add, edit, or remove other users"
- Click on "Add someone else to this PC"
- Click on "I don't have this person's sign-in information" > "Add a user without a Microsoft Account"
- Type your new Windows account login and password. I recommend a sim
- Press CTRL+ALT+DEL and click "switch user"
- Login as the newly created Windows user.
- [Download and install the second instance MS Teams.](https://teams.microsoft.com/downloads)
- Now press CTRL+ALT+DEL again > "switch user" > select the original account.

- Use the below to script the setup of the account:
```
$Password = Read-Host -AsSecureString -Prompt "Enter password  for the new Teams Account"
$User = Read-Host -Prompt "Name of MS Teams Account"
New-LocalUser "MSTeamsUser" -Password $Password -FullName "MS Teams User" -Description "User account used to run MS Teams"

Write-Host "Account created. Please sign in As that account"
```

- Open PowerShell console and run the following script:

```
#run as admin 
if (!([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) { Start-Process powershell.exe "-NoProfile -ExecutionPolicy Bypass -File `"$PSCommandPath`"" -Verb RunAs; exit }


$credential = Get-Credential
Start-Process "C:\Users\$($credential.UserName)\AppData\Local\Microsoft\Teams\Update.exe" '--processStart "Teams.exe"' -WorkingDirectory $env:windir -Credential $credential
```

- When prompted - enter your secondary Windows account that you created earlier.
- Then login to the new MS Teams instance using any account you want. You will now see your second of Teams.
- Done. Enjoy your second Team instance.

## Automate running the second instance

- Option 1 - Run_Teams_Instance_Ask_For_Credentials.bat - you can double-click this script to open a second instance of Teams. Upon running the script, you will be prompted to enter your secondary Windows credentials.
- Option 2 - You can hardcode your secondary Windows account's login and password in the Run_Multiple_Teams_PA.ps1 file
- You can now double-click Run_Multiple_Teams_PA.bat file. Second Teams instance will launch with no credentials prompt.


## Limitations of the approach

- If you run multiple Team instances and use the same Microsoft account - you will receive notifications in all of them. If someone calls you - you will see two incoming calls at the same time.
- Teams is a resource hog. It uses a lot of RAG and processing power.

## Troubleshooting

If you run the scripts and everything appears to work correctly but no new instance of teams starts up, close all open instaces of teams, open Task Manager (CTRL+SHIFT+ESC) and find all Teams processes and end the task. Run the script againa and you should now have a new instance of teams to log into. 

