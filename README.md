## Networks

### 1. Utilities

#### ip

```
skirata@debian:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:9b:91:34 brd ff:ff:ff:ff:ff:ff
    altname enx0800279b9134
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 85115sec preferred_lft 85115sec
    inet6 fe80::a00:27ff:fe9b:9134/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

1. lo - loopback, UP/LOWER_UP - активен
2. enp0s3 - основной адаптер, UP/LOWER_UP - активен, link/ether 08:00:27:9b:91:34 - мак адрес, altname enx0800279b9134 - альтернативное имя, 
inet 10.0.2.15/24 - IPv4 адрес, brd 10.0.2.255 broadcast, valid_lft 85115sec preferred_lft 85115sec - время валидности адреса 
```


#### ping

```
skirata@debian:~$ ping -c 4 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=63 time=98.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=63 time=123 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=63 time=44.2 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=63 time=67.5 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3000ms
rtt min/avg/max/mdev = 44.213/83.296/123.106/29.955 ms

icmp_seq - номер пакета, ttl=63 - время жизни пакета (прыжки до его уничтожения), time=98.4 ms - время, за которое пакет дошёл до узла и вернулся, rtt min/avg/max/mdev = 44.213/83.296/123.106/29.955 ms - минимальное, среднее, максимальное, время отклонения пакетов
```

#### traceroute

```
skirata@debian:~$ traceroute -n 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2  0.398 ms  0.287 ms  0.248 ms
 2  192.168.31.1  72.103 ms  72.060 ms  72.020 ms
 3  192.168.100.1  73.881 ms  71.943 ms  71.902 ms
 4  100.123.128.1  79.926 ms  79.174 ms  79.556 ms
 5  93.84.80.53  79.088 ms  79.050 ms  79.014 ms
 6  178.124.190.96  78.970 ms 178.124.190.68  86.955 ms 178.124.190.96  78.949 ms
 7  178.124.190.124  80.028 ms 178.124.190.128  7.931 ms  7.865 ms
 8  178.124.190.209  15.108 ms 178.124.190.201  7.798 ms 178.124.190.212  7.754 ms
 9  194.158.196.123  13.974 ms * 86.57.224.42  12.576 ms
10  * * 216.239.49.149  15.431 ms
11  142.250.224.89  13.560 ms 8.8.8.8  13.507 ms 142.250.236.51  33.333 ms

первая цифра по порядку - номер прыжка, ip-адрес - адрес маршрутизатора на данном прыжке, три записи времени - время отклика для трёх запросов к данному маршрутизатору, * - ответ не пришёл (по таймауту)
```

#### ss/netstat

```
skirata@debian:~$ ss -tuln
Netid   State    Recv-Q   Send-Q     Local Address:Port      Peer Address:Port  
udp     UNCONN   0        0                0.0.0.0:52236          0.0.0.0:*     
udp     UNCONN   0        0                0.0.0.0:5353           0.0.0.0:*     
udp     UNCONN   0        0                   [::]:39292             [::]:*     
udp     UNCONN   0        0                   [::]:5353              [::]:*     
tcp     LISTEN   0        4096           127.0.0.1:631            0.0.0.0:*     
tcp     LISTEN   0        128              0.0.0.0:22             0.0.0.0:*     
tcp     LISTEN   0        4096               [::1]:631               [::]:*     
tcp     LISTEN   0        128                 [::]:22                [::]:* 

tuln - TCP, UDP, только listen/unconn, только ip
netid - протокол, state - состояние сокета (listen - слушает, unconn - не имеет установленного соединения), Recv-Q/Send-Q - очередь получения/отправки, Local Address:Port/Peer Address:Port локальный и удалённый адрес (назначения), записи вида [::1]:631 для IPv6
```

#### netcat

```
skirata@debian:~$ netcat -zv google.com 80
DNS fwd/rev mismatch: google.com != nt-in-f113.1e100.net
DNS fwd/rev mismatch: google.com != nt-in-f138.1e100.net
DNS fwd/rev mismatch: google.com != nt-in-f102.1e100.net
DNS fwd/rev mismatch: google.com != nt-in-f101.1e100.net
DNS fwd/rev mismatch: google.com != nt-in-f139.1e100.net
DNS fwd/rev mismatch: google.com != nt-in-f100.1e100.net
google.com [142.251.98.113] 80 (http) open

-zv - режим сканирования, подробный результат проверки

DNS fwd/rev mismatch - балансировщики, не сам google.com
google.com [142.251.98.113] 80 (http) open - 80 порт открыт
```

#### dig

```
skirata@debian:~$ dig google.com A

; <<>> DiG 9.20.30-1~deb13u1-Debian <<>> google.com A
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 36598
;; flags: qr rd ra; QUERY: 1, ANSWER: 6, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		215	IN	A	142.250.109.102
google.com.		215	IN	A	142.250.109.101
google.com.		215	IN	A	142.250.109.138
google.com.		215	IN	A	142.250.109.113
google.com.		215	IN	A	142.250.109.100
google.com.		215	IN	A	142.250.109.139

;; Query time: 51 msec
;; SERVER: 10.0.2.3#53(10.0.2.3) (UDP)
;; WHEN: Sun Nov 01 17:39:29 +03 2026
;; MSG SIZE  rcvd: 135

DiG 9.20.30-1~deb13u1-Debian - версия утилиты, 
;; ->>HEADER<<- opcode: QUERY(обычный запрос) , status: NOERROR (статус - нет ошибок), id: 36598 (идентификатор транзакции)
;; flags(флаги ответа): qr(ответ) rd(запрос рекурсии) ra(сервер поддерживает рекурсию); QUERY: 1(в запросе 1 вопрос), ANSWER: 6(количество записей в ответе), AUTHORITY: 0(секция авторитетных серверов пуста, рекурсивный ответ из кэша), ADDITIONAL: 1(дополнительная псевдозапись)
;; OPT PSEUDOSECTION(расширение DNS, псевдозапись):
; EDNS: version: 0(версия), flags:; udp: 65494 (размер UDP буфера)

;; QUESTION SECTION(наш вопрос):
;google.com.                    IN      A
;; ANSWER SECTION(результаты):
google.com.(имя, к которому относится запись)             215 (ттл в секундах)     IN (класс интернет, №1)      A (тип записи IPv4)       142.250.109.102 (адрес, который принадлежит домену, 6шт в результате - балансировщики)

```


### NAT between kali <- debian 1 <- debian 2

```
debian 2

sudo nmcli connection add type ethernet ifname enp0s3 con-name "to_D1" ipv4.method manual ipv4.addresses 192.168.20.2/24 ipv4.gateway 192.168.20.1 ipv4.dns "1.1.1.1"

sudo nmcli connection up "to_D1"

sudo mkdir -p /etc/NetworkManager/conf.d/

sudo tee /etc/NetworkManager/conf.d/90-dns.conf > /dev/null <<'EOF'
[main]
dns=default
EOF

user@debian2:~$ ip a show enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:4a:24:79 brd ff:ff:ff:ff:ff:ff
    altname enx0800274a2479
    inet 192.168.20.2/24 brd 192.168.20.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::183d:ae56:adfa:8648/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

```
debian 1

sudo mkdir -p /etc/NetworkManager/conf.d/

sudo tee /etc/NetworkManager/conf.d/90-dns.conf > /dev/null <<'EOF'
[main]
dns=default
EOF

sudo nmcli connection add type ethernet ifname enp0s3 con-name "to_Kali" ipv4.method manual ipv4.addresses 192.168.10.2/24 ipv4.gateway 192.168.10.1 ipv4.dns "8.8.8.8"

sudo nmcli connection add type ethernet ifname enp0s8 con-name "to_D2" ipv4.method manual ipv4.addresses 192.168.20.1/24

sudo systemctl restart NetworkManager

sudo nmcli connection up "to_Kali"

sudo nmcli connection up "to_D2"

skirata@debian:~$ cat /etc/resolv.conf
#Generated by NetworkManager
nameserver 8.8.8.8

skirata@debian:~$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1

skirata@debian:~$ echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
net.ipv4.ip_forward=1

sudo nft add table ip nat

sudo nft add table ip nat

sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }

sudo nft add rule ip nat postrouting oifname "enp0s3" masquerade

sudo nft add table ip filter

sudo nft add chain ip filter forward { type filter hook forward priority 0 \; policy drop \; }

sudo nft add rule ip filter forward ct state established,related accept

sudo nft add rule ip filter forward iifname "enp0s8" oifname "enp0s3" accept

sudo nft add rule ip filter forward iifname "enp0s3" oifname "enp0s8" accept

sudo sh -c 'nft list ruleset > /etc/nftables.conf'

sudo systemctl enable nftables

sudo systemctl restart nftables

доп настройка в результате диагностики, ответ приходил не с того мак адреса

skirata@debian:~$ sudo sysctl -w net.ipv4.conf.all.arp_ignore=1
[sudo] password for skirata: 
net.ipv4.conf.all.arp_ignore = 1
skirata@debian:~$ sudo sysctl -w net.ipv4.conf.all.arp_announce=2
net.ipv4.conf.all.arp_announce = 2
skirata@debian:~$ echo "net.ipv4.conf.all.arp_ignore=1" | sudo tee -a /etc/sysctl.conf
net.ipv4.conf.all.arp_ignore=1
skirata@debian:~$ echo "net.ipv4.conf.all.arp_announce=2" | sudo tee -a /etc/sysctl.conf
net.ipv4.conf.all.arp_announce=2

```

```
kali

┌──(aden-skirata㉿kali)-[~]
└─$ sudo nmcli connection add type ethernet ifname eth0 con-name "WAN" ipv4.method auto
[sudo] password for aden-skirata: 
Connection 'WAN' (a5eed25f-cc60-4b1c-8fda-e71f0d5f765c) successfully added.

┌──(aden-skirata㉿kali)-[~]
└─$ sudo nmcli connection add type ethernet ifname eth1 con-name "LAN_D1" ipv4.method manual ipv4.addresses 192.168.10.1/24
Connection 'LAN_D1' (5f59edac-7324-4b0a-9576-4adbe9941d49) successfully added.

┌──(aden-skirata㉿kali)-[~]
└─$ sudo nmcli connection up "WAN"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/35)

┌──(aden-skirata㉿kali)-[~]
└─$ sudo nmcli connection up "LAN_D1"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/36)

┌──(aden-skirata㉿kali)-[~]
└─$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
                                                                                                                                                                                                                                    
┌──(aden-skirata㉿kali)-[~]
└─$ echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
net.ipv4.ip_forward=1

sudo nft add table ip nat

sudo nft add chain ip nat postrouting '{ type nat hook postrouting priority 100 ; }'

sudo nft add rule ip nat postrouting oifname "eth0" masquerade

sudo nft add table ip filter

sudo nft add chain ip filter forward '{ type filter hook forward priority 0 ; policy drop ; }'

sudo nft add rule ip filter forward ct state established,related accept

sudo nft add rule ip filter forward iifname "eth1" oifname "eth0" accept

sudo nft add rule ip filter forward iifname "eth0" oifname "eth1" accept

sudo sh -c 'nft list ruleset > /etc/nftables.conf'

sudo systemctl enable nftables

sudo systemctl restart nftables
```

#### Команды

```
ping

user@debian2:~$ ping -c 4 onliner.by
PING onliner.by (178.124.129.14) 56(84) bytes of data.
64 bytes from onliner.by (178.124.129.14): icmp_seq=1 ttl=54 time=5.77 ms
64 bytes from onliner.by (178.124.129.14): icmp_seq=2 ttl=54 time=6.38 ms
64 bytes from onliner.by (178.124.129.14): icmp_seq=3 ttl=54 time=9.78 ms
64 bytes from onliner.by (178.124.129.14): icmp_seq=4 ttl=54 time=6.59 ms

--- onliner.by ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3020ms
rtt min/avg/max/mdev = 5.774/7.133/9.782/1.558 ms
```

```
traceroute

user@debian2:~$ traceroute -n google.com
traceroute to google.com (142.250.109.101), 30 hops max, 60 byte packets
 1  192.168.20.1  0.635 ms  0.580 ms  0.557 ms
 2  192.168.10.1  1.357 ms  1.334 ms  1.311 ms
 3  10.0.2.1  1.462 ms  1.439 ms  1.395 ms
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *

мы не видим промежуточные точки из-за особенностей VirtualBox, посмотреть финальную точку можно с помощью утилиты tcptraceroute

user@debian2:~$ tcptraceroute google.com
Selected device enp0s3, address 192.168.20.2, port 36541 for outgoing packets
Tracing the path to google.com (142.250.120.138) on TCP port 80 (http), 30 hops max
 1  192.168.20.1  0.844 ms  0.447 ms  0.473 ms
 2  192.168.10.1  0.891 ms  0.596 ms  0.495 ms
 3  zo-in-f138.1e100.net (142.250.120.138) [open]  147.984 ms  102.395 ms  101.961 ms
```

#### Работа DNS

```
user@debian2:~$ cat /etc/resolv.conf
#Generated by NetworkManager
nameserver 1.1.1.1

skirata@debian:~$ cat /etc/resolv.conf
#Generated by NetworkManager
nameserver 8.8.8.8
```

#### Правила NAT

```
skirata@debian:~$ sudo nft list ruleset
[sudo] password for skirata: 
table ip nat {
	chain postrouting {
		type nat hook postrouting priority srcnat; policy accept;
		oifname "enp0s3" masquerade
	}
}
table ip filter {
	chain forward {
		type filter hook forward priority filter; policy drop;
		ct state established,related accept
		iifname "enp0s8" oifname "enp0s3" accept
		iifname "enp0s3" oifname "enp0s8" accept
	}
}

┌──(aden-skirata㉿kali)-[~]
└─$ sudo nft list ruleset 
[sudo] password for aden-skirata: 
table ip nat {
        chain postrouting {
                type nat hook postrouting priority srcnat; policy accept;
                oifname "eth0" masquerade
                oifname "eth0" masquerade
        }
}
table ip filter {
        chain forward {
                type filter hook forward priority filter; policy drop;
                ct state established,related accept
                iifname "eth1" oifname "eth0" accept
                iifname "eth0" oifname "eth1" accept
                ct state established,related accept
                iifname "eth1" oifname "eth0" accept
                iifname "eth0" oifname "eth1" accept
        }
}
   
```
