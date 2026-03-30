# Arch Linux + Hyprland — Setup Guide

> Personal installation and configuration guide for the Arch + Hyprland environment.

---

## Table of Contents

1. [Base System](#1-base-system)
2. [AUR (yay)](#2-aur-yay)
3. [Packages](#3-packages)
4. [Pacman](#4-pacman)
5. [Hyprland](#5-hyprland)
6. [Hyprpaper](#6-hyprpaper)
7. [Hyprlock](#7-hyprlock)
8. [Hypridle](#8-hypridle)
9. [Kitty](#9-kitty)
10. [Swaync](#10-swaync)
11. [Waybar](#11-waybar)
12. [ZShell](#12-zshell)
13. [Google Drive (rclone)](#13-google-drive-rclone)
14. [Bambu Studio](#14-bambu-studio)
15. [SDDM Dark Theme](#15-sddm-dark-theme)

---

## 1. Base System

### Update

```bash
sudo pacman -Syyuu
```
```bash
reboot
```

---

## 2. AUR (yay)

### Install git and yay

```bash
sudo pacman -S git base-devel
git config --global user.email "you@example.com"
git config --global user.name "Your Name"

git clone https://aur.archlinux.org/yay.git
cd yay/
makepkg -si
cd ..
rm -rf yay/
```

## 3. Packages

### Uninstall packages

```bash
sudo pacman -Rns dolphin vim
```

### Pacman

```bash
sudo pacman -S nemo wofi eog mpv libreoffice-fresh libreoffice-fresh-pt-br fastfetch timeshift jdk17-openjdk dotnet-sdk nodejs npm docker docker-compose dbeaver postgresql xdg-user-dirs file-roller nemo-fileroller zip unzip flatpak discord blender gimp handbrake noto-fonts ttf-jetbrains-mono-nerd firewalld firewall-config blueman adw-gtk-theme xdg-desktop-portal xdg-desktop-portal-hyprland xdg-desktop-portal-gtk qt6ct qt5ct kvantum breeze-icons qt5-graphicaleffects qt5-quickcontrols qt5-quickcontrols2 hyprshot hyprpaper hyprlock hypridle waybar pavucontrol zsh starship zsh-autosuggestions zsh-syntax-highlighting fzf rclone webkit2gtk-4.1 fuse2 gnome-disk-utility swaync
```
```bash
reboot
```

### Yay

```bash
yay -S google-chrome
yay -S visual-studio-code-bin
yay -S postman-bin
yay -S zoom
yay -S jetbrains-toolbox
```
```bash
reboot
```

### Flatpak

```bash
flatpak install flathub com.github.IsmaelMartinez.teams_for_linux
flatpak install flathub com.rtosta.zapzap
```
```bash
reboot
```

### Configurations

```bash
# Default user folders
xdg-user-dirs-update

# Open in terminal
gsettings set org.cinnamon.desktop.default-applications.terminal exec kitty

# Timeshift
sudo timeshift-gtk

# Docker
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER

# PostgreSQL
sudo -iu postgres initdb --locale=C.UTF-8 -D /var/lib/postgres/data
sudo systemctl enable --now postgresql
sudo -iu postgres psql
ALTER USER postgres WITH PASSWORD '123456';

# Kitten font
kitten choose-font
# Select: JetBrainsMono Nerd Font → Enter → Enter

# Firewall
sudo systemctl enable firewalld
sudo systemctl start firewalld

# Bluetooth
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
mkdir -p ~/.config/autostart
cp /etc/xdg/autostart/blueman.desktop ~/.config/autostart/
nano ~/.config/autostart/blueman.desktop
Hidden=true

# Audio
wpctl status
# Set audio output (adjust ID according to wpctl status)
wpctl set-default 58
# Set microphone
wpctl set-default 56

# Kitten theme
kitten themes
# Select: Catppuccin-Mocha → m
```
```bash
reboot
```

---

## 4. Pacman

### Configuration

```bash
sudo nano /etc/pacman.conf
```

<details>
<summary>View full example file</summary>

```ini
#
# /etc/pacman.conf
#

[options]
HoldPkg     = pacman glibc
Architecture = auto

Color
CheckSpace
ParallelDownloads = 10
DownloadUser = alpm
ILoveCandy

SigLevel    = Required DatabaseOptional
LocalFileSigLevel = Optional

[core]
Include = /etc/pacman.d/mirrorlist

[extra]
Include = /etc/pacman.d/mirrorlist

[multilib]
Include = /etc/pacman.d/mirrorlist
```

</details>

---

## 5. Hyprland

### Edit configuration

```bash
code ~/.config/hypr/hyprland.conf
```

<details>
<summary>View full hyprland.conf</summary>

```ini
#################
### DARK MODE ###
#################

exec = gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3-dark'
exec = gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'
env = QT_QPA_PLATFORMTHEME,gt6ct

################
### MONITORS ###
################

monitor=DP-1,1920x1080,0x0,1
monitor=HDMI-A-1,1600x900,1920x90,1

workspace = 1, monitor:DP-1
workspace = 2, monitor:HDMI-A-1

###################
### MY PROGRAMS ###
###################

$terminal = kitty
$fileManager = nemo
$menu = wofi --show drun
$browser = google-chrome-stable

#################
### AUTOSTART ###
#################

exec-once = waybar
exec-once = hyprpaper
exec-once = hypridle
exec-once = swaync
exec-once = discord
exec-once = flatpak run com.rtosta.zapzap
exec-once = flatpak run com.github.IsmaelMartinez.teams_for_linux
exec-once = rclone mount Drive: ~/Documents/Drive --vfs-cache-mode writes &
exec-once = /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1

#############################
### ENVIRONMENT VARIABLES ###
#############################

env = XCURSOR_SIZE,24
env = HYPRCURSOR_SIZE,24

#####################
### LOOK AND FEEL ###
#####################

general {
    gaps_in = 2
    gaps_out = 10

    border_size = 2

    col.active_border = rgba(33ccffee) rgba(00ff99ee) 45deg
    col.inactive_border = rgba(595959aa)

    resize_on_border = false

    allow_tearing = false

    layout = dwindle
}

decoration {
    rounding = 10
    rounding_power = 2

    active_opacity = 1.0
    inactive_opacity = 1.0

    shadow {
        enabled = true
        range = 4
        render_power = 3
        color = rgba(1a1a1aee)
    }

    blur {
        enabled = true
        size = 3
        passes = 1

        vibrancy = 0.1696
    }
}

animations {
    enabled = yes, please :)

    bezier = easeOutQuint,   0.23, 1,    0.32, 1
    bezier = easeInOutCubic, 0.65, 0.05, 0.36, 1
    bezier = linear,         0,    0,    1,    1
    bezier = almostLinear,   0.5,  0.5,  0.75, 1
    bezier = quick,          0.15, 0,    0.1,  1

    animation = global,        1,     10,    default
    animation = border,        1,     5.39,  easeOutQuint
    animation = windows,       1,     4.79,  easeOutQuint
    animation = windowsIn,     1,     4.1,   easeOutQuint, popin 87%
    animation = windowsOut,    1,     1.49,  linear,       popin 87%
    animation = fadeIn,        1,     1.73,  almostLinear
    animation = fadeOut,       1,     1.46,  almostLinear
    animation = fade,          1,     3.03,  quick
    animation = layers,        1,     3.81,  easeOutQuint
    animation = layersIn,      1,     4,     easeOutQuint, fade
    animation = layersOut,     1,     1.5,   linear,       fade
    animation = fadeLayersIn,  1,     1.79,  almostLinear
    animation = fadeLayersOut, 1,     1.39,  almostLinear
    animation = workspaces,    1,     1.94,  almostLinear, fade
    animation = workspacesIn,  1,     1.21,  almostLinear, fade
    animation = workspacesOut, 1,     1.94,  almostLinear, fade
    animation = zoomFactor,    1,     7,     quick
}

windowrule {
    name = teams-workspace
    match:class = com.github.IsmaelMartinez.teams_for_linux
    monitor = HDMI-A-1
}

windowrule {
    name = discord-workspace
    match:class = discord
    monitor = HDMI-A-1
}

windowrule {
    name = zapzap-workspace
    match:class = com.rtosta.zapzap
    monitor = HDMI-A-1
}

dwindle {
    pseudotile = true # Master switch for pseudotiling. Enabling is bound to mainMod + P in the keybinds section below
    preserve_split = true # You probably want this
}

master {
    new_status = master
}

misc {
    force_default_wallpaper = -1 # Set to 0 or 1 to disable the anime mascot wallpapers
    disable_hyprland_logo = false # If true disables the random hyprland logo / anime girl background. :(
}


#############
### INPUT ###
#############

input {
    kb_layout = br
    kb_variant = abnt2
    kb_model =
    kb_options =
    kb_rules =

    follow_mouse = 1

    sensitivity = 0 # -1.0 - 1.0, 0 means no modification.

    touchpad {
        natural_scroll = false
    }
}

gesture = 3, horizontal, workspace

device {
    name = epic-mouse-v1
    sensitivity = -0.5
}


###################
### KEYBINDINGS ###
###################

$mainMod = SUPER # Sets "Windows" key as main modifier

bind = $mainMod, T, exec, $terminal
bind = $mainMod, C, killactive,
bind = $mainMod, Escape, exec, command -v hyprshutdown >/dev/null 2>&1 && hyprshutdown || hyprctl dispatch exit
bind = $mainMod, E, exec, $fileManager
bind = $mainMod, V, togglefloating,
bind = $mainMod, Space, exec, $menu
bind = $mainMod, R, pseudo, # dwindle
bind = $mainMod, J, layoutmsg, togglesplit # dwindle
bind = $mainMod, F, fullscreen, 1
bind = $mainMod, B, exec, $browser
bind = $mainMod, L, exec, hyprlock
bind = $mainMod, P, exec, .config/waybar/power.sh
bind = $mainMod, F12, exec, hyprshot -m output -o ~/Pictures/Screenshots
bind = $mainMod SHIFT, X, exec, hyprshot -m region -o ~/Pictures/Screenshots
bind = $mainMod SHIFT, F12, exec, hyprshot -m window -o ~/Pictures/Screenshots

# Move focus with mainMod + arrow keys
bind = $mainMod, left, movefocus, l
bind = $mainMod, right, movefocus, r
bind = $mainMod, up, movefocus, u
bind = $mainMod, down, movefocus, d

# Switch workspaces with mainMod + [0-9]
bind = $mainMod, 1, workspace, 1
bind = $mainMod, 2, workspace, 2
bind = $mainMod, 3, workspace, 3
bind = $mainMod, 4, workspace, 4
bind = $mainMod, 5, workspace, 5
bind = $mainMod, 6, workspace, 6
bind = $mainMod, 7, workspace, 7
bind = $mainMod, 8, workspace, 8
bind = $mainMod, 9, workspace, 9
bind = $mainMod, 0, workspace, 10

# Move active window to a workspace with mainMod + SHIFT + [0-9]
bind = $mainMod SHIFT, 1, movetoworkspace, 1
bind = $mainMod SHIFT, 2, movetoworkspace, 2
bind = $mainMod SHIFT, 3, movetoworkspace, 3
bind = $mainMod SHIFT, 4, movetoworkspace, 4
bind = $mainMod SHIFT, 5, movetoworkspace, 5
bind = $mainMod SHIFT, 6, movetoworkspace, 6
bind = $mainMod SHIFT, 7, movetoworkspace, 7
bind = $mainMod SHIFT, 8, movetoworkspace, 8
bind = $mainMod SHIFT, 9, movetoworkspace, 9
bind = $mainMod SHIFT, 0, movetoworkspace, 10

# Example special workspace (scratchpad)
bind = $mainMod, S, togglespecialworkspace, magic
bind = $mainMod SHIFT, S, movetoworkspace, special:magic

# Scroll through existing workspaces with mainMod + scroll
bind = $mainMod, mouse_down, workspace, e+1
bind = $mainMod, mouse_up, workspace, e-1

# Move/resize windows with mainMod + LMB/RMB and dragging
bindm = $mainMod, mouse:272, movewindow
bindm = $mainMod, mouse:273, resizewindow

# Laptop multimedia keys for volume and LCD brightness
bindel = ,XF86AudioRaiseVolume, exec, wpctl set-volume -l 1 @DEFAULT_AUDIO_SINK@ 5%+
bindel = ,XF86AudioLowerVolume, exec, wpctl set-volume @DEFAULT_AUDIO_SINK@ 5%-
bindel = ,XF86AudioMute, exec, wpctl set-mute @DEFAULT_AUDIO_SINK@ toggle
bindel = ,XF86AudioMicMute, exec, wpctl set-mute @DEFAULT_AUDIO_SOURCE@ toggle
bindel = ,XF86MonBrightnessUp, exec, brightnessctl -e4 -n2 set 5%+
bindel = ,XF86MonBrightnessDown, exec, brightnessctl -e4 -n2 set 5%-

# Requires playerctl
bindl = , XF86AudioNext, exec, playerctl next
bindl = , XF86AudioPause, exec, playerctl play-pause
bindl = , XF86AudioPlay, exec, playerctl play-pause
bindl = , XF86AudioPrev, exec, playerctl previous

##############################
### WINDOWS AND WORKSPACES ###
##############################

windowrule {
    name = suppress-maximize-events
    match:class = .*

    suppress_event = maximize
}

windowrule {
    name = fix-xwayland-drags
    match:class = ^$
    match:title = ^$
    match:xwayland = true
    match:float = true
    match:fullscreen = false
    match:pin = false

    no_focus = true
}

windowrule {
    name = move-hyprland-run

    match:class = hyprland-run

    move = 20 monitor_h-120
    float = yes
}
```

</details>

---

## 6. Hyprpaper

```bash
nano ~/.config/hypr/hyprpaper.conf
```

```ini
wallpaper {
    monitor = DP-1
    path = /home/lcsvinhas/Pictures/Wallpapers/default.jpg
}

wallpaper {
    monitor = HDMI-A-1
    path = /home/lcsvinhas/Pictures/Wallpapers/default.jpg
}

splash = false
ipc = off
```

---

## 7. Hyprlock

```bash
nano ~/.config/hypr/hyprlock.conf
```

```ini
background {
    monitor =
    path = /home/lcsvinhas/Pictures/Wallpapers/default.jpg
    blur_passes = 2
    blur_size = 6
}

label {
    monitor =
    text = $TIME
    font_size = 80
    position = 0, 100
    halign = center
    valign = center
}

input-field {
    monitor =
    size = 200, 50
    outline_thickness = 2
    dots_size = 0.2
    dots_spacing = 0.3
    dots_center = true
    outer_color = rgba(255,255,255,0.2)
    inner_color = rgba(0,0,0,0.5)
    font_color = rgb(255,255,255)
    fade_on_empty = false
    placeholder_text = Password...
    hide_input = false
    position = 0, -100
    halign = center
    valign = center
}
```

---

## 8. Hypridle

```bash
nano ~/.config/hypr/hypridle.conf
```

```ini
general {
    lock_cmd = pidof hyprlock || hyprlock
    before_sleep_cmd = loginctl lock-session
    after_sleep_cmd = hyprctl dispatch dpms on
}

listener {
    timeout = 300
    on-timeout = hyprlock
}

listener {
    timeout = 600
    on-timeout = hyprctl dispatch dpms off
    on-resume = hyprctl dispatch dpms on
}

listener {
    timeout = 3600
    on-timeout = systemctl suspend
}
```

---

## 9. Kitty

```bash
nano ~/.config/kitty/kitty.conf
```

```ini
# BEGIN_KITTY_FONTS
font_family      family="JetBrainsMono Nerd Font"
bold_font        auto
italic_font      auto
bold_italic_font auto
# END_KITTY_FONTS

background_opacity 0.75
background #24273a
```

---

## 10. Swaync

```bash
code .config/swaync/style.css
```
<details>
<summary>View style.css (Catppuccin Mocha)</summary>

```css
@define-color rosewater #f5e0dc;
@define-color flamingo #f2cdcd;
@define-color pink #f5c2e7;
@define-color mauve #cba4f7;
@define-color red #f38ba8;
@define-color maroon #eba0ac;
@define-color peach #fab387;
@define-color yellow #f9e2af;
@define-color green #a6e3a1;
@define-color teal #94e2d5;
@define-color sky #89dceb;
@define-color sapphire #74c7ec;
@define-color blue #89b4fa;
@define-color lavender #b4befe;
@define-color text #cdd6f4;
@define-color subtext1 #bac2de;
@define-color subtext0 #a6adc8;
@define-color overlay2 #9399b2;
@define-color overlay1 #7f849c;
@define-color overlay0 #6c7086;
@define-color surface2 #585b70;
@define-color surface1 #45475a;
@define-color surface0 #313244;
@define-color base #1e1e2e;
@define-color mantle #181825;
@define-color crust #11111b;

* {
    font-family: "JetBrains Mono Nerd", monospace;
    border: none;
    min-height: 0;
}

.control-center {
    background-color: alpha(@base, 0.9);
    border-radius: 12px;
    color: @sapphire;
    padding: 2px 12px;
    margin: 10px;
    transition: 0.3s all
}

.notification {
    background: alpha(@base, 0.9);
    border-radius: 12px;
    border: 1px solid alpha(@overlay0, 0.4);
    padding: 10px;
    margin: 3px 0px 20px 3px;
    box-shadow: 2px 4px 20px rgba(30, 30, 46, 0.4);
}
```

</details>

---

## 11. Waybar

### Installation

```bash
mkdir -p ~/.config/waybar
cd ~/.config/waybar
```

### Create `config.jsonc`

```jsonc
{
  "layer": "top",
  "reload_on_style_change": true,
  "position": "top",
  "passthrough": false,
  "reload_style_on_change": true,
  "spacing": 10,
  "margin-top": 10,
  "margin-left": 10,
  "margin-right": 10,
  "margin-bottom": 1,
  "modules-left": [
    "custom/icon",
    "hyprland/window"
  ],
  "modules-center": [
    "hyprland/workspaces"
  ],
  "modules-right": [
    "tray",
    "pulseaudio",
    "custom/bluetooth",
    "clock",
    "custom/notifications",
    "custom/power"
  ],
  "custom/icon": {
    "format": "󰣇"
  },
  "hyprland/window": {
    "separate-outputs": true,
    "format": "{class}"
  },
  "hyprland/workspaces": {
    "disable-scroll": true,
    "on-click": "activate",
    "format": "{icon}"
  },
  "tray": {
    "icon-size": 16,
    "spacing": 5,
    "show-passive-items": true
  },
  "clock": {
    "format": "[{:%H:%M}]",
    "interval": 1,
    "tooltip-format": "<tt>{calendar}</tt>",
    "calendar": {
      "format": {
        "today": "<span color='#eed49f'><b>{}</b></span>"
      }
    },
    "actions": {
      "on-click-right": "shift_down",
      "on-click": "shift_up"
    }
  },
  "custom/bluetooth": {
    "exec": "bash -c 'rfkill list bluetooth | grep -q \"Soft blocked: no\" && echo \"󰂯 ON]\" || echo \"󰂲 OFF]\"'",
    "interval": 2,
    "on-click": "blueman-manager",
    "on-click-right": "rfkill toggle bluetooth"
  },
  "pulseaudio": {
    "format": "[{icon} {volume}%",
    "format-muted": "[󰖁 MUTE",
    "format-icons": {
      "default": [
        "",
        "",
        " "
      ]
    },
    "on-click": "pavucontrol",
    "on-click-right": "pactl set-sink-mute @DEFAULT_SINK@ toggle",
    "on-scroll-up": "pactl set-sink-volume @DEFAULT_SINK@ +5%",
    "on-scroll-down": "pactl set-sink-volume @DEFAULT_SINK@ -5%"
  },
  "custom/notifications": {
    "format": "{}",
    "exec": "bash -c 'swaync-client -D | grep -q true && echo \"󰂛\" || echo \"󰂚\"'",
    "interval": 2,
    "on-click": "swaync-client -t",
    "on-click-right": "swaync-client -d"
  },
  "custom/power": {
    "format": "",
    "tooltip": "Power Menu",
    "on-click": "~/.config/waybar/power.sh"
  }
}
```

### Create `style.css`

<details>
<summary>View style.css (Catppuccin Mocha)</summary>

```css
@define-color rosewater #f5e0dc;
@define-color flamingo #f2cdcd;
@define-color pink #f5c2e7;
@define-color mauve #cba4f7;
@define-color red #f38ba8;
@define-color maroon #eba0ac;
@define-color peach #fab387;
@define-color yellow #f9e2af;
@define-color green #a6e3a1;
@define-color teal #94e2d5;
@define-color sky #89dceb;
@define-color sapphire #74c7ec;
@define-color blue #89b4fa;
@define-color lavender #b4befe;
@define-color text #cdd6f4;
@define-color subtext1 #bac2de;
@define-color subtext0 #a6adc8;
@define-color overlay2 #9399b2;
@define-color overlay1 #7f849c;
@define-color overlay0 #6c7086;
@define-color surface2 #585b70;
@define-color surface1 #45475a;
@define-color surface0 #313244;
@define-color base #1e1e2e;
@define-color mantle #181825;
@define-color crust #11111b;

* {
    font-family: "JetBrains Mono Nerd", monospace;
    border: none;
    min-height: 0;
}

window#waybar {
    background-color: alpha(@base, 0.75);
    border-radius: 12;
    color: @sapphire;
    padding: 2px 12px;
    transition: 0.3s all;
}

.modules-left,
.modules-center,
.modules-right {
    padding: 0 1rem;
    color: @sapphire;
    border: none;
}

.modules-center {
    padding: 2px;
}

#custom-icon {
    font-size: 15px;
    padding-right: 4px;
}

#window {
    font-weight: bold;
    color: @sky;
    transition: all 0.3s ease;
}

#workspaces {
    font-weight: bold;
    font-size: 12px;
    border: none;
}

#workspaces button {
    color: @sapphire;
    border-radius: 0;
    transition: 0.2s ease-out;
    border-bottom: 1px solid transparent;
}

#workspaces button.urgent {
    border-bottom: 1px solid @blue;
}

#workspaces button.active {
    border-bottom: 1px solid @yellow;
    color: @yellow;
}

#tray {
    padding: 0 4px;
    color: @sapphire;
}

#clock {
    transition: all 0.3s ease;
    font-weight: bold;
}

#clock:hover {
    color: @yellow;
}

#pulseaudio {
    font-weight: bold;
    transition: all 0.3s ease;
}

#pulseaudio:hover {
    color: @yellow;
}

#custom-bluetooth {
    transition: all 0.3s ease;
    font-weight: bold;
}

#custom-bluetooth:hover {
    color: @yellow;
}

#custom-notifications {
    padding: 0 4px;
    transition: all 0.3s ease;
    font-weight: bold;
}

#custom-notifications:hover {
    color: @yellow;
}

#custom-power {
    padding: 0 4px;
    transition: all 0.3s ease;
    font-weight: bold;
}

#custom-power:hover {
    color: @yellow;
}
```

</details>

### Create `power.sh`

```bash
nano ~/.config/waybar/power.sh
```

```bash
#!/bin/bash

option=$(printf "Lock\nShutdown\nReboot\nLogout" | wofi --dmenu -i -p "Power Menu")

case "$option" in
    Shutdown) systemctl poweroff ;;
    Reboot)   systemctl reboot ;;
    Logout)   hyprctl dispatch exit ;;
    Lock)     hyprlock ;;
    *)        exit 0 ;;
esac
```

```bash
chmod +x ~/.config/waybar/power.sh
```

---

## 12. ZShell

```bash
chsh -s /usr/bin/zsh
```
```bash
reboot
```

### Configure `~/.zshrc`

```zsh
# Binds
bindkey "^[[H"    beginning-of-line
bindkey "^[[F"    end-of-line
bindkey "^[[3~"   delete-char
bindkey "^H"      backward-kill-word
bindkey "^[[3;5~" kill-word
bindkey "^[[1;5D" backward-word
bindkey "^[[1;5C" forward-word

# Plugins
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# Fzf
source <(fzf --zsh)

# Starship Prompt
eval "$(starship init zsh)"
```
```bash
exec zsh
```

### Starship preset

```bash
starship preset nerd-font-symbols -o ~/.config/starship.toml
```

---

## 13. Google Drive (rclone)

### Install and configure

```bash
rclone config
```

Interactive wizard steps:

```
n           → New remote
Drive       → Remote name
<ID>        → Client ID (Enter to skip)
Enter       → Client Secret (skip)
1           → Full access
Enter       → Service Account (skip)
Enter       → Advanced config (no)
n           → Auto config (no — use headless)
y           → Confirm
Login to Google (browser)
n           → Root folder ID (skip)
Enter       → Confirm
y           → OK
q           → Quit
```

### Mount the Drive

```bash
mkdir -p ~/Documents/Drive
rclone mount Drive: ~/Documents/Drive --vfs-cache-mode writes &
```

> Autostart is already configured in `hyprland.conf` via `exec-once`.

---

## 14. Bambu Studio

### Install AppImage

```bash
sudo mkdir -p /opt/bambu-studio
sudo mv ~/Downloads/Bambu_Studio_ubuntu-24.04_PR-9540.AppImage /opt/bambu-studio/
sudo chmod +x /opt/bambu-studio/Bambu_Studio_ubuntu-24.04_PR-9540.AppImage
```

### Create menu entry

```bash
nano ~/.local/share/applications/bambu-studio.desktop
```

```ini
[Desktop Entry]
Name=Bambu Studio
Exec=/opt/bambu-studio/Bambu_Studio_ubuntu-24.04_PR-9540.AppImage
Icon=bambu-studio
Type=Application
Categories=Utility;
Terminal=false
```
---

## 15. SDDM Dark Theme

### Theme download

Download: https://www.opendesktop.org/p/1272122

### Enable theme

```bash
sudo nano /etc/sddm.conf.d/sddm.conf
```

```ini
[Theme]
Current=sugar-dark
```

### Copy wallpaper

```bash
sudo mkdir -p /usr/share/sddm/themes/sugar-dark/Pictures/Wallpapers
sudo cp ~/Pictures/Wallpapers/default.jpg /usr/share/sddm/themes/sugar-dark/Pictures/Wallpapers/
```

### Configure theme

```bash
sudo nano /usr/share/sddm/themes/sugar-dark/theme.conf
```

```ini
[General]
Background="Pictures/Wallpapers/default.jpg"
ScaleImageCropped=true
ScreenWidth=1440
ScreenHeight=900

MainColor="navajowhite"
AccentColor="white"
RoundCorners=20
ScreenPadding=0

Font="JetBrainsMono Nerd Font"
FontSize=

HourFormat="HH:mm"
DateFormat="dddd, d of MMMM"

ForceRightToLeft=false
ForceLastUser=true
ForcePasswordFocus=true
ForceHideCompletePassword=false
ForceHideVirtualKeyboardButton="false"

HeaderText=Welcome!
```
