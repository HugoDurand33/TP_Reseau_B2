#  TP1 : Maîtrise réseau du votre poste

## I. Basics

☀️ Carte réseau WiFi 

- l'adresse MAC de votre carte WiFi

```
PS C:\Users\gogob_jaotmdf> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : ☀️8C-F8-C5-0E-69-C4☀️
```

- l'adresse IP de votre carte WiFi

```
PS C:\Users\gogob_jaotmdf> ipconfig

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%10
   Adresse IPv4. . . . . . . . . . . . . .: ☀️10.33.72.148☀️
```

- le masque de sous-réseau du réseau LAN auquel vous êtes connectés 

- en notation décimale, par exemple 255.255.0.0


```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%10
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.72.148
   Masque de sous-réseau. . . . . . . . . : ☀️255.255.240.0☀️
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
```
- en notation CIDR, par exemple /16

```
255.255.240.0/20
```

☀️ Déso pas déso

- l'adresse de réseau du LAN auquel vous êtes connectés en WiFi

```
10.33.64.0
```

- l'adresse de broadcast

```
10.33.79.255
```

- le nombre d'adresses IP disponibles dans ce réseau

```
4,094 IP disponibles.
```

☀️ Hostname

```
PS C:\Users\gogob_jaotmdf> hostname

HUUUUUGGGGGOOOO
```

☀️ Passerelle du réseau

- l'adresse IP de la passerelle du réseau

```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : 8C-F8-C5-0E-69-C4
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%10(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.72.148(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : jeudi 3 octobre 2024 13:53:12
   Bail expirant. . . . . . . . . . . . . : vendredi 4 octobre 2024 13:53:12
   Passerelle par défaut. . . . . . . . . : ☀️10.33.79.254☀️
```

- l'adresse MAC de la passerelle du réseau

```
PS C:\Users\gogob_jaotmdf> arp -a

Interface : 10.33.72.148 --- 0xa
  Adresse Internet      Adresse physique      Type
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
```

☀️ Serveur DHCP et DNS

- l'adresse IP du serveur DHCP qui vous a filé une IP

```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : 8C-F8-C5-0E-69-C4
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%10(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.72.148(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : jeudi 3 octobre 2024 13:53:13
   Bail expirant. . . . . . . . . . . . . : vendredi 4 octobre 2024 13:53:13
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : ☀️10.33.79.254☀️
```

- l'adresse IP du serveur DNS que vous utilisez quand vous allez sur internet

```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : 8C-F8-C5-0E-69-C4
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%10(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.72.148(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : jeudi 3 octobre 2024 13:53:13
   Bail expirant. . . . . . . . . . . . . : vendredi 4 octobre 2024 13:53:13
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   IAID DHCPv6 . . . . . . . . . . . : 109902021
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-F8-FF-74-00-E0-4C-68-BF-96
   Serveurs DNS. . .  . . . . . . . . . . : ☀️1.1.1.1☀️
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```

☀️ Table de routage

```
PS C:\Users\gogob_jaotmdf> route print

IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0     10.33.79.254     10.33.72.148     30
```

## II. Go further

☀️ Hosts ?

- faites en sorte que pour votre PC, le nom b2.hello.vous corresponde à l'IP 1.1.1.1

```
PS C:\Users\gogob_jaotmdf> cat C:\Windows\System32\drivers\etc\hosts

# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
☀️1.1.1.1 b2.hello.vous☀️
```

- prouvez avec un ping b2.hello.vous que ça ping bien 1.1.1.1

```
PS C:\Users\gogob_jaotmdf> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=15 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=16 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=15 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=15 ms TTL=55

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 15ms, Maximum = 16ms, Moyenne = 15ms
```

☀️ Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...

- l'adresse IP du serveur auquel vous êtes connectés pour regarder la vidéo

```
PS C:\Windows\system32>  netstat -n -a -b

[svchost.exe]
  UDP    0.0.0.0:50467          ☀️74.125.105.234☀️:443
```

- le port du serveur auquel vous êtes connectés

```
PS C:\Windows\system32>  netstat -n -a -b

[svchost.exe]
  UDP    0.0.0.0:50467          74.125.105.234:☀️443☀️
```

- le port que votre PC a ouvert en local pour se connecter au port du serveur distant

```
PS C:\Windows\system32>  netstat -n -a -b

[svchost.exe]
  UDP    0.0.0.0:☀️50467☀️         74.125.105.234:443
```

☀️ Requêtes DNS

- à quelle adresse IP correspond le nom de domaine www.thinkerview.com

```
PS C:\Windows\system32> nslookup  www.thinkerview.com
Serveur :   one.one.one.one
Address:  1.1.1.1

Réponse ne faisant pas autorité :
Nom :    www.thinkerview.com
Addresses:  2a06:98c1:3120::6
          2a06:98c1:3121::6
          188.114.96.6
          188.114.97.6
```

- à quel nom de domaine correspond l'IP 143.90.88.12

```
PS C:\Windows\system32> nslookup 143.90.88.12
Serveur :   one.one.one.one
Address:  1.1.1.1

Nom :    EAOcf-140p12.ppp15.odn.ne.jp
Address:  143.90.88.12
```

☀️ Hop hop hop

- par combien de machines vos paquets passent quand vous essayez de joindre www.ynov.com

```
PS C:\Users\gogob_jaotmdf> tracert www.ynov.com

Détermination de l’itinéraire vers www.ynov.com [104.26.10.233]
avec un maximum de 30 sauts :

  1     4 ms     2 ms     2 ms  10.33.79.254
  2     2 ms     2 ms     3 ms  145.117.7.195.rev.sfr.net [195.7.117.145]
  3     3 ms     2 ms     2 ms  237.195.79.86.rev.sfr.net [86.79.195.237]
  4     7 ms     6 ms     4 ms  196.224.65.86.rev.sfr.net [86.65.224.196]
  5    11 ms    13 ms    11 ms  164.147.6.194.rev.sfr.net [194.6.147.164]
  6     *        *       38 ms  162.158.20.24
  7    17 ms    16 ms    15 ms  162.158.20.240
  8    15 ms    15 ms    15 ms  104.26.10.233

Itinéraire déterminé.
```

☀️ IP publique

```
IP publique de ynov: 195.7.117.146
```

## III. Le requin

☀️ Capture ARP

[Wireshark ARP](wireshark/tp1_arp.pcap)

☀️ Capture DNS

[Wireshark DNS](wireshark/tp1_dns.pcap)

☀️ Capture TCP

[Wireshark TCP](wireshark/tp1_tcp.pcap)

