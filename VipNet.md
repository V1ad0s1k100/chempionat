## Распределение IP-адресов в сети

| ПК                | Внутренний IP | Внутренняя маска | Внешний IP   | Внешняя маска |
| ----------------- | ------------- | ---------------- | ------------ | ------------- |
| Net1-Coord        | 172.20.30.1   | 255.255.255.192  | 192.168.99.1 | 255.255.255.0 |
| Net1-Admin        | 172.20.30.3   | 255.255.255.192  |              |               |
| Net1-Open         | 172.20.30.2   | 255.255.255.192  |              |               |
| Net1-Oper         | 172.20.30.4   | 255.255.255.192  |              |               |
|                   |
| Net2-Coord        | 192.168.90.1  | 255.255.255.0    | 192.168.99.2 | 255.255.255.0 |
| Net2-Client-win   | 192.168.99.3  | 255.255.255.240  |              |               |
| Net2-Client-astra | 192.168.99.4  | 255.255.255.240  |              |               |
|                   |
| Net3-Coord        | 192.168.110.1 | 255.255.255.240  | 192.168.99.3 | 255.255.255.0 |
| Net3-Admin        | 192.168.110.2 | 255.255.255.240  |              |               |
| Net3-Open         | 192.168.110.3 | 255.255.255.240  |              |               |

## Структура сети VipNet

| ПК                | HW-Coordinator | MSSQL БД | ЦУС-Server | VipNet-Client | УКЦ | CA Informing | VipNet RegistrationPoint | VipNet PublicationService |
| ----------------- | -------------- | -------- | ---------- | ------------- | --- | ------------ | ------------------------ | ------------------------- |
| Net1-Coord        | \*             |          |            |               |     |              |                          |                           |
| Net1-Admin        |                |          | \*         | \*            | \*  | \*           |                          |                           |
| Net1-Open         |                | \*       |            |               |     |              |                          |                           |
| Net1-Oper         |                |          |            | \*            |     |              | \*                       | \*                        |
|                   |                |          |            |               |     |              |                          |                           |
| Net2-Coord        | \*             |          |            |               |     |              |                          |                           |
| Net2-Client-win   |                |          |            | \*            |     |              |                          |                           |
| Net2-Client-astra |                |          |            | \*            |     |              |                          |                           |
|                   |                |          |            |               |     |              |                          |                           |
| Net3-Coord        | \*             |          |            |               |     |              |                          |                           |
| Net3-Admin        |                | \*       | \*         | \*            | \*  |              |                          |                           |
| Net3-Open         |                |          |            |               |     |              |                          |                           |

## Для FTP на Net1-Oper

- Установка в "Диспетчере серверов"
- Создать папку `Pub` и в ней `in` и `out`
- Дать общий и расширенный доступы на папки `inetpub`, `Pub`, `in`, `out` для пользователей `все`, `СЕТЬ`, `СИСТЕМА`
- В управлении компьютером создать пользователя FTP и развернуть FTP на папку `inetpub`
- В "Центре управления сетью и общим доступом"/"Изменение параметров общего доступа для различных сетевых профилей" "Общий доступ к общедоступным папкам" `включить` и "Общий доступ с парольной защитой" `отключить`

## Строка подключения к базе данных ViPNet Administrator в CA Informing

```
Data Source=172.20.30.2\SQLExpress; Initial Catalog=ViPNetAdministrator;User Id=KcaUser;Password=Number1
```

## Агрегированный канал на Net2-Coord

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

## Проверка доступа на координаторах

```
inet ping «ip»
inet show interface ethN (где N – номер интерфейса)
machine show date
```

### Кластер

```
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
testip = 127.0.0.1
[channel] eth1 – 172.21.30.1/26
testip = 127.0.0.1
[sendconfig] – 192.168.30.2/24
2 координатор (Cluster)
eth0 – 192.18.30.2/27
eth1 – 172.21.30.1/26
eth2 – 192.168.30.2/24
[channel] eth0 – 192.18.30.2/27
testip = 127.0.0.1
[channel] eth1 – 172.21.30.1/26
testip = 127.0.0.1
[sendconfig] – 192.168.30.1/24
```
