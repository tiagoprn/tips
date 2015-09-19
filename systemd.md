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

There are some notable advantages to starting a tmux server at startup.
Notably, when you start a new tmux session, having the service already running
reduces any delays in the startup.

Furthermore, any customization attached to your tmux session will be retained
and your tmux session can be made to persist even if you have never logged in,
if you have some reason to do that (like a heavily scripted tmux configuration
or shared user tmux sessions).

The service below starts tmux for the specified user (i.e. start with
tmux@username.service):

/etc/systemd/system/tmux@.service

[Unit]
Description=Start tmux in detached session

[Service]
Type=forking
User=%I
ExecStart=/usr/bin/tmux new-session -s %u -d
ExecStop=/usr/bin/tmux kill-session -t %u

[Install]
WantedBy=multi-user.target

Tip: You may want to add WorkingDirectory=custom_path to customize working
directory.

Alternatively, you can place this file within your systemd/User directory
(without User=%I), for example ~/.config/systemd/user/tmux.service. This way
the tmux service will start when you log in.

---

The parameters supported by a systemd unit file:

http://www.freedesktop.org/software/systemd/man/systemd.service.html

---

# LOGS:

### How to view all journalctl log in real-time:

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

systemd slow boot: http://littledaemons.tumblr.com/post/106832805315

---

https://medium.com/@johannes_gehrs/getting-started-with-systemd-on-debian-jessie-e024758ca63d

---

https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files
