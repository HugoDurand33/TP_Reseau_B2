# TP5 INFRA : Intégration

## I. Tester

🌞 Récupérer l'application dans la VM hosting.tp5.b1s

```
[hugo@hosting ~]$ wget https://gitlab.com/it4lik/b2-network-2024/-/raw/main/tp/net/5/server.py?inline=false
```

🌞 Essayer de lancer l'app

- c'est le serveur qu'il faut lancer

```
[hugo@hosting ~]$ ./server.py &
[1] 14721
```

- une commande ss qui me montre que le programme écoute derrière IP:port

```
[hugo@hosting ~]$ ss -atnl
State         Recv-Q        Send-Q               Local Address:Port               Peer Address:Port       Process
LISTEN        0             128                        0.0.0.0:22                      0.0.0.0:*
LISTEN        0             1                        ☀️127.0.0.1:9999☀️                   0.0.0.0:*
LISTEN        0             128                           [::]:22                         [::]:*v
```

🌞 Tester l'app depuis hosting.tp5.b1

```
[hugo@hosting ~]$ ./client.py
Données reçues du client : b'Hello'
Calcul à envoyer: 3+3
6
[1]+  Done                    ./server.py
```

## II. Intégrer

### 1. Environnement

🌞 Créer un dossier /opt/calculatrice

```
[hugo@hosting /]$ sudo mkdir /opt/calculatrice

[hugo@hosting /]$ sudo mv /home/hugo/server.py /opt/calculatrice/
```

### 2. systemd service

🌞 Démarrer le service

```
[hugo@hosting system]$ sudo systemctl daemon-reload
[hugo@hosting system]$ sudo systemctl start calculatrice
```

- prouvez que...

- le service est actif avec un systemctl status

```
[hugo@hosting system]$ systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: active (running) since Fri 2024-10-18 15:59:59 CEST; 1s ago
   Main PID: 14889 (python3.9)
      Tasks: 1 (limit: 4665)
     Memory: 3.3M
        CPU: 45ms
     CGroup: /system.slice/calculatrice.service
             └─14889 /usr/bin/python3.9 /opt/calculatrice/server.py

Oct 18 15:59:59 hosting systemd[1]: Started Super calculatrice réseau.
```

- le service tourne derrière un port donné avec un ss

```
[hugo@hosting system]$ ss -atnl
State             Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port            Process
LISTEN            0                 128                                0.0.0.0:22                                0.0.0.0:*
LISTEN            0                 1                                127.0.0.1:9999                              0.0.0.0:*
LISTEN            0                 128                                   [::]:22                                   [::]:*
```

- c'est fonctionnel : vous pouvez utiliser l'app en lançant le client

```
[hugo@hosting ~]$ ./client.py
Calcul à envoyer: 9+10
21
```

🌞 Configurer une politique de redémarrage dans le fichier calculatrice.service

- un cat du fichier pour le compte-rendu

```
[hugo@hosting ~]$ cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python3.9 /opt/calculatrice/server.py
Restart=always
RestartSec=30s

[Install]
WantedBy=multi-user.target
```

🌞 Tester que la politique de redémarrage fonctionne

- lancez le service, repérer son PID, et tuer le process avec un kill -9 <PID>

```
[hugo@hosting ~]$ sudo !!
sudo kill -9 14998

[hugo@hosting ~]$ systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: activating (auto-restart) (Result: signal) since Fri 2024-10-18 16:16:21 CEST; 7s ago
    Process: 14998 ExecStart=/usr/bin/python3.9 /opt/calculatrice/server.py (code=killed, signal=KILL)
   Main PID: 14998 (code=killed, signal=KILL)
        CPU: 40ms

[hugo@hosting ~]$ systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: active (running) since Fri 2024-10-18 16:16:51 CEST; 7s ago
   Main PID: 15043 (python3.9)
      Tasks: 1 (limit: 4665)
     Memory: 3.3M
        CPU: 450ms
     CGroup: /system.slice/calculatrice.service
             └─15043 /usr/bin/python3.9 /opt/calculatrice/server.py

Oct 18 16:16:51 hosting systemd[1]: Started Super calculatrice réseau.
```

🌞 Ouverture automatique du firewall dans le fichier calculatrice.service

```
[hugo@hosting ~]$ cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStartPre=sudo firewall-cmd --add-port=9999/tcp --permanent
ExecStartPre=sudo firewall-cmd --reload
ExecStart=/usr/bin/python3.9 /opt/calculatrice/server.py
ExecStopPost=sudo firewall-cmd --remove-port=9999/tcp --permanent
ExecStopPost=sudo firewall-cmd --reload
Restart=always
RestartSec=30s

[Install]
WantedBy=multi-user.target
```

🌞 Vérifier l'ouverture automatique du firewall

- lancer/stopper le service et constater avec un firewall-cmd --list-all que le port s'ouvre/se ferme bien automatiquement

```
[hugo@hosting ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 9999/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
[hugo@hosting ~]$ sudo systemctl stop calculatrice
[hugo@hosting ~]$ sudo firewall-cmd --list-all
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

- tester une connexion depuis votre PC

```
PS C:\Users\gogob_jaotmdf> python .\Downloads\client.py
Calcul à envoyer: 0.1+0.2
0.30000000000000004
```

# 3. Monitoring

🌞 Installer Netdata sur hosting.tp5.b1

```
wget -O /tmp/netdata-kickstart.sh https://get.netdata.cloud/kickstart.sh && sh /tmp/netdata-kickstart.sh
```

🌞 Configurer une sonde TCP