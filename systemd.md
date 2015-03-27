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

----

The parameters supported by a systemd unit file:

http://www.freedesktop.org/software/systemd/man/systemd.service.html

---

How to view journalctl log in real-time (for example, to monitor docker logs):

    # journalctl -xn -f

---

systemd slow boot: http://littledaemons.tumblr.com/post/106832805315

---

https://medium.com/@johannes_gehrs/getting-started-with-systemd-on-debian-jessie-e024758ca63d

---

https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files
