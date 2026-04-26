# Лабораторная работа: Настройка безопасности коммутатора

## Топология

<img width="526" height="489" alt="image" src="https://github.com/user-attachments/assets/4b00834b-7c51-43f2-bc2f-07f8c43660c4" />


## Таблица адресации

| Устройство | Интерфейс/VLAN | IP-адрес       | Маска подсети  |
|------------|----------------|----------------|----------------|
| R1         | G0/0/1         | 192.168.10.1   | 255.255.255.0  |
| R1         | Loopback 0     | 10.10.1.1      | 255.255.255.0  |
| S1         | VLAN 10        | 192.168.10.201 | 255.255.255.0  |
| S2         | VLAN 10        | 192.168.10.202 | 255.255.255.0  |
| PC-A       | NIC            | DHCP           | 255.255.255.0  |
| PC-B       | NIC            | DHCP           | 255.255.255.0  |

## Цели

**Часть 1. Настройка основного сетевого устройства**

*   Создайте сеть.
*   Настройте маршрутизатор R1.
*   Настройка и проверка основных параметров коммутатора.

**Часть 2. Настройка сетей VLAN**

*   Сконфигурируйте VLAN 10.
*   Сконфигурируйте SVI для VLAN 10.
*   Настройте VLAN 333 с именем Native на S1 и S2.
*   Настройте VLAN 999 с именем ParkingLot на S1 и S2.

**Часть 3. Настройки безопасности коммутатора**

*   Реализация магистральных соединений 802.1Q.
*   Настройка портов доступа.
*   Безопасность неиспользуемых портов коммутатора.
*   Документирование и реализация функций безопасности порта.
*   Реализовать безопасность DHCP snooping.
*   Реализация PortFast и BPDU Guard.
*   Проверка сквозной связанности.

## Общие сведения и сценарий

Это комплексная лабораторная работа, нацеленная на повторение ранее изученных функций безопасности уровня 2.

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.3 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.0(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

**Примечание**: Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору.

## Необходимые ресурсы

*   1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.3 или аналогичным).
*   2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель).
*   2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term).
*   Консольные кабели для настройки устройств Cisco IOS через консольные порты.
*   Кабели Ethernet, расположенные в соответствии с топологией.

## Инструкции

### Часть 1: Настройка основного сетевого устройства

#### Шаг 1: Создайте сеть.

a.  Создайте сеть согласно топологии.
b.  Выполните инициализацию и перезагрузку устройств (при необходимости).

#### Шаг 2: Настройте маршрутизатор R1.

a.  Загрузите следующий конфигурационный скрипт на R1.
```cisco
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
ip dhcp relay information trust-all
!
ip dhcp pool Students
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
description Link to S1
ip address 192.168.10.1 255.255.255.0
no shutdown
!
line con 0
logging synchronous
exec-timeout 0 0
```

b.  Проверьте текущую конфигурацию на R1, используя следующую команду:
  <img width="1081" height="163" alt="2026-04-25_23-30" src="https://github.com/user-attachments/assets/f22e7559-3cc4-4108-9807-e4b9da5d6a13" />
  
c.  Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up/up (при необходимости устраните неполадки).

*Интерфейс  в состоянии up/up
igabitEthernet0/0/1	192.168.10.1	up/up	
Loopback0	10.10.1.1	up/up*	

#### Шаг 3: Настройка и проверка основных параметров коммутатора.

a.  Настройте имя хоста для коммутаторов S1 и S2.

b.  Запретите нежелательный поиск в DNS.

c.  Настройте описания интерфейса для портов, которые используются на S1 и S2 (согласно топологии).

d.  Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.



<img width="810" height="356" alt="2026-04-25_23-40" src="https://github.com/user-attachments/assets/6d7709df-7d86-4c04-a185-53da3735ccec" />
*проверка*
<img width="749" height="105" alt="2026-04-25_23-52" src="https://github.com/user-attachments/assets/49f276fc-a4f0-4c88-9bf6-05a9c1eec4b8" />


### Часть 2: Настройка сетей VLAN на коммутаторах.

#### Шаг 1: Сконфигурируйте VLAN 10.

Добавьте VLAN 10 на S1 и S2 и назовите VLAN - **Management**.
```cisco
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 10
S1(config-vlan)#name Management
S1(config-vlan)#exit
S1(config)#end
S1#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
```cisco
S2(config)#vlan 10
S2(config-vlan)#name Management
S2(config-vlan)#exit
S2(config)#end
S2#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
```

#### Шаг 2: Сконфигурируйте SVI для VLAN 10.

Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.
*   На S1:
```cisco
    S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#interface vlan 10
S1(config-if)#description Management Interface for S1
S1(config-if)#ip address 192.168.10.201 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#end
S1#copy running-config startup-config
%LINK-5-CHANGED: Interface Vlan10, changed state to up

%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
*   На S2:
```cisco
    S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#interface vlan 10
S2(config-if)#description Management Interface for S2
S2(config-if)#ip address 192.168.10.202 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#end
S2#copy running-config startup-config
%LINK-5-CHANGED: Interface Vlan10, changed state to up

%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```

#### Шаг 3: Настройте VLAN 333 с именем Native на S1 и S2.
```cisco

S1(config)#vlan 333
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
S1#

S2(config)#vlan 333
S2(config-vlan)#name Native
S2(config-vlan)#exit
S2(config)#end
S2#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```

#### Шаг 4: Настройте VLAN 999 с именем ParkingLot на S1 и S2.
```cisco
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 999
S1(config-vlan)#name ParkingLot
S1(config-vlan)#exit
S1(config)#end
S1#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
```cisco
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#vlan 999
S2(config-vlan)#name ParkingLot
S2(config-vlan)#exit
S2(config)#end
S2#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```

### Часть 3: Настройки безопасности коммутатора.

#### Шаг 1: Реализация магистральных соединений 802.1Q.

a.  Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.

b.  Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.

<img width="1048" height="229" alt="2026-04-26_00-23" src="https://github.com/user-attachments/assets/8fea5f64-6582-4b74-b70e-024a63900e3a" />

    
c.  Отключить согласование DTP на F0/1 для S1 и S2.
```cisco
    SS1(config)#interface FastEthernet 0/1
S1(config-if)#switchport nonegotiate
S1(config-if)#end
S1#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
```
```cisco
S2(config)#interface FastEthernet 0/1
S2(config-if)#switchport nonegotiate
S2(config-if)#end
S2#copy running-config startup-config
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]

```
d.  Проверьте с помощью команды **show interfaces**.
    <img width="925" height="69" alt="2026-04-26_00-37" src="https://github.com/user-attachments/assets/88df37d0-edb7-4630-8fd4-244d1116feb9" />


#### Шаг 2: Настройка портов доступа.

a.  На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.

b.  На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.
<img width="986" height="281" alt="2026-04-26_00-50" src="https://github.com/user-attachments/assets/dac65a49-17aa-47a6-b4f4-de00dc22aeb4" />

    

#### Шаг 3: Безопасность неиспользуемых портов коммутатора.

a.  На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.
  
    *Примечание: Данный пример настраивает диапазоны портов. Необходимо настроить все неиспользуемые порты на ваших коммутаторах.*
 
```cisco
        S1(config)# interface range f0/2-4, f0/7-24, g0/1-2
        S1(config-if-range)# switchport access vlan 999
        S1(config-if-range)# shutdown
```

```cisco
        S2(config)# interface range f0/2-17, f0/19-24, g0/1-2
        S2(config-if-range)# switchport access vlan 999
        S2(config-if-range)# shutdown
```
b.  Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999.
    <img width="993" height="588" alt="2026-04-26_00-57" src="https://github.com/user-attachments/assets/9e2c37ac-7e9f-4736-a396-9e74a952bc32" />

        

#### Шаг 4: Документирование и реализация функций безопасности порта.

a.  На S1 введите команду **show port-security interface f0/6** для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.
    ## Итоговая таблица документации (настройки безопасности порта по умолчанию)

| Функция | Настройка по умолчанию (S1 F0/6) | Настройка по умолчанию (S2 F0/18) |
| --- | --- | --- |
| **Защита портов** | `Disabled` | `Disabled` |
| **Максимальное количество записей MAC-адресов** | `1` | `1` |
| **Режим проверки на нарушение безопасности** | `Shutdown` | `Shutdown` |
| **Aging Time** | `0 mins` | `0 mins` |
| **Aging Type** | `Absolute` | `Absolute` |
| **Secure Static Address Aging** | `Disabled` | `Disabled` |
| **Sticky MAC Address** | `Disabled` | `Disabled` |

b.  На S1 включите защиту порта на F0/6 со следующими настройками:

    *   Максимальное количество записей MAC-адресов: **3**
    *   Режим безопасности: **restrict**
    *   Aging time: **60 мин.**
    *   Aging type: **неактивный (inactivity)**
    
**Так как не поддерживается команда (switchport port-security aging type inactivity), настройка выполнена с параметром Aging Type: Absolute (значение по умолчанию). Остальные параметры соответствуют требованиям лабораторной работы.**
  
```cisco
S1# conf t
S1(config)# interface FastEthernet 0/6
S1(config-if)# switchport port-security
S1(config-if)# switchport port-security maximum 3
S1(config-if)# switchport port-security violation restrict
S1(config-if)# switchport port-security aging time 60
S1(config-if)# end
S1# copy running-config startup-config
```
        
c.  Проверьте безопасность порта на S1 F0/6.

   <img width="676" height="501" alt="2026-04-26_01-55" src="https://github.com/user-attachments/assets/29062b49-a527-4665-a214-bae5508e6080" />

    
d.  Включите безопасность порта для F0/18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС, изученные на этом порту, в текущую конфигурацию (**sticky**).

    ```
    S2(config)# interface f0/18
    S2(config-if)# switchport port-security
    S2(config-if)# switchport port-security mac-address sticky
    ```

e.  Настройте следующие параметры безопасности порта на S2 F0/18:
    *   Максимальное количество записей MAC-адресов: **2**
    *   Режим нарушения безопасности: **Protect**
    *   Aging time: **60 мин.**
    
        ```
        S2(config-if)# switchport port-security maximum 2
        S2(config-if)# switchport port-security violation protect
        S2(config-if)# switchport port-security aging time 60
        ```

f.  Проверка функции безопасности портов на S2 F0/18.
   
<img width="609" height="521" alt="2026-04-26_01-59" src="https://github.com/user-attachments/assets/8e7473bd-52dd-4831-9d10-bfb5436ff0b8" />


#### Шаг 5: Реализовать безопасность DHCP snooping.

a.  На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.
```cisco
    S2(config)# ip dhcp snooping
    S2(config)# ip dhcp snooping vlan 10
```

b.  Настройте магистральные порты на S2 как доверенные порты.
```cisco
    S2(config)# interface f0/1
    S2(config-if)# ip dhcp snooping trust
```
c.  Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.
```cisco
    S2(config)# interface f0/18
    S2(config-if)# ip dhcp snooping limit rate 5
```

d.  Проверка DHCP Snooping на S2.

<img width="465" height="505" alt="2026-04-26_02-15" src="https://github.com/user-attachments/assets/cd729b31-7f54-4df9-9df5-1efb5b19916b" />


e.  В командной строке на PC-B освободите, а затем обновите IP-адрес.

    ```
    C:\Users\Student> ipconfig /release
    C:\Users\Student> ipconfig /renew
    ```
    
f.  Проверьте привязку отслеживания DHCP с помощью команды **show ip dhcp snooping binding**.

<img width="1355" height="470" alt="2026-04-26_02-20" src="https://github.com/user-attachments/assets/b60be336-9cc0-4e6c-a3ba-aebbc72587fd" />


#### Шаг 6: Реализация PortFast и BPDU Guard.

a.  Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.
    *   На S1 для F0/6:
    
```cisco
S1(config)# interface FastEthernet 0/5
S1(config)# spanning-tree portfast
exit
S1(config)# interface FastEthernet 0/6
S1(config)# spanning-tree portfast
```
        
        *Примечание: Команда `spanning-tree portfast default` в глобальной конфигурации включила бы PortFast на всех портах доступа, но здесь мы настраиваем конкретные порты.*
        
    *   На S2 для F0/18:
```cisco
        S2(config)# interface f0/18
        S2(config-if)# spanning-tree portfast
```

<img width="1015" height="644" alt="2026-04-26_02-31" src="https://github.com/user-attachments/assets/fcf28146-759a-4a76-9ef2-c9655aea9979" />

b.  Включите защиту BPDU на портах доступа VLAN 10 S1 и S2, подключенных к PC-A и PC-B.
    *   На S1 для F0/6:
        ```
        S1(config)# interface f0/6
        S1(config-if)# spanning-tree bpduguard enable
        ```
    *   На S2 для F0/18:
        ```
        S2(config)# interface f0/18
        S2(config-if)# spanning-tree bpduguard enable
        ```
c.  Убедитесь, что защита BPDU и PortFast включены на соответствующих портах.

    S1# show spanning-tree interface f0/6 detail
    
<img width="562" height="459" alt="2026-04-26_02-39" src="https://github.com/user-attachments/assets/33df3d9f-4bca-4f09-9c46-3737dc8fe1c8" />


#### Шаг 7: Проверьте наличие сквозного подключения.

Проверьте связь командой PING между всеми устройствами в таблице IP-адресации. В случае сбоя проверки связи может потребоваться отключить брандмауэр на хостах.
<img width="1109" height="1351" alt="2026-04-26_02-50" src="https://github.com/user-attachments/assets/f84da314-4779-4b7e-b608-28a25c2c6a65" />

<img width="1444" height="674" alt="2026-04-26_02-55" src="https://github.com/user-attachments/assets/ac922a9a-e3d8-4bab-b9be-c6bd79d796fe" />


## Вопросы для повторения

1.  С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky?

**Ответ:** 
В Sticky MAC-адреса сохраняются как статические, Aging не применяется к статическим записям, они считаются постоянно валидными устройствами.
    

2  Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP?

**Ответ:** 
При загрузке коммутатор не знает MAC-адрес PC-B и отбрасывает все первые пакеты от него, включая DHCP-запрос без уведомлений, и компьютер не может получить IP

3.  Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности?

**Ответ:**
    При Абсолюте адрес удаляется гарантированно через N минут, при неактивности только если устройство "молчит" N минут
