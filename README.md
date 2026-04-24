# Setup Windows

## Install PowerShell

1. Run windows terminal with powershell and update system apps: 

```powershell
winget update --id Microsoft.WindowsTerminal -e --accept-source-agreements --source winget; if ($?) { winget update --id Microsoft.AppInstaller -e --source winget };
```

2. Restart Windows Terminal and install Microsoft opensource PowerShell:

```powershell
winget install --id Microsoft.PowerShell -e --source winget
```

3. Restart Terminal again and setup default profile as `powershell`.

## Install apps

1. Run Windows Terminal as Administrator and create a list of apps:

```powershell
notepad.exe .\PowerShellInstall_Winget_Machine.ps1
```

2. Add apps for your need, here my list of choice:

```ps1
winget install --id Git.Git -e --source winget;
if ($?) { winget install --id Microsoft.VCRedist.2015+.x64 -e --source winget };
if ($?) { winget install --id Skillbrains.Lightshot -e --source winget };
if ($?) { winget install --id qBittorrent.qBittorrent -e --source winget };
if ($?) { winget install --id Neovim.Neovim -e --source winget };
if ($?) { winget install --id 7zip.7zip -e --source winget };
if ($?) { winget install --id DEVCOM.JetBrainsMonoNerdFont -e --source winget };
if ($?) { winget install --id Mozilla.Thunderbird -e --source winget };
if ($?) { winget install --id gerardog.gsudo -e --source winget };
if ($?) { winget install --id Bitwarden.Bitwarden -e --source winget };
if ($?) { winget install --id Telegram.TelegramDesktop -e --source winget };
if ($?) { winget install --id Microsoft.VisualStudioCode -e --source winget };
if ($?) { winget install --id Obsidian.Obsidian -e --source winget };
```

3. Install listed apps:

```powershell
.\PowerShellInstall_Winget_Machine.ps1
```

## Enable Hyper-V and WSL

1. Run Terminal as Administrator to enable WSL and Hyper-V:

```powershell
wsl --install
```

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

2. After reboot install OS Linux into WSL:

```powershell
wsl --install
```

## Install PowerShell's modules

1. Run Windows Terminal and create a list of extensions:

```powershell
notepad.exe .\PowerShellInstall_Modules.ps1
```

2. Add extensions for your need, here my list of choice:

```powershell
Install-Module -Name posh-git -Scope CurrentUser;
if ($?) { Install-Module -Name z -Scope CurrentUser };
if ($?) { Install-Module -Name PSReadLine -Scope CurrentUser };
if ($?) { Install-Module -Name Terminal-Icons -Scope CurrentUser };
```

3. Install listed extensions:

```powershell
.\PowerShellInstall_Modules.ps1
```

4. Delete installation scripts:

```powershell
Remove-Item $env:USERPROFILE\PowerShellInstall_*.ps1
```

## Setup PowerShell

### Create profile

#### Types

- Current User, Current Host - `$PROFILE`, `$PROFILE.CurrentUserCurrentHost`
- Current User, All Hosts - `$PROFILE.CurrentUserAllHosts`
- All Users, Current Host - `$PROFILE.AllUsersCurrentHost`
- All Users, All Hosts - `$PROFILE.AllUsersAllHosts`

1. Create profile for `Current User, Current Host`:

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

2. Open created profile:

```powershell
notepad.exe $PROFILE
```

3. Add settings for your need, here my list of choice:

```ps1
# import ps modules
Import-Module -Name PSReadLine
Import-Module -Name z
Import-Module -Name Terminal-Icons
Import-Module -Name posh-git

# functions
Function SingBoxON {
  Start-Process -FilePath "sing-box.exe" -ArgumentList "-C", "C:\Users\Ivan\Projects\Sing-box", "run" -NoNewWindow
}

Function SingBoxOFF {
  Stop-Process -Name "sing-box"
}

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

  
# setup PSReadLineOption
Set-PSReadLineOption -EditMode Emacs
Set-PSReadLineOption -PredictionSource HistoryAndPlugin
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -HistoryNoDuplicates:$True
Set-PSReadLineOption -ShowToolTips:$True
#Get KeyHandlers 'Get-PSReadLineKeyHandler -Bound -Unbound'
Set-PSReadLineKeyHandler -Key F1 -ScriptBlock {
    [Console]::Write("`e[H`e[2J`e[3J")
    [Microsoft.PowerShell.PSConsoleReadLine]::AcceptLine()
}
Set-PSReadLineKeyHandler -Chord Ctrl+d -Function DeleteChar
Set-PSReadLineKeyHandler -Chord Ctrl+f -Function ForwardWord
Set-PSReadLineKeyHandler -Chord Enter -Function ValidateAndAcceptLine

# aliases
Set-Alias -Name sbon -Value SingBoxON
Set-Alias -Name sboff -Value SingBoxOFF
Set-Alias -Name vim -Value "$env:ProgramFiles\Neovim\bin\nvim.exe"
Set-Alias -Name v -Value "$env:ProgramFiles\Neovim\bin\nvim.exe"
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

# starship
$ENV:STARSHIP_CONFIG = "$env:USERPROFILE\starship.toml"
Invoke-Expression (&starship init powershell)
```

> [!IMPORTANT]
> Do the command below before use winget upgrade alias.

4. Restart Terminal and execute the command:

```powershell
sudo winget settings --enable InstallerHashOverride
```

## Install PotPlayer (optinal)

> [!INFO]
> This is my choice of video player for Windows.
> Follow your personal references!

Direct link: https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe

Powershell commands for instalaltion:

```powershell
Invoke-WebRequest https://t1.daumcdn.net/potplayer/PotPlayer/Version/Latest/PotPlayerSetup64.exe -OutFile $env:USERPROFILE\Downloads; if ($?) { Start-Process "$env:USERPROFILE\Downloads\PotPlayerSetup64.exe" "powershell" -Verb RunAs };
```
