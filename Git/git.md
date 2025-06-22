# Git - Version Control System
> The default VCS that is being used by 99% of the world.

It is a terminal application that makes it possible to contribute and share code.
## Setup

**How to Install**  
1. *Windows* --> Get the installer from [https://git-scm.com/downloads]. Don't install via package manager (winget/choco/scoop) as you'd have to manually configure it.
2. *Linux* --> Use the package manager.
	1. Ubuntu/Debian: `sudo apt install git`
	2. Fedora/CentOS: `sudo dnf install git`
	3. Arch: `sudo pacman -S git`
3. *MacOS* --> Use homebrew `brew install git`

**Verify Installation**
```shell
git --version
```

**Set a Global Username and Email**
```shell
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

**Check Full Config**
```shell
git config --list
```
