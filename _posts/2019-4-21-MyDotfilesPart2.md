---
title: "My Dotfiles - Part 2: Arch Linux"
author: Peer Rheinboldt
categories:
  - Blog
tags:
  - Dotfiles
  - Dev. Environment
---

**Warning:** As I'm creating my [dotfiles](https://github.com/peerlator/dotfiles-new) while writing this series, the dotfiles aren't completely finished. So please don't install them yet  
{: .notice--warning}

## What's this?
In this blog post series I will tell you about how I created my dotfiles and how you can create yours. I will tell you about what I use, how I use and configure these programs and how I have made changing the configurations easy. The structure of the following blogposts will be:
{% include dotfiles-table-of-contents.md %}

## What's Arch Linux
Arch Linux is a Linux distribution, just like Ubuntu or Fedora. What makes Arch Linux "special" is the minimalism of the distro, when you install it. In comparison to Ubuntu for example, you don't get a preinstalled GUI or web browser. Almost everything has to be installed manually. As you only start with a bash interface, you should already know the basics of the terminal. If you don't know about the terminal but still want to use Arch, I recommend that you start out with an distribution, that is based on Arch, like Manjaro. You could most probably also follow along with this series with your distro of choice, however some steps may be more difficult. The reasons why I chose Arch are:
- Complete Customization
- No bloatware
- The Package Manager and the AUR (Arch User Repository)
- The Arch Wiki
- Rolling Release = No system upgrades

Some of the reasons, why you should not choose Arch:
- Steep learning curve
- Can sometimes break, because of the rolling releases

## Installation
The installation process of Arch Linux can be extremely difficult. However this is already pretty well documented. The [article](https://wiki.archlinux.org/index.php/Installation_guide) in the Arch Wiki is what I would always have nearby when installing the OS. However personally I needed to watch a tutorial on the first few installs, as I could see what someone else did. Here are some useful links:
- [Luke Smith's Installation](https://www.youtube.com/watch?v=4PBqpX0_UOc)
- [The installation process in only 13 minute](https://www.youtube.com/watch?v=Wqh9AQt3nho)
- [A more beginner-friendly article](https://www.fosslinux.com/7117/how-to-install-arch-linux-complete-guide.htm)
> Note some tutorials don't show you how to add a user. To continue with this article you will have to have a user with sudo permission. Simply run `pacman -S sudo` `useradd -m -G audio,wheel <USERNAME>`, `passwd <USERNAME>` ,`visudo` and uncomment the line `%wheel ALL=(ALL) ALL`. Now you can Continue
After you got Arch up and running continue with this post, where I will show you some of the programs I use, which I believe are almost part of the instalition process of Arch.

## Yay for AUR
The default package manager of Arch is called "pacman" (not to be confused with the game character). With pacman you can install many packages, however not the ones in the AUR (You can, however it  is a little difficult). This is where [yay](https://github.com/Jguer/yay) comes in handy. Yay is short for Yet Another Yogurt. Yay sadly can't be installed via pacman so you will have to do so via source. Simply cd yourself into a directory of choice and run the following commands:
```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

You use yay just like you would use pacman. Simply substitute the pacman with yay and delete the sudo. Now you are good to go to install packages from the AUR.

## Starting the dotfiles
At this point I decided to start with my dotfiles. From this point on I want my dotfiles to take over the installation. To start out simply create a `.dotfiles` folder in your home directory and create some files:
```
mkdir ~/.dotfiles
cd ~/.dotfiles
touch installation.sh
touch distro.sh
touch helpers.sh
touch packages.sh
touch special_installs.sh
``` 

If you don't know what the purpose of each of these files is, then check out [part 1](https://www.peerlator.com/2019/04/14/MyDotfilesPart1.html) of this series. Now our goal is to set up an extensible and easily modifiable system for installing the dotfiles. Start by editing the `install.sh` file with your editor of choice:
```shell
#!/bin/bash

find $HOME/.dotfiles/ -exec chmod +x {} \; # add executable permission to every file in this directory

. distro.sh # source the distro specific variables
. packages.sh # load the packages to install
. helpers.sh # load helper functions

echo_info "Updating system"
_update

echo_info "Installing packages..."
_install $PKGS

. special_installs # run special installation steps
```

Then edit the `distro.sh`:
```shell
#!/bin/bash

export PKGMN=yay # The packagemanager to use
export PKGOPT=(--needed --noconfirm) # The options passed when installing packages
export PKGU=(-Syu) # Updating options
export PKGI=(-S) # Installation options
```

Now we want to define the functions, which we need in our `helpers.sh` file. You may have noticed that the functions called are *_install*, *_update*, *_echo_info*. For future installscripts we will also define *_echo_warning*, *_echo_error* and *_echo_success*. Add the following code to your `helpers.sh` file:
```shell
#!/bin/bash
blue=$(tput setaf 4)
green=$(tput setaf 2)
red=$(tput setaf 1)
yellow=$(tput setaf 3)
normal=$(tput sgr0)

function echo_error() {
  printf "[${red}!!${normal}] $1 \n"
}

function echo_warning() {
  printf "[${yellow}/\${normal}] $1 \n"
}

function echo_success() {
  printf "[${green}OK${normal}] $1 \n"
}

function echo_info() {
  printf "[${blue}..${normal}] $1 \n"
}

function _install() {
    echo_info "Installing $1..."
    "$PKGMN" "$PKGI" "$1" "${PKGOPT[@]}"
    echo_success "Installed $1"
}

function _update() {
    echo_info "Updating $1"
    "$PKGMN" "$PKGU" "$1" "${PKGOPT[@]}"
    echo_success "Updated $1"
}
```

Now that we have all this we can simply add the packages to the `packages.sh` file, which will have the form:
```shell
export PKGS=(<PACKAGE_1> <PACKAGE_2> ...)
```
As we can see these packages are installed in the `install.sh` file. Now we have a extensible installation system, where we can install programs with ease. In future posts we will discuss how we can store configuration files and sync them.

## NetworkManager for Internet Connections
For managing my Wifi and Ethernet connections I have recently discovered a program called [NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager). Luckily the installation of this program is extremely simple and with our installation scripts even simpler. Simply add `networkmanager` to $PKGS and append `special_installs.sh` with the following 2 lines of code:
```shell
# Networkmanager
sudo systemctl enable NetworkManager.service
```

When this command is run, Networkmanager is started on boot and you won't have to start it manually. Using networkmanager luckily isn't difficult and you can use two interfaces:
- nmtui is a curses based gui, to edit your connections
- nmcli is a command line interface
Check out the Arch Wiki, to get to know how to use these interfaces and in general, whenever you want to know something about a program or want to know how to solve a problem, simply google it. Often someone already had the same problem and you can easily fix the issue. 

## Pulseaudio for Audio
I personally use pulseaudio for my audio. Additionally I installed an extension for alsa and bluetooth and use pulsemixer, to change volume, output source, ... Again, if you want to know more about each of the programs, just google it. To install my audio "setup" simply add the following packages to $PKGS: `pulseaudio`, `pulseaudio-alsa`, `pulseaudio-bluetooth` and `pulsemixer`. As we can see our system for installing packages can easily be extended by just adding the package to a file and when you have many many packages to install, like me, it is extremely helpful to not always have to type out `yay -S <PACKAGE_1> --needed --noconfirm`.

## Bluez for Bluetooth
For Bluetooth connections I use [Bluez](https://wiki.archlinux.org/index.php/Bluetooth) and bluetoothctl. I guess by now you know what to do when you want to find out more. To install these programs simply add `bluez` and `bluez-utils` to $PKGS and add the following lines to `special_installs.sh`:
```shell
# Bluez
sudo systemctl enable bluetooth.service
```

With this my overview of my Arch Linux installation is finished. After finishing these steps, you should have a functionable, but not really usable installation of Arch Linux. In the next blog post we will discover how we can make this installation usable by installing a desktop environment. To wrap it all up here is another helpful article on the Arch wiki, which is about what they recommend after installing Arch: [https://wiki.archlinux.org/index.php/General_recommendations](https://wiki.archlinux.org/index.php/General_recommendations)
