# ElementaryWSL
ElementaryOS on WSL2 (Windows 10 FCU or later) based on [wsldl](https://github.com/yuk7/wsldl).

[![Screenshot-2021-08-16-134253.png](https://i.postimg.cc/9ftx8cN8/Screenshot-2021-08-16-134253.png)](https://postimg.cc/06jdr1vD)
[![Github All Releases](https://img.shields.io/github/downloads/sileshn/ElementaryWSL/total.svg?style=flat-square)](https://github.com/sileshn/ElementaryWSL/releases)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com) [![License](https://img.shields.io/github/license/sileshn/ElementaryWSL.svg?style=flat-square)](https://github.com/sileshn/ElementaryWSL/blob/main/LICENSE)

## Important information
ElementaryWSL includes a wsl.conf file which only has section headers. Users can use this to configure the distro to their liking. You can read more about wsl.conf and its configuration settings [here](https://docs.microsoft.com/en-us/windows/wsl/wsl-config).

## Requirements
* For x64 systems: Version 1903 or higher, with Build 18362 or higher.
* For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
* Builds lower than 18362 do not support WSL 2.
* Enable Windows Subsystem for Linux feature.
```cmd
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
* Enable Virtual Machine feature
```cmd
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
* Download and install the latest Linux kernel update package from [here](https://www.catalog.update.microsoft.com/Search.aspx?q=wsl). Its a cab file. Open and extract the exe file within using 7zip/winzip/winrar.

For more details, check [this](https://docs.microsoft.com/en-us/windows/wsl/install-win10) microsoft document.

## Install
1. [Download](https://github.com/sileshn/ElementaryWSL/releases/latest) installer zip
2. Extract all files in zip file to same directory
3. Run Elementary.exe to Extract rootfs and Register to WSL

**Note:**
Exe filename is using the instance name to register. If you rename it you can register with a diffrent name and have multiple installs.

If you want to use WSL2 after install, convert it with the following command.
```dos
wsl --set-version Elementary 2
```

You can also set wsl2 as default. Use the command below before running Elementary.exe.
```dos
wsl --set-default-version 2
```

## How-to-Use(for Installed Instance)
#### exe Usage
```
Usage :
    <no args>
      - Open a new shell with your default settings.

    run <command line>
      - Run the given command line in that instance. Inherit current directory.

    runp <command line (includes windows path)>
      - Run the given command line in that instance after converting its path.

    config [setting [value]]
      - `--default-user <user>`: Set the default user of this instance to <user>.
      - `--default-uid <uid>`: Set the default user uid of this instance to <uid>.
      - `--append-path <true|false>`: Switch of Append Windows PATH to $PATH
      - `--mount-drive <true|false>`: Switch of Mount drives
      - `--default-term <default|wt|flute>`: Set default type of terminal window.

    get [setting]
      - `--default-uid`: Get the default user uid in this instance.
      - `--append-path`: Get true/false status of Append Windows PATH to $PATH.
      - `--mount-drive`: Get true/false status of Mount drives.
      - `--wsl-version`: Get the version os the WSL (1/2) of this instance.
      - `--default-term`: Get Default Terminal type of this instance launcher.
      - `--lxguid`: Get WSL GUID key for this instance.

    backup [contents]
      - `--tar`: Output backup.tar to the current directory.
      - `--reg`: Output settings registry file to the current directory
	  - `--tgz`: Output backup.tar.gz to the current directory.
      - `--vhdx`: Output backup.ext4.vhdx to the current directory.
      - `--vhdxgz`: Output backup.ext4.vhdx.gz to the current directory.

    clean
      - Uninstall that instance.

    help
      - Print this usage message.
```

#### Just Run exe
```cmd
>{InstanceName}.exe
[root@PC-NAME user]#
```

#### Run with command line
```cmd
>{InstanceName}.exe run uname -r
4.4.0-43-Microsoft
```

#### Run with command line with path translation
```cmd
>{InstanceName}.exe runp echo C:\Windows\System32\cmd.exe
/mnt/c/Windows/System32/cmd.exe
```

#### Change Default User(id command required)
```cmd
>{InstanceName}.exe config --default-user user

>{InstanceName}.exe
[user@PC-NAME dir]$
```

#### Set "Windows Terminal" as default terminal
```cmd
>{InstanceName}.exe config --default-term wt
```

## How to setup

ElementaryWSL will ask you to create a new user during its first run. If you chose to create a new user during initial setup, the steps below are not required unless you want to create additional users.
```dos
passwd
useradd -m -s /bin/bash <username>
passwd <username>
exit
```

You can set the user you created as default user using 2 methods.

Open Elementary.exe, run the following command (replace username with the actual username you created).
```dos
sed -i '/\[user\]/a default = username' /etc/wsl.conf
```

Shutdown and restart the distro (this step is important).

(or)

Execute the command below in a windows cmd terminal from the directory where Elementary.exe is installed.
```dos
>Elementary.exe config --default-user <username>
```

## How to uninstall instance
```dos
>Elementary.exe clean

```

## How to backup instance
export to backup.tar.gz
```cmd
>Elementary.exe backup --tgz
```
export to backup.ext4.vhdx.gz
```cmd
>Elementary.exe backup --vhdxgz
```

## How to restore instance

There are 2 ways to do it. 

Rename the backup to rootfs.tar.gz and run Elementary.exe

(or)

.tar(.gz)
```cmd
>Elementary.exe install backup.tar.gz
```
.ext4.vhdx(.gz)
```cmd
>Elementary.exe install backup.ext4.vhdx.gz
```

You may need to run the command below in some circumstances.
```cmd
>Elementary.exe --default-uid 1000
```

## How to build

#### Prerequisites

Docker, tar, zip, unzip, bsdtar need to be installed.

```dos
git clone git@gitlab.com:sileshn/ElementaryWSL.git
cd ElementaryWSL
make

```
Copy the ElementaryWSL.zip file to a safe location and run the command below to clean.
```dos
make clean

```

## How to run docker in ElementaryWSL without using docker desktop.

Delete older versions of docker if installed.
```dos
sudo apt-get remove docker docker-engine docker.io containerd runc

```

Execute the commands below to install docker.
```dos
sudo apt -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(. /etc/os-release; echo "$UBUNTU_CODENAME") stable"
sudo apt update
sudo apt install docker-ce
sudo usermod -aG docker $USER
```

Follow [this](https://blog.nillsf.com/index.php/2020/06/29/how-to-automatically-start-the-docker-daemon-on-wsl2/) blog post for further details on how to set it up.

[![Screenshot-2021-01-27-175029.png](https://i.postimg.cc/Z5vGPXwn/Screenshot-2021-01-27-175029.png)](https://postimg.cc/fVZqDqnQ)