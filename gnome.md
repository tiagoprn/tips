If your "switch user" is not working (blank screen and freeze): 

    $ vim /etc/gdm/custom.conf

Uncomment the line:

    WaylandEnable = False
