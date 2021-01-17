# Desktop environment setup:

### Install video drivers:
Intel: `pacman -S xf86-video-intel`

### Install display and windows manager (i3):
```
pacman -S xorg i3 dmenu lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
```
Enable lightdm service:
```
systemctl enable lightdm
```

### Fonts and themes:
```
pacman -S ttf-dejavu papirus-icon-theme materia-gtk-theme 
```


### Install basic utilities:
```
pacman -S nautilus xfce4-terminal lxappearance network-manager-applet pulseaudio alsa-utils pavucontrol
```

### Office / Daily tools
```
pacman -S vlc git libreoffice firefox xfce4-screenshooter
```

### Install xrandr monitor configuration tool:
```
pacman -S arandr
```
