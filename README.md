# A handy list of commands/tricks!

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Linux](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

---

Note - This is a collection of quickfixes/ commands that helped me solve issues on Linux ( Ubuntu in my machine ), mostly intended for personal use but feel free to refer the steps, may help you troubleshoot some problems! (a few shortcuts and useful aliases that i use too!)

Specs - 

![CPU](https://img.shields.io/badge/Intel-Core_i5_9th-0071C5?style=for-the-badge&logo=intel&logoColor=white)
![GPU](https://img.shields.io/badge/NVIDIA-GTX1660ti-76B900?style=for-the-badge&logo=nvidia&logoColor=white)

---

## My aliases:

```bash
# mylog => for quick SQL login
sudo mysql -h localhost -p 

# nocam => unload cam module from the kernel
sudo modprobe -r uvcvideo
# note to re-enable the module type 
sudo modprobe uvcvideo

# netstart => restart network manager to quickfix wifi/networking issue. See flush DNS Cache for another network troubleshooting step 
sudo service network-manager restart 
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

## Ram Speed Check

```bash
sudo dmidecode --type 17
# alternative 
sudo lshw -C memory
```

## Spicetify Setup 🎶

Few steps to setup spicetify on the flatak

```bash
sudo chmod a+wr /var/lib/flatpak/app/com.spotify.Client/current/active/files/extra/share/spotify
sudo chmod a+wr /var/lib/flatpak/app/com.spotify.Client/current/active/files/extra/share/spotify/Apps -R
alias spicetify="/home/tanmay/spicetify-cli/spicetify" #already done in my system
# replace "tanmay" in the path above with your user name. Make sure it is the path to the spicetify executable in your system
spicetify config color_scheme horizon
spicetify apply
```

## Flush DNS Cache

```bash
# check if systemcl is the one 
sudo systemctl is-active systemd-resolved.service
# if active: yes in case of ubuntu
sudo systemd-resolve --flush-caches
```
