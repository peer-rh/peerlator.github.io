---
title: "My Dotfiles - Part 3: i3wm & Polybar"
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

## What's i3wm
i3wm is a window manager, just like KDE, GNOME and Openbox. A window manager is basically a program with which you can manage the placement of windows. However i3wm is different to most of the mainstream window managers. i3wm is a tiling window manager, which can be controlled through only the keyboard. A tiling window manager has the advantage, that there is basically no screen space wasted as it the application windows maximise to the space on the screen, which is not used by other applications:
![](https://opensource.com/sites/default/files/uploads/i3_screenshot.png)
*https://opensource.com/sites/default/files/uploads/i3_screenshot.png*

Pros of i3wm:
- Complete customization
- Minimal screen space wasted
- High efficiency
- Extremely beautiful [setups](https://www.reddit.com/r/unixporn/search?q=i3&restrict_sr=1)
- Floating support

Cons:
- Takes time to get used to
- Windows made for floating are often loaded into tiling, which can look ugly (For Example the pop up login windows in chrome)

I recommend, that you go ahead and learn a little about i3wm. Here are some resources
- [Homepage](https://i3wm.org/)
- [i3-gaps: i3 with more features]()
- [i3wm series from Code Cast](https://www.youtube.com/watch?v=j1I63wGcvU4)

## How we install and configure i3wm
```
dotfiles
├── i3
│   ├── install.sh (The installation file)
│   ├── reload.sh (Script to concatenate the configuration files)
│   ├── general-settings.i3
│   ├── general_keyboard_shortcuts.i3
│   ├── resize.i3
│   ├── startups.i3
│   ├── workspaces.i3
│   └── colors.i3
├── distro.sh
├── packages.sh
├── helpers.sh
├── special_installs.sh
└── install.sh
```
As we can see we now have the install.sh files, which means that we have to add a function to `helpers.sh` and execute in `install.sh`. Append the following code to `helpers.sh`:
```shell
function _run_install_files() {
  for filename in $(echo $HOME/.dotfiles/**/install.sh | tr " " "\n")
  do
    source $filename
  done
}
```
and then call this function in the `install.sh` file (The one in the root directory). Now we will have to create the `i3/install.sh` file:
```shell
_install i3-gaps # better i3wm
_install xorg xorg-init
mkdir $HOME/.config/i3/
mkdir $HOME/.config/polybar/ # needed for the script to run
source i3/reload.sh
echo "exec i3" > $HOME/.xinitrc # start i3 by running startx
```
As you can see we have multiple `.i3` configuration files. However this is not the default method of configuring i3. The config is located in the `~/.config/i3/config` file. When configuring i3 the config file can get 200+ lines long. That's why I chose to simply concatenate the `.i3` files. When changing the configuration I have a keyboard shortcut to run the `reload.sh` script. We will simply run cat on all of these files:
```shell
echo > ~/.config/i3/config && # empty the current config file
for f in $HOME/.dotfiles/**/*.i3; do (cat "${f}"; echo) >> ~/.config/i3/config; done && # cat every file and add a new line every file and append everything to the config file
i3-msg restart # Restart the window manager
```
Now we have everything set up to configure i3wm. Let's start by learning about the basics

## Basics of i3wm's Configuration
The configuration of i3wm is luckily fairly simple once you get the trick. I recommend that you don't desperately try to memorize these commands, as you can easily search online for the problem. Luckily the i3 community is fairly big. Some of the basics are:

**Note:** The comments(`#`) can only be used at the beginning of lines and not at the end of a line, like in the examples
{: .notice}

Keybinding Configuration:
```
bindsym <keybinding>      <command>
Examples:                                               # The space between the keybinding and command aren't necessary
  bindsym	$mod+Return	    exec $term                    # $mod is short for Modifier key (Windows/Super) and $term is a variable set in the config file
  bindsym $mod+m          move workspace to output left # move let's you move a window to a different location
  bindsym XF86AudioStop   exec playerctl stop           # exec let's you execute a shell command
```
Variables:
```
set <variable name>       <value>
Examples:
  set $term 	            st                            # Sets $term to st
  set $mod	              Mod4                          # Sets $mod to the modifier key
  set $shutdown 	        sudo -A shutdown -h now       # You can also use variables as shortcuts for commands
With Xresources:
  set_from_resource <variable-name> <xresources-name> <default-value>
  set_from_resource $darkblack      color0	          #0000000
```

Modes:
```
# modes are "states", where different keybindings are used in comparison to the default mode
mode <mode-name> {
  <the keybindings>
}
bindsym <keybinding> mode <mode-name>
Example: See resize.i3
```

Exec:
```
exec <command> # execute command only when i3 is booted
exec_always <command> # execute command everytime i3 reloads (also boots)
Examples:
  exec_always --no-startup-id $HOME/.dotfiles/polybar/launch.sh # launch polybar everytime i3 reloads
  exec --no-startup-id compton # launch compton only when i3 boots
```

## My Configuration
As my config is split up into multiple files. Many things are self explanatory. With the knowledge you currently have you should be able to figure out most of the meanings of each of the configuration by yourself. I will just give you the file name and content:
```
# colors.i3
# Set Xresources Colors
set_from_resource	$darkblack 	  color0	#000000
set_from_resource	$black	 	    color8	#000000
set_from_resource	$darkred 	    color1	#ff0000
set_from_resource	$red	 	      color9	#ff0000
set_from_resource	$darkgreen 	  color2	#00ff00
set_from_resource	$green	 	    color10	#00ff00
set_from_resource	$darkyellow 	color3	#ffff00
set_from_resource	$yellow 	    color11	#ffff00
set_from_resource	$darkblue 	  color4	#0000ff
set_from_resource	$blue	 	      color12	#0000ff
set_from_resource	$darkmagenta 	color5	#ff00ff
set_from_resource	$magenta 	    color13	#ff00ff
set_from_resource	$darkcyan 	  color6	#00ffff
set_from_resource	$cyan	 	      color14	#00ffff
set_from_resource	$darkwhite 	  color7	#ffffff
set_from_resource	$white        color15	#ffffff
set			          $transparent		      #00000000
```

```
# general_keyboard_shortcuts.i3

# Basic Bindings
bindsym	$mod+Return		    exec $term
bindsym $mod+Shift+e 		exec prompt "Exit i3" "i3-msg exit"
bindsym Mod1+q			    [con_id="__focused__" instance="^(?!dropdown|pythonshell|conky-quickview|rangerdropdown).*$"] kill
bindsym $mod+Shift+r		exec $HOME/.dotfiles/i3/reload.sh
bindsym $mod+d			    exec rofi -show drun -padding 80 -bw 0 -lines 8 -width 100% -location 1 -show-icons 
bindsym $mod+Shift+x		exec i3lock-fancy -n -p
bindsym $mod+m			    move workspace to output left

# layout
bindsym $mod+s			    layout stacking
bindsym $mod+w			    layout tabbed
bindsym	$mod+e			    layout toggle split
bindsym $mod+f			    fullscreen toggle
bindsym $mod+v			    split toggle
bindsym $mod+space 		    focus mode_toggle
bindsym $mod+Shift+space	floating toggle

# change focus
bindsym $mod+h 			    focus left
bindsym $mod+j			    focus down
bindsym $mod+k 			    focus up
bindsym $mod+l		 	    focus right
bindsym $mod+Left 		    focus left
bindsym $mod+Down 		    focus down
bindsym $mod+Up 		    focus up
bindsym $mod+Right 		    focus right

# move focused window
bindsym $mod+Shift+h 		move left
bindsym $mod+Shift+j 		move down
bindsym $mod+Shift+k 		move up
bindsym $mod+Shift+l	 	move right
bindsym $mod+Shift+Left 	move left
bindsym $mod+Shift+Down 	move down
bindsym $mod+Shift+Up 		move up
bindsym $mod+Shift+Right 	move right

# floating move with mouse and $mod
floating_modifier $mod

# Media Controls
bindsym $mod+F2             exec playerctl play-pause
bindsym $mod+F1             exec playerctl previous
bindsym $mod+F3             exec playerctl next
bindsym XF86AudioNext       exec playerctl next
bindsym XF86AudioPrev       exec playerctl previous
bindsym XF86AudioPlay       exec playerctl play-pause
bindsym XF86AudioStop       exec playerctl stop

# Gaps
bindsym $mod+Mod1+plus      gaps inner current plus 5
bindsym $mod+Mod1+minus     gaps inner current minus 5

bindsym $mod+plus           gaps outer current plus 5
bindsym $mod+minus          gaps outer current minus 5

bindsym $mod+shift+plus     gaps inner current set 15; gaps outer current set 15
bindsym $mod+shift+minus    gaps inner current set 0; gaps outer current set 0

# Volume
bindsym $mod+F9                 exec pulsemixer --change-volume -5
bindsym $mod+F10                exec pulsemixer --change-volume +5
bindsym XF86AudioRaiseVolume    exec pulsemixer --change-volume +5
bindsym XF86AudioLowerVolume    exec pulsemixer --change-volume -5
bindsym XF86AudioMute           exec pulsemixer --mute
```

```
# general_settings.i3
# Font
font pango:'Fura Code Regular Nerd Font Complete Mono' 10

# Gaps
gaps inner 15
gaps outer 15

# General variables
set $term 	st
set $mod	Mod4
set $shutdown 	sudo -A shutdown -h now
set $reboot 	sudo -A reboot

#                       border              background         text                 indicator
client.focused          $darkblack          $darkblack         $white	            $darkred
client.unfocused        $darkblack          $darkblack         $darkwhite           $darkred
client.focused_inactive $darkblack 	        $darkblack	       $darkwhite	        $darkred
client.urgent           $darkred            $darkred 	       $white	            $darkred

for_window [class=".*"] border pixel 0
hide_edge_borders both
```

```
# resize.i3
mode "resize" {
    # These bindings trigger as soon as you enter the resize mode

    # Pressing left will shrink the window’s width.
    # Pressing right will grow the window’s width.
    # Pressing up will shrink the window’s height.
    # Pressing down will grow the window’s height.
    bindsym h resize shrink width 10 px or 10 ppt
    bindsym j resize grow height 10 px or 10 ppt
    bindsym k resize shrink height 10 px or 10 ppt
    bindsym l resize grow width 10 px or 10 ppt

    # same bindings, but for the arrow keys
    bindsym Left resize shrink width 10 px or 10 ppt
    bindsym Down resize grow height 10 px or 10 ppt
    bindsym Up resize shrink height 10 px or 10 ppt
    bindsym Right resize grow width 10 px or 10 ppt

    # back to normal: Enter or Escape or $mod+r
    bindsym Return mode "default"
    bindsym Escape mode "default"
    bindsym $mod+r mode "default"
}

bindsym $mod+r mode "resize"
```

```
# startups.i3
# All programs which should start at the beginning should be put here

exec_always --no-startup-id feh --bg-scale $HOME/.dotfiles/wallpapers/current.png

exec --no-startup-id compton

exec --no-startup-id dunst --mon 1

exec_always --no-startup-id $HOME/.dotfiles/polybar/launch.sh

exec --no-startup-id unclutter

exec xrandr --output HDMI-1 --auto --output HDMI-3 --auto --left-of HDMI-1
exec setxkbmap -layout de &
```

```
# workspaces.i3
# Define names for default workspaces for which we configure key bindings later on.
# We use variables to avoid repeating the names in multiple places.
set $ws1 "1"
set $ws2 "2"
set $ws3 "3"
set $ws4 "4"
set $ws5 "5"
set $ws6 "6"
set $ws7 "7"
set $ws8 "8"
set $ws9 "9"
set $ws10 "10"

# switch to workspace
bindsym $mod+1 workspace $ws1
bindsym $mod+2 workspace $ws2
bindsym $mod+3 workspace $ws3
bindsym $mod+4 workspace $ws4
bindsym $mod+5 workspace $ws5
bindsym $mod+6 workspace $ws6
bindsym $mod+7 workspace $ws7
bindsym $mod+8 workspace $ws8
bindsym $mod+9 workspace $ws9
bindsym $mod+0 workspace $ws10

# move focused container to workspace
bindsym $mod+Shift+1 move container to workspace $ws1
bindsym $mod+Shift+2 move container to workspace $ws2
bindsym $mod+Shift+3 move container to workspace $ws3
bindsym $mod+Shift+4 move container to workspace $ws4
bindsym $mod+Shift+5 move container to workspace $ws5
bindsym $mod+Shift+6 move container to workspace $ws6
bindsym $mod+Shift+7 move container to workspace $ws7
bindsym $mod+Shift+8 move container to workspace $ws8
bindsym $mod+Shift+9 move container to workspace $ws9
bindsym $mod+Shift+0 move container to workspace $ws10
```

These are my configurations. These commands may seem like a lot of commands to remember, however I tried to make them very intuitive and in addition, you will use most of them frequently. Now we have an usable system, however the system does not look very nice. One of the features of i3 is, that the status bar can be changed. I chose *polybar*

## What's polybar
[Polybar](https://polybar.github.io/) is a statusbar, which can be used in many desktop environments. Polybar supports multiple bars, which means you can have one at the bottom and one at the top (like me). The bars are then populated with so called *modules*. These modules simply display a text, which changes according to what you specify. In addition you can interact with some of the modules, like the date module. Polybar has many predefined modules, which can be customized, however you can also create your own. I recommend that you go ahead and look at some of the configs over at [unixporn](https://www.reddit.com/r/unixporn/search?q=polybar&restrict_sr=1&sort=top) and some of the [examples](https://github.com/jaagr/dots/tree/master/.local/etc/themer/themes) by the creator of polybar.

## Basics of Config
As polybar already has an good [configuration documentation](https://github.com/jaagr/polybar/wiki/Configuration), you can just browse through it and find relevant Information. The configuration use the INI file format and I believe are easily readable and understandable. I believe the best way to learn about polybar (just like almost any other program) is experimentation. I encourage you to just use my config or any other config and experiment

## Installation of Polybar
```
dotfiles
├── polybar
│   ├── install.sh (The installation file)
│   ├── launch.sh (Launch Polybar)
│   ├── bar-bottom.polybar
│   ├── bar-top.polybar
│   ├── battery.polybar (Can be deleted when your computer does not have a battery)
│   └── general.polybar
├── distro.sh
├── packages.sh
├── helpers.sh
├── special_installs.sh
└── install.sh
```
As you can see, we will use `.polybar` files, which means, we will simply have to duplicate the code in `i3/reload.sh` and modify it. Append the following to the `i3/reload.sh` file:
```shell
echo > ~/.config/polybar/config &&
for f in $HOME/.dotfiles/**/*.polybar; do (cat "${f}"; echo) >> ~/.config/polybar/config; done &&
i3-msg restart
```
To install polybar you will simply have to install the package *polybar*. `polybar/install.sh` is sort of overkill, how ever it is necessary to have a modular system where you can simply delete a folder:
```shell
_install polybar
```
To launch polybar, we want to do the following:
1. Kill current polybars
2. Get all monitors
3. Loop through the monitors and load both bars <br>
 
This translates to: 
```shell
# polybar/launch.sh
killall polybar
for MONITOR in $(polybar --list-monitors | cut -d":" -f1);
do
	polybar --reload top &
	polybar --reload bottom &
done
```
The `$MONITOR` variable will be called in the configuration file. With all this we can now configure polybar to our likings and can avoid 300+ lines long config files.

## My Configuration
![](/assets/images/polybar_current_config.png)
*This is what my config will look like*
Just like with i3 I will simply show you my config files and you can decide wether to use them or use other config files.
```ini
; polybar/bar-bottom.polybar
[bar/bottom]
monitor = ${env:MONITOR:}
width = 100%
height = 23
offset-y = 0
bottom = true

fixed-center = true

font-0 = FuraCode Nerd Font Mono:style=medium:size=12;3
font-1 = FuraCode Nerd Font Mono:style=italic:size=12;3
font-2 = FuraCode Nerd Font Mono:style=medium:size=16;3

border-size = 8
border-color = ${colors.background}
line-size = 0
padding-left = 0
padding-right = 0
module-margin-left = 0
module-margin-right = 0
background = ${colors.background}
foreground = ${colors.background}

modules-left = i3
modules-center = 
modules-right =  filesystem memory cpu temperature battery

#### Modules ####
[module/i3]
type = internal/i3

format = <label-state> <label-mode>
index-sort=true
wrapping-scroll=false
pin-workspaces = true

label-mode-padding = 2
label-mode-foreground = ${colors.black}
label-mode-background = ${colors.blue}

label-focused = "%{T3}%{T-} %icon% %{T3}%{T-}"
label-focused-background = ${colors.yellow}
label-focused-foreground = ${colors.background}
label-focused-padding = 0

label-unfocused = "%{T3}%{T-}%{T3}%{T-}"
label-unfocused-padding = 0
label-unfocused-background = ${colors.blue}
label-unfocused-foreground = ${colors.background}

label-urgent = "%{T3}%{T-}%icon%%{T3}%{T-}"
label-urgent-padding = 0
label-urgent-background = ${colors.magenta}
label-urgent-foreground = ${colors.background}

label-visible = "%{T3}%{T-}%icon%%{T3}%{T-}"
label-visible-padding = 0
label-visible-foreground = ${colors.background}
label-visible-background = ${colors.blue}


ws-icon-0 = 1;
ws-icon-1 = 2;
ws-icon-2 = 3;﬏
ws-icon-3 = 4;ﴬ
ws-icon-4 = 5;5
ws-icon-5 = 6;6
ws-icon-6 = 7;7
ws-icon-7 = 8;8
ws-icon-8 = 9;9
ws-icon-9 = 10;阮

[module/filesystem]
type = internal/fs
interval = 25

mount-0 = /

label-mounted = %{T3}%{T-} %mountpoint%: %percentage_used%% %{T3}%{T-}
label-unmounted = 
label-mounted-background = ${colors.dark-green}
label-mounted-foreground = ${colors.background}

[module/cpu]
type = internal/cpu
interval = 2
format-prefix = "%{T3}%{T-}  "
format-underline = ${colors.cyan}
label = %percentage:2%% %{T3}%{T-}
format-background = ${colors.blue}
format-foreground = ${colors.background}

[module/memory]
type = internal/memory
interval = 2
format-prefix = "%{T3}%{T-}  "
format-underline = ${colors.dark-green}
label = %percentage_used%% %{T3}%{T-}

format-background = ${colors.dark-cyan}
format-foreground = ${colors.background}
```

```ini
;polybar/bar-top.polybar
[bar/top]
monitor = ${env:MONITOR:}
width = 100%
height = 23
offset-y = 0

fixed-center = true

font-0 = FuraCode Nerd Font Mono:style=medium:size=12;3
font-1 = FuraCode Nerd Font Mono:style=italic:size=12;3
font-2 = FuraCode Nerd Font Mono:style=medium:size=16;3

border-size = 8
border-color = ${colors.background}
line-size = 0
padding-left = 0
padding-right = 0
module-margin-left = 0
module-margin-right = 0
background = ${colors.background}
foreground = ${colors.background}

modules-left =  power-menu audio
modules-center = music-info music-prev music-pause music-next 
modules-right = xkeyboard wlan date

#### Modules ####
[module/power-menu]
type = custom/script
exec = echo "%{T3}%{T-}%{T3}%{T-}"
interval = 1
click-left = exec $HOME/.dotfiles/rofi/menus/power_menu
format-background = ${colors.dark-red}
format-foreground = ${colors.background}

[module/music-info]
type = custom/script
exec = echo "$(playerctl metadata xesam:artist) | $(playerctl metadata xesam:title)"
interval = 1
format-prefix = "%{T3}%{T-}  "
format-background = ${colors.dark-blue}
format-foreground = ${colors.background}

[module/music-prev]
type = custom/script
exec = echo " 玲"
click-left = playerctl previous
format-background = ${colors.dark-blue}
format-foreground = ${colors.background}

[module/music-pause]
type = custom/script
exec = echo " 懶"
click-left = playerctl play-pause
format-background = ${colors.dark-blue}
format-foreground = ${colors.background}

[module/music-next]
type = custom/script
exec = echo " 怜 %{T3}%{T-}"
click-left = playerctl next
format-background = ${colors.dark-blue}
format-foreground = ${colors.background}

[module/xkeyboard]
type = internal/xkeyboard
blacklist-0 = num lock

format-prefix = "%{T3}%{T- }  "
format-prefix-foreground = ${colors.background}
format-prefix-background = ${colors.cyan}

label-layout = "%layout% %{T3}%{T-}"
label-layout-foreground = ${colors.background}
label-layout-background = ${colors.cyan}

label-indicator-padding = 2
label-indicator-margin = 1
label-indicator-background = ${colors.cyan}

[module/wlan]
type = internal/network
interface = wls3
interval = 3.0

format-connected = <ramp-signal> <label-connected>
format-connected-underline = ${colors.dark-red}
format-connected-background = ${colors.dark-magenta}
label-connected-foreground = ${colors.background}
label-connected = %essid% %{T3}%{T-}
format-connected-prefix = "%{T3}%{T- }"
format-connected-prefix-foreground = ${colors.background}

format-disconnected =
;format-disconnected =
;format-disconnected-underline = ${colors.dark-red}
;label-disconnected = %ifname% disconnected
;label-disconnected-foreground = ${colors.foreground-alt}

ramp-signal-0 = 睊
ramp-signal-1 = 直
ramp-signal-2 = 直
ramp-signal-3 = 直
ramp-signal-4 = 直
ramp-signal-foreground = ${colors.background}

[module/date]
type = internal/date
interval = 5

date =
date-alt = " %d-%m-%Y"

time = %H:%M
time-alt = %H:%M:%S

format-prefix = %{T3}%{T- } 
format-prefix-foreground = ${colors.background}
format-prefix-background = ${colors.dark-yellow}
label = %date% %time% %{T3}%{T-}
label-background = ${colors.dark-yellow}
label-foreground = ${colors.background}

[module/audio]
type = internal/pulseaudio

sink = alsa_output.pci-0000_00_1b.0.analog-stereo

format-volume = " <label-volume><bar-volume>"
label-volume = "%{T3}%{T-} 墳 "
format-background = ${colors.dark-green}
format-foreground = ${colors.background}
label-volume-background = ${colors.dark-green}
label-volume-foreground = ${colors.background}
format-volume-suffix = %{T3}%{T-}
format-volume-suffix-background = ${colors.dark-green}
format-volume-suffix-foreground = ${colors.background}
;format-volume-foreground = #FFFFFF

format-muted= "<label-muted>"
label-muted = "%{T3}%{T-} 婢 %{T3}%{T-}"  
label-muted-background = ${colors.dark-green}
label-muted-foreground = ${colors.background}

bar-volume-width = 10
bar-volume-foreground = ${colors.background}
bar-volume-background = ${colors.dark-green}
bar-volume-gradient = false

bar-volume-indicator = 
bar-volume-indicator-foreground = ${colors.background-alt}
bar-volume-indicator-background = ${colors.dark-green}

bar-volume-fill = 
bar-volume-fill-foreground = ${colors.background}
bar-volume-fill-background = ${colors.dark-green}

bar-volume-empty = 
bar-volume-empty-foreground = ${colors.background}
bar-volume-empty-background = ${colors.dark-green}
```

```ini
; polybar/battery.polybar

[module/battery]
type = internal/battery

; This is useful in case the battery never reports 100% charge
full-at = 9git config --global credential.helper store
6

; Use the following command to list batteries and adapters:
; $ ls -1 /sys/class/power_supply/
battery = BAT0
adapter = AC

; If an inotify event haven't been reported in this many
; seconds, manually poll for new values.
;
; Needed as a fallback for systems that don't report events
; on sysfs/procfs.
;
; Disable polling by setting the interval to 0.
;
; Default: 5
poll-interval = 5

; Available tags:
;   <label-charging> (default)
;   <bar-capacity>
;   <ramp-capacity>
;   <animation-charging>
format-charging = "%{T3}%{T-} <ramp-capacity> <label-charging> %{T3}%{T-}"
; Available tags:
;   <label-discharging> (default)
;   <bar-capacity>
;   <ramp-capacity>
;   <animation-discharging>
format-discharging = "%{T3}%{T-} <ramp-capacity> <label-discharging> %{T3}%{T-}"

label-charging = %percentage%% ﮣ
label-discharging = %percentage%%

format-discharging-foreground = ${colors.background}
format-discharging-background = ${colors.dark-red}

format-charging-foreground = ${colors.background}
format-charging-background = ${colors.dark-green}

ramp-capacity-0 = 
ramp-capacity-1 = 
ramp-capacity-2 = 
ramp-capacity-3 = 
ramp-capacity-4 = 

animation-charging-0 = 
animation-charging-1 = 
animation-charging-2 = 
animation-charging-3 = 
animation-charging-4 = 
```

```ini
; polybar/general.polybar
[colors]
background = ${xrdb:color0}
background-alt = ${xrdb:color8}
foreground = ${xrdb:color15}
foreground-alt = ${xrdb:color15}
black = ${xrdb:color0}
dark-grey = ${xrdb:color8}
dark-red = ${xrdb:color1}
red = ${xrdb:color9}
dark-green = ${xrdb:color2}
green = ${xrdb:color10}
dark-yellow = ${xrdb:color3}
yellow = ${xrdb:color11}
dark-blue = ${xrdb:color4}
blue = ${xrdb:color12}
dark-magenta = ${xrdb:color5}
magenta = ${xrdb:color13}
dark-cyan = ${xrdb:color6}
cyan = ${xrdb:color14}
light-grey = ${xrdb:color7}
white = ${xrdb:color15
```

And with this we have finished Part 3 of my dotfiles series. I hope you enjoyed it and if you have any questions you can contact me via email or your platform of choice. After finishing this tutorial you should have a usable system. However note that you will have to install stuff like st and a browser, before using this setup. These configuration files still need many applications to function correctly, so if you want to use my configuration, you will either have to modify it to your likings or will have to install my [dotfiles](https://github.com/peerlator/dotfiles-new), where the other programs are included. In the next post I will present to you my zsh configuration and what I use to make my zsh experience better.
