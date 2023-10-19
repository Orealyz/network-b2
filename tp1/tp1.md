# TP1 : Maîtrise réseau du poste

- [TP1 : Maîtrise réseau du poste](#tp1--maîtrise-réseau-du-poste)
- [I. Basics](#i-basics)
- [II. Go further](#ii-go-further)
- [III. Le requin](#iii-le-requin)

# I. Basics

☀️ **Carte réseau WiFi**

```
PS C:\Users\b> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
Adresse physique . . . . . . . . . . . : 20-1E-88-3A-B3-35
Adresse IPv4. . . . . . . . . . . . . .: 10.33.76.199(préféré)
 Masque de sous-réseau. . . . . . . . . : 255.255.240.0
/20
```

---

☀️ **Déso pas déso**

```
Adresse de réseau : 10.33.64.0
```

```
Adresse de broadcast : 10.33.79.255
```

```
Nombre d'adresses IP disponibles : 4094
```

---

☀️ **Hostname**

```
PS C:\Users\b> hostname
DESKTOP-0JKFI2T
```

---

☀️ **Passerelle du réseau**

```
PS C:\Users\b> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
Passerelle par défaut. . . . . . . . . : 10.33.79.254
```

```
PS C:\Users\b> arp -a
[...]
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
```

---

☀️ **Serveur DHCP et DNS**

```
PS C:\Users\b> ipconfig /all
[...]
Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
```

---

☀️ **Table de routage**

```
PS C:\Users\b> netstat -rn
[...]
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0     10.33.79.254     10.33.76.199     30
```

---

# II. Go further

---

☀️ **Hosts ?**

```
PS C:\Users\b> cat C:\Windows\System32\drivers\etc\hosts
[...]
1.1.1.1         b2.hello.vous
```

```
PS C:\Users\b> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=12 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=11 ms TTL=57

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 3, reçus = 3, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 10ms, Maximum = 12ms, Moyenne = 11ms
```

---

☀️ **Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...**

J'ai utiliséWireshark pour m'aider à identifier l'adresse ip du serveur auquel je suis connecté pour regarder la vidéo

```
PS C:\Windows\system32> netstat -n -b -a | Select-String 172.217.20.163 -Context 0,1

>   TCP    10.33.76.199:50746     172.217.20.163:443     ESTABLISHED
   [chrome.exe]
```

```
443
```

```
50746
```

---

☀️ **Requêtes DNS**

```
PS C:\Users\b> nslookup www.ynov.com
Serveur :   bouygues.lan
Address:  2001:861:4482:b250:46d4:54ff:fe85:8895

Réponse ne faisant pas autorité :
Nom :    www.ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::ac43:4ae2
          2606:4700:20::681a:ae9
          104.26.11.233
          104.26.10.233
          172.67.74.226
```

```
PS C:\Users\b> nslookup 174.43.238.89
Serveur :   bouygues.lan
Address:  2001:861:4482:b250:46d4:54ff:fe85:8895

Nom :    89.sub-174-43-238.myvzw.com
Address:  174.43.238.89
```

---

☀️ **Hop hop hop**

```
PS C:\Users\b> tracert www.ynov.com

Détermination de l’itinéraire vers www.ynov.com [2606:4700:20::681a:ae9]
avec un maximum de 30 sauts :

  1     1 ms     1 ms     1 ms  bouygues.lan [2001:861:4482:b250:46d4:54ff:fe85:8895]
  2     *        *        *     Délai d’attente de la demande dépassé.
```

Par un seul paquet donc.

---

☀️ **IP publique**

```
PS C:\Users\b> ipconfig
[...]
 Passerelle par défaut. . . . . . . . . : 10.33.79.254
```

---

☀️ **Scan réseau**

```
PS C:\Users\b> arp -a

Interface : 172.16.64.1 --- 0x3
  Adresse Internet      Adresse physique      Type
  172.16.64.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  [...]
```

Il y en a 35.

# III. Le requin

---

☀️ **Capture ARP**
j'ai utilisé le filtre "arp"

[ARP](./captures/arp.pcap)

---

☀️ **Capture DNS**

nslookup google.com

```
PS C:\Users\b> nslookup google.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:818::200e
          142.250.179.110
```

## [DNS](./captures/dns.pcap)

☀️ **Capture TCP**

[TCP](./captures/tcp.pcap)

---
