- STATUS DE TODOS OS SERVIÇOS:
    $ systemctl

- HABILITAR/DESABILITAR/PARAR/INICIAR/REINICIAR UM SERVIÇO:
    $ systemctl [enable/disable/stop/start/restart/is-active] [nome_servico][.service]
  Ex.
    $ systemctl enable cups
    $ systemctl disable sshd.service

- DESLIGAR / REINICIAR VIA SYSTEMD:
    $ systemctl halt
    $ systemctl reboot

---

How to make a daemon automatically restart on systemd (previouly you needed to use something like supervisord):

Edit the daemon unit file and add the configuration below:

[Service]
Restart=always
RestartSec=30


Then run the following for your changes to take effect:

# systemctl daemon-reload
# systemctl restart [daemon_name]

---

USING SYSTEMD TO START SOMETHING AT THE USER LOGIN. E.g.: tmux

PRE-REQUISITE: 
	1) Enable the systemd user service independently of active user sessions or not:
		$ sudo loginctl enable-linger tiago

	2) Check if the systemd user instance is running:
		$ systemctl --user status.

If you want to run services on login, execute:
	$ systemctl --user enable service
	E.g.:
		$ systemctl --user enable tmux

To manually start a user service:
	$ systemctl --user start tmux

Check the current user environment variables: 
	$ systemctl --user show-environment

---

The parameters supported by a systemd unit file:

http://www.freedesktop.org/software/systemd/man/systemd.service.html

---

# LOGS:

### How to view all journalctl log (which is also the linux general system log) in real-time:

    $ journalctl -xn -f
    $ journalctl -u docker -xn -f

### Monitoring specific daemons (e.g. docker and nginx):

    $ journalctl -o short -u nginx --since today
    $ journalctl -b -u docker --since today -o json-pretty
    $ journalctl -b -u docker --since today -o short

### Logs disk usage:

    $ journalctl --disk-usage

### Deleting old logs:

1) This will remove old entries until the total journal space taken up on disk is
at the requested size:

    $ journalctl --vacuum-size=1G

2) To keep entries from the last year, you can type:

    $ journalctl --vacuum-time=1years

### Configuring journald (journalctl backend):

$ vim /etc/systemd/journald.conf file.

    SystemMaxUse=: Specifies the maximum disk space that can be used by the journal
    in persistent storage.

    SystemKeepFree=: Specifies the amount of space that the journal should leave
    free when adding journal entries to persistent storage.

    SystemMaxFileSize=: Controls how large individual journal files can grow to in
    persistent storage before being rotated.

    RuntimeMaxUse=: Specifies the maximum disk space that can be used in volatile
    storage (within the /run filesystem).

    RuntimeKeepFree=: Specifies the amount of space to be set aside for other uses
    when writing data to volatile storage (within the /run filesystem).

    RuntimeMaxFileSize=: Specifies the amount of space that an individual journal
    file can take up in volatile storage (within the /run filesystem) before being
    rotated.

---

Where to put the unit files:

SystemD looks for system unit files in this order:

    /etc/systemd/system
    /run/systemd/system
    /usr/lib/systemd/system

Unit files in the earlier directories override later ones. 
Prefer to make changes in the /etc or /run directory, where configuration is expected. 
You should avoid making changes in /usr, because your system installs package data there that’s not expected to change. 

---

A systemd user file to automatically start dropbox for the user "tiago": 

    $ vim /etc/systemd/system/dropbox.service

    [Unit]
    Description=Dropbox for user tiago
    After=network.target sshd.service
    Requires=network.target

    [Service]
    # "TimeoutStartSec=0" to turn off timeouts, 
    # as dropboxd may take a while.
    TimeoutStartSec=0 
    Type=forking
    User=tiago
    Group=users
    Restart=on-failure
    RestartSec=10s
    TimeoutSec=300
    ExecStart=/bin/nohup /opt/dropbox/dropboxd
    ExecStop=/bin/ps aux | /usr/bin/grep dropbox | /usr/bin/grep opt | /usr/bin/awk
    '{print $2}' | /usr/bin/xargs /usr/bin/kill -9

    [Install]
    WantedBy=multi-user.target

---

systemd slow boot: http://littledaemons.tumblr.com/post/106832805315

---

https://medium.com/@johannes_gehrs/getting-started-with-systemd-on-debian-jessie-e024758ca63d

---

https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files
