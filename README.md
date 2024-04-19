# Setup Windows

## Set preference for specific Network Interface

```powershell
Get-NetIPInterface
```

```powershell
Set-NetIPInterface -InterfaceIndex {Specify index} -InterfaceMetric {Specifies a metric}
```

> [!IMPORTANT]
> [`InterfaceMetric`](https://learn.microsoft.com/en-us/powershell/module/nettcpip/set-netipinterface?view=windowsserver2022-ps) - Specifies a metric for an IP interface. When routes are chosen, the overall metric used to determine the preference is the sum of the route metric and the interface metric. Typically, the interface metric gives preference to a particular interface, such as using wired if both wired and wireless are available. This parameter value uses the value generated by AutomaticMetric by default. If this parameter value is set, then the AutomaticMetric parameter is automatically disabled.

```powershell
Restart-NetAdapter -InterfaceAlias {"Specify IneterfaceAlias"}
```

## Administrator

### Create Local Accounts

```powershell
New-LocalUser -Name {'user name'}
```

```powershell
Add-LocalGroupMember -Group "Users" -Member "{user name}"
```

### Install PowerShell

Install PowerShell

https://github.com/PowerShell/PowerShell/releases

### Install Apps

```powershell
notepad.exe .\WingetInstallMachine.ps1
```

```powershell
winget install --id Git.Git -e --source winget;
if ($?) { winget install --id Microsoft.VCRedist.2015+.x64 -e --source winget };
if ($?) { winget install --id Skillbrains.Lightshot -e --source winget };
if ($?) { winget install --id LibreWolf.LibreWolf -e --source winget };
if ($?) { winget install --id Google.Chrome -e --source winget };
if ($?) { winget install --id Neovim.Neovim -e --source winget };
if ($?) { winget install --id 7zip.7zip -e --source winget };
```

##### Optional

```powershell
if ($?) { winget install --id qBittorrent.qBittorrent -e --source winget }; # torrnent client
if ($?) { winget install --id Kitware.CMake -e --source winget }; # compiler for Neovim
```

## User

### Install JetBrainsMono Nerd Font

https://github.com/ryanoasis/nerd-fonts/releases

### Install Apps

```powershell
notepad.exe .\WingetInstallUser.ps1
```

```powershell
winget install --id Bitwarden.Bitwarden -e --source winget --scope user;
if ($?) { winget install --id Telegram.TelegramDesktop -e --source winget --scope user };
if ($?) { winget install --id Microsoft.VisualStudioCode -e --source winget --scope user };
if ($?) { winget install --id Obsidian.Obsidian -e --source winget --scope user };
if ($?) { winget install --id SlackTechnologies.Slack -e --source winget --scope user };
```

#### Optional

```powershell

if ($?) { winget install --id BurntSushi.ripgrep.MSVC -e --source winget --scope user }; # rigrep for Neovim 
```

### Setup PowerShell

#### Add Custom Theme

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

#### Install PowerShell Modules

```powershell
notepad.exe .\PowerShellInstallModules.ps1
```

```powershell
Install-Module -Name posh-git -Scope CurrentUser;
if ($?) { Install-Module -Name z -Scope CurrentUser };
if ($?) { Install-Module -Name PSReadLine -Scope CurrentUser };
if ($?) { Install-Module -Name Terminal-Icons -Scope CurrentUser };
```

##### Check Modules path

```powershell
Get-Module -ListAvailable -Name {Module Name} | Select-Object -Property Path
```

#### Add Profile

##### Types

- Current User, Current Host - `$PROFILE`
- Current User, Current Host - `$PROFILE.CurrentUserCurrentHost`
- Current User, All Hosts - `$PROFILE.CurrentUserAllHosts`
- All Users, Current Host - `$PROFILE.AllUsersCurrentHost`
- All Users, All Hosts - `$PROFILE.AllUsersAllHosts`

##### Create Profile

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

##### Edit Profile

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
  Remove-Item -Path $env:TEMP\* -Recurse -ErrorAction SilentlyContinue
}

Function Which ($command) {
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
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\z\1.1.13\z.psd1
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\Terminal-Icons\0.11.0\Terminal-Icons.psd1
Import-Module -Name $env:USERPROFILE\Documents\PowerShell\Modules\posh-git\1.1.0\posh-git.psd1

## Setup PSReadLineOption

Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadlineKeyHandler -Chord 'Ctrl+d' -Function DeleteChar
Set-PSReadLineKeyHandler -Chord 'Ctrl+f' -Function ForwardWord
Set-PSReadLineKeyHandler -Chord 'Enter' -Function ValidateAndAcceptLine
```

## Install PotPlayer

https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe
