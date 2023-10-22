# TP2 : Environnement virtuel


- [TP2 : Environnement virtuel](#tp2--environnement-virtuel)
- [I. Topologie réseau](#i-topologie-réseau)
  - [Compte-rendu](#compte-rendu)
- [II. Interlude accès internet](#ii-interlude-accès-internet)
- [III. Services réseau](#iii-services-réseau)
  - [1. DHCP](#1-dhcp)
  - [2. Web web web](#2-web-web-web)


# I. Topologie réseau

## Compte-rendu

☀️ Sur **`node1.lan1.tp2`**

```
[root@node1 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:16:c5:5d brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe16:c55d/64 scope link
       valid_lft forever preferred_lft forever
```

```
[root@node1 ~]# ip route
10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.11 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s8
```
```
[root@node1 ~]# ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=0.481 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=0.879 ms
^C
--- 10.1.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1034ms
rtt min/avg/max/mdev = 0.481/0.680/0.879/0.199 ms
```

```
[root@node1 ~]# traceroute 10.1.2.11
traceroute to 10.1.2.11 (10.1.2.11), 30 hops max, 60 byte packets
 1  10.1.1.254 (10.1.1.254)  0.524 ms  0.495 ms  0.487 ms
 2  10.1.2.11 (10.1.2.11)  0.912 ms !X  0.929 ms !X  0.884 ms !X
```
# II. Interlude accès internet

☀️ **Sur `router.tp2`**

```
[root@router ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=28.5 ms
^C
--- 8.8.8.8 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 28.500/28.500/28.500/0.000 ms
```

```
[root@router ~]# ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=115 time=22.8 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 22.842/22.842/22.842/0.000 ms
```
☀️ **Accès internet LAN1 et LAN2**

```
[root@node2 ~]# sudo ip route add default via 10.1.1.254
```

```
[root@node2 ~]# ip route show
default via 10.1.1.254 dev enp0s8
10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.12 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s8
```
```
[root@node2 ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=49.3 ms
^C
--- 8.8.8.8 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 49.282/49.282/49.282/0.000 ms
[root@node2 ~]# ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=114 time=24.1 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 24.118/24.118/24.118/0.000 ms
```

# III. Services réseau



## 1. DHCP


---

☀️ **Sur `dhcp.lan1.tp2`**

```
[root@dhcp dhcp]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:67:05:e0 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.253/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe67:5e0/64 scope link
       valid_lft forever preferred_lft forever
```

```
dnf -y install dhcp-server
```
```
  [root@dhcp dhcp]# cat dhcpd.conf
# create new
# specify domain name
option domain-name     "dhcp.lan1.tp2";
# specify DNS server's hostname or IP address
option domain-name-servers     dlp.srv.world;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.1.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.1.1.100 10.1.1.200;
    # specify broadcast address
    option broadcast-address 10.1.1.255;
    # specify gateway
    option routers 10.1.1.254;
    option domain-name-servers 1.1.1.1;
}
  
```
```
[root@dhcp dhcp]# systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Thu 2023-10-19 16:17:13 CEST; 2min 14s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1717 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 4611)
     Memory: 4.6M
        CPU: 4ms
     CGroup: /system.slice/dhcpd.service
             └─1717 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid
[...]
```

☀️ **Sur `node1.lan1.tp2`**


```
[root@node1 ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
NAME=enp0s8
DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
[root@node1 ~]# sudo systemctl restart NetworkManager
[root@node1 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:16:c5:5d brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet 10.1.1.100/24 brd 10.1.1.255 scope global secondary dynamic noprefixroute enp0s8
       valid_lft 557sec preferred_lft 557sec
    inet6 fe80::a00:27ff:fe16:c55d/64 scope link
       valid_lft forever preferred_lft forever
```

```
[root@node1 ~]# ping 10.1.2.11
PING 10.1.2.11 (10.1.2.11) 56(84) bytes of data.
64 bytes from 10.1.2.11: icmp_seq=1 ttl=63 time=0.594 ms
64 bytes from 10.1.2.11: icmp_seq=2 ttl=63 time=0.618 ms
^C
--- 10.1.2.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1042ms
rtt min/avg/max/mdev = 0.594/0.606/0.618/0.012 ms
```
## 2. Web web web


☀️ **Sur `web.lan2.tp2`**


```
  [root@web ~]# sudo dnf install nginx
```
```
sudo mkdir -p /var/www/site_nul/
```



 
```
[root@web ~]# sudo cat /var/www/site_nul/index.html
<!DOCTYPE html>
<html>
<head>
    <title>Site Nul</title>
</head>
<body>
    <h1>Bienvenue sur mon site nul !</h1>
</body>
</html>
[root@web ~]# sudo cat /etc/nginx/conf.d/site_nul.conf
server {
    listen 80;
    server_name site_nul.tp2;

    location / {
        root /var/www/site_nul;
        index index.html;
    }
}

```
  
```
[root@web ~]# sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
     Active: active (running) since Thu 2023-10-19 16:37:24 CEST; 2min 47s ago
    Process: 1645 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 1646 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 1647 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1648 (nginx)
  [...]
```

```
[root@web ~]# sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
```

```
nginx: configuration file /etc/nginx/nginx.conf test is successful
[root@web ~]# sudo ss -alpnt | grep nginx
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=2221,fd=6),("nginx",pid=2182,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=2221,fd=7),("nginx",pid=2182,fd=7))
[root@web ~]# sudo firewall-cmd --list-ports
80/tcp
```
☀️ **Sur `node1.lan1.tp2`**

```
[root@node1 ~]# cat /etc/hosts
10.1.2.12   site_nul.tp2 localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
[root@node1 ~]# curl site_nul.tp2
<!DOCTYPE html>
<html>
<head>
    <title>Site Nul</title>
</head>
<body>
    <h1>Bienvenue sur mon site nul !</h1>
</body>
</html>
```
