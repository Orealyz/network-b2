# III. Héberger

## Sommaire

- [III. Héberger](#iii-héberger)
  - [Sommaire](#sommaire)
  - [1. Interface bridge](#1-interface-bridge)
  - [2. Firewall](#2-firewall)
  - [3. Serveur SSH](#3-serveur-ssh)
  - [4. Serveur Calculatrice](#4-serveur-calculatrice)

## 1. Interface bridge

## 2. Firewall

🌞 **Assurez-vous qu'aucun port est inutilement ouvert**

## 3. Serveur SSH

🌞 **Conf serveur SSH**

```
[root@hosting calculatrice]# ss -tulnp | grep sshd
tcp   LISTEN 0      128     192.168.56.2:22         0.0.0.0:*    users:(("sshd",pid=1845,fd=3))    
```
## 4. Serveur Calculatrice

🌞 **Conf serveur Calculatrice**

```

[root@hosting calculatrice]# ss -tulnp | grep "13337"
tcp   LISTEN 0      1          10.0.2.15:13337      0.0.0.0:*    users:(("python",pid=1884,fd=4))  
```

Test effectué avec toi et cela était fonctionnel.
