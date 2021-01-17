# Desktop environment setup:

### Install video drivers:
Intel: `pacman -S xf86-video-intel`

### Install display and windows manager (i3):
```
pacman -S xorg i3 dmenu lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings picom
```

### Fonts and themes:
```
pacman -S ttf-dejavu papirus-icon-theme materia-gtk-theme 
```


### Install basic utilities:
```
pacman -S nautilus xfce4-terminal lxappearance firefox 
