# Лабораторная работа. Настройка протокола DHCPv4

## Топология

<img width="1561" height="134" alt="image" src="https://github.com/user-attachments/assets/17fe86f3-e705-4035-ab22-785b22bed880" />


## Таблица адресации

| **Устройство** | **Интерфейс**  | **IP-адрес**    | **Маска подсети** | **Шлюз по умолчанию** |
|----------------|----------------|-----------------|-------------------|------------------------|
| R1             | G0/0/0         | 10.0.0.1        | 255.255.255.252   | ---                    |
| *R1*           | G0/0/1         | ---             | ---               | *---*                  |
| *R1*           | G0/0/1.100     |                 |                   | *---*                  |
| *R1*           | G0/0/1.200     |                 |                   | *---*                  |
| *R1*           | G0/0/1.1000    | ---             | ---               | *---*                  |
| R2             | G0/0/0         | 10.0.0.2        | 255.255.255.252   | ---                    |
| *R2*           | G0/0/1         |                 |                   | *---*                  |
| S1             | VLAN 200       |                 |                   |                        |
| S2             | VLAN 1         |                 |                   |                        |
| PC-A           | NIC            | DHCP            | DHCP              | DHCP                   |
| PC-B           | NIC            | DHCP            | DHCP              | DHCP                   |

## Таблица VLAN

| **VLAN** | **Имя**      | **Назначенный интерфейс**      |
|----------|--------------|---------------------------------|
| 1        | Нет          | S2: F0/18                       |
| 100      | Клиенты      | S1: F0/6                        |
| 200      | Управление   | S1: VLAN 200                    |
| 999      | Parking_Lot  | S1: F0/1-4, F0/7-24, G0/1-2    |
| 1000     | Собственная  | ---                             |

## Задачи

> **Часть 1. Создание сети и настройка основных параметров устройства**
>
> **Часть 2. Настройка и проверка двух серверов DHCPv4 на R1**
>
> **Часть 3. Настройка и проверка DHCP-ретрансляции на R2**

## Общие сведения/сценарий

> Протокол динамической конфигурации сетевого узла (DHCP) — сетевой протокол, позволяющий сетевым администраторам управлять и автоматизировать назначение IP-адресов. Без использования DHCP для IPv4 администратору необходимо вручную назначать и настраивать IP-адреса, предпочтительные DNS-серверы и шлюзы по умолчанию. По мере увеличения сети и перемещении устройств из одной внутренней сети в другую это становится административной проблемой.
>
> В предложенном сценарии размеры компании увеличились, и сетевые администраторы больше не имеют возможности назначать IP-адреса для устройств вручную. Ваша задача заключается в настройке маршрутизатора R1 для назначения IPv4-адресов в двух разных подсетях.
>
> **Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.
>
> **Примечание.** Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены в этом, обратитесь к инструктору.

## Необходимые ресурсы

- 2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
- 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
- 2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- Кабели Ethernet, расположенные в соответствии с топологией

## Инструкции

### Часть 1. Создание сети и настройка основных параметров устройства

> В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

#### Шаг 1. Создание схемы адресации

> Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

a.  Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).

    **Подсеть A:**
    Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100.

b.  Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1).

    **Подсеть B:**
    Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200.
    Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

c.  Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).

    **Подсеть C:**
    Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

| Вопрос | Ответ |
|--------|-------|
| Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 | 192.168.1.1 |
| Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200 | 192.168.1.65 |
| Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию | IP-адрес: 192.168.1.66<br>Шлюз по умолчанию: 192.168.1.65 |
| Запишите первый IP-адрес в таблице адресации для R2 G0/0/1 | 192.168.1.97 |

#### Шаг 2. Создайте сеть согласно топологии.

> Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### Шаг 3. Произведите базовую настройку маршрутизаторов.

*a.  Назначьте маршрутизатору имя устройства.*
b.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.*
*c.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.*
*d.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.*
*e.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.*
*f.  Зашифруйте открытые пароли.*
*g.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.*
*h.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.*
*i.  Установите часы на маршрутизаторе на сегодняшнее время и дату.*

```cisco
enable
configure terminal
hostname R1
no ip domain-lookup
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit
service password-encryption
banner motd #Unauthorized access is prohibited!#
exit
clock set 15:30:00 21 Mar 2026
copy running-config startup-config
```
```cisco
enable
configure terminal
hostname R2
no ip domain-lookup
enable secret class
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
exit
service password-encryption
banner motd #Unauthorized access is prohibited!#
exit
clock set 15:30:00 21 Mar 2026
copy running-config startup-config
```

#### Шаг 4. Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

a.  Активируйте интерфейс G0/0/1 на маршрутизаторе.

b.  Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

c.  Убедитесь, что вспомогательные интерфейсы работают.

```cisco
R1>en
Password: 
R1#conf t
R1(config)#interf
R1(config)#interface g0/0/1
R1(config-if)#no shut
R1(config-if)#no shutdown 
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
R1(config-if)#exit
R1(config)#inter
R1(config)#interface f0/0/1.100
%Invalid interface type and number
R1(config)#interface g0/0/1.100
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.100, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.100, changed state to up
R1(config-subif)#encap
R1(config-subif)#encapsulation dot1q 100
R1(config-subif)#descrip
R1(config-subif)#description Client_VLAN_100
R1(config-subif)#exit

R1(config)#interface g0/0/1.200
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.200, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.200, changed state to up
R1(config-subif)#encapsulation dot1q 200
R1(config-subif)#description Management_VLAN_200
R1(config-subif)#ip address 192.168.1.65 255.255.255.192
R1(config-subif)#exit

R1(config)#interface g0/0/1.100
R1(config-subif)#ip addre
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit

R1(config)#interface g0/0/1.200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit

R1(config)#inerf
R1(config)#interface g0/0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up
R1(config-subif)#encapsulati
R1(config-subif)#encapsulation dot1Q 1000 native
R1(config-subif)#descrip
R1(config-subif)#description NATIVE_VLAN_1000
R1(config-subif)#no ip address
R1(config-subif)#exit
R1(config)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console
R1#show ip interfa

R1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down

R1#copy runni
R1#copy running-config startu
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
R1#
R1#en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface g0/0/1.200
R1(config-subif)#no ip address 192.168.1.65 255.255.255.192
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
#### Шаг 5. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a.  Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.
```cisco
R2>en
Password: 
Password: 
R2#en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interfa
R2(config)#interface g0/0/1
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#no sh
R2(config-if)#no shutdown 

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R2(config-if)#end
R2#
%SYS-5-CONFIG_I: Configured from console by console

R2#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   192.168.1.97    YES manual up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R2#
R2#copy run
R2#copy running-config strt
R2#copy running-config star
R2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
b.  Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

```cisco
R1>en
Password: 
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface g0/0/0
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up

R1(config-if)#end
R1#

R1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   10.0.0.1        YES manual up                    up 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
```

```cisco

R2(config)#interface g0/0/0
R2(config-if)#ip address 10.0.0.1 255.255.255.252
R2(config-if)#no sh
R2(config-if)#no shutdown 

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

R2(config-if)#end
R2#

R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface g0/0/0
R2(config-if)#no ip address 10.0.0.1 255.255.255.252
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no shutdown 
R2(config-if)#end
R2#
%SYS-5-CONFIG_I: Configured from console by console

R2#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   10.0.0.2        YES manual up                    down 
GigabitEthernet0/0/1   192.168.1.97    YES manual up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R2#
```

c.  Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.
```cisco
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
R1(config)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#show ip rou
R1#show ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 10.0.0.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.0.0.0/30 is directly connected, GigabitEthernet0/0/0
L       10.0.0.1/32 is directly connected, GigabitEthernet0/0/0
     192.168.1.0/24 is variably subnetted, 4 subnets, 3 masks
C       192.168.1.0/26 is directly connected, GigabitEthernet0/0/1.100
L       192.168.1.1/32 is directly connected, GigabitEthernet0/0/1.100
C       192.168.1.64/27 is directly connected, GigabitEthernet0/0/1.200
L       192.168.1.65/32 is directly connected, GigabitEthernet0/0/1.200
S*   0.0.0.0/0 [1/0] via 10.0.0.2

R1#copy run
R1#copy running-config sta
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```cisco
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#p route 0.0.0.0 0.0.0.0 10.0.0.1
% Ambiguous command: "p route 0.0.0.0 0.0.0.0 10.0.0.1"
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#end
R2#
%SYS-5-CONFIG_I: Configured from console by console

R2#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.0.0.0/30 is directly connected, GigabitEthernet0/0/0
L       10.0.0.2/32 is directly connected, GigabitEthernet0/0/0
     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.96/28 is directly connected, GigabitEthernet0/0/1
L       192.168.1.97/32 is directly connected, GigabitEthernet0/0/1
S*   0.0.0.0/0 [1/0] via 10.0.0.1

R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```
d.  Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.
<img width="824" height="587" alt="image" src="https://github.com/user-attachments/assets/a9725347-2cc1-429d-82bb-289cd9fd8d19" />


e.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
<img width="714" height="91" alt="image" src="https://github.com/user-attachments/assets/666a9ccf-d39c-4b5e-b650-28b8d57626c2" />

#### Шаг 6. Настройте базовые параметры каждого коммутатора.

a.  Присвойте коммутатору имя устройства.
b.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
c.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
d.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
e.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
f.  Зашифруйте открытые пароли.
g.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
h.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
i.  Установите часы на маршрутизаторе на сегодняшнее время и дату.
j.  Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

```cisco
Switch>
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
R1(config)#hostname S1

S1(config)#no ip dom
S1(config)#no ip domain-lookup
	
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit

S1(config)#service password-en
S1(config)#service password-encryption 
S1(config)#banner motd #Unauthorized access is prohibited DZ_Maxim!#
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#clock set 22:23:00 22 Mar 2026
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```

```cisco
Switch>
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#ip domain-l
S2(config)#ip domain-lookup 
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)##banner motd #Unauthorized access is prohibited! DZ_Maxim#
           ^
% Invalid input detected at '^' marker.
	
S2(config)#banner motd #Unauthorized access is prohibited! DZ_Maxim#
S2(config)#exit
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#clock set 22:29:00 22 Mar 2026
S2#opy running-config startup-config
       ^
% Invalid input detected at '^' marker.
	
S2#
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```

#### Шаг 7. Создайте сети VLAN на коммутаторе S1.


a.  Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.

```cisco
S1>en
Password: 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#exit
S1(config)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#exit
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#exit
S1(config)#vlan 100
S1(config-vlan)#exit
S1(config)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#
```
b.  Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
c.  Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
d.  Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

<img width="1032" height="860" alt="image" src="https://github.com/user-attachments/assets/b42f7a16-df2d-44b6-9bb1-39f1ab5f5c42" />

#### Шаг 8. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.  Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```cisco
Password: 

S1>en
Password: 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface f0/6
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 100
S1(config-if)#no shutdown
S1(config-if)#end
```
```cisco
S2>en
Password: 
S2#cof t
       ^
% Invalid input detected at '^' marker.
	
S2#conf t
S2(config)#interface f0/18
S2(config-if)#switchport mode access
S2(config-if)#switchport access vlan 1
S2(config-if)#no shutdown
S2(config-if)#end
```
  
b.  Убедитесь, что VLAN назначены на правильные интерфейсы.
<img width="1481" height="1096" alt="2026-04-20_21-23" src="https://github.com/user-attachments/assets/f35dbc3c-9cb8-4471-abec-573bc80f9cc5" />


##### Вопрос:

> Почему интерфейс F0/5 указан в VLAN 1?

**Ответ:**
После настройки F0/5 не находится в VLAN 1. В таблице VLAN задания он указан в VLAN 1 лишь как начальное состояние по умолчанию до настройки транка.

#### Шаг 9. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.

a.  Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

```cisco
S1> enable
S1# configure terminal
S1(config)# interface f0/5
S1(config-if)# switchport mode trunk
```
b.  В рамках конфигурации транка установите для native VLAN значение 1000.

```cisco
S1(config-if)# switchport trunk native vlan 1000
```

c.  В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

```cisco
S1(config-if)# switchport trunk allowed vlan 100,200,1000
```

d.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```cisco
S1# copy running-config startup-config
```

e.  Проверьте состояние транка.
```cisco
S1# show interfaces trunk
```
<img width="805" height="616" alt="2026-04-20_21-44" src="https://github.com/user-attachments/assets/b5b498ee-a7af-4b0b-b8c4-21fba20aa3f8" />


##### Вопрос:

> Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

**Ответ:**
Адрес ПК зависел бы от того, в какой VLAN подключён порт коммутатора, к которому подсоединён ПК, и какой пул настроен для этой VLAN на R1


### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

> В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.

#### Шаг 1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A

a.  Исключите первые пять используемых адресов из каждого пула адресов.

```cisco
R1> en
R1# conf t
R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)# ip dhcp excluded-address 192.168.1.97 192.168.1.101
```    

b.  Создайте пул DHCP (используйте уникальное имя для каждого пула).

```cisco
R1(config)# ip dhcp pool A_CLIENT_LAN
R1(dhcp-config)# 
```  

c.  Укажите сеть, поддерживающую этот DHCP-сервер.

```cisco
R1(dhcp-config)# network 192.168.1.0 255.255.255.192
```  

d.  В качестве имени домена укажите **CCNA-lab.com**.

```cisco
R1(dhcp-config)# domain-name CCNA-lab.com
```  

e.  Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

```cisco
R1(dhcp-config)# default-router 192.168.1.1
```  

f.  Настройте время аренды на 2 дня 12 часов и 30 минут.
```cisco
R1(dhcp-config)# lease 2 12 30
```
lease не поддерживается в данной версии ( пока выяснил, глаз дергаться начал)))

g.  Затем настройте второй пул DHCPv4, используя имя пула **R2_Client_LAN** и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

```cisco
R1(dhcp-config)# exit
R1(config)# ip dhcp pool R2_Client_LAN
R1(dhcp-config)# network 192.168.1.96 255.255.255.240
R1(dhcp-config)# domain-name CCNA-lab.com
R1(dhcp-config)# default-router 192.168.1.97
R1(dhcp-config)# lease 2 12 30
R1(dhcp-config)# end
```  

#### Шаг 2. Сохраните конфигурацию.

> Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```cisco
R1# copy running-config startup-config
```  

#### Шаг 3. Проверка конфигурации сервера DHCPv4

a.  Чтобы просмотреть сведения о пуле, выполните команду **`show ip dhcp pool`** .
<img width="699" height="603" alt="2026-04-20_22-36" src="https://github.com/user-attachments/assets/a24a71ce-3d02-440c-a3b0-6d48629c50a8" />

b.  Выполните команду **`show ip dhcp bindings`** для проверки установленных назначений адресов DHCP.
  
c.  Выполните команду **`show ip dhcp server statistics`** для проверки сообщений DHCP.


#### Шаг 4. Попытка получить IP-адрес от DHCP на PC-A

a.  Из командной строки компьютера PC-A выполните команду **`ipconfig /all`**.

b.  После завершения процесса обновления выполните команду **`ipconfig`** для просмотра новой информации об IP-адресе.

<img width="754" height="932" alt="2026-04-20_22-42" src="https://github.com/user-attachments/assets/e8c30610-d74c-4882-923f-c3a55d985339" />

c.  Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

<img width="629" height="584" alt="2026-04-20_22-46" src="https://github.com/user-attachments/assets/d85df5d7-302c-4b27-9290-0849f3356869" />
<img width="1026" height="322" alt="2026-04-20_22-49" src="https://github.com/user-attachments/assets/64026824-7a9c-4aed-a757-6a66320fed8e" />



### Часть 3. Настройка и проверка DHCP-ретрансляции на R2

> В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1).

#### Шаг 1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

a.  Настройте команду **`ip helper-address`** на G0/0/1, указав IP-адрес G0/0/0 R1.

```cisco
   Password: 

R2>en
Password: 
Password: 
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface g0/0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#exit
R2(config)#exit

R2#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
b.  Сохраните конфигурацию.

#### Шаг 2. Попытка получить IP-адрес от DHCP на PC-B

a.  Из командной строки компьютера PC-B выполните команду **`ipconfig /all`**.
<img width="841" height="655" alt="2026-04-20_23-08" src="https://github.com/user-attachments/assets/d894d97e-a96e-4ba2-84b7-1f7c1eea910b" />


b.  После завершения процесса обновления выполните команду **`ipconfig`** для просмотра новой информации об IP-адресе.
<img width="823" height="615" alt="2026-04-20_23-09" src="https://github.com/user-attachments/assets/4471be72-e0c0-46d6-9442-3b08d4a86f08" />

c.  Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.
<img width="863" height="238" alt="2026-04-20_23-11" src="https://github.com/user-attachments/assets/38200776-f412-40d5-8750-01818476c645" />

d.  Выполните **`show ip dhcp binding`** для R1 для проверки назначений адресов в DHCP.
<img width="639" height="563" alt="2026-04-20_23-13" src="https://github.com/user-attachments/assets/c85f0d4c-b771-4aa5-8806-e7c96d82eebb" />
e.  Выполните команду **`show ip dhcp server statistics`** для проверки сообщений DHCP.
не доступна команда
