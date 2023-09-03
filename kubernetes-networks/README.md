Вопрос для самопроверки:
1. Почему следующая конфигурация валидна, но не имеет смысла?
    livenessProbe:
    exec:
    command:
    - 'sh'
    - '-c'
    - 'ps aux | grep my_web_server_process'
2. Бывают ли ситуации, когда она все-таки имеет смысл?
---
1) Потому что контейнеры, как правило, выполняют один лишь процесс, если процесс не будет выполняться, сам контейнер умрёт
2) Осмысленно разве что для проверки каких-либо второстепенных вспомогательных процессов, но это редкий случай, т.к. канонически пистаь один контейнер под один процесс
---
---
---
root@minikube:~# ipvsadm --list -n
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.96.0.1:443 rr
  -> 192.168.49.2:8443            Masq    1      0          0         
TCP  10.96.0.10:53 rr
  -> 10.244.0.2:53                Masq    1      0          0         
TCP  10.96.0.10:9153 rr
  -> 10.244.0.2:9153              Masq    1      0          0         
TCP  10.100.108.207:80 rr
  -> 10.244.0.24:8000             Masq    1      0          0         
  -> 10.244.0.25:8000             Masq    1      0          0         
  -> 10.244.0.26:8000             Masq    1      0          0         
UDP  10.96.0.10:53 rr
  -> 10.244.0.2:53                Masq    1      0          0  



root@minikube:~# ping -c1 10.100.108.207
PING 10.100.108.207 (10.100.108.207) 56(84) bytes of data.
From 10.100.108.207 icmp_seq=1 Destination Port Unreachable


root@minikube:~# ip addr show kube-ipvs0
30: kube-ipvs0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN group default 
    link/ether 12:37:cc:2c:0a:cc brd ff:ff:ff:ff:ff:ff
    inet 10.96.0.10/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.96.0.1/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever
    inet 10.100.108.207/32 scope global kube-ipvs0
       valid_lft forever preferred_lft forever


root@minikube:~# ipset list
Name: KUBE-EXTERNAL-IP-LOCAL
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536
Size in memory: 216
References: 0
Number of entries: 0
Members:

Name: KUBE-LOAD-BALANCER-SOURCE-IP
Type: hash:ip,port,ip
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 12 initval 0xb856fbbf
Size in memory: 208
References: 0
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-LOCAL-UDP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-SCTP-HASH
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 12 initval 0xb856fbbf
Size in memory: 216
References: 0
Number of entries: 0
Members:

Name: KUBE-LOOP-BACK
Type: hash:ip,port,ip
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 12 initval 0x4b08ea89
Size in memory: 544
References: 1
Number of entries: 6
Members:
10.244.0.25,tcp:8000,10.244.0.25
10.244.0.2,udp:53,10.244.0.2
10.244.0.26,tcp:8000,10.244.0.26
10.244.0.2,tcp:9153,10.244.0.2
10.244.0.24,tcp:8000,10.244.0.24
10.244.0.2,tcp:53,10.244.0.2

Name: KUBE-LOAD-BALANCER
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 216
References: 1
Number of entries: 0
Members:

Name: KUBE-LOAD-BALANCER-FW
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 216
References: 0
Number of entries: 0
Members:

Name: KUBE-LOAD-BALANCER-LOCAL
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 216
References: 0
Number of entries: 0
Members:

Name: KUBE-CLUSTER-IP
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 456
References: 3
Number of entries: 5
Members:
10.100.108.207,tcp:80
10.96.0.10,tcp:53
10.96.0.10,tcp:9153
10.96.0.10,udp:53
10.96.0.1,tcp:443

Name: KUBE-EXTERNAL-IP
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 216
References: 1
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-LOCAL-TCP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-LOCAL-SCTP-HASH
Type: hash:ip,port
Revision: 6
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 216
References: 0
Number of entries: 0
Members:

Name: KUBE-HEALTH-CHECK-NODE-PORT
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 2
Number of entries: 0
Members:

Name: KUBE-IPVS-IPS
Type: hash:ip
Revision: 5
Header: family inet hashsize 1024 maxelem 65536 bucketsize 10 initval 0x4b08ea89
Size in memory: 336
References: 1
Number of entries: 3
Members:
10.96.0.1
10.96.0.10
10.100.108.207

Name: KUBE-LOAD-BALANCER-SOURCE-CIDR
Type: hash:ip,port,net
Revision: 8
Header: family inet hashsize 1024 maxelem 65536 bucketsize 12 initval 0x7e90993b
Size in memory: 464
References: 0
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-TCP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-NODE-PORT-UDP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-SCTP-HASH
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x7e90993b
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-IPVS-IPS
Type: hash:ip
Revision: 5
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x7e90993b
Size in memory: 224
References: 1
Number of entries: 0
Members:

Name: KUBE-6-EXTERNAL-IP
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x7e90993b
Size in memory: 232
References: 1
Number of entries: 0
Members:

Name: KUBE-6-EXTERNAL-IP-LOCAL
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x7e90993b
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-LOCAL-TCP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-LOCAL-UDP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-6-LOAD-BALANCER-SOURCE-IP
Type: hash:ip,port,ip
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x5e1e835c
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-TCP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-UDP
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 0
Number of entries: 0
Members:

Name: KUBE-6-NODE-PORT-LOCAL-SCTP-HAS
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0x5e1e835c
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-LOOP-BACK
Type: hash:ip,port,ip
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xcc285d85
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-LOAD-BALANCER-FW
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xcc285d85
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-LOAD-BALANCER-LOCAL
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xcc285d85
Size in memory: 232
References: 0
Number of entries: 0
Members:

Name: KUBE-6-HEALTH-CHECK-NODE-PORT
Type: bitmap:port
Revision: 3
Header: range 0-65535
Size in memory: 8264
References: 2
Number of entries: 0
Members:

Name: KUBE-6-CLUSTER-IP
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xcc285d85
Size in memory: 232
References: 1
Number of entries: 0
Members:

Name: KUBE-6-LOAD-BALANCER
Type: hash:ip,port
Revision: 6
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xcc285d85
Size in memory: 232
References: 1
Number of entries: 0
Members:

Name: KUBE-6-LOAD-BALANCER-SOURCE-CID
Type: hash:ip,port,net
Revision: 8
Header: family inet6 hashsize 1024 maxelem 65536 bucketsize 12 initval 0xafd6517d
Size in memory: 1256
References: 0
Number of entries: 0
Members: