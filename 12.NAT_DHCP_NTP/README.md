# Lab - NAT. DHCP. NTP

## Цель:

   Настроить DHCP в офисе Москва
   
   Настроить синхронизацию времени в офисе Москва
   
   Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах

## Описание/Пошаговая инструкция выполнения домашнего задания:

   1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
   
   2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
   
   3. Настроите статический NAT для R20.
   
   4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.
   
   5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.
   
   5*. Настроите статический NAT(PAT) для офиса Чокурдах.
   
   6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.
   
   7. Все офисы в лабораторной работе должны иметь IP связность.

## Настройка

### NAT для R14

   1.1 Настроим ACL для VPC1 и VPC7
   
```
access-list 1 permit 10.10.10.0 0.0.0.255
access-list 1 permit 70.70.70.0 0.0.0.255
```

   1.2. Создадим loopback-интерфейс

```
interface Loopback1
ip address 101.101.101.14 255.255.255.255
```

   1.3 Проанонсируем сеть через протокол BGP

```
router bgp 1001
address-family ipv4
network 101.101.101.14 mask 255.255.255.255
```

   1.4 Настроим интерфейсы включим NAT

```
interface Ethernet0/2
ip nat outside
interface range Ethernet0/0-1, Ethernet0/3
ip nat inside

ip nat inside source list 1 interface Loopback1 overload
```

### NAT для R15

   1.1 Настроим ACL для VPC1 и VPC7
   
```
access-list 1 permit 10.10.10.0 0.0.0.255
access-list 1 permit 70.70.70.0 0.0.0.255
```

   1.2. Создадим loopback-интерфейс

```
interface Loopback1
ip address 101.101.101.15 255.255.255.255
```

   1.3 Проанонсируем сеть через протокол BGP

```
router bgp 1001
address-family ipv4
network 101.101.101.15 mask 255.255.255.255
```

   1.4 Настроим интерфейсы включим NAT

```
interface Ethernet0/2
ip nat outside
interface range Ethernet0/0-1, Ethernet0/3
ip nat inside

ip nat inside source list 1 interface Loopback1 overload
```

### NAT для R18

   2.1 Настроим ACL для VPC и VPC8
   
```
access-list 1 permit 100.100.100.0 0.0.0.255
access-list 1 permit 80.80.80.0 0.0.0.255
```

   2.2. Создадим loopback-интерфейс

```
interface Loopback1
ip address 101.101.101.18 255.255.255.248
```

   2.3 Проанонсируем сеть через протокол BGP

```
router bgp 2048
address-family ipv4
network 101.101.101.16 mask 255.255.255.248
```

   2.4 Настроим интерфейсы включим NAT

```
interface range Ethernet0/2-3
ip nat outside
interface range Ethernet0/0-1
ip nat inside

ip nat pool SPB_NAT 101.101.101.17 101.101.101.21 netmask 255.255.255.248
ip nat inside source list 1 pool SPB_NAT overload
```

### NAT для R20

   3.1 На R15
   
```
access-list 1 permit 100.100.100.0 0.0.0.255
access-list 1 permit 80.80.80.0 0.0.0.255
```

   Проверим

![](img/r20_to_r21.png)


### Доступ к R19

   Добавим loopback-интерфейс

```
ip address 19.19.19.19 255.255.255.255
ip ospf 1 area 101
```

   Настроим ssh-доступ

```
ip domain-name network.lab
crypto key generate rsa
ip ssh version 2
username pogodin secret pogodin
line vty 0 4
transport input ssh
login local
```

   На R14

```
ip nat inside source static tcp 19.19.19.19 22 101.101.101.14 22
```

   На R15

![](img/r25_to_r19.png)

![](img/r14_nat_tr.png)

### DHCP

   R12

```
ip dhcp excluded-address 10.10.10.1 10.10.10.15
ip dhcp pool POOL_FOR_VPC1
network 10.10.10.0 255.255.255.0
default-router 10.10.10.1
```

   R13

```
ip dhcp excluded-address 70.70.70.1 70.70.70.15
ip dhcp pool POOL_FOR_VPC1
network 70.70.70.0 255.255.255.0
default-router 70.70.70.1
```

### NAT на R28

```
ip nat inside source static 30.30.30.10 172.16.1.38
ip nat inside source static 31.31.31.10 172.16.1.14 

int e0/2.30           
ip nat inside
int e0/2.31  
ip nat inside
```

![](img/r28_nat.png)

### NTP

   На R12 и R13

```
ntp master 1
ntp update-calendar 
int range e0/1-3
ntp broadcast
```
   На R14 и R15

```
ntp master 2
ntp update-calendar 
int range e0/0-1
ntp broadcast client
int e0/3
ntp broadcast
```

![](img/r14_ntp.png)
   
   На R19 и R20

```
int e0/0
ntp broadcast client
```

![](img/r19_ntp.png)
