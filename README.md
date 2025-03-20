# Setup Windows

## Set preference for specific network interface

Run Windows Terminal with Windows PowerShell as Administrator from Windows Termanial and execute commands:

```powershell
Get-NetIPInterface -AddressFamily IPv4
```

```powershell
Set-NetIPInterface -InterfaceIndex <Specify an index> -InterfaceMetric <Specify a metric>
```

> [!NOTE]
> [`InterfaceMetric`](https://learn.microsoft.com/en-us/powershell/module/nettcpip/set-netipinterface?view=windowsserver2022-ps) - Specifies a metric for an IP interface. When routes are chosen, the overall metric used to determine the preference is the sum of the route metric and the interface metric. Typically, the interface metric gives preference to a particular interface, such as using wired if both wired and wireless are available. This parameter value uses the value generated by AutomaticMetric by default. If this parameter value is set, then the AutomaticMetric parameter is automatically disabled.

```powershell
Restart-NetAdapter -InterfaceAlias "Specify an IneterfaceAlias"
```

Close Windows Terminal.

## Install PowerShell

Run Windows Terminal with Windows PowerShell and update system apps: 

```powershell
winget update --id Microsoft.WindowsTerminal -e --accept-source-agreements --source winget; if ($?) { winget update --id Microsoft.AppInstaller -e --source winget };
```

Restart Windows Terminal and install Microsoft PowerShell:

```powershell
winget install --id Microsoft.PowerShell -e --source winget
```

Restart Terminal again and setup default profile as PowerShell.

## Install apps

Run Windows Terminal as Administrator and create list of apps. My current list:

```powershell
notepad.exe .\PowerShellInstall_Winget_Machine.ps1
```

```ps1
#
## Windows PowerShell
### Winget's apps installation 
#

winget install --id Git.Git -e --source winget;
if ($?) { winget install --id Microsoft.VCRedist.2015+.x64 -e --source winget };
if ($?) { winget install --id Skillbrains.Lightshot -e --source winget }; # Screaanshot app
if ($?) { winget install --id qBittorrent.qBittorrent -e --source winget }; # Bittorrent client
if ($?) { winget install --id Neovim.Neovim -e --source winget }; # Neovim is a refactor, and sometimes redactor, in the tradition of Vim
if ($?) { winget install --id Mozilla.Firefox --source winget };
if ($?) { winget install --id Yandex.Browser -e --source winget };
if ($?) { winget install --id 7zip.7zip -e --source winget };
if ($?) { winget install --id wez.wezterm -e --source winget };
if ($?) { winget install --id DEVCOM.JetBrainsMonoNerdFont -e --source winget };
if ($?) { winget install --id Mozilla.Thunderbird -e --source winget };
if ($?) { winget install --id gerardog.gsudo -e --source winget }; # sudo for Powershell
if ($?) { winget install --id Bitwarden.Bitwarden -e --source winget }; # Password manager
if ($?) { winget install --id Telegram.TelegramDesktop -e --source winget };
if ($?) { winget install --id Microsoft.VisualStudioCode -e --source winget };
if ($?) { winget install --id Obsidian.Obsidian -e --source winget };
```

Execute PowerShell script:

```powershell
.\PowerShellInstall_Winget_Machine.ps1
```

## Enable Hyper-V and WSL

Run Terminal as Administrator to enable WSL and Hyper-V:

```powershell
wsl --install
```

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

After reboot install OS Linux into WSL:

```powershell
wsl --install
```

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

### Add profile

#### Types

- Current User, Current Host - `$PROFILE`, `$PROFILE.CurrentUserCurrentHost`
- Current User, All Hosts - `$PROFILE.CurrentUserAllHosts`
- All Users, Current Host - `$PROFILE.AllUsersCurrentHost`
- All Users, All Hosts - `$PROFILE.AllUsersAllHosts`

#### Create profile

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

#### Edit profile

```powershell
notepad.exe $PROFILE
```

```ps1
#
## Windows PowerShell
### PowerShell User Profile
#

### Oh My Posh init
#oh-my-posh init pwsh --config $env:USERPROFILE\.ohmyposh\zen.toml | Invoke-Expression

### Starship
$ENV:STARSHIP_CONFIG = "$env:USERPROFILE\starship.toml"
function Invoke-Starship-PreCommand {
  $starship_title = Split-Path -Path $pwd -Leaf
  $host.ui.RawUI.WindowTitle = "$starship_title"
}
Invoke-Expression (&starship init powershell)


### Alias
Set-Alias -Name vim -Value "$env:ProgramFiles\Neovim\bin\nvim.exe"
Set-Alias -Name cle -Value Clear-Host
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name cln -Value CleanTemp
Set-Alias -Name touch -Value New-Item
Set-Alias -Name gst -Value GitStatus
Set-Alias -Name ua -Value UpdateAll
Set-Alias -Name winin -Value WingetInstall
Set-Alias -Name winun -Value WingetUninstall
Set-Alias -Name winsr -Value WingetSearch
Set-Alias -Name gsh -Value GitPush
Set-Alias -Name gll -Value GitPull
Set-Alias -Name hosts -Value ChangeHosts
Set-Alias -Name sudo -Value "C:\Program Files\gsudo\Current\gsudo.exe"
Set-Alias -Name pubkey -Value SSHPubkey
Set-Alias -Name g -Value Git.exe
Set-Alias -Name gshss -Value GitPushSS

### Functions
Function WingetInstall {
  sudo winget install --source winget --id $(Read-Host -Prompt 'Enter Package Name') -e
}

Function WingetUninstall {
  sudo winget uninstall --id $(Read-Host -Prompt 'Enter Package Name') -e
}

Function WingetSearch {
  winget search $(Read-Host -Prompt 'Enter Package Name')
}

Function SSHPubkey {
  Get-Content $env:USERPROFILE\.ssh\id_rsa.pub | ssh $(Read-Host -Prompt 'Enter user@ip-address') 'cat >> $HOME/.ssh/authorized_keys'
}

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
  sudo winget.exe upgrade --recurse --source winget --verbose --include-unknown --ignore-security-hash
}

Function GitPush {
  git.exe add .; if ($?) { git.exe commit -am "Work with repository" }; if ($?) { git.exe push }; if ($?) { Clear-Host }
}
  
### Import-Module
Import-Module -Name PSReadLine
Import-Module -Name z
Import-Module -Name Terminal-Icons
Import-Module -Name posh-git

### Setup PSReadLineOption
Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -HistoryNoDuplicates:$True
Set-PSReadLineOption -ShowToolTips:$True
#Get KeyHandlers 'Get-PSReadLineKeyHandler -Bound -Unbound'
Set-PSReadlineKeyHandler -Chord Ctrl+d -Function DeleteChar
Set-PSReadLineKeyHandler -Chord Ctrl+f -Function ForwardWord
Set-PSReadLineKeyHandler -Chord Enter -Function ValidateAndAcceptLine
```

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

Restart Terminal and execute the command:

```powershell
sudo winget settings --enable InstallerHashOverride
```

## Install Brave Browser

```powershell
Invoke-WebRequest https://laptop-updates.brave.com/latest/winx64 -OutFile $env:USERPROFILE\Downloads; if ($?) { Start-Process "$env:USERPROFILE\Downloads\BraveBrowserSetup.exe" };
```

## Install PotPlayer

Direct link:

https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe

Powershell commands:

```powershell
Invoke-WebRequest https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe -OutFile $env:USERPROFILE\Downloads;
if ($?) { Start-Process "$env:USERPROFILE\Downloads\PotPlayerSetup64.exe" "powershell" -Verb RunAs };
```

## Add SSH key-pair

Run Windows Terminal as Administrator to start SSH-Agent:

```powershell
Get-Service -Name ssh-agent | Set-Service -StartupType Automatic;
if ($?) { Start-Service ssh-agent };
```

Open Windows Terminal as a current User, create SSH key-pair and add it into SSH-Agent:

```powershell
ssh-keygen -t ed25519 -C "ivan.a.lobanov"
```

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

> [!TIP]
> Alternative - use a password manager's ssh-agent.
