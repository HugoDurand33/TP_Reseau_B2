# TP4 INFRA : Router-on-a-stick

## I. Topo 1 : VLAN et Routing

🌞 Adressage

- définissez les IPs statiques sur toutes les machines sauf le routeur

```
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.10.1/24

PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.1.10.2/2

adm1> show ip

NAME        : adm1[1]
IP/MASK     : 10.1.20.1/24

[hugo@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e2:28:6c brd ff:ff:ff:ff:ff:ff
    inet 10.1.30.1/24 brd 10.1.30.255 scope global noprefixroute enp0s3
```

🌞 Configuration des VLANs

- ajout des ports du switches dans le bon VLAN (voir le tableau d'adressage de la topo 2 juste au dessus)

```
IOU1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   clients                          active    Et0/1, Et0/2
20   admins                           active    Et0/3
30   servers                          active    Et1/0
```

- il faudra ajouter le port qui pointe vers le routeur comme un trunk : c'est un port entre deux équipements réseau (un switch et un routeur)

```
IOU1#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20,30
```

🌞 Config du routeur

```
R1#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up
FastEthernet0/0.10         10.1.10.254     YES manual up                    up
FastEthernet0/0.20         10.1.20.254     YES manual up                    up
FastEthernet0/0.30         10.1.30.254     YES manual up                    up
FastEthernet1/0            unassigned      YES unset  administratively down down
FastEthernet1/1            unassigned      YES unset  administratively down down
FastEthernet2/0            unassigned      YES unset  administratively down down
FastEthernet2/1            unassigned      YES unset  administratively down down
```

🌞 Vérif

- tout le monde doit pouvoir ping le routeur sur l'IP qui est dans son réseau

```
PC1> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=19.546 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=20.013 ms
84 bytes from 10.1.10.254 icmp_seq=3 ttl=255 time=9.650 ms
84 bytes from 10.1.10.254 icmp_seq=4 ttl=255 time=6.992 ms

PC2> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=13.932 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=5.344 ms
84 bytes from 10.1.10.254 icmp_seq=3 ttl=255 time=12.748 ms
84 bytes from 10.1.10.254 icmp_seq=4 ttl=255 time=6.197 ms

adm1> ping 10.1.20.254

84 bytes from 10.1.20.254 icmp_seq=1 ttl=255 time=8.670 ms
84 bytes from 10.1.20.254 icmp_seq=2 ttl=255 time=7.717 ms
84 bytes from 10.1.20.254 icmp_seq=3 ttl=255 time=13.922 ms
84 bytes from 10.1.20.254 icmp_seq=4 ttl=255 time=12.820 ms

[hugo@web ~]$ ping 10.1.30.254
PING 10.1.30.254 (10.1.30.254) 56(84) bytes of data.
64 bytes from 10.1.30.254: icmp_seq=1 ttl=255 time=47.9 ms
64 bytes from 10.1.30.254: icmp_seq=2 ttl=255 time=20.2 ms
64 bytes from 10.1.30.254: icmp_seq=3 ttl=255 time=10.5 ms
64 bytes from 10.1.30.254: icmp_seq=4 ttl=255 time=17.7 ms
^C
--- 10.1.30.254 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 10.495/24.051/47.876/14.205 ms
```

- en ajoutant une route vers les réseaux, ils peuvent se ping entre eux

```
PC1> ping 10.1.20.1

84 bytes from 10.1.20.1 icmp_seq=1 ttl=63 time=35.866 ms
84 bytes from 10.1.20.1 icmp_seq=2 ttl=63 time=119.159 ms
84 bytes from 10.1.20.1 icmp_seq=3 ttl=63 time=110.925 ms
84 bytes from 10.1.20.1 icmp_seq=4 ttl=63 time=88.559 ms

adm1> ping 10.1.10.2

10.1.10.2 icmp_seq=1 timeout
84 bytes from 10.1.10.2 icmp_seq=2 ttl=63 time=24.421 ms
84 bytes from 10.1.10.2 icmp_seq=3 ttl=63 time=22.658 ms
84 bytes from 10.1.10.2 icmp_seq=4 ttl=63 time=319.834 ms
84 bytes from 10.1.10.2 icmp_seq=5 ttl=63 time=56.448 ms

[hugo@web ~]$ ping 10.1.10.1
PING 10.1.10.1 (10.1.10.1) 56(84) bytes of data.
64 bytes from 10.1.10.1: icmp_seq=1 ttl=63 time=19.5 ms
64 bytes from 10.1.10.1: icmp_seq=2 ttl=63 time=19.1 ms
64 bytes from 10.1.10.1: icmp_seq=3 ttl=63 time=35.9 ms
64 bytes from 10.1.10.1: icmp_seq=4 ttl=63 time=41.0 ms
^C
--- 10.1.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 19.051/28.864/40.992/9.749 ms
```
## II. NAT

### 3. Setup topologie 2

🌞 Ajoutez le noeud Cloud à la topo

- côté routeur, il faudra récupérer un IP en DHCP (voir le mémo Cisco)

```
R1#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up
FastEthernet0/0.10         10.1.10.254     YES manual up                    up
FastEthernet0/0.20         10.1.20.254     YES manual up                    up
FastEthernet0/0.30         10.1.30.254     YES manual up                    up
FastEthernet1/0            10.0.3.16       YES DHCP   up 
```

- vous devriez pouvoir ping 1.1.1.1

```
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 36/72/140 ms
```

🌞 Test

- configurez l'utilisation d'un DNS

```
PC1> ip dns 1.1.1.1
```

- vérifiez un ping vers un nom de domaine

```
PC1> ping google.com
google.com resolved to 142.251.37.46

84 bytes from 142.251.37.46 icmp_seq=1 ttl=113 time=45.376 ms
84 bytes from 142.251.37.46 icmp_seq=2 ttl=113 time=30.474 ms
84 bytes from 142.251.37.46 icmp_seq=3 ttl=113 time=35.473 ms
84 bytes from 142.251.37.46 icmp_seq=4 ttl=113 time=39.328 ms

[hugo@web ~]$ curl gitlab.com
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```

## III. Add a building

🌞  Vous devez me rendre le show running-config de tous les équipements

- config du routeur.

```
R1#show running-config
Building configuration...

Current configuration : 1482 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
no ip icmp rate-limit unreachable
!
!
ip cef
no ip domain lookup
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
ip tcp synwait-time 5
! 
!
!
!
!
interface FastEthernet0/0
 no ip address
 ip nat inside
 ip virtual-reassembly
 duplex half
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.254 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.254 255.255.255.0
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.254 255.255.255.0
!
interface FastEthernet1/0
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
!
ip nat inside source list 1 interface FastEthernet1/0 overload
!
access-list 1 permit any
no cdp log mismatch duplex
!
!
!
control-plane
!
!
!
!
!
!
gatekeeper
 shutdown
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
 stopbits 1
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
 stopbits 1
line vty 0 4
 login
!
!
end

```

- config du switch 1.

```

!
! Last configuration change at 14:45:00 UTC Thu Oct 17 2024
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU1
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL 
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
! 
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
!
interface Ethernet0/1
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
!
interface Ethernet1/0
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end
```

- config du switch 2

```

!
! Last configuration change at 13:20:29 UTC Thu Oct 17 2024
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU2
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL 
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
! 
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 20
 switchport mode access
!
interface Ethernet1/0
 switchport access vlan 30
 switchport mode access
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end
```

- config du switch 3

```

!
! Last configuration change at 14:46:59 UTC Thu Oct 17 2024
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU3
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL 
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
! 
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
!
!
!
!
control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
!
end
```

🌞  Mettre en place un serveur DHCP dans le nouveau bâtiment

- il doit distribuer des IPs aux clients dans le réseau clients qui sont branchés au même switch que lui

```
[hugo@dhcp1 ~]$ sudo !!
sudo cat /etc/dhcp/dhcpd.conf
[sudo] password for hugo:
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
subnet 10.1.10.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.1.10.100 10.1.10.200;
    # specify broadcast address
    option broadcast-address 10.1.10.255;
    # specify gateway
    option routers 10.1.10.254;
    option domain-name-servers 1.1.1.1;
}
```

🌞  Vérification

- un client récupère une IP en DHCP

```
PC3> sh ip

NAME        : PC3[1]
IP/MASK     : 10.1.10.100/24
GATEWAY     : 10.1.10.254
DNS         : 1.1.1.1
DHCP SERVER : 10.1.10.253
DHCP LEASE  : 0, 600/300/525
DOMAIN NAME : srv.world
MAC         : 00:50:79:66:68:03
LPORT       : 20028
RHOST:PORT  : 127.0.0.1:20029
MTU         : 1500
```

- il peut ping le serveur Web

```
PC3> ping 10.1.30.1

84 bytes from 10.1.30.1 icmp_seq=1 ttl=63 time=51.904 ms
84 bytes from 10.1.30.1 icmp_seq=2 ttl=63 time=31.693 ms
84 bytes from 10.1.30.1 icmp_seq=3 ttl=63 time=26.566 ms
84 bytes from 10.1.30.1 icmp_seq=4 ttl=63 time=19.196 ms
```

- il peut ping 1.1.1.1

```
PC3> ping 1.1.1.1

1.1.1.1 icmp_seq=1 timeout
84 bytes from 1.1.1.1 icmp_seq=2 ttl=53 time=45.621 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=53 time=48.633 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=53 time=29.164 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=53 time=40.843 ms
```

- il peut ping ynov.com

```
PC3> ping ynov.com
ynov.com resolved to 172.67.74.226

84 bytes from 172.67.74.226 icmp_seq=1 ttl=53 time=37.175 ms
84 bytes from 172.67.74.226 icmp_seq=2 ttl=53 time=47.886 ms
84 bytes from 172.67.74.226 icmp_seq=3 ttl=53 time=32.278 ms
84 bytes from 172.67.74.226 icmp_seq=4 ttl=53 time=48.325 ms
```