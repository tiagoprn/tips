- If your "switch user" is not working (blank screen and freeze): 

    $ vim /etc/gdm/custom.conf

Uncomment the line:

    WaylandEnable = False

- Restore touchpad configuration (on and off) on gnome 3.20 (Arch Linux):
    $ sudo pacman -Rd xf86-input-synaptics
    $ sudo rm /etc/X11/xorg.conf.d/50-synaptics.conf
    $ sudo pacman -S xf86-input-libinput
    (logout and login again)
