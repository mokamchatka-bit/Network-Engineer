# *Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями*

# Топология
<img width="783" height="297" alt="image" src="https://github.com/user-attachments/assets/4c7f34ca-f945-44a5-912e-24d638f791b6" />


# Таблица адресации

| Устройство | Интерфейс  | IP-адрес      | Маска подсети   | Шлюз по умолчанию |
|------------|------------|---------------|-----------------|-------------------|
| R1         | G0/0/1.10  | 192.168.10.1  | 255.255.255.0   | ---               |
| R1         | G0/0/1.20  | 192.168.20.1  | 255.255.255.0   | ---               |
| R1         | G0/0/1.30  | 192.168.30.1  | 255.255.255.0   | ---               |
| R1         | G0/0/1.1000| ---           | ---             | ---               |
| S1         | VLAN 10    | 192.168.10.11 | 255.255.255.0   | 192.168.10.1      |
| S2         | VLAN 10    | 192.168.10.12 | 255.255.255.0   | 192.168.10.1      |
| PC-A       | NIC        | 192.168.20.3  | 255.255.255.0   | 192.168.20.1      |
| PC-B       | NIC        | 192.168.30.3  | 255.255.255.0   | 192.168.30.1      |

В этой таблице показана адресация для устройства, интерфейса, IP-адрес, маска подсети, префикс и шлюз по умолчанию.

# Таблица VLAN

| VLAN  | Имя         | Назначенный интерфейс                             |
|-------|-------------|---------------------------------------------------|
| 10    | Управление  | S1: VLAN 10<br>S2: VLAN 10                         |
| 20    | Sales       | S1: F0/6                                          |
| 30    | Operations  | S2: F0/18                                         |
| 999   | Parking_Lot | С1: F0/2-4, F0/7-24, G0/1-2<br>С2: F0/2-17, F0/19-24, G0/1-2 |
| 1000  | Собственная | ---                                               |

В этой таблице показан vlan, имя и назначенный интерфейс.

# Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Создание сетей VLAN и назначение портов коммутатора

Часть 3. Настройка транка 802.1Q между коммутаторами.

Часть 4. Настройка маршрутизации между сетями VLAN

Часть 5. Проверка, что маршрутизация между VLAN работает

# Общие сведения/сценарий

В целях повышения производительности сети большие широковещательные домены 2-го уровня делят на домены меньшего размера. Для этого современные коммутаторы используют виртуальные локальные сети (VLAN). VLAN также можно использовать в качестве меры безопасности, отделяя конфиденциальный трафик данных от остальной части сети. Сети VLAN облегчают процесс проектирования сети, обеспечивающей помощь в достижении целей организации. Для связи между VLAN требуется устройство, работающее на уровне 3 модели OSI. Добавление маршрутизации между VLAN позволяет организации разделять и разделять широковещательные домены, одновременно позволяя им обмениваться данными друг с другом.

Транковые каналы сети VLAN используются для распространения сетей VLAN по различным устройствам. Транковые каналы разрешают передачу трафика из множества сетей VLAN через один канал, не нанося вред идентификации и сегментации сети VLAN. Особый вид маршрутизации между VLAN, называемый «Router-on-a-Stick», использует магистраль от маршрутизатора к коммутатору, чтобы все VLAN могли переходить к маршрутизатору.

В этой лабораторной работе вы создадите VLAN на обоих коммутаторах в топологии, назначите VLAN для коммутации портов доступа, убедитесь, что VLAN работают должным образом, создадите транки VLAN между двумя коммутаторами и между S1 и R1, и настройте маршрутизацию между VLAN на R1 для разрешения связи между хостами в разных VLAN независимо от подсети, в которой находится хост.

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

**Примечание.** Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены в этом, обратитесь к инструктору.

# Необходимые ресурсы

- 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
- 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
- 2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1. Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

## Часть 2. Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

### Шаг 1. Настройте базовые параметры для маршрутизатора.

a. Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
b. Войдите в режим конфигурации.
c. Назначьте маршрутизатору имя устройства.
d. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
e. Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
f. Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
g. Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
h. Зашифруйте открытые пароли.
i. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
k. Настройте на маршрутизаторе время.

```cisco
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain-l
R1(config)#no ip domain-lookup 
R1(config)#en sec
R1(config)#en secr
R1(config)#en secret class
% Ambiguous command: "en secret class"
R1(config)#enable secret class
R1(config)#line con
R1(config)#line console 0
R1(config-line)#pass
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 15
R1(config-line)#pass
R1(config-line)#password cisco
R1(config-line)#login 
R1(config-line)#exit
R1(config)#serv
R1(config)#service pass
R1(config)#service password-encryption 
R1(config)#bann
R1(config)#banner motd #R1_LaB_Maxim#
R1(config)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#exit
R1 con0 is now available
Press RETURN to get started.
R1_LaB_Maxim

R1>en
Password: 
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.

R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#clock set 23:52:00 22 Feb 2026
R1#
```

### Шаг 2. Настройке базовые параметры каждого коммутатора.

a. Присвойте коммутатору имя устройства.
b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
c. Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
d. Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
e. Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
f. Зашифруйте открытые пароли.
g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
h. Настройте на коммутаторах время.
i. Сохранение текущей конфигурации в качестве начальной.

```cisco
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain-l
S1(config)#no ip domain-lookup 
S1(config)#enable secret class
S1(config)#line con
S1(config)#line console 0
S1(config-line)#pass
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#pass
S1(config-line)#password cisco
S1(config-line)#log
S1(config-line)#login
S1(config-line)#exit
S1(config)#serv
S1(config)#service password-e
S1(config)#service password-encryption 
S1(config)#banner motd #S1-LaB_Maxim#
S1(config)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#clock set 00:23:00 23 feb 2026
S1#copy r
S1#copy running-config st
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```

```cisco
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#no ip domain_l
S2(config)#no ip domain-l
S2(config)#no ip domain-lookup 
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#servise oassword-e
S2(config)#servise password-e
S2(config)#servise password-en
S2(config)#service password-e
S2(config)#service password-encryption 
S2(config)#banner mo
S2(config)#banner motd #S2_Lab_Maxim#
S2(config)#end
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#clock set 00:33:00 23 Feb 2026
S2#cppy r
S2#copy r
S2#copy running-config st
S2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```


### Шаг 3.  Настройке узлов ПК.

<img width="701" height="683" alt="2026-02-23_00-40-33" src="https://github.com/user-attachments/assets/d23b8e8f-ccbf-42c6-9dc9-a5e25e8adeee" />
<img width="713" height="683" alt="2026-02-23_00-40-02" src="https://github.com/user-attachments/assets/f7bc72ec-2161-4434-88bd-fb786adee637" />


## Часть 3. Создание сетей VLAN и назначение портов коммутатора

Во второй части вы создадите VLAN, как указано в таблице выше, на обоих коммутаторах. Затем вы назначите VLAN соответствующему интерфейсу и проверите настройки конфигурации. Выполните следующие задачи на каждом коммутаторе.

### Шаг 1. Создайте сети VLAN на коммутаторах.

a. Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.
```cisco
S1>en
Password: 
Password: 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 10
S1(config-vlan)#name Upravlenie
S1(config-vlan)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#vlan 20
        ^
% Invalid input detected at '^' marker.
	
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 20
S1(config-vlan)#name Sales
S1(config-vlan)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console
```
<img width="1373" height="694" alt="image" src="https://github.com/user-attachments/assets/05871298-12fc-40c0-a9a3-9ce5009a667c" />

```cisco
S2_Lab_Maxim

User Access Verification

Password: 

S2>en
Password: 
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#vlan 10
S2(config-vlan)#name Upravlenie
S2(config-vlan)#vlan 20
S2(config-vlan)#name Sales
S2(config-vlan)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#vlan 999
S2(config-vlan)#name Parking_Lot
S2(config-vlan)#vlan 1000
S2(config-vlan)#name Native
S2(config-vlan)#end
S2#
%SYS-5-CONFIG_I: Configured from console by console
```
<img width="1431" height="629" alt="image" src="https://github.com/user-attachments/assets/8edd7dee-a179-4e5a-9ffe-a067b3145a90" />


b. Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.

c. Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

    **Примечание.** Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

###  Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

b. Убедитесь, что VLAN назначены на правильные интерфейсы.

Закройте окно настройки.

## Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

В части 3 вы вручную настроите интерфейс F0/1 как транк.

### Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

a. Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

Откройте окно конфигурации

b. Установите native VLAN 1000 на обоих коммутаторах.

c. Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

d. Проверьте транки, native VLAN и разрешенные VLAN через транк.

### Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

a. Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

b. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

c. Проверка транкинга.

#### Вопрос:

Что произойдет, если G0/0/1 на R1 будет отключен?

Закройте окно настройки.

## Настройка маршрутизации между сетями VLAN

### Настройке маршрутизатор.

Откройте окно конфигурации

a. При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

b. Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

c. Убедитесь, что вспомогательные интерфейсы работают

Закройте окно настройки.

## Проверьте, работает ли маршрутизация между VLAN

### Выполните следующие тесты с PC-A. Все должно быть успешно.

**Примечание.** Возможно, вам придется отключить брандмауэр ПК для работы ping

a. Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

b. Отправьте эхо-запрос с PC-A на PC-B.

c. Отправьте команду ping с компьютера PC-A на коммутатор S2.

### Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду **tracert** на адрес PC-A.

#### Вопрос:

Какие промежуточные IP-адреса отображаются в результатах?

# Сводная таблица по интерфейсам маршрутизаторов

| Модель маршрутизатора | Интерфейс Ethernet № 1              | Интерфейс Ethernet № 2              | Последовательный интерфейс № 1 | Последовательный интерфейс № 2 |
|------------------------|-------------------------------------|-------------------------------------|--------------------------------|--------------------------------|
| 1 800                  | Fast Ethernet 0/0 (F0/0)           | Fast Ethernet 0/1 (F0/1)           | Serial 0/0/0 (S0/0/0)         | Serial 0/0/1 (S0/0/1)         |
| 1900                   | Gigabit Ethernet 0/0 (G0/0)        | Gigabit Ethernet 0/1 (G0/1)        | Serial 0/0/0 (S0/0/0)         | Serial 0/0/1 (S0/0/1)         |
| 2801                   | Fast Ethernet 0/0 (F0/0)           | Fast Ethernet 0/1 (F0/1)           | Serial 0/1/0 (S0/1/0)         | Serial 0/1/1 (S0/1/1)         |
| 2811                   | Fast Ethernet 0/0 (F0/0)           | Fast Ethernet 0/1 (F0/1)           | Serial 0/0/0 (S0/0/0)         | Serial 0/0/1 (S0/0/1)         |
| 2900                   | Gigabit Ethernet 0/0 (G0/0)        | Gigabit Ethernet 0/1 (G0/1)        | Serial 0/0/0 (S0/0/0)         | Serial 0/0/1 (S0/0/1)         |
| 4221                   | Gigabit Ethernet 0/0/0 (G0/0/0)    | Gigabit Ethernet 0/0/1 (G0/0/1)    | Serial 0/1/0 (S0/1/0)         | Serial 0/1/1 (S0/1/1)         |
| 4300                   | Gigabit Ethernet 0/0/0 (G0/0/0)    | Gigabit Ethernet 0/0/1 (G0/0/1)    | Serial 0/1/0 (S0/1/0)         | Serial 0/1/1 (S0/1/1)         |

В этой таблице представлен интерфейс маршрутизатора для каждой модели маршрутизатора для интерфейсов Ethernet 1 и 2, а также последовательные интерфейсы 1 и 2.

**Примечание**. Чтобы определить конфигурацию маршрутизатора, можно посмотреть на интерфейсы и установить тип маршрутизатора и количество его интерфейсов. Перечислить все комбинации конфигураций для каждого класса маршрутизаторов невозможно. Эта таблица содержит идентификаторы для возможных комбинаций интерфейсов Ethernet и последовательных интерфейсов на устройстве. Другие типы интерфейсов в таблице не представлены, хотя они могут присутствовать в данном конкретном маршрутизаторе. В качестве примера можно привести интерфейс ISDN BRI. Строка в скобках — это официальное сокращение, которое можно использовать в командах Cisco IOS для обозначения интерфейса.

Конец документа
