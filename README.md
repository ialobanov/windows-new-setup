# Setup Windows

## Install PowerShell and Fonts

Install PowerShell

https://github.com/PowerShell/PowerShell/releases

JetBrainsMono Nerd Fonts

https://github.com/ryanoasis/nerd-fonts/releases

## Prepare PowerShell

### Types

- Current User, Current Host - `$PROFILE`
- Current User, Current Host - `$PROFILE.CurrentUserCurrentHost`
- Current User, All Hosts - `$PROFILE.CurrentUserAllHosts`
- All Users, Current Host - `$PROFILE.AllUsersCurrentHost`
- All Users, All Hosts - `$PROFILE.AllUsersAllHosts`

### Create Profile

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

### Setup Profile

```powershell
notepad.exe .\PowerShellInstallModules.ps1
```

```powershell
Install-Module -Name posh-git -Scope CurrentUser;
if ($?) { Install-Module -Name z -Scope CurrentUser };
if ($?) { Install-Module -Name PSReadLine -Scope CurrentUser };
if ($?) { Install-Module -Name Terminal-Icons -Scope CurrentUser };
```

One Half Dark Edited

```json
{
	"background": "#333361",
	"black": "#5E5D5D",
	"blue": "#61AFEF",
	"brightBlack": "#757575",
	"brightBlue": "#61AFEF",
	"brightCyan": "#56B6C2",
	"brightGreen": "#98C379",
	"brightPurple": "#C678DD",
	"brightRed": "#E06C75",
	"brightWhite": "#DCDFE4",
	"brightYellow": "#FFFD58",
	"cursorColor": "#0CFF93",
	"cyan": "#56B6C2",
	"foreground": "#FCFCFC",
	"green": "#98C379",
	"name": "One Half Dark Edited",
	"purple": "#C678DD",
	"red": "#E06C75",
	"selectionBackground": "#FFFFFF",
	"white": "#DCDFE4",
	"yellow": "#E5E34F"
},
```

Check Modules' path

```powershell
Get-Module -ListAvailable -Name {Module Name} | Select-Object -Property  Path
```

Profile

```powershell
notepad.exe $PROFILE
```

```powershell
## Alias

Set-Alias -Name vim -Value $env:ProgramFiles\Neovim\bin\nvim.exe
Set-Alias -Name cle -Value Clear-Host
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name indeed -Value $Env:USERPROFILE\Documents\Default.rdp
Set-Alias -Name cln -Value CleanTemp 
Set-Alias -Name touch -Value New-Item

## Functions

Function CleanTemp {
  Remove-Item -Path $env:TEMP\* -Recurse -ErrorAction SilentlyContinue;
  Remove-Item -Path C:\Windows\Temp\* -Recurse -ErrorAction  SilentlyContinue
}
Function cleanrb {
  Clear-RecycleBin
}
Function which ($command) {
  Get-Command -Name $command -ErrorAction SilentlyContinue |
  Select-Object -ExpandProperty Path -ErrorAction SilentlyContinue
}
Function UpdateAll {
  winget.exe upgrade --source winget --recurse --verbose
}
Function GitPush {
  git.exe add .; if ($?) { git.exe commit -am "." }; if ($?) { git.exe push }; if ($?) { Clear-Host }
}
  
## Import-Module

Import-Module -Name $env:ProgramFiles\powershell\7\Modules\PSReadLine\PSReadLine.psd1
Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadlineKeyHandler -Chord 'Ctrl+d' -Function DeleteChar
Set-PSReadLineKeyHandler -Chord 'Ctrl+f' -Function ForwardWord
Set-PSReadLineKeyHandler -Chord 'Enter' -Function ValidateAndAcceptLine
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\z\1.1.13\z.psd1
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\Terminal-Icons\0.11.0\Terminal-Icons.psd1
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\posh-git\1.1.0\posh-git.psd1
```

## Install Apps

```powershell
notepad.exe .\WingetInstall.ps1
```

```powershell
winget install --id Git.Git -e --source winget;
if ($?) { winget install --id Bitwarden.Bitwarden -e --source winget --scope user };
if ($?) { winget install --id Telegram.TelegramDesktop -e --source winget --scope user };
if ($?) { winget install --id Microsoft.VisualStudioCode -e --source winget --scope user };
if ($?) { winget install --id Obsidian.Obsidian -e --source winget --scope user };
if ($?) { winget install --id SlackTechnologies.Slack -e --source winget --scope user };
if ($?) { winget install --id Skillbrains.Lightshot -e --source winget };
if ($?) { winget install --id LibreWolf.LibreWolf -e --source winget };
if ($?) { winget install --id Google.Chrome -e --source winget };
if ($?) { winget install --id Neovim.Neovim -e --source winget };
```

Neovim

```powershell
winget install --id zig.zig -e --source winget --scope user;
if ($?) { winget install --id -e BurntSushi.ripgrep.MSVC --source winget --scope user };
```

## POTPlyaer

https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe
