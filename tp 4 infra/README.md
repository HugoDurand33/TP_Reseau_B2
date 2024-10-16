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

