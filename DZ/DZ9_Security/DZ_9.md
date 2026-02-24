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
    ```
    R1# show ip interface brief
    ```
c.  Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up/up (при необходимости устраните неполадки).

#### Шаг 3: Настройка и проверка основных параметров коммутатора.

a.  Настройте имя хоста для коммутаторов S1 и S2.
b.  Запретите нежелательный поиск в DNS.
    ```
    S1(config)# no ip domain lookup
    ```
c.  Настройте описания интерфейса для портов, которые используются на S1 и S2 (согласно топологии).
    *   На S1:
        ```
        S1(config)# interface f0/1
        S1(config-if)# description Link to S2
        S1(config)# interface f0/5
        S1(config-if)# description Link to R1
        S1(config)# interface f0/6
        S1(config-if)# description Link to PC-A
        ```
    *   На S2:
        ```
        S2(config)# interface f0/1
        S2(config-if)# description Link to S1
        S2(config)# interface f0/18
        S2(config-if)# description Link to PC-B
        ```
d.  Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.
    ```
    S1(config)# ip default-gateway 192.168.10.1
    S2(config)# ip default-gateway 192.168.10.1
    ```

### Часть 2: Настройка сетей VLAN на коммутаторах.

#### Шаг 1: Сконфигурируйте VLAN 10.

Добавьте VLAN 10 на S1 и S2 и назовите VLAN - **Management**.
```cisco
S1(config)# vlan 10
S1(config-vlan)# name Management
S2(config)# vlan 10
S2(config-vlan)# name Management
```

#### Шаг 2: Сконфигурируйте SVI для VLAN 10.

Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.
*   На S1:
    ```
    S1(config)# interface vlan 10
    S1(config-if)# description Management Interface
    S1(config-if)# ip address 192.168.10.201 255.255.255.0
    S1(config-if)# no shutdown
    ```
*   На S2:
    ```
    S2(config)# interface vlan 10
    S2(config-if)# description Management Interface
    S2(config-if)# ip address 192.168.10.202 255.255.255.0
    S2(config-if)# no shutdown
    ```

#### Шаг 3: Настройте VLAN 333 с именем Native на S1 и S2.
```cisco
S1(config)# vlan 333
S1(config-vlan)# name Native
S2(config)# vlan 333
S2(config-vlan)# name Native
```

#### Шаг 4: Настройте VLAN 999 с именем ParkingLot на S1 и S2.
```cisco
S1(config)# vlan 999
S1(config-vlan)# name ParkingLot
S2(config)# vlan 999
S2(config-vlan)# name ParkingLo
```

### Часть 3: Настройки безопасности коммутатора.

#### Шаг 1: Реализация магистральных соединений 802.1Q.

a.  Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.
    *   На S1:
        ```
        S1(config)# interface f0/1
        S1(config-if)# switchport mode trunk
        S1(config-if)# switchport trunk native vlan 333
        ```
    *   На S2:
        ```
        S2(config)# interface f0/1
        S2(config-if)# switchport mode trunk
        S2(config-if)# switchport trunk native vlan 333
        ```
b.  Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.
    *   На S1:
        ```
        S1# show interface trunk
        ```
        *Ожидаемый вывод:*
        ```
        Port        Mode         Encapsulation  Status        Native vlan
        Fa0/1       on           802.1q         trunking      333

        Port        Vlans allowed on trunk
        Fa0/1       1-4094

        Port        Vlans allowed and active in management domain
        Fa0/1       1,10,333,999

        Port        Vlans in spanning tree forwarding state and not pruned
        Fa0/1       1,10,333,999
        ```
    *   На S2:
        ```
        S2# show interface trunk
        ```
        *Ожидаемый вывод:*
        ```
        Port        Mode         Encapsulation  Status        Native vlan
        Fa0/1       on           802.1q         trunking      333

        Port        Vlans allowed on trunk
        Fa0/1       1-4094

        Port        Vlans allowed and active in management domain
        Fa0/1       1,10,333,999

        Port        Vlans in spanning tree forwarding state and not pruned
        Fa0/1       1,10,333,999
        ```
c.  Отключить согласование DTP на F0/1 для S1 и S2.
    ```
    S1(config)# interface f0/1
    S1(config-if)# switchport nonegotiate
    S2(config)# interface f0/1
    S2(config-if)# switchport nonegotiate
    ```
d.  Проверьте с помощью команды **show interfaces**.
    ```
    S1# show interfaces f0/1 switchport | include Negotiation
    Negotiation of Trunking: Off
    S2# show interfaces f0/1 switchport | include Negotiation
    Negotiation of Trunking: Off
    ```

#### Шаг 2: Настройка портов доступа.

a.  На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.
    ```
    S1(config)# interface f0/5
    S1(config-if)# switchport mode access
    S1(config-if)# switchport access vlan 10
    S1(config)# interface f0/6
    S1(config-if)# switchport mode access
    S1(config-if)# switchport access vlan 10
    ```
b.  На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.
    ```
    S2(config)# interface f0/18
    S2(config-if)# switchport mode access
    S2(config-if)# switchport access vlan 10
    ```

#### Шаг 3: Безопасность неиспользуемых портов коммутатора.

a.  На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.
    *Примечание: Данный пример настраивает диапазоны портов. Необходимо настроить все неиспользуемые порты на ваших коммутаторах.*
    *   На S1:
        ```
        S1(config)# interface range f0/2-4, f0/7-24, g0/1-2
        S1(config-if-range)# switchport access vlan 999
        S1(config-if-range)# shutdown
        ```
    *   На S2:
        ```
        S2(config)# interface range f0/2-17, f0/19-24, g0/1-2
        S2(config-if-range)# switchport access vlan 999
        S2(config-if-range)# shutdown
        ```
b.  Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999.
    *   На S1:
        ```
        S1# show interfaces status
        ```
        *Ожидаемый вывод (сокращен):*
        ```
        Port      Name         Status       Vlan   Duplex  Speed  Type
        Fa0/1     Link to S2   connected    trunk  a-full a-100  10/100BaseTX
        Fa0/2                  disabled     999    auto   auto   10/100BaseTX
        Fa0/3                  disabled     999    auto   auto   10/100BaseTX
        Fa0/4                  disabled     999    auto   auto   10/100BaseTX
        Fa0/5     Link to R1   connected    10     a-full a-100  10/100BaseTX
        Fa0/6     Link to PC-A connected    10     a-full a-100  10/100BaseTX
        Fa0/7                  disabled     999    auto   auto   10/100BaseTX
        ...<output omitted>...
        ```
    *   На S2:
        ```
        S2# show interfaces status
        ```
        *Ожидаемый вывод (сокращен):*
        ```
        Port      Name         Status       Vlan   Duplex  Speed  Type
        Fa0/1     Link to S1   connected    trunk  a-full a-100  10/100BaseTX
        Fa0/2                  disabled     999    auto   auto   10/100BaseTX
        Fa0/3                  disabled     999    auto   auto   10/100BaseTX
        ...<output omitted>...
        Fa0/14                 disabled     999    auto   auto   10/100BaseTX
        Fa0/15                 disabled     999    auto   auto   10/100BaseTX
        Fa0/16                 disabled     999    auto   auto   10/100BaseTX
        Fa0/17                 disabled     999    auto   auto   10/100BaseTX
        Fa0/18    Link to PC-B connected    10     a-full a-100  10/100BaseTX
        Fa0/19                 disabled     999    auto   auto   10/100BaseTX
        ...<output omitted>...
        Gi0/1                  disabled     999    auto   auto   10/100/1000BaseTX
        Gi0/2                  disabled     999    auto   auto   10/100/1000BaseTX
        ```

#### Шаг 4: Документирование и реализация функций безопасности порта.

a.  На S1 введите команду **show port-security interface f0/6** для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.
    ```
    S1# show port-security interface f0/6
    ```

    | Конфигурация безопасности порта по умолчанию                                   |
    |---------------------------------------------------------------------------------|
    | Функция                           | Настройка по умолчанию                    |
    |-----------------------------------|--------------------------------------------|
    | Защита портов                     | `Disabled`                                 |
    | Максимальное количество записей   | `1`                                        |
    | MAC-адресов                       |                                            |
    | Режим проверки на нарушение       | `Shutdown`                                 |
    | безопасности                      |                                            |
    | Aging Time                        | `0 mins`                                   |
    | Aging Type                        | `Absolute`                                 |
    | Secure Static Address Aging        | `Disabled`                                 |
    | Sticky MAC Address                | `Disabled`                                 |

b.  На S1 включите защиту порта на F0/6 со следующими настройками:
    *   Максимальное количество записей MAC-адресов: **3**
    *   Режим безопасности: **restrict**
    *   Aging time: **60 мин.**
    *   Aging type: **неактивный (inactivity)**
        ```
        S1(config)# interface f0/6
        S1(config-if)# switchport port-security
        S1(config-if)# switchport port-security maximum 3
        S1(config-if)# switchport port-security violation restrict
        S1(config-if)# switchport port-security aging time 60
        S1(config-if)# switchport port-security aging type inactivity
        ```
c.  Проверьте безопасность порта на S1 F0/6.
    ```
    S1# show port-security interface f0/6
    Port Security              : Enabled
    Port Status                : Secure-up
    Violation Mode             : Restrict
    Aging Time                 : 60 mins
    Aging Type                 : Inactivity
    SecureStatic Address Aging : Disabled
    Maximum MAC Addresses      : 3
    Total MAC Addresses        : 1
    Configured MAC Addresses   : 0
    Sticky MAC Addresses       : 0
    Last Source Address:Vlan   : 0022.5646.3411:10
    Security Violation Count   : 0

    S1# show port-security address
               Secure Mac Address Table
    -----------------------------------------------------------------------------
    Vlan    Mac Address       Type        Ports   Remaining Age
                                                   (mins)
    ----    -----------       ----        -----   -------------
     10     0022.5646.3411    SecureDynamic Fa0/6   60 (I)
    -----------------------------------------------------------------------------
    Total Addresses in System (excluding one mac per port)     : 0
    Max Addresses limit in System (excluding one mac per port) : 8192
    ```
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
    ```
    S2# show port-security interface f0/18
    Port Security              : Enabled
    Port Status                : Secure-up
    Violation Mode             : Protect
    Aging Time                 : 60 mins
    Aging Type                 : Absolute
    SecureStatic Address Aging : Disabled
    Maximum MAC Addresses      : 2
    Total MAC Addresses        : 1
    Configured MAC Addresses   : 0
    Sticky MAC Addresses       : 1
    Last Source Address:Vlan   : 0022.5646.3413:10
    Security Violation Count   : 0

    S2# show port-security address
               Secure Mac Address Table
    -----------------------------------------------------------------------------
    Vlan    Mac Address       Type        Ports   Remaining Age
                                                   (mins)
    ----    -----------       ----        -----   -------------
     10     0022.5646.3413    SecureSticky Fa0/18   -
    -----------------------------------------------------------------------------
    Total Addresses in System (excluding one mac per port)     : 0
    Max Addresses limit in System (excluding one mac per port) : 8192
    ```

#### Шаг 5: Реализовать безопасность DHCP snooping.

a.  На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.
    ```
    S2(config)# ip dhcp snooping
    S2(config)# ip dhcp snooping vlan 10
    ```
b.  Настройте магистральные порты на S2 как доверенные порты.
    ```
    S2(config)# interface f0/1
    S2(config-if)# ip dhcp snooping trust
    ```
c.  Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.
    ```
    S2(config)# interface f0/18
    S2(config-if)# ip dhcp snooping limit rate 5
    ```
d.  Проверка DHCP Snooping на S2.
    ```
    S2# show ip dhcp snooping
    Switch DHCP snooping is enabled
    DHCP snooping is configured on following VLANs:
    10
    DHCP snooping is operational on following VLANs:
    10
    DHCP snooping is configured on the following L3 Interfaces:

    Insertion of option 82 is enabled
    circuit-id default format: vlan-mod-port
    remote-id: 0cd9.96d2.3f80 (MAC)
    Option 82 on untrusted port is not allowed
    Verification of hwaddr field is enabled
    Verification of giaddr field is enabled
    DHCP snooping trust/rate is configured on the following Interfaces:

    Interface                    Trusted    Allow option    Rate limit (pps)
    ------------------------     -------    ------------    ----------------
    FastEthernet0/1              yes        yes              unlimited
    Custom circuit-ids:
    FastEthernet0/18             no         no               5
    Custom circuit-ids:
    ```
e.  В командной строке на PC-B освободите, а затем обновите IP-адрес.
    ```
    C:\Users\Student> ipconfig /release
    C:\Users\Student> ipconfig /renew
    ```
f.  Проверьте привязку отслеживания DHCP с помощью команды **show ip dhcp snooping binding**.
    ```
    S2# show ip dhcp snooping binding
    MacIp адресAddress        Lease(sec)  Type           VLAN  Interface
    --------------------       ----------  -------------  ----  --------------------
    00:50:56:90:D0:8E          192.168.10.11   86213        dhcp-snooping   10    FastEthernet0/18
    Total number of bindings: 1
    ```

#### Шаг 6: Реализация PortFast и BPDU Guard.

a.  Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.
    *   На S1 для F0/6:
        ```
        S1(config)# interface f0/6
        S1(config-if)# spanning-tree portfast
        ```
        *Примечание: Команда `spanning-tree portfast default` в глобальной конфигурации включила бы PortFast на всех портах доступа, но здесь мы настраиваем конкретные порты.*
    *   На S2 для F0/18:
        ```
        S2(config)# interface f0/18
        S2(config-if)# spanning-tree portfast
        ```
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
    ```
    S1# show spanning-tree interface f0/6 detail
    Port 8 (FastEthernet0/6) of VLAN0010 is designated forwarding
    Port path cost 19, Port priority 128, Port Identifier 128.6.
    <output omitted for brevity>
    Number of transitions to forwarding state: 1
    The port is in the portfast mode
    Link type is point-to-point by default
    Bpdu guard is enabled
    BPDU: sent 128, received 0
    ```

#### Шаг 7: Проверьте наличие сквозного подключения.

Проверьте связь командой PING между всеми устройствами в таблице IP-адресации. В случае сбоя проверки связи может потребоваться отключить брандмауэр на хостах.

*   Пинг с PC-A (получившего адрес по DHCP) на его шлюз 192.168.10.1.
*   Пинг с PC-B на его шлюз 192.168.10.1.
*   Пинг с R1 на адреса управления S1 (192.168.10.201) и S2 (192.168.10.202).
*   Пинг между PC-A и PC-B (через R1).

## Вопросы для повторения

1.  С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах (`Remaining Age (mins)`), когда было сконфигурировано динамическое обучение - sticky?

    **Ответ:** В выводе команды `show port-security address` для S2 тип адреса указан как `SecureSticky`. Для таких адресов таймер устаревания (aging) не применяется. Адреса, изученные как "sticky", сохраняются в конфигурации запуска (running-config) и, если конфигурация сохранена, в startup-config. Они считаются статическими и не удаляются по истечении таймера, в отличие от динамических (`SecureDynamic`) записей, для которых отображается оставшееся время.

2.  Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порт 18 на PC-B никогда не получит IP-адрес через DHCP?

    **Ответ:** Если в конфигурации S2 для интерфейса F0/18 настроена безопасность порта с опцией `switchport port-security mac-address sticky`, но в скрипте отсутствуют конкретные "sticky" MAC-адреса, то после перезагрузки коммутатор не будет знать MAC-адрес PC-B. При попытке PC-B получить IP-адрес по DHCP, коммутатор изучит его MAC-адрес, но если при этом настроено максимальное количество MAC-адресов (например, 2) и порт настроен в режиме `protect`, коммутатор просто заблокирует передачу данных с неизвестного MAC-адреса, не уведомляя об этом. Однако, если PC-B уже обменивался данными ранее и его MAC-адрес не был изучен как "sticky" и сохранен в конфигурации, он не сможет пройти DHCP-процесс, так как коммутатор не разрешит его трафик. Чаще всего проблема в том, что если порт находится в режиме `protect` или `restrict` и превышен лимит MAC-адресов, новые кадры (включая DHCP-запросы) будут отбрасываться.

3.  Что касается безопасности порта, в чем разница между типом абсолютного устаревания (`absolute`) и типом устаревание по неактивности (`inactivity`)?

    **Ответ:**
    *   **Absolute (Абсолютный):** Таймер запускается с момента изучения MAC-адреса. Запись безопасности удаляется из таблицы MAC-адресов по истечении заданного времени (`aging time`) независимо от того, передавал ли устройство данные через этот порт за это время или нет.
    *   **Inactivity (Неактивность):** Таймер сбрасывается каждый раз, когда порт получает кадр от данного MAC-адреса. Запись безопасности удаляется только в том случае, если в течение заданного времени (`aging time`) от этого MAC-адреса не было получено ни одного кадра.
