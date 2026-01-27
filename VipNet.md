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
