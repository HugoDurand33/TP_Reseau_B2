# TP3 INFRA : Premiers pas GNS, Cisco et VLAN

## I. Dumb switch

🌞 Commençons simple

- définissez les IPs statiques sur les deux VPCS

```
PC1> ip 10.3.1.1
Checking for duplicate address...
PC1 : 10.3.1.1 255.255.255.0

PC2> ip 10.3.1.2
Checking for duplicate address...
PC1 : 10.3.1.2 255.255.255.0
```

- ping un VPCS depuis l'autre

```
PC1> ping 10.3.1.2
84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=6.588 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=6.757 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=3.706 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=18.852 ms
```

- afficher la CAM table du switch et vérifier les MAC qui s'y trouvent (Google pour ça, c'est pas dans le mémo)

```
IOU2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0050.7966.6801    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
```

## II. VLAN

🌞 Adressage

- définissez les IPs statiques sur tous les VPCS

```
PC3> ip 10.3.1.3
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0
```

- vérifiez avec des ping que tout le monde se ping

```
PC2> ping 10.3.1.3
84 bytes from 10.3.1.3 icmp_seq=1 ttl=64 time=6.980 ms
84 bytes from 10.3.1.3 icmp_seq=2 ttl=64 time=5.488 ms
84 bytes from 10.3.1.3 icmp_seq=3 ttl=64 time=5.807 ms
84 bytes from 10.3.1.3 icmp_seq=4 ttl=64 time=3.826 ms

PC2> ping 10.3.1.1
84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=4.572 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=4.564 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=64 time=11.495 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=64 time=7.502 ms

PC1> ping 10.3.1.3

84 bytes from 10.3.1.3 icmp_seq=1 ttl=64 time=7.978 ms
84 bytes from 10.3.1.3 icmp_seq=2 ttl=64 time=6.783 ms
84 bytes from 10.3.1.3 icmp_seq=3 ttl=64 time=7.772 ms
84 bytes from 10.3.1.3 icmp_seq=4 ttl=64 time=2.397 ms
```

🌞 Configuration des VLANs

- déclaration des VLANs sur le switch sw1

```
IOU2#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/3, Et1/0, Et1/1, Et1/2
                                                Et1/3, Et2/0, Et2/1, Et2/2
                                                Et2/3, Et3/0, Et3/1, Et3/2
                                                Et3/3
10   VLAN0010                         active    Et0/0, Et0/1
20   VLAN0020                         active    Et0/2
```

🌞 Vérif

```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=6.054 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=2.794 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=1.542 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=1.272 ms

PC3> ping 10.3.1.1

host (10.3.1.1) not reachable

PC3> ping 10.3.1.2

host (10.3.1.2) not reachable
```

## III. Ptite VM DHCP

🌞 VM dhcp.tp3.b2

- installez un serveur DHCP

```
[hugo@localhost ~]$ sudo !!
sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
# create new
# specify domain name
option domain-name     "srv.world";
# specify DNS server's hostname or IP address
option domain-name-servers     dlp.srv.world;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.3.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.3.1.100 10.3.1.200;
    # specify broadcast address
    option broadcast-address 10.3.1.255;
    # specify gateway
}
```

- vérifier avec le pc4 que vous pouvez récupérer une IP en DHCP

```
PC4> dhcp
DDORA
PC4> show ip IP 10.3.1.100/24

NAME        : PC4[1]
IP/MASK     : 10.3.1.100/24
GATEWAY     : 0.0.0.0
DNS         : 180.43.145.38
DHCP SERVER : 10.3.1.253
DHCP LEASE  : 597, 600/300/525
DOMAIN NAME : srv.world
MAC         : 00:50:79:66:68:03
LPORT       : 20017
RHOST:PORT  : 127.0.0.1:20018
MTU         : 1500
```

- vérifier avec le pc5 que vous ne pouvez PAS récupérer une IP en DHCP

```
PC5> dhcp
DDD
Can't find dhcp server
```