# TP6 INFRA : STP, OSPF, bigger infra

## I. STP

### 1. Basic setup

🌞 Configurer STP sur les 3 switches

- je veux bien un show spanning-tree

```
IOU3#sh sp

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Root FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
```

🌞 Altérer le spanning-tree en désactivant un port

```
IOU1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#
IOU1(config)#int e0/2
IOU1(config-if)#
IOU1(config-if)#shutdown 

port vers le switch 3


IOU3#sh sp

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        200
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               🌞Root FWD 100🌞       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
```

🌞 Altérer le spanning-tree en modifiant le coût d'un lien

```
IOU3(config)#int e0/2
IOU3(config-if)#
IOU3(config-if)#spanning-tree vlan 1 cost 65536

le port vers IOU 1 qui n'est pas BLK

IOU3#sh sp

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        200
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Altn BLK 65536     128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
```

🦈 tp6_stp.pcapng

- [Capture STP](wireshark/STP1.pcapng)

## II. OSPF

🌞 Configurer OSPF sur tous les routeurs

```
routeur 5 

router ospf 1
 router-id 5.5.5.5
 log-adjacency-changes
 network 10.6.52.0 0.0.0.3 area 1
 default-information originate always
!

routeur 1

router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 network 10.6.3.0 0.0.0.255 area 2
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.41.0 0.0.0.3 area 3
!

routeur 4

router ospf 1
 router-id 4.4.4.4
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 3
 network 10.6.2.0 0.0.0.255 area 3
 network 10.6.41.0 0.0.0.3 area 3
!

routeur 2

router ospf 1
 router-id 2.2.2.2
 log-adjacency-changes
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
 network 10.6.52.0 0.0.0.3 area 1
!

routeur 3

router ospf 1
 router-id 3.3.3.3
 log-adjacency-changes
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
!
```

🌞 Test

```
waf.tp6.b2> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=50 time=171.622 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=50 time=169.522 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=50 time=150.412 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=50 time=187.603 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=50 time=177.647 ms

meo.tp6.b2> ping 10.6.3.11

10.6.3.11 icmp_seq=1 timeout
84 bytes from 10.6.3.11 icmp_seq=2 ttl=62 time=50.108 ms
84 bytes from 10.6.3.11 icmp_seq=3 ttl=62 time=41.855 ms
84 bytes from 10.6.3.11 icmp_seq=4 ttl=62 time=47.120 ms
84 bytes from 10.6.3.11 icmp_seq=5 ttl=62 time=40.000 ms

john.tp6.b2> ping 10.6.1.254

84 bytes from 10.6.1.254 icmp_seq=1 ttl=254 time=111.246 ms
84 bytes from 10.6.1.254 icmp_seq=2 ttl=254 time=106.226 ms
84 bytes from 10.6.1.254 icmp_seq=3 ttl=254 time=60.569 ms
84 bytes from 10.6.1.254 icmp_seq=4 ttl=254 time=156.896 ms
84 bytes from 10.6.1.254 icmp_seq=5 ttl=254 time=117.616 ms
```

🦈 tp6_ospf.pcapng

- [Capture OSPF](wireshark/OSPF1.pcapng)

## III. DHCP relay

🌞 Configurer un serveur DHCP sur dhcp.tp6.b1

```
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
subnet 10.6.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.6.1.100 10.6.1.200;
    # specify broadcast address
    option broadcast-address 10.6.1.255;
    # specify gateway
    option routers 10.6.1.254;
    option domain-name-servers 1.1.1.1;
}

subnet 10.6.3.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.6.3.100 10.6.3.200;
    # specify broadcast address
    option broadcast-address 10.6.3.255;
    # specify gateway
    option routers 10.6.3.254;
    option domain-name-servers 1.1.1.1;
}
```

```
[hugo@dhcp1 ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset>
     Active: active (running) since Thu 2024-10-31 16:46:00 CET; 4min 1s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1313 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 4665)
     Memory: 4.6M
        CPU: 40ms
     CGroup: /system.slice/dhcpd.service
```

🌞 Configurer un DHCP relay sur la passerelle de John

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int f1/0
R1(config-if)#ip helper-address 10.6.1.253
```

```
vers john

Oct 31 17:02:21 dhcp1 dhcpd[1313]: DHCPDISCOVER from 00:50:79:66:68:02 (john.tp6.b2) via 10.6.3.254
Oct 31 17:02:22 dhcp1 dhcpd[1313]: DHCPOFFER on 10.6.3.100 to 00:50:79:66:68:02 (john.tp6.b2) via 10.6.3.254
Oct 31 17:02:25 dhcp1 dhcpd[1313]: DHCPREQUEST for 10.6.3.100 (10.6.1.253) from 00:50:79:66:68:02 (john.tp6.b2) via 10.6.3.254
Oct 31 17:02:25 dhcp1 dhcpd[1313]: DHCPACK on 10.6.3.100 to 00:50:79:66:68:02 (john.tp6.b2) via 10.6.3.254

vers waf

Oct 31 17:04:28 dhcp1 dhcpd[1313]: DHCPDISCOVER from 00:50:79:66:68:00 (waf.tp6.b2) via enp0s3
Oct 31 17:04:28 dhcp1 dhcpd[1313]: DHCPOFFER on 10.6.1.100 to 00:50:79:66:68:00 (waf.tp6.b2) via enp0s3
Oct 31 17:04:29 dhcp1 dhcpd[1313]: DHCPREQUEST for 10.6.1.100 (10.6.1.253) from 00:50:79:66:68:00 (waf.tp6.b2) via enp0s3
Oct 31 17:04:29 dhcp1 dhcpd[1313]: DHCPACK on 10.6.1.100 to 00:50:79:66:68:00 (waf.tp6.b2) via enp0s3
```

- conf routeur 1

```
interface FastEthernet1/0
 ip address 10.6.3.254 255.255.255.0
 ip helper-address 10.6.1.253
 duplex auto
 speed auto
!
```

## IV. Bonus

### 1. ACL

🌞 Configurer une access-list


