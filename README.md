# Setup Windows

## Set preference for specific network interface

```powershell
Get-NetIPInterface
```

```powershell
Set-NetIPInterface -InterfaceIndex <Specify an index> -InterfaceMetric <Specify a metric>
```

[`InterfaceMetric`](https://learn.microsoft.com/en-us/powershell/module/nettcpip/set-netipinterface?view=windowsserver2022-ps) - Specifies a metric for an IP interface. When routes are chosen, the overall metric used to determine the preference is the sum of the route metric and the interface metric. Typically, the interface metric gives preference to a particular interface, such as using wired if both wired and wireless are available. This parameter value uses the value generated by AutomaticMetric by default. If this parameter value is set, then the AutomaticMetric parameter is automatically disabled.

```powershell
Restart-NetAdapter -InterfaceAlias "Specify an IneterfaceAlias"
```

## Install PowerShell

https://github.com/PowerShell/PowerShell/releases

## Update apps

Update through Microsoft Store next apps:

- Terminal.
- App Installer.

## Install apps

```powershell
notepad.exe .\PowerShellInstall_Winget_Machine.ps1
```

```powershell
#
## Windows PowerShell
### Winget's apps installation 
#

winget install --id Git.Git -e --source winget;
if ($?) { winget install --id Microsoft.VCRedist.2015+.x64 -e --source winget };
if ($?) { winget install --id Skillbrains.Lightshot -e --source winget };
if ($?) { winget install --id qBittorrent.qBittorrent -e --source winget };
if ($?) { winget install --id Google.Chrome -e --source winget };
if ($?) { winget install --id Neovim.Neovim -e --source winget }; # Neovim is a refactor, and sometimes redactor, in the tradition of Vim
if ($?) { winget install --id 7zip.7zip -e --source winget };
if ($?) { winget install --id Mozilla.Thunderbird -e --source winget };
if ($?) { winget install --id DEVCOM.JetBrainsMonoNerdFont -e --source winget };
if ($?) { winget install --id ALCPU.CoreTemp --source winget }; # Program to monitor processor temperature and other vital information
if ($?) { winget install --id FxSoundLLC.FxSound --source winget }; # Equalizer for Windows
if ($?) { winget install --id Bitwarden.Bitwarden -e --source winget --scope user };
if ($?) { winget install --id Telegram.TelegramDesktop -e --source winget --scope user };
if ($?) { winget install --id Microsoft.VisualStudioCode -e --source winget --scope user };
if ($?) { winget install --id Obsidian.Obsidian -e --source winget --scope user };
if ($?) { winget install --id Yandex.Browser -e --source winget --scope user };
```

```powershell
.\PowerShellInstall_Winget_Machine.ps1
```

## Enable Hyper-V

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

## Install JetBrainsMono nerd font

https://github.com/ryanoasis/nerd-fonts/releases

## Install PowerShell modules

```powershell
notepad.exe .\PowerShellInstall_Modules.ps1
```

```powershell
#
## Windows PowerShell
### PowerShell modules installation
#

Install-Module -Name posh-git -Scope CurrentUser;
if ($?) { Install-Module -Name z -Scope CurrentUser };
if ($?) { Install-Module -Name PSReadLine -Scope CurrentUser };
if ($?) { Install-Module -Name Terminal-Icons -Scope CurrentUser };
```

```powershell
.\PowerShellInstall_Modules.ps1
```

## Delete installation files

```powershell
Remove-Item $env:USERPROFILE\PowerShellInstall_*.ps1
```

## Setup PowerShell

### Add custom theme "One Half Dark Edited"

Insert inside brackets into "schemes":

```json
{
	"background": "#2C2845",
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
}
```

### Add profile

#### Types

- Current User, Current Host - `$PROFILE`
- Current User, Current Host - `$PROFILE.CurrentUserCurrentHost`
- Current User, All Hosts - `$PROFILE.CurrentUserAllHosts`
- All Users, Current Host - `$PROFILE.AllUsersCurrentHost`
- All Users, All Hosts - `$PROFILE.AllUsersAllHosts`

#### Create profile `$PROFILE`

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

#### Edit profile

```powershell
notepad.exe $PROFILE
```

```powershell
#
## Windows PowerShell
### PowerShell User Profile
#

##########
#### Alias

Set-Alias -Name vim -Value $env:ProgramFiles\Neovim\bin\nvim.exe
Set-Alias -Name cle -Value Clear-Host
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name cln -Value CleanTemp 
Set-Alias -Name touch -Value New-Item
Set-Alias -Name sudo -Value $env:LOCALAPPDATA\Microsoft\WinGet\Packages\gerardog.gsudo_Microsoft.Winget.Source_8wekyb3d8bbwe\x64\gsudo.exe
Set-Alias -Name ua -Value UpdateAll
Set-Alias -Name gst -Value GitStatus
Set-Alias -Name gsh -Value GitPush
Set-Alias -Name gll -Value GitPull
Set-Alias -Name hosts -Value ChangeHosts

##############
#### Functions

Function ChangeHosts {
  sudo nvim $env:SystemRoot\System32\drivers\etc\hosts
}

Function GitPull {
  git.exe pull; if ($?) { Clear-Host }
}

Function GitStatus {
  git.exe status
}

Function CleanTemp {
  Remove-Item -Path $env:TEMP\* -Recurse -ErrorAction SilentlyContinue
}

Function Which ($command) {
  Get-Command -Name $command -ErrorAction SilentlyContinue |
  Select-Object -ExpandProperty Path -ErrorAction SilentlyContinue
}

Function UpdateAll {
  winget.exe upgrade --recurse --source winget --verbose
}

Function GitPush {
  git.exe add .; if ($?) { git.exe commit -am "." }; if ($?) { git.exe push }; if ($?) { Clear-Host }
}

##################  
#### Import-Module

Import-Module -Name PSReadLine
Import-Module -Name z
Import-Module -Name Terminal-Icons
Import-Module -Name posh-git

###########################
#### Setup PSReadLineOption

Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadlineKeyHandler -Chord 'Ctrl+d' -Function DeleteChar
Set-PSReadLineKeyHandler -Chord 'Ctrl+f' -Function ForwardWord
Set-PSReadLineKeyHandler -Chord 'Enter' -Function ValidateAndAcceptLine
```

## Install PotPlayer

https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe
