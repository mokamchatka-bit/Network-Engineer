# *Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах*

# Топология
<img width="952" height="128" alt="image" src="https://github.com/user-attachments/assets/dfd35407-84a5-422a-b316-6f6791933f28" />



# Таблица адресации

| Устройство | Интерфейс | IPv6-адрес           | Link local IPv6-адрес | Длина префикса | Шлюз по умолчанию |
|------------|-----------|----------------------|------------------------|----------------|-------------------|
| R1         | G0/0/0    | 2001:db8:acad:a::1   | fe80::1                | 64             | ---               |
| R1         | G0/0/1    | 2001:db8:acad:1::1   | fe80::1                | 64             | ---               |
| S1         | VLAN 1    | 2001:db8:acad:1::b   | fe80::b                | 64             | ---               |
| PC-A       | NIC       | 2001:db8:acad:1::3   | SLACC                  | 64             | fe80::1           |
| PC-B       | NIC       | 2001:db8:acad:a::3   | SLACC                  | 64             | fe80::1           |

В этой таблице показана адресация для устройства, интерфейса, IP-адрес, маска подсети, префикс и шлюз по умолчанию.

# Задачи

Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

Часть 2. Ручная настройка IPv6-адресов

Часть 3. Проверка сквозного соединения

# Общие сведения/сценарий

В этой лабораторной работе вы будете настраивать хосты и интерфейсы устройств с IPv6-адресами. Для просмотра индивидуальных и групповых IPv6-адресов вы будете использовать команду **show**. Вы также будете проверять сквозное соединение с помощью команд **ping** and **traceroute**.

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

**Примечание**: Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены, обратитесь к инструктору.

Примечание. Шаблон по умолчанию менеджера базы данных 2960 Switch Database Manager (SDM) не поддерживает IPv6. Перед назначением IPv6-адреса SVI VLAN 1 может понадобиться выполнение команды sdm prefer dual-ipv4-and-ipv6 default **для включения IPv6-адресации.**

Примечание. **Шаблон default bias**, который по умолчанию используется диспетчером SDM (диспетчер базы данных коммутатора), не предоставляет возможностей адресации IPv6. Убедитесь, что SDM использует шаблон dual-ipv4-and-ipv6 или **lanbase-routing.** Новый шаблон будет использоваться после перезагрузки.

S1# **show sdm prefer**

Чтобы установить шаблон dual-ipv4-and-ipv6 в качестве шаблона SDM по умолчанию, выполните следующие действия:

S1# **configure terminal**

S1(config)# **sdm prefer dual-ipv4-and-ipv6 default**

S1(config)# **end**

S1# **reload**

# Необходимые ресурсы

- 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
- 1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
- 2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- Кабели Ethernet, расположенные в соответствии с топологией

Примечание**. Интерфейсы Gigabit Ethernet на маршрутизаторах Cisco 4221 определяют скорость автоматически, поэтому для подключения маршрутизатора к PC-B можно использовать прямой кабель Ethernet. При использовании другой модели маршрутизатора Cisco может возникнуть необходимость использовать перекрестный кабель Ethernet.**

# Инструкции

## Часть 1  Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:

### Шаг 1 Настройте маршрутизатор.

Назначьте имя хоста и настройте основные параметры устройства.

```cisco
R1#sh running-config 
Building configuration...

Current configuration : 717 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname R1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
ip cef
no ipv6 cef
!
no ip domain-lookup
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
banner motd ^C ______MAXIM_DZ4____^C
!
line con 0
 password 7 0822455D0A16
 login
!
line aux 0
!
line vty 0 4
 password 7 0822455D0A16
 login
end
```

### Шаг 2 Настройте коммутатор.
Назначьте имя хоста и настройте основные параметры устройства.
```cisco
S1#sh running-config 
Building configuration...

Current configuration : 1294 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
no ip domain-lookup
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
ip default-gateway 192.168.1.1
!
banner motd ^C___MAXIM_DZ4$___^C
!
line con 0
 password 7 0822455D0A16
 login
!
line vty 0 4
 password 7 0822455D0A16
 login
line vty 5 15
 password 7 0822455D0A16
 login
!
end
```
## Часть 2. Ручная настройка IPv6-адресов

### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.

a.	Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.
```cisco
R1(config)#interface Gig
R1(config)#interface GigabitEthernet 0/0/0
R1(config-if)#ipv6 addr
R1(config-if)#ipv6 address 2001:db8:acad:a::1
% Incomplete command.
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#no shutdown 
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
```
```cisco
R1(config)#interface gigabitEthernet 0/0/1
R1(config-if)#ipv
R1(config-if)#ipv6 addr
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no shut
R1(config-if)#no shutdown 
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
R1(config-if)#exit
```

b. Введите команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.
```cisco
R1(config)#do show ipv6 interface brief
R1(config)#do show ipv6 interface brief
GigabitEthernet0/0/0       [up/up]
    FE80::200:CFF:FEDB:CA01
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::200:CFF:FEDB:CA02
    2001:DB8:ACAD:1::1
Vlan1                      [administratively down/down]
    unassigned
R1(config)#
```
Примечание**. Отображаемый локальный адрес канала основан на адресации EUI-64, которая автоматически использует MAC-адрес интерфейса для создания 128-битного локального IPv6-адреса канала.**

c. Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.

**Примечание**. Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.
```cisco
R1>en
Password: 
R1#cof t
       ^
% Invalid input detected at '^' marker.
	
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#iter
R1(config)#inter
R1(config)#interface G
R1(config)#interface GigabitEthernet 0/0/0
R1(config-if)#ipv
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address fe80::1 lin
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#ex
R1(config-if)#exit 
R1(config)#inter
R1(config)#interface g
R1(config)#interface gigabitEthernet 0/0/1
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#ex
R1(config-if)#exit 
R1(config)#
```
d. Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.
```cisco
R1(config-if)# do show ipv6 interface brief
GigabitEthernet0/0/0       [up/up]
    FE80::1
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
Vlan1                      [administratively down/down]
    unassigned
R1(config-if)#
```
#### Вопрос:

Какие группы многоадресной рассылки назначены интерфейсу G0/0?
```cisco
R1# show ipv6 interface GigabitEthernet0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
R1#
```
Интерфейсу GigabitEthernet0/0/0 присвоены две группы многоадресной рассылки:

1. FF02::1 — группа всех узлов, обязательная группа для всех IPv6-устройств. Используется для отправки сообщений всем устройствам в подсети
2. FF02::1:FF00:1 — группа запрашиваемого узла, позволяет сетевой карте фильтровать кадры на аппаратном уровне

### Шаг 2. Активируйте IPv6-маршрутизацию на R1.

a. В командной строке на PC-B введите команду **ipconfig**, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.
<img width="734" height="716" alt="2026-02-08_01-35-00" src="https://github.com/user-attachments/assets/5413cebe-5ad4-4eaf-b279-9cacb5bcd2c9" />

#### Вопрос:
Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?
Ответ: НЕТ.


b. Активируйте IPv6-маршрутизацию на R1 с помощью команды **IPv6 unicast-routing**.
```cisco
R1(config)#ipv6 unicast-routing 
R1(config)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console
R1#show ipv6 interface gigabitEthernet 0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    *FF02::2*
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
R1#
```
**Примечание**. Это позволит компьютерам получать IP-адреса и данные шлюза по умолчанию с помощью функции SLAAC (Stateless Address Autoconfiguration (Автоконфигурация без сохранения состояния адреса)).

c. Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду **ipconfig** на PC-B. Проверьте данные IPv6-адреса.
<img width="950" height="683" alt="2026-02-08_01-59-06" src="https://github.com/user-attachments/assets/29edb81b-1aa6-430d-bde7-ca9284b63b93" />


#### Вопрос:
Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?
ОТВЕТ: PC-B получил глобальный префикс маршрутизации 2001:db8:acad:a::/64 и идентификатор подсети через механизм SLAAC, который активировался после включения IPv6-маршрутизации на R1 командой ipv6 unicast-routing.

### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.

a. Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала fe80::b.
```cisco
S1>en
Password: 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int
S1(config)#interface vlan 1
S1(config-if)#ipv6 add
S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
S1(config-if)#ipv6 address fe80::b li
S1(config-if)#ipv6 address fe80::b link-local 
S1(config-if)#no s
S1(config-if)#no shu
S1(config-if)#no shutdown 

S1(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S1(config-if)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
```
b. Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1.
```cisco
S1# show ipv6 interface vlan 1
Vlan1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::B
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:B
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  Output features: Check hwidb
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
S1#
```
### Шаг 4. Назначьте компьютерам статические IPv6-адреса.

a. Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.
Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6
 <img width="1577" height="646" alt="2026-02-08_02-35-08" src="https://github.com/user-attachments/assets/6fc4f325-c08f-4f62-99d3-d34d0947115e" />
 
    **Примечание**. При выполнении работы в среде Cisco Packet Tracer установите статический и SLACC адреса на компьютеры последовательно, отразив результаты в отчете
    
**Результаты SLAAC (автоматической настройки):**
<img width="1844" height="671" alt="2026-02-08_02-42-13" src="https://github.com/user-attachments/assets/47757b7c-94c8-464b-9bb5-f144eea67afd" />


## Часть 3. Проверка сквозного подключения

С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.
<img width="894" height="646" alt="2026-02-08_03-08-13" src="https://github.com/user-attachments/assets/ad198c39-eb9c-42ac-8528-f5aaf7832b3c" />

Отправьте эхо-запрос на интерфейс управления S1 с PC-A.
<img width="865" height="641" alt="2026-02-08_03-10-11" src="https://github.com/user-attachments/assets/098ce83d-fc68-4853-990f-f6a282c8a522" />

Введите команду **tracert** на PC-A, чтобы проверить наличие сквозного подключения к PC-B.
<img width="910" height="682" alt="2026-02-08_03-11-54" src="https://github.com/user-attachments/assets/57482d8a-e257-40ea-a49e-e418ca18613e" />

С PC-B отправьте эхо-запрос на PC-A.
<img width="1008" height="691" alt="2026-02-08_03-13-25" src="https://github.com/user-attachments/assets/9e231732-306e-4927-9a89-d9fb168f452d" />

С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.
<img width="980" height="643" alt="2026-02-08_03-19-13" src="https://github.com/user-attachments/assets/6c5efe75-2979-4aee-ae95-a5ea0450d89d" />


**Примечание.** В случае отсутствия сквозного подключения проверьте, правильно ли указаны IPv6-адреса на всех устройствах.

# Вопросы для повторения

1. Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала --- FE80::1?33
ОТВЕТ: Link-local адреса предназначены для связи в пределах одного сегмента сети (broadcast domain) и не маршрутизируются. не выходят за пределы своего сегмента. Пакеты с адресом FE80::1, отправленные PC-B, достигают только интерфейса G0/0/0 R1 и не могут достичь G0/0/1, и наоборот. Это обеспечивает изоляцию позволяя использовать одинаковые link-local адреса на разных интерфейсах.
   
3. Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?
Первые 64 бита: 2001:0db8:acad:0000 — префикс сети
Последние 64 бита: 0000:0000:aaaa:1234 — идентификатор интерфейса
Таким образом, в данном адресе используется нулевая подсеть (::)




# Сводная таблица по интерфейсам маршрутизаторов

| Модель маршрутизатора | Интерфейс Ethernet № 1 | Интерфейс Ethernet № 2 | Последовательный интерфейс № 1 | Последовательный интерфейс № 2 |
|------------------------|------------------------|------------------------|--------------------------------|--------------------------------|
| 1 800                  | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/0/0 (S0/0/0)        | Serial 0/0/1 (S0/0/1)        |
| 1900                   | Gigabit Ethernet 0/0 (G0/0) | Gigabit Ethernet 0/1 (G0/1) | Serial 0/0/0 (S0/0/0)        | Serial 0/0/1 (S0/0/1)        |
| 2801                   | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/1/0 (S0/1/0)        | Serial 0/1/1 (S0/1/1)        |
| 2811                   | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/0/0 (S0/0/0)        | Serial 0/0/1 (S0/0/1)        |
| 2900                   | Gigabit Ethernet 0/0 (G0/0) | Gigabit Ethernet 0/1 (G0/1) | Serial 0/0/0 (S0/0/0)        | Serial 0/0/1 (S0/0/1)        |
| 4221                   | Gigabit Ethernet 0/0/0 (G0/0/0) | Gigabit Ethernet 0/0/1 (G0/0/1) | Serial 0/1/0 (S0/1/0)        | Serial 0/1/1 (S0/1/1)        |
| 4300                   | Gigabit Ethernet 0/0/0 (G0/0/0) | Gigabit Ethernet 0/0/1 (G0/0/1) | Serial 0/1/0 (S0/1/0)        | Serial 0/1/1 (S0/1/1)        |

В этой таблице представлен интерфейс маршрутизатора для каждой модели маршрутизатора для интерфейсов Ethernet 1 и 2, а также последовательные интерфейсы 1 и 2.

**Примечание**. Чтобы определить конфигурацию маршрутизатора, можно посмотреть на интерфейсы и установить тип маршрутизатора и количество его интерфейсов. Перечислить все комбинации конфигураций для каждого класса маршрутизаторов невозможно. Эта таблица содержит идентификаторы для возможных комбинаций интерфейсов Ethernet и последовательных интерфейсов на устройстве. Другие типы интерфейсов в таблице не представлены, хотя они могут присутствовать в данном конкретном маршрутизаторе. В качестве примера можно привести интерфейс ISDN BRI. Строка в скобках --- это официальное сокращение, которое можно использовать в командах Cisco IOS для обозначения интерфейса.
