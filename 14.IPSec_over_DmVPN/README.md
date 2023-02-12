# Lab - IPSec over DmVPN

## Цель:

   Настроить GRE поверх IPSec между офисами Москва и С.-Петербург
   
   Настроить DMVPN поверх IPSec между офисами Москва и Чокурдах, Лабытнанги

## Описание/Пошаговая инструкция выполнения домашнего задания:
   
   В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

   1. Настроите GRE поверх IPSec между офисами Москва и С.-Петербург.
   
   2. Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.
   
   3. Все узлы в офисах в лабораторной работе должны иметь IP связность.
   
   4. План работы и изменения зафиксированы в документации.
   
   Дополнительно: Для IPSec использовать CA и сертификаты.

## Настройка

R15

```
R15(config)#crypto isakmp policy 1
R15(config-isakmp)#enc aes
R15(config-isakmp)#auth pre-s
R15(config-isakmp)#group 14
R15(config-isakmp)#life 3600

R15(config)#crypto isakmp key 151515 addr 201.201.201.18
R15(config)#cry ipsec transform-set IPSEC esp-aes esp-sha-hmac
R15(cfg-crypto-trans)#mode transport

R15(config)#crypto ipsec profile IPSEC
R15(ipsec-profile)#set trans
R15(ipsec-profile)#set transform-set IPSEC


R15(config)#int tunnel0
R15(config-if)#tunnel protection ipsec profile IPSEC
```
