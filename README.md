# Fedora Post Install for Thinkpad T480
As well as for T460+.

## DNF Configuration
```bash
sudo nano /etc/dnf/dnf.conf
```

Add to the end of file:
```
fastestmirror=1 
max_parallel_downloads=10 
deltarpm=true
```

## RPM Fusion
```bash
sudo dnf install -y https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```
```bash
sudo dnf config-manager --enable fedora-cisco-openh264
```

```bash
sudo dnf groupupdate core
```

## System Update
```bash
sudo dnf -y update
```

```bash
sudo dnf -y upgrade --refresh
```

**Reboot.**

## Firmware
```bash
sudo fwupdmgr get-devices 
```

```bash
sudo fwupdmgr get-devices
```

```bash
sudo fwupdmgr refresh --force
```

```bash
sudo fwupdmgr get-updates
```

```bash
sudo fwupdmgr update
```


## Media Codecs
```bash
sudo dnf groupupdate 'core' 'multimedia' 'sound-and-video' --setop='install_weak_deps=False' --exclude='PackageKit-gstreamer-plugin' --allowerasing && sync
```

```bash
sudo dnf swap 'ffmpeg-free' 'ffmpeg' --allowerasing
```

```bash
sudo dnf swap 'ffmpeg-free' 'ffmpeg' --allowerasing
```

```bash
sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel ffmpeg gstreamer-ffmpeg
```

```bash
sudo dnf install lame\* --exclude=lame-devel
```

```bash
sudo dnf group upgrade --with-optional Multimedia
```

## Hardware Acceleration
```bash
sudo dnf install ffmpeg ffmpeg-libs libva libva-utils
```

```bash
sudo dnf install intel-media-driver
```

### OpenH264 for Firefox
```bash
sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264
```

Enable the OpenH264 Plugin in Firefox's settings.

## Flatpak & Flathub
```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

```bash
flatpak update
```

## Windows Fonts
```bash
sudo dnf install curl cabextract xorg-x11-font-utils fontconfig
```

```bash

sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm
```

## Touchpad gestures for X11
```bash
sudo dnf install -y touchegg
```

```bash
sudo systemctl enable --now touchegg
```

Install Gnome Shell Extension: https://extensions.gnome.org/extension/4033/x11-gestures/

## Optimizations
### Throttled (for Thinkpad T480 only)
```bash
sudo dnf install python3-cairo-devel cairo-gobject-devel gobject-introspection-devel dbus-glib-devel python3-devel make libX11-devel
```

```bash
git clone https://github.com/erpalma/throttled.git
```

```bash
sudo ./throttled/install.sh
```

### Modern Standby
```bash
sudo grubby --update-kernel=ALL --args="mem_sleep_default=s2idle"
```

### Tuned
```bash
sudo dnf install -y tuned
```

```bash
sudo systemctl mask power-profiles-daemon
```

```bash
sudo systemctl enable --now tuned
```

```bash
tuned-adm list
```

Chose what you need:
```bash
sudo tuned-adm profile desktop
```

```bash
sudo tuned-adm profile laptop-battery-powersave
```

```bash
sudo tuned-adm profile realtime
```

```bash
sudo tuned-adm profile latency-performance
```

```bash
sudo tuned-adm profile throughput-performance
```

### Disable Gnome Software from Startup Apps
```bash
sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```

### Disable services for better boot time
```bash
sudo systemctl disable NetworkManager-wait-online.service
```

```bash
sudo systemctl mask systemd-udev-settle
```

### Reduce the Systemd timeout for services
```bash
sudo nano /etc/systemd/system.conf
```

```
DefaultTimeoutStartSec=15s
DefaultTimeoutStopSec=15s
```

### Fix slow Wi-Fi on Linux
```bash
sudo nano /etc/modprobe.d/iwlwifi.conf
```

```
options iwlwifi 11n_disable=8
```

**Reboot.**

### sysctl.conf
```bash
sudo nano /etc/sysctl.conf
```

```bash
vm.swappiness=10
vm.max_map_count=2147483642
vm.vfs_cache_pressure = 50
net.core.netdev_max_backlog = 16384
net.core.somaxconn = 8192
```

### Ext4 - Performance Improvement
```bash
sudo nano /etc/fstab
```

Add `noatime` to your system partition.
```
/dev/sda5    /    ext4    defaults,noatime    0    1
```

### Xanmod Kernel
```bash
sudo dnf copr enable rmnscnce/kernel-xanmod
```

```bash
sudo nano /etc/dnf/dnf.conf
```

Add to the end of file:
```
exclude=kernel
```

```bash
sudo dnf install -y kernel-xanmod-edge
```

Choose default kernel:
```bash
sudo grubby --info=ALL | grep -E "^kernel|^index"
```

Where `N` is a kernel number:
```bash
sudo grubby --set-default-index=N
```

## Configuring GNOME Software
```bash
gsettings set org.gnome.software download-updates false
```

```bash
gsettings set org.gnome.software download-updates-notify false
```

## Software installation
### From repositories

### From Flathub
```bash
flatpak install flathub com.mattjakeman.ExtensionManager flathub com.github.tchx84.Flatseal
```

## Gnome Shell Extensions
* [X11 Gestures](https://extensions.gnome.org/extension/4033/x11-gestures/)
* [Quick Lang Switch](https://extensions.gnome.org/extension/4559/quick-lang-switch/)
* [Just Perfection](https://extensions.gnome.org/extension/3843/just-perfection/)
* [Panel Corners](https://extensions.gnome.org/extension/4805/panel-corners/)
* [Alphabetical App Grid](https://extensions.gnome.org/extension/4269/alphabetical-app-grid/)
* [Blur My Shell](https://extensions.gnome.org/extension/3193/blur-my-shell/)
* [Frequency Boost Switch](https://extensions.gnome.org/extension/4792/frequency-boost-switch/)

## Theming
### gtk3-awd
```bash
sudo dnf install -y adw-gtk3-theme
```

```bash
flatpak install org.gtk.Gtk3theme.adw-gtk3 org.gtk.Gtk3theme.adw-gtk3-dark
```

Enable light:
```bash
gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3' && gsettings set org.gnome.desktop.interface color-scheme 'default'
```

Enable dark:
```bash
gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3-dark' && gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'```
```

### KvLibadwaita for Kvantum apps
```bash
git clone https://github.com/GabePoel/KvLibadwaita.git
```

```bash
./KvLibadwaita/install.sh
```

### Firefox Gnome Theme
```bash
curl -s -o- https://raw.githubusercontent.com/rafaelmardojai/firefox-gnome-theme/master/scripts/install-by-curl.sh | bash
```

### Starship
```bash
curl -sS https://starship.rs/install.sh | sh
```

#### For ZSH:
Add the following to the end of `~/.zshrc`:
```bash
eval "$(starship init zsh)"
```

#### For Bash:
Add the following to the end of `~/.bashrc`:
```bash
eval "$(starship init bash)"
```

### Fix the cursor issue in Telegram Desktop (Flatpak) on X11 running Linux (Fedora)
```bash
cd /usr/share/icons
```

```bash
cp -r Adwaita ~/.icons/Adwaita
```
 
```bash
flatpak install flathub com.github.tchx84.Flatseal
```

```bash
echo "Xcursor.theme: Adwaita" > ~/.Xresources
```

1. Launch *Flatseal* and append rules to the *Telegram Desktop* app.
2. Under *Filesystem*, enable **"All user files"** and append `~/.themes` and `~/.icons` in **Other files**.

## Use Alt-Shift for keyboard layout switching in GNOME 40+
```bash
gsettings set org.gnome.desktop.wm.keybindings switch-input-source "['<Shift>Alt_L']"
```

```bash
gsettings set org.gnome.desktop.wm.keybindings switch-input-source-backward "['<Alt>Shift_L']"
```
