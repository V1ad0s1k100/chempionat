### Строка подключения к базе данных ViPNet Administrator

```
Data Source=172.20.30.2\SQLExpress; Initial Catalog=ViPNetAdministrator;User Id=KcaUser;Password=Number1
```

### Агрегированный канал

```
en

inet ifconfig eth1 class slave
inet ifconfig eth2 class slave

inet bonding add 0 mode balance-rr slaves eth1 eth2

inet ifconfig bond0 address 192.168.99.2 netmask 255.255.255.0

iplir adapter add bond0 traffic on
inet ifconfig eth1 up
inet ifconfig eth2 up

inet ifconfig bond0 up
```

### Проверка доступа на координаторах

```
inet ping «ip»
inet show interface ethN (где N – номер интерфейса)
machine show date
```

### Кластер

Cluster
failover stop
failover config edit
failover config mode cluster и выбрать Yes
failover start
Пример:
1 координатор
eth0 – 192.18.30.2/27
eth1 – 172.21.30.1/26
eth2 – 192.168.30.1/24
[channel] eth0 – 192.18.30.2/27
[channel] eth1 – 172.21.30.1/26
[sendconfig] – 192.168.30.2/24
2 координатор (Cluster)
eth0 – 192.18.30.2/27
eth1 – 172.21.30.1/26
eth2 – 192.168.30.2/24
[channel] eth0 – 192.18.30.2/27
[channel] eth1 – 172.21.30.1/26
[sendconfig] – 192.168.30.1/24
