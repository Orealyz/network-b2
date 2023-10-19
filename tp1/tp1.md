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

Détermination de l’itinéraire vers www.ynov.com [104.26.11.233]
avec un maximum de 30 sauts :

  1     4 ms     1 ms     1 ms  10.33.79.254
  2     2 ms     2 ms     2 ms  145.117.7.195.rev.sfr.net [195.7.117.145]
  3     2 ms     3 ms     3 ms  237.195.79.86.rev.sfr.net [86.79.195.237]
  4     4 ms    15 ms    13 ms  196.224.65.86.rev.sfr.net [86.65.224.196]
  5    12 ms    13 ms    11 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  6    18 ms    13 ms    15 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  7    11 ms    11 ms    11 ms  141.101.67.48
  8    12 ms    10 ms    10 ms  172.71.124.4
  9    14 ms    10 ms    19 ms  104.26.11.233

Itinéraire déterminé.
```

Par 9 paquets donc.

---

☀️ **IP publique**

```
PS C:\Users\b> curl  ifconfig.me


StatusCode        : 200
StatusDescription : OK
Content           : 195.7.117.146
[...]
```

---

☀️ **Scan réseau**

```
PS C:\Users\b> arp -a

Interface : 10.33.76.199 --- 0x14
  Adresse Internet      Adresse physique      Type
  10.33.65.112          0c-54-15-d9-32-3f     dynamique
  10.33.65.136          74-3a-f4-ce-4d-61     dynamique
  10.33.65.162          00-45-e2-31-61-4f     dynamique
  10.33.65.168          ac-74-b1-b5-72-1e     dynamique
  10.33.65.211          60-14-b3-ba-82-e9     dynamique
  10.33.65.233          18-1d-ea-73-80-7a     dynamique
  10.33.65.245          50-76-af-41-1d-b5     dynamique
  10.33.66.10           28-11-a8-30-cf-05     dynamique
  10.33.66.75           44-03-2c-9e-87-3e     dynamique
  10.33.66.126          14-13-33-73-94-83     dynamique
  10.33.66.136          1e-33-f9-04-58-a0     dynamique
  10.33.66.177          48-e7-da-0d-94-d9     dynamique
  10.33.67.35           88-d8-2e-eb-70-ad     dynamique
  10.33.68.212          28-cd-c4-ae-00-0f     dynamique
  10.33.68.234          bc-f4-d4-c0-b7-2f     dynamique
  10.33.68.252          94-e2-3c-43-51-98     dynamique
  10.33.69.1            08-d2-3e-35-00-a2     dynamique
  10.33.69.3            e0-2b-e9-77-20-bd     dynamique
  10.33.69.34           c8-58-c0-63-5a-92     dynamique
  10.33.69.124          9c-fc-e8-42-f1-7b     dynamique
  10.33.69.215          8c-c6-81-4b-6f-2c     dynamique
  10.33.70.118          f8-e4-e3-4a-1a-b9     dynamique
  10.33.70.125          8c-8d-28-f6-33-17     dynamique
  10.33.70.183          34-6f-24-93-79-ad     dynamique
  10.33.70.195          f0-b6-1e-0a-d0-34     dynamique
  10.33.70.211          a0-88-69-de-d7-c6     dynamique
  10.33.70.213          10-63-c8-75-91-79     dynamique
  10.33.71.18           48-e7-da-a7-c7-5f     dynamique
  10.33.71.23           74-4c-a1-d8-c2-59     dynamique
  10.33.71.26           00-41-0e-2b-91-5d     dynamique
  10.33.71.59           20-16-b9-29-84-75     dynamique
  10.33.71.75           60-e9-aa-dd-59-21     dynamique
  10.33.71.87           c4-3d-1a-c1-f6-97     dynamique
  10.33.71.105          40-5b-d8-cb-99-b9     dynamique
  10.33.71.185          84-1b-77-fd-5c-a0     dynamique
  10.33.71.195          3c-55-76-6a-ed-c3     dynamique
  10.33.73.67           40-1a-58-4b-44-84     dynamique
  10.33.73.108          08-71-90-4b-c0-44     dynamique
  10.33.74.39           f0-9e-4a-52-d2-53     dynamique
  10.33.75.38           46-8e-c4-2a-10-c9     dynamique
  10.33.75.120          4c-03-4f-e3-dc-c6     dynamique
  10.33.75.121          f4-26-79-3f-b9-66     dynamique
  10.33.75.126          14-13-33-e1-48-51     dynamique
  10.33.75.153          f4-26-79-a4-97-ff     dynamique
  10.33.76.128          b4-8c-9d-5c-a6-9f     dynamique
  10.33.76.134          d8-f8-83-fc-a2-79     dynamique
  10.33.76.137          10-3d-1c-ec-cf-2f     dynamique
  10.33.76.158          60-dd-8e-c7-2c-ca     dynamique
  10.33.77.21           00-91-9e-4d-0c-7e     dynamique
  10.33.77.232          94-b8-6d-bd-08-12     dynamique
  10.33.78.173          40-ed-00-58-b9-cc     dynamique
  10.33.79.151          70-66-55-fe-0f-41     dynamique
  10.33.79.174          f4-7b-09-73-b2-75     dynamique
  10.33.79.197          90-e8-68-d3-6f-8f     dynamique
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```



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
