# Lab - Finish

## Цель:
   
   Организация удаленного доступа в сети предприятия с элементами автоматизации

## Описание/Пошаговая инструкция выполнения домашнего задания:

1. Подготовка устройств (руками)
2. Настройка интерфейсов (ansible)
3. Настройка VLAN и VPC  
4. BGP (ansible)
5. DMVPN (ansible)
   

## Topology

![](img/topology.png)

| Офис         | IPv4-сеть        |
|--------------|------------------|
| Москва       | 10.10.111.0/24 |
| (AS 1001)    |                  |
|--------------|------------------|
| Краснодар    | 10.10.121.0/24 |
| (AS 101)     |                  |
|--------------|------------------|
| Ростов       | 10.10.131.0/24 |
| (AS 201)     |                  |
|--------------|------------------|
| Региональные | 10.10.141.0/24 |
| провайдеры   |                  |
| (AS 301)     |                  |
|--------------|------------------|

Москва (AS 1001):
| Устройство | Порт | IP-адрес           | Описание     |
|------------|------|--------------------|--------------|
| MSK-R      | e0/0 | 10.10.40.111/24    | ANSIBLE_PORT |
|            | e0/1 | 10.10.111.10/30    | TO_ISP2      |
|            | e0/2 | 10.10.111.14/30    | TO_ISP1      |
|            | e0/3 | 10.10.141.10/30    | to_REG-ISP1  |
|            | e1/0 | 10.10.141.14/30    | to_REG-ISP2  |
|            | lo0  | 111.111.111.111/32 |              |
|------------|------|--------------------|--------------|
| ISP1       | e0/0 | 10.10.40.112       | ANSIBLE_PORT |
|            | e0/1 | 10.10.111.13/30    | TO_MSK-R     |
|            | e0/2 | 10.10.111.18/30    | TO_DC1       |
|            | e0/3 | 10.10.111.22/30    | TO_DC2       |
|            | lo0  | 112.112.112.112/32 |              |
|------------|------|--------------------|--------------|
| ISP2       | e0/0 | 10.10.40.113       | ANSIBLE_PORT |
|            | e0/1 | 10.10.111.9/30     | TO_MSK-R     |
|            | e0/2 | 10.10.111.26/30    | TO_DC1       |
|            | e0/3 | 10.10.111.30/30    | TO_DC2       |
|            | lo0  | 113.113.113.113/32 |              |
|------------|------|--------------------|--------------|
| DC1        | e0/0 | 10.10.40.114       | ANSIBLE_PORT |
|            | e0/1 | 10.10.111.17/30    | TO_ISP1      |
|            | e0/2 | 10.10.111.25/30    | TO_ISP2      |
|            | lo0  | 114.114.114.114/32 |              |
|------------|------|--------------------|--------------|
| DC2        | e0/0 | 10.10.40.115       | ANSIBLE_PORT |
|            | e0/1 | 10.10.1.21/30      | TO_ISP1      |
|            | e0/2 | 10.10.1.29/30      | TO_ISP2      |
|            | lo0  | 115.115.115.115/32 |              |
|------------|------|--------------------|--------------|

Краснодар(AS101)
| Устройство | Порт | IP-адрес           | Описание     |
|------------|------|--------------------|--------------|
| KRD-R1     | e0/0 | 10.10.40.121       | ANSIBLE_PORT |
|            | e0/1 | 10.10.141.17/30    | TO_REG-ISP1  |
|            | e0/2 | 10.10.141.29/30    | TO_REG_ISP2  |
|            | e0/3 | 10.10.121.10/30    | TO_ANAPA-R   |
|            | e1/0 | 10.10.121.14/30    | TO_SOCHI-R   |
|            | e1/1 | 10.10.121.18/30    | TO_NVRSK-R   |
|            | lo0  | 121.121.121.121/32 |              |
|------------|------|--------------------|--------------|
| ANAPA-R    | e0/0 | 10.10.40.122       | ANSIBLE_PORT |
|            | e0/1 | 10.10.121.9/30     | TO_KRD-R1    |
|            | lo0  | 122.122.122.122/32 |              |
|------------|------|--------------------|--------------|
| SOCHI-R    | e0/0 | 10.10.40.123       | ANSIBLE_PORT |
|            | e0/1 | 10.10.121.13/30    | TO_KRD-R1    |
|            | lo0  | 123.123.123.123/32 |              |
|------------|------|--------------------|--------------|
| NVRSK-R    | e0/0 | 10.10.40.124       | ANSIBLE_PORT |
|            | e0/1 | 10.10.121.17/30    | TO_KRD-R1    |
|            | lo0  | 124.124.124.124/32 |              |
|------------|------|--------------------|--------------|


Ростов (AS 1001)
| Устройство | Порт | IP-адрес           | Описание     |
|------------|------|--------------------|--------------|
| RSTV-R1    | e0/0 | 10.10.40.131       | ANSIBLE_PORT |
|            | e0/1 | 10.10.141.21/30    | TO_REG-ISP1  |
|            | e0/2 | 10.10.141.33/30    | TO_REG-ISP2  |
|            | e0/3 | 10.10.131.10/30    | TO_AZOV-R    |
|            | e1/0 | 10.10.131.14/30    | TO_TAGANROG-R|
|            | e1/1 | 10.10.131.18/30    | TO_NVCHRKSK-R|
|            | lo0  | 131.131.131.131/32 |              |
|------------|------|--------------------|--------------|
| AZOV-R     | e0/0 | 10.10.40.132       | ANSIBLE_PORT |
|            | e0/1 | 10.10.131.9/30     | TO_RSTV-R1   |
|            | lo0  | 132.132.132.132/32 |              |
|------------|------|--------------------|--------------|
| TAGANROG-R | e0/0 | 10.10.40.133       | ANSIBLE_PORT |
|            | e0/1 | 10.10.131.13/30    | TO_RSTV-R1   |
|            | lo0  | 133.133.133.133/32 |              |      |------------|------|--------------------|--------------|
| NVCHRKSK-R | e0/0 | 10.10.40.134       | ANSIBLE_PORT |
|            | e0/1 | 10.10.131.17/30    | TO_RSTV-R1   |
|            | lo0  | 134.134.134.134/32 |              |
|------------|------|--------------------|--------------|

Региональные провайдеры (AS 301)
| Устройство | Порт | IP-адрес           | Описание     |
|------------|------|--------------------|--------------|
| REG-ISP1   | e0/0 | 10.10.40.141       | ANSIBLE_PORT |
|            | e0/1 | 10.10.141.9/30     | TO_MSK-R     |
|            | e0/2 | 10.10.141.18/30    | TO_KRD-R1    |
|            | e0/3 | 10.10.141.22/30    | TO_RSTV-R1   |
|            | e1/0 | 10.10.141.26/30    | TO_REG-ISP2  |
|            | lo0  | 141.141.141.141/32 |              |
|------------|------|--------------------|--------------|
| REG-ISP2   | e0/0 | 10.10.40.142       | ANSIBLE_PORT |
|            | e0/1 | 10.16.1.13/30      | TO-MSK-R     |
|            | e0/2 | 10.10.141.30/30    | TO_KRD-R1    |
|            | e0/3 | 10.10.141.34/30    | TO_RSTV-R1   |
|            | e1/0 | 10.10.141.25/30    | TO_REG-ISP1  |
|            | lo0  | 142.142.142.142/30 |              |
|------------|------|--------------------|--------------|

1. Вручную настраиваем на маршрутизаторах ip-адрес и имя хоста для подключения ansible:

```
Router>
Router#conf t
Router(config)#no service config
Router(config)#hostname DC1
DC1(config)#int e0/0
DC1(config-if)#description ANSIBLE_PORT
DC1(config-if)#ip addr 10.10.40.114 255.255.255.0
DC1(config-if)#no shut
DC1(config-if)#exit
DC1(config)#ip domain-name pogodin.online
DC1(config)#crypto key generate rsa mod 2048
DC1(config)#ip ssh ver 2
DC1(config)#username admin secret admin
DC1(config)#username admin priv 15
DC1(config)#line vty 0 4
DC1(config-line)#login local
DC1(config-line)#transport input ssh
DC1(config-line)#end
DC1#wr
```

2. Создаем для каждого маршрутизатора файл с настройками интерфесов

![](img/host_vars.png)

   и шаблон для ansible

![](img/interfaces_template.png)

3. Конфигурируем субинтерфейсы на маршрутизоторах и vlans на коммутаторах

![](img/host_vars_r-o-s.png)

![](img/host_vars_sw.png)
  
   вручную добавляем адреса на vpcs
   
![](img/vpc_ip.png)

   шаблоны:

![](img/template_vlan_r.png)

![](img/template_vlan_sw.png)   


4. Добавляем в файлы с настройками маршрутизатора конфигурации BGP

![](img/host_vars_bgp.png)
   
   и соответствующий шаблон для ansible
   
![](img/bgp_template.png)


Получаю проблему: 
на ISP1 и ISP2 приходят все маршруты (и с нижней части схемы и с DC1 и DC 2)

```
ISP1#sh ip ro
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 22 subnets, 2 masks
B        10.10.111.8/30 [200/0] via 10.10.111.14, 00:35:03
C        10.10.111.12/30 is directly connected, Ethernet0/1
L        10.10.111.13/32 is directly connected, Ethernet0/1
C        10.10.111.16/30 is directly connected, Ethernet0/2
L        10.10.111.18/32 is directly connected, Ethernet0/2
C        10.10.111.20/30 is directly connected, Ethernet0/3
L        10.10.111.22/32 is directly connected, Ethernet0/3
B        10.10.111.24/30 [200/0] via 10.10.111.17, 00:35:04
B        10.10.111.28/30 [200/0] via 10.10.111.21, 00:35:05
B        10.10.121.8/30 [200/0] via 10.10.111.14, 00:34:35
B        10.10.121.12/30 [200/0] via 10.10.111.14, 00:34:35
B        10.10.121.16/30 [200/0] via 10.10.111.14, 00:34:35
B        10.10.131.8/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.131.12/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.131.16/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.141.8/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.141.12/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.141.16/30 [200/0] via 10.10.111.14, 00:34:35
B        10.10.141.20/30 [200/0] via 10.10.111.14, 00:34:35
B        10.10.141.24/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.141.28/30 [200/0] via 10.10.111.14, 00:35:03
B        10.10.141.32/30 [200/0] via 10.10.111.14, 00:35:03
      111.0.0.0/32 is subnetted, 1 subnets
B        111.111.111.111 [200/0] via 10.10.111.14, 00:35:03
      112.0.0.0/32 is subnetted, 1 subnets
C        112.112.112.112 is directly connected, Loopback0
      114.0.0.0/32 is subnetted, 1 subnets
B        114.114.114.114 [200/0] via 10.10.111.17, 00:35:04
      115.0.0.0/32 is subnetted, 1 subnets
B        115.115.115.115 [200/0] via 10.10.111.21, 00:35:05
      121.0.0.0/32 is subnetted, 1 subnets
B        121.121.121.121 [200/0] via 10.10.111.14, 00:34:35
      122.0.0.0/32 is subnetted, 1 subnets
B        122.122.122.122 [200/0] via 10.10.111.14, 00:34:35
      123.0.0.0/32 is subnetted, 1 subnets
B        123.123.123.123 [200/0] via 10.10.111.14, 00:34:35
      124.0.0.0/32 is subnetted, 1 subnets
B        124.124.124.124 [200/0] via 10.10.111.14, 00:34:35
      131.131.0.0/32 is subnetted, 1 subnets
B        131.131.131.131 [200/0] via 10.10.111.14, 00:35:03
      132.132.0.0/32 is subnetted, 1 subnets
B        132.132.132.132 [200/0] via 10.10.111.14, 00:35:03
      133.133.0.0/32 is subnetted, 1 subnets
B        133.133.133.133 [200/0] via 10.10.111.14, 00:35:03
      134.134.0.0/32 is subnetted, 1 subnets
B        134.134.134.134 [200/0] via 10.10.111.14, 00:35:03
      141.141.0.0/32 is subnetted, 1 subnets
B        141.141.141.141 [200/0] via 10.10.111.14, 00:34:35
      142.142.0.0/32 is subnetted, 1 subnets
B        142.142.142.142 [200/0] via 10.10.111.14, 00:35:03
      172.18.0.0/24 is subnetted, 8 subnets
B        172.18.10.0 [200/0] via 10.10.111.14, 00:34:04
B        172.18.20.0 [200/0] via 10.10.111.14, 00:34:04
B        172.18.30.0 [200/0] via 10.10.111.14, 00:34:04
B        172.18.40.0 [200/0] via 10.10.111.14, 00:34:35
B        172.18.50.0 [200/0] via 10.10.111.14, 00:34:35
B        172.18.60.0 [200/0] via 10.10.111.14, 00:34:35
B        172.18.100.0 [200/0] via 10.10.111.17, 00:35:04
B        172.18.200.0 [200/0] via 10.10.111.21, 00:35:05
      192.168.40.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.40.0/24 is directly connected, Ethernet0/0
L        192.168.40.112/32 is directly connected, Ethernet0/0
ISP1#
```

```
ISP2#sh ip ro
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 22 subnets, 2 masks
C        10.10.111.8/30 is directly connected, Ethernet0/1
L        10.10.111.9/32 is directly connected, Ethernet0/1
B        10.10.111.12/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.111.16/30 [200/0] via 10.10.111.25, 00:40:11
B        10.10.111.20/30 [200/0] via 10.10.111.29, 00:40:11
C        10.10.111.24/30 is directly connected, Ethernet0/2
L        10.10.111.26/32 is directly connected, Ethernet0/2
C        10.10.111.28/30 is directly connected, Ethernet0/3
L        10.10.111.30/32 is directly connected, Ethernet0/3
B        10.10.121.8/30 [200/0] via 10.10.111.10, 00:39:44
B        10.10.121.12/30 [200/0] via 10.10.111.10, 00:39:44
B        10.10.121.16/30 [200/0] via 10.10.111.10, 00:39:44
B        10.10.131.8/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.131.12/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.131.16/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.141.8/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.141.12/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.141.16/30 [200/0] via 10.10.111.10, 00:39:44
B        10.10.141.20/30 [200/0] via 10.10.111.10, 00:39:44
B        10.10.141.24/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.141.28/30 [200/0] via 10.10.111.10, 00:40:11
B        10.10.141.32/30 [200/0] via 10.10.111.10, 00:40:11
      111.0.0.0/32 is subnetted, 1 subnets
B        111.111.111.111 [200/0] via 10.10.111.10, 00:40:11
      113.0.0.0/32 is subnetted, 1 subnets
C        113.113.113.113 is directly connected, Loopback0
      114.0.0.0/32 is subnetted, 1 subnets
B        114.114.114.114 [200/0] via 10.10.111.25, 00:40:11
      115.0.0.0/32 is subnetted, 1 subnets
B        115.115.115.115 [200/0] via 10.10.111.29, 00:40:11
      121.0.0.0/32 is subnetted, 1 subnets
B        121.121.121.121 [200/0] via 10.10.111.10, 00:39:44
      122.0.0.0/32 is subnetted, 1 subnets
B        122.122.122.122 [200/0] via 10.10.111.10, 00:39:44
      123.0.0.0/32 is subnetted, 1 subnets
B        123.123.123.123 [200/0] via 10.10.111.10, 00:39:44
      124.0.0.0/32 is subnetted, 1 subnets
B        124.124.124.124 [200/0] via 10.10.111.10, 00:39:44
      131.131.0.0/32 is subnetted, 1 subnets
B        131.131.131.131 [200/0] via 10.10.111.10, 00:40:11
      132.132.0.0/32 is subnetted, 1 subnets
B        132.132.132.132 [200/0] via 10.10.111.10, 00:40:11
      133.133.0.0/32 is subnetted, 1 subnets
B        133.133.133.133 [200/0] via 10.10.111.10, 00:40:11
      134.134.0.0/32 is subnetted, 1 subnets
B        134.134.134.134 [200/0] via 10.10.111.10, 00:40:11
      141.141.0.0/32 is subnetted, 1 subnets
B        141.141.141.141 [200/0] via 10.10.111.10, 00:39:44
      142.142.0.0/32 is subnetted, 1 subnets
B        142.142.142.142 [200/0] via 10.10.111.10, 00:40:11
      172.18.0.0/24 is subnetted, 8 subnets
B        172.18.10.0 [200/0] via 10.10.111.10, 00:39:13
B        172.18.20.0 [200/0] via 10.10.111.10, 00:39:13
B        172.18.30.0 [200/0] via 10.10.111.10, 00:39:13
B        172.18.40.0 [200/0] via 10.10.111.10, 00:39:44
B        172.18.50.0 [200/0] via 10.10.111.10, 00:39:44
B        172.18.60.0 [200/0] via 10.10.111.10, 00:39:44
B        172.18.100.0 [200/0] via 10.10.111.25, 00:40:11
B        172.18.200.0 [200/0] via 10.10.111.29, 00:40:11
      192.168.40.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.40.0/24 is directly connected, Ethernet0/0
L        192.168.40.113/32 is directly connected, Ethernet0/0
ISP2# 
```

при этом на MSK-R и ниже (REG-ISP, KRD-R, RSTV-R и т.д) приходят маршруты только нижней части схемы

```
MSK-R#sh ip ro
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 23 subnets, 2 masks
C        10.10.111.8/30 is directly connected, Ethernet0/1
L        10.10.111.10/32 is directly connected, Ethernet0/1
C        10.10.111.12/30 is directly connected, Ethernet0/2
L        10.10.111.14/32 is directly connected, Ethernet0/2
B        10.10.111.16/30 [200/0] via 10.10.111.13, 00:40:49
B        10.10.111.20/30 [200/0] via 10.10.111.13, 00:40:49
B        10.10.111.24/30 [200/0] via 10.10.111.9, 00:40:48
B        10.10.111.28/30 [200/0] via 10.10.111.9, 00:40:48
B        10.10.121.8/30 [20/0] via 10.10.141.13, 00:40:22
                        [20/0] via 10.10.141.9, 00:40:22
B        10.10.121.12/30 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
B        10.10.121.16/30 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
B        10.10.131.8/30 [20/0] via 10.10.141.13, 00:40:49
                        [20/0] via 10.10.141.9, 00:40:49
B        10.10.131.12/30 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
B        10.10.131.16/30 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
C        10.10.141.8/30 is directly connected, Ethernet0/3
L        10.10.141.10/32 is directly connected, Ethernet0/3
C        10.10.141.12/30 is directly connected, Ethernet1/0
L        10.10.141.14/32 is directly connected, Ethernet1/0
B        10.10.141.16/30 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
B        10.10.141.20/30 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
B        10.10.141.24/30 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
B        10.10.141.28/30 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
B        10.10.141.32/30 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      111.0.0.0/32 is subnetted, 1 subnets
C        111.111.111.111 is directly connected, Loopback0
      112.0.0.0/32 is subnetted, 1 subnets
B        112.112.112.112 [200/0] via 10.10.111.13, 00:40:49
      113.0.0.0/32 is subnetted, 1 subnets
B        113.113.113.113 [200/0] via 10.10.111.9, 00:40:48
      121.0.0.0/32 is subnetted, 1 subnets
B        121.121.121.121 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
      122.0.0.0/32 is subnetted, 1 subnets
B        122.122.122.122 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
      123.0.0.0/32 is subnetted, 1 subnets
B        123.123.123.123 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
      124.0.0.0/32 is subnetted, 1 subnets
B        124.124.124.124 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
      131.131.0.0/32 is subnetted, 1 subnets
B        131.131.131.131 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      132.132.0.0/32 is subnetted, 1 subnets
B        132.132.132.132 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      133.133.0.0/32 is subnetted, 1 subnets
B        133.133.133.133 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      134.134.0.0/32 is subnetted, 1 subnets
B        134.134.134.134 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      141.141.0.0/32 is subnetted, 1 subnets
B        141.141.141.141 [20/0] via 10.10.141.13, 00:40:22
                         [20/0] via 10.10.141.9, 00:40:22
      142.142.0.0/32 is subnetted, 1 subnets
B        142.142.142.142 [20/0] via 10.10.141.13, 00:40:49
                         [20/0] via 10.10.141.9, 00:40:49
      172.18.0.0/24 is subnetted, 6 subnets
B        172.18.10.0 [20/0] via 10.10.141.13, 00:39:51
                     [20/0] via 10.10.141.9, 00:39:51
B        172.18.20.0 [20/0] via 10.10.141.13, 00:39:51
                     [20/0] via 10.10.141.9, 00:39:51
B        172.18.30.0 [20/0] via 10.10.141.13, 00:39:51
                     [20/0] via 10.10.141.9, 00:39:51
B        172.18.40.0 [20/0] via 10.10.141.13, 00:40:22
                     [20/0] via 10.10.141.9, 00:40:22
B        172.18.50.0 [20/0] via 10.10.141.13, 00:40:22
                     [20/0] via 10.10.141.9, 00:40:22
B        172.18.60.0 [20/0] via 10.10.141.13, 00:40:22
                     [20/0] via 10.10.141.9, 00:40:22
      192.168.40.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.40.0/24 is directly connected, Ethernet0/0
L        192.168.40.111/32 is directly connected, Ethernet0/0
MSK-R#
```
Т.е Москва уже не видит loopback интерфейсы и vlan на DC1 и DC2, при этом эти маршруты есть за MSK-К на роутерах ISP

DC1 и DC2 видят только

```
DC1#sh ip ro
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
B        10.10.111.8/30 [200/0] via 10.10.111.26, 00:44:16
B        10.10.111.12/30 [200/0] via 10.10.111.18, 00:44:19
C        10.10.111.16/30 is directly connected, Ethernet0/1
L        10.10.111.17/32 is directly connected, Ethernet0/1
B        10.10.111.20/30 [200/0] via 10.10.111.18, 00:44:19
C        10.10.111.24/30 is directly connected, Ethernet0/2
L        10.10.111.25/32 is directly connected, Ethernet0/2
B        10.10.111.28/30 [200/0] via 10.10.111.26, 00:44:16
      112.0.0.0/32 is subnetted, 1 subnets
B        112.112.112.112 [200/0] via 10.10.111.18, 00:44:19
      113.0.0.0/32 is subnetted, 1 subnets
B        113.113.113.113 [200/0] via 10.10.111.26, 00:44:16
      114.0.0.0/32 is subnetted, 1 subnets
C        114.114.114.114 is directly connected, Loopback0
      172.18.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.18.100.0/24 is directly connected, Ethernet0/3.100
L        172.18.100.1/32 is directly connected, Ethernet0/3.100
      192.168.40.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.40.0/24 is directly connected, Ethernet0/0
L        192.168.40.114/32 is directly connected, Ethernet0/0
DC1# 
```

```
DC2#sh ip ro
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
B        10.10.111.8/30 [200/0] via 10.10.111.30, 00:46:29
B        10.10.111.12/30 [200/0] via 10.10.111.22, 00:46:32
B        10.10.111.16/30 [200/0] via 10.10.111.22, 00:46:32
C        10.10.111.20/30 is directly connected, Ethernet0/1
L        10.10.111.21/32 is directly connected, Ethernet0/1
B        10.10.111.24/30 [200/0] via 10.10.111.30, 00:46:29
C        10.10.111.28/30 is directly connected, Ethernet0/2
L        10.10.111.29/32 is directly connected, Ethernet0/2
      112.0.0.0/32 is subnetted, 1 subnets
B        112.112.112.112 [200/0] via 10.10.111.22, 00:46:32
      113.0.0.0/32 is subnetted, 1 subnets
B        113.113.113.113 [200/0] via 10.10.111.30, 00:46:29
      115.0.0.0/32 is subnetted, 1 subnets
C        115.115.115.115 is directly connected, Loopback0
      172.18.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.18.200.0/24 is directly connected, Ethernet0/3.200
L        172.18.200.1/32 is directly connected, Ethernet0/3.200
      192.168.40.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.40.0/24 is directly connected, Ethernet0/0
L        192.168.40.115/32 is directly connected, Ethernet0/0
DC2# 
```
Конфигурации находятся в host_vars
