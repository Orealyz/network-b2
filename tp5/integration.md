# II. Intégrer

Ce qu'on appelle l'intégration d'une application, c'est le fait de l'installer mais aussi de l'adapter aux règles et bonnes pratiques d'une infrastructure donnée.

Il existe en effet des bonnes pratiques partagées par à peu près tous les admins, mais aussi des règles spécifiques à un environnement ou un autres. On peut par exemple imaginer des règles de sécu plus restrictives pour une infrastructure qui gère des données médicales.

Ici on va se cantonner à des réflexes assez élémentaires :

- on va **créer un *service* systemd**
  - pour gérer plus facilement le serveur
  - le gérer de façon unifiée, comme n'importe quel autre service de la machine
  - proposer des politiques de restart par exemple
- et **monitoring du port TCP** sur lequel le serveur écoute
  - on monitore pour avoir des infos au moins si le service tombe
  - on met en place cette surveillance avec mon copain Netdata

## Sommaire

- [II. Intégrer](#ii-intégrer)
  - [Sommaire](#sommaire)
  - [1. Environnement](#1-environnement)
  - [2. systemd service](#2-systemd-service)
    - [B. Service basique](#b-service-basique)
    - [C. Amélioration du service](#c-amélioration-du-service)
  - [3. Monitoring](#3-monitoring)

## 1. Environnement

Petite section pour préparer un environnement correct. On va faire le strict minimum.

C'est une application installée à la main, on va la positionner un peu à l'arrache dans `/opt`.

🌞 **Créer un dossier /opt/calculatrice**

```
[root@hosting ~]# ls /opt/calculatrice/
client.py  server.py
```

## 2. systemd service

Dans cette section, on va créer un fichier `calculatrice.service` qui nous permettra de saisir des commandes comme :

```bash
$ sudo systemctl start calculatrice
$ sudo systemctl enable calculatrice

$ sudo journalctl -xe -u calculatrice
```

### B. Service basique

🌞 **Créer le fichier `/etc/systemd/system/calculatrice.service`**

```
[root@hosting ~]# cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py

[Install]
WantedBy=multi-user.target

```
- vous devrez apposer des permissions correctes sur ce fichier
  - c'est quoi "correctes" ?
  - go faire un `ls -al` dans le dossier pour voir les permissions des autres fichiers `.service` déjà présents et faire pareil
- un contenu minimal serait :

j'ai pris comme exemple :

```
[root@hosting system]# ls -al /usr/lib/systemd/system/dbus-broker.service
-rw-r--r--. 1 root root 529 Nov  1  2022 /usr/lib/systemd/system/dbus-broker.service
```

J'ai fais la meme chose pour la calculatrice.service

```
[root@hosting system]# ls -al /etc/systemd/system/calculatrice.service
-rw-r--r-- 1 root root 152 Nov 28 14:07 /etc/systemd/system/calculatrice.service
```

```bash
$ sudo cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py

[Install]
WantedBy=multi-user.target
```

> On préfère utiliser le chemin absolu vers `python` plutôt que juste écrire la commande. Cela permet d'éviter des soucis s'il existe plusieurs version de Python installée par exemple. Sur certaines versions de systemd, ça marche carrément pas si on précise pas le chemin absolu.

➜ **Il est nécessaire** de taper la commande `systemctl daemon-reload` dès qu'on modifie un fichier `.service` pour indiquer à systemd de relire les fichiers et adopter la nouvelle configuration.

🌞 **Démarrer le service**

- avec une commande `sudo systemctl start calculatrice`
- prouvez que...
  - le service est actif avec un `systemctl status`
  - le service tourne derrière un port donné avec un `ss`
  - c'est fonctionnel : vous pouvez utiliser l'app en lançant le client

```
[root@hosting system]# sudo systemctl daemon-reload
[root@hosting system]# sudo systemctl start calculatrice
[root@hosting system]# sudo systemctl enable calculatrice
Created symlink /etc/systemd/system/multi-user.target.wants/calculatrice.service → /etc/systemd/system/calculatrice.service.
[root@hosting calculatrice]# sudo systemctl status calculatrice.service
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 14:34:39 CET; 1s ago
   Main PID: 1713 (python)
      Tasks: 2 (limit: 4611)
     Memory: 5.5M
        CPU: 55ms
     CGroup: /system.slice/calculatrice.service
             └─1713 /usr/bin/python /opt/calculatrice/bs_server.py

Nov 28 14:34:39 hosting.tp5.b1 systemd[1]: Started Super calculatrice réseau.
Nov 28 14:34:39 hosting.tp5.b1 python[1713]: Le serveur tourne sur 127.0.0.1:13337

```

```
[root@hosting ~]# ss -tulpn | grep :13337
tcp   LISTEN 0      1          127.0.0.1:13337      0.0.0.0:*    users:(("python",pid=1713,fd=4))

```

```
[root@hosting calculatrice]# python client.py 
Veuillez saisir une opération arithmétique : 
```
### C. Amélioration du service

➜ **Redémarrage automatique**

🌞 **Configurer une politique de redémarrage** dans le fichier `calculatrice.service`

```
[root@hosting calculatrice]# cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target

```


> [Référez-vous à cette page de la doc officielle de systemd](https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html) pour + d'informations sur ces clauses. Notamment savoir quoi écrire pour `Restart=`.

🌞 **Tester que la politique de redémarrage fonctionne**

```
[root@hosting calculatrice]# sudo kill -9 1857 
[root@hosting calculatrice]# sudo systemctl status calculatrice.service
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: activating (auto-restart) (Result: signal) since Tue 2023-11-28 14:53:54 CET; 7s ago
    Process: 1857 ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py (code=killed, signal=KILL)
   Main PID: 1857 (code=killed, signal=KILL)
        CPU: 59ms

Nov 28 14:53:54 hosting.tp5.b1 systemd[1]: calculatrice.service: Main process exited, code=killed, status=9/KILL
Nov 28 14:53:54 hosting.tp5.b1 systemd[1]: calculatrice.service: Failed with result 'signal'.
[root@hosting calculatrice]# sudo systemctl status calculatrice.service
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 14:54:24 CET; 22s ago
   Main PID: 1875 (python)
      Tasks: 2 (limit: 4611)
     Memory: 5.5M
        CPU: 55ms
     CGroup: /system.slice/calculatrice.service
             └─1875 /usr/bin/python /opt/calculatrice/bs_server.py

Nov 28 14:54:24 hosting.tp5.b1 systemd[1]: Started Super calculatrice réseau.
Nov 28 14:54:24 hosting.tp5.b1 python[1875]: Le serveur tourne sur 127.0.0.1:13337

```
---

➜ **Firewall !**


🌞 **Ouverture automatique du firewall** dans le fichier `calculatrice.service`



```
[root@hosting calculatrice]# sudo systemctl cat calculatrice.service
# /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStartPre=/usr/bin/firewall-cmd --add-port=13337/tcp
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py
Restart=on-failure
RestartSec=30
ExecStopPost=/usr/bin/firewall-cmd --remove-port=13337/tcp

[Install]
WantedBy=multi-user.target

```
🌞 **Vérifier l'ouverture automatique du firewall**


```
[root@hosting calculatrice]# sudo systemctl status calculatrice.service
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 15:14:08 CET; 1s ago
    Process: 2093 ExecStartPre=/usr/bin/firewall-cmd --add-port=13337/tcp (code=exited, status=0/SUCCESS)
   Main PID: 2094 (python)
      Tasks: 2 (limit: 4611)
     Memory: 5.6M
        CPU: 252ms
     CGroup: /system.slice/calculatrice.service
             └─2094 /usr/bin/python /opt/calculatrice/bs_server.py

Nov 28 15:14:08 hosting.tp5.b1 systemd[1]: Starting Super calculatrice réseau...
Nov 28 15:14:08 hosting.tp5.b1 firewall-cmd[2093]: success
Nov 28 15:14:08 hosting.tp5.b1 systemd[1]: Started Super calculatrice réseau.
Nov 28 15:14:08 hosting.tp5.b1 python[2094]: Le serveur tourne sur 127.0.0.1:13337
[root@hosting calculatrice]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 13337/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
[root@hosting calculatrice]# sudo systemctl stop calculatrice
[root@hosting calculatrice]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

Connexion avec le PC: 

```
 ~/D/2/n/tp5   main ±  py client.py
Veuillez saisir une opération arithmétique : 8+8
```

```
[root@hosting calculatrice]# sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 15:44:48 CET; 1min 0s ago
    Process: 1830 ExecStartPre=/usr/bin/firewall-cmd --add-port=13337/tcp (code=exited, status=0/SUCCESS)
   Main PID: 1831 (python)
      Tasks: 2 (limit: 4611)
     Memory: 5.6M
        CPU: 253ms
     CGroup: /system.slice/calculatrice.service
             └─1831 /usr/bin/python /opt/calculatrice/bs_server.py

Nov 28 15:44:48 hosting.tp5.b1 systemd[1]: Starting Super calculatrice réseau...
Nov 28 15:44:48 hosting.tp5.b1 firewall-cmd[1830]: success
Nov 28 15:44:48 hosting.tp5.b1 systemd[1]: Started Super calculatrice réseau.
Nov 28 15:44:48 hosting.tp5.b1 python[1831]: Le serveur tourne sur 10.33.74.69:13337
Nov 28 15:44:59 hosting.tp5.b1 python[1831]: Un client ('10.33.74.1', 47242) s'est connecté.
Nov 28 15:45:05 hosting.tp5.b1 python[1831]: Le client ('10.33.74.1', 47242) a envoyé 8+8
Nov 28 15:45:05 hosting.tp5.b1 python[1831]: Réponse envoyée au client ('10.33.74.1', 47242) : 16.

```

```
[root@hosting calculatrice]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 13337/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 

```


## 3. Monitoring

🌞 **Installer Netdata** sur `hosting.tp5.b1`

- en suivant la doc officielle
- assurez-vous que le dashboard Web est fonctionnel une fois l'install terminée

```
[root@hosting calculatrice]# sudo systemctl status netdata
● netdata.service - Real time performance monitoring
     Loaded: loaded (/usr/lib/systemd/system/netdata.service; enabled; preset: enabled)
     Active: active (running) since Tue 2023-11-28 15:38:56 CET; 22min ago
    Process: 693 ExecStartPre=/bin/mkdir -p /var/cache/netdata (code=exited, status=0/SUCCESS)
    Process: 700 ExecStartPre=/bin/chown -R netdata /var/cache/netdata (code=exited, status=0/SUCCESS)
    Process: 708 ExecStartPre=/bin/mkdir -p /run/netdata (code=exited, status=0/SUCCESS)
    Process: 711 ExecStartPre=/bin/chown -R netdata /run/netdata (code=exited, status=0/SUCCESS)
   Main PID: 712 (netdata)
      Tasks: 87 (limit: 4611)
     Memory: 199.7M
        CPU: 33.927s
     CGroup: /system.slice/netdata.service
             ├─712 /usr/sbin/netdata -P /run/netdata/netdata.pid -D
             ├─737 /usr/sbin/netdata --special-spawn-server
             ├─951 bash /usr/libexec/netdata/plugins.d/tc-qos-helper.sh 1
             ├─958 /usr/libexec/netdata/plugins.d/apps.plugin 1
             ├─962 /usr/libexec/netdata/plugins.d/debugfs.plugin 1
             ├─964 /usr/libexec/netdata/plugins.d/ebpf.plugin 1
             ├─968 /usr/libexec/netdata/plugins.d/go.d.plugin 1
             └─973 /usr/libexec/netdata/plugins.d/systemd-journal.plugin 1

```

🌞 **Configurer une sonde TCP**

```
jobs:
  - name: calculatrice
    host: 127.0.0.1
    ports:
      - 33337

```
- [netdata](./netdata.png)
🌞 **Alerting Discord**

- [discord](./discord.png)
