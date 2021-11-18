# A handy list of commands/tricks!

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Linux](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

---

Note - This is a collection of quick-fixesand/ commands that helped me solve issues on Linux ( Ubuntu on my machine ), mostly intended for personal use but feel free to refer the steps, may help you troubleshoot some problems! (a few shortcuts, useful aliases and customization tools that I use too!)

Specs - 

![CPU](https://img.shields.io/badge/Intel-Core_i5_9th-0071C5?style=for-the-badge&logo=intel&logoColor=white)
![GPU](https://img.shields.io/badge/NVIDIA-GTX1660ti-76B900?style=for-the-badge&logo=nvidia&logoColor=white)

---

## My aliases:

```bash
#restart netowrk => restart network manager to quickfix wifi/networking issue. See flush DNS Cache for another network troubleshooting step 
alias netstart='sudo service network-manager restart'

#turn off the cam => unload cam module from the kernel
alias nocam='sudo modprobe -r uvcvideo'

#turn on the cam
alias camon='sudo modprobe uvcvideo'

#for scpicetify setup
alias spicetify="/home/tanmay/spicetify-cli/spicetify"

#mysql login => for quick SQL login
alias mylog="sudo mysql -h localhost -p"

#launch flatpak version of neovim
alias nvim="flatpak run io.neovim.nvim"

#python venv activate
alias activatepyenv="source ./env/bin/activate"
```

## Clean up older verions of snaps

Source [ItsFoss-Free-Up-Space](https://itsfoss.com/free-up-space-ubuntu-linux/)

Snaps store previous versions of the apps, which may take up loads of storage space.
Check amount of space taken up by snaps

```bash
du -h /var/lib/snapd/snaps  
```

- Save the following script, ( source the above article ) as a shell script file ( say clean.sh )

```bash
#!/bin/bash
# Removes old revisions of snaps
# CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        snap remove "$snapname" --revision="$revision"
    done
```

- Give the file execution permission, then execute it as superuser 

```bash
chmod +x ./clean.sh #gave execute permission

sudo ./clean.sh #execute it
```

## Evince (Document vievwer fails to open links when chrome is the default browser) 📚

The error message it dumps is along the lines of 

```bash
    [11589:11589:1118/193800.293444:FATAL:double_fork_and_exec.cc(131)] execv /opt/google/chrome/chrome_crashpad_handler: Permission denied (13)
```

To fix it, we need to edit ```/etc/apparmor.d/abstractions/ubuntu-helpers``` as root

```bash
# can replace nano with the text editor of your choice, such as gedit or vim
sudo nano /etc/apparmor.d/abstractions/ubuntu-helpers 
```

inside profile_sanitized_helper{} insert the following line:

```   /opt/google/chrome/chrome_crashpad_handler Pixr, ```

```bash
# reload
cd /etc/apparmor.d
sudo apparmour_parser -r usr.bin.evince
```

## Uninstall Apps 📲

```bash
#this if for disros using apt/dpkg
dpkg -list
dpkg -l
apt-get –-purge remove #Name
apt-get --purge remove #Name
sudo apt-get --purge remove #Name
```

## Nvidia Screen Tearing fixes 🖥

[NVIDIA/Troubleshooting/#Screen-Tearing - ArchWiki](https://wiki.archlinux.org/index.php/NVIDIA/Troubleshooting#Avoid_screen_tearing)

In /etc/X11/xorg.conf.d/20-nvidia.conf Insert the following Lines as a root user
Read the above arch wiki article for details and steps!

```
Section "Device"
        Identifier "Nvidia Card"
        Driver     "nvidia"
        VendorName "NVIDIA Corporation"
        BoardName  "GeForce GTX 1050 Ti"
EndSection

Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
    Monitor        "Monitor0"
    Option         "MetaModes" "nvidia-auto-select +0+0 {ForceFullCompositionPipeline=On}"
    Option         "AllowIndirectGLXProtocol" "off"
    Option         "TripleBuffer" "on"
EndSection
```

###### This is the startup command that I added instead of the above fix

```bash
nvidia-settings --assign CurrentMetaMode="DPY-0: nvidia-auto-select +0+0 {ForceCompositionPipeline=On}"
```

## Ram Speed Check

```bash
sudo dmidecode --type 17
# alternative 
sudo lshw -C memory
```

## Spicetify Setup 🎶

Few steps to setup Spicetify on the Flatpak version of the Spotify client. For documentation check [GitHub - khanhas/spicetify-cli: Commandline tool to customize Spotify client. Supports Windows, MacOS and Linux.](https://github.com/khanhas/spicetify-cli)

```bash
sudo chmod a+wr /var/lib/flatpak/app/com.spotify.Client/current/active/files/extra/share/spotify
sudo chmod a+wr /var/lib/flatpak/app/com.spotify.Client/current/active/files/extra/share/spotify/Apps -R
alias spicetify="/home/tanmay/spicetify-cli/spicetify" #already done in my system
# replace "tanmay" in the path above with your user name. Make sure it is the path to the spicetify executable in your system
# taking the example of a popular Dribbblish Theme.
cd "$(dirname "$(spicetify -c)")/Themes/DribbblishDynamic"
mkdir -p ../../Extensions
cp dribbblish-dynamic.js ../../Extensions/.
spicetify config extensions dribbblish-dynamic.js
spicetify config current_theme DribbblishDynamic color_scheme dark
spicetify config inject_css 1 replace_colors 1 overwrite_assets 1
spicetify apply
```

## Flush DNS Cache

```bash
# check if systemcl is used on your system 
sudo systemctl is-active systemd-resolved.service
# if active: yes in case of ubuntu
sudo systemd-resolve --flush-caches
```
