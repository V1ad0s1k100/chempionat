## Распределение IP-адресов в сети (распределенная)

| ПК                   | IP             | ОС    | домен demolab | домен aldpro |
| -------------------- | -------------- | ----- | ------------- | ------------ |
| demolab              | 192.168.88.21  | win10 |               |              |
| aldpro               | 192.168.88.30  | astra |               |              |
|                      |                |
| iwdm-db              | 192.168.88.31  | astra | \*            |
| iwdm-server          | 192.168.88.32  | win10 | \*            |
|                      |                |
| iwtm-db              | 192.168.88.33  | astra |               |              |
| iwtm-server          | 192.168.88.34  | astra |               |              |
|                      |                |
| iwdd                 | 192.168.88.35  | win10 | \*            |
|                      |                |
| demolab-client-astra | 192.168.88.101 | astra | \*            |
| demolab-client-win   | 192.168.88.102 | win10 | \*            |
| aldpro-client        | 192.168.88.103 | astra |               | \*           |

---

## Для подключения диска `deb file:///media/cdrom0/ 1.7_x86-64 main contrib non-free`

## Ввод `demolab-client-astra` в `demolab`

### Настройка прокси-сервера для загрузки пакетов (APT) `/etc/apt/apt.conf`

```
Acquire::http::proxy "http://fepo:fepo18@10.0.0.67:3128";
Acquire::https::proxy "http://fepo:fepo18@10.0.0.67:3128";
Acquire::::proxy "http://fepo:fepo18@10.0.0.67:3128";
```

### `/etc/apt/sources.list` с `dist-upgrade`

```
#deb cdrom:[OS Astra Linux 1.7.3 1.7_x86-64 DVD 1/] 1.7_x86-64 contrib main non-free
deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-main/ 1.7_x86-64 main contrib non-free
deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-update/ 1.7_x86-64 main contrib non-free

#deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-base/ 1.7_x86-64 main contrib non-free
#deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-extended/ 1.7_x86-64 main contrib non-free
```

### Для ввода утилита `fly-admin-ad-client`

## Доверительные отношения на `aldpro`

### `/etc/hosts`

```
192.168.88.21 demolab.demo.lab demo.lab demo
192.168.88.30 dc.aldpro.lab dc
192.168.88.103 client.aldpro.lab client
```

### Настройка сетевых интерфейсов `/etc/network/interfaces`

```
auto eth0
iface eth0 inet static
address 192.168.88.30/24
gateway 192.168.88.1
dns-nameservers 192.168.88.30 192.168.88.21
```

### `/etc/resolv.conf`

```
search dc aldpro.lab demo.lab
nameserver 127.0.0.1
nameserver 192.168.88.30
nameserver 192.168.88.21
```

### `/etc/sysctl.d/999-astra.conf `

```
kernel.sysrq = 0
fs.suid_dumpable = 0
kernel.randomize_va_space = 2
net.ipv4.tcp_timestamps = 0
net.ipv6.conf.all.disable_ipv6 = 1
```

### `/etc/sssd/sssd.conf ` с перезапуском sssd

```
subdomain_inherit = ignore_group_members, ldap_purge_cache_timeout
ignore_group_members = True
ldap_purge_cache_timeout = 0
```

### `/etc/bind/ipa-options-ext.conf ` с перезапуском ipactl

```
dnssec-enable no;
dnssec-validation no;
allow-query-cache { any; };
```

### Пароль для LDAP-администратора

```
sudo ldappasswd -ZZ -D 'cn=Directory Manager' -W -S uid=admin,cn=users,cn=accounts,dc=aldpro,dc=lab -H ldap://dc.aldpro.lab
```

### Очистка кеша Kerberos

```
kdestroy -A
kinit admin
```

### Создание доверительных отношений

```
sudo ipa-adtrust-install

ipa trust-add --type=ad demo.lab --range-type ipa-ad-trust --admin Administrator --password --two-way TRUE
```

> Пользователя в домен можно добавить двумя способами: при помощи постоянного пароля и одноразового. Для одноразового пароля выполняем команды ниже

### Выдача одноразового пароля на `aldpro`

```
ipa host-add client.aldpro.lab --random --ip-address=192.168.88.103 --force
```

---

## Доверительные отношения на `aldpro-client`

### Настройка прокси-сервера для загрузки пакетов (APT) `/etc/apt/apt.conf`

```
Acquire::http::proxy "http://fepo:fepo18@10.0.0.67:3128";
Acquire::https::proxy "http://fepo:fepo18@10.0.0.67:3128";
Acquire::::proxy "http://fepo:fepo18@10.0.0.67:3128";
```

### `/etc/apt/sources.list` с `dist-upgrade`

```
#deb cdrom:[OS Astra Linux 1.7.3 1.7_x86-64 DVD 1/] 1.7_x86-64 contrib main non-free
deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-main/ 1.7_x86-64 main contrib non-free
deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-update/ 1.7_x86-64 main contrib non-free

#deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-base/ 1.7_x86-64 main contrib non-free
#deb https://download.astralinux.ru/astra/stable/1.7_x86-64/repository-extended/ 1.7_x86-64 main contrib non-free
```

### Для ввода утилита `fly-admin-freeipa-client`

### Ввод по одноразовому паролю

```
sudo ipa-client-install --mkhomedir --password='ПАРОЛЬ' -U
```
