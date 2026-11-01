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
