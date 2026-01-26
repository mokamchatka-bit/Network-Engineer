# Лабораторная работа (ДЗ №2): Изучение таблицы MAC-адресов коммутатора

## Описание
Лабораторная работа посвящена изучению процесса формирования и функционирования таблицы MAC-адресов на коммутаторах Cisco. В ходе работы вы научитесь:
- Настраивать базовые параметры коммутаторов
- Определять MAC-адреса сетевых устройств
- Наблюдать за динамическим заполнением таблицы MAC-адресов
- Анализировать процесс передачи кадров Ethernet в локальной сети

## Топология

<img width="501" height="295" alt="image" src="https://github.com/user-attachments/assets/ddc61bcb-e489-4658-9e83-a86837591ddf" />

PC-A (192.168.1.1/24) --- F0/6 --- S1 (192.168.1.11/24) --- F0/1 --- S2 (192.168.1.12/24) --- F0/18 --- PC-B (192.168.1.2/24)

## Таблица адресации
| Устройство | Интерфейс   | IP-адрес / маска      |
|------------|-------------|-----------------------|
| S1         | VLAN 1      | 192.168.1.11 /24     |
| S2         | VLAN 1      | 192.168.1.12 /24     |
| PC-A       | NIC         | 192.168.1.1 /24      |
| PC-B       | NIC         | 192.168.1.2 /24      |

## Цели работы
**Часть 1:** Создание и настройка сети  
**Часть 2:** Изучение таблицы MAC-адресов коммутатора

## 	Общие сведения/сценарий
Коммутатор локальной сети на уровне 2 предназначен для доставки кадров Ethernet всем узловым устройствам в локальной сети (LAN). Он записывает МАС-адреса узлов, отображаемые в сети, и сопоставляет их с собственными портами коммутатора Ethernet. Этот процесс называется созданием таблицы МАС-адресов. Получив кадр от ПК, коммутатор изучает МАС-адреса источника и назначения кадра. MAC-адрес источника регистрируется и сопоставляется с портом коммутатора, от которого он был получен. Затем по таблице MAC-адресов определяется МАС-адрес назначения. Если MAC-адрес назначения известен, кадр пересылается через соответствующий порт коммутатора, связанный с этим MAC-адресом. Если MAC-адрес неизвестен, то кадр отправляется по широковещательной рассылке через все порты коммутатора, кроме того, через который он был получен. Важно видеть и понимать работу коммутатора и то, как он осуществляет передачу данных по сети. Понимание функционала коммутатора особенно важно для сетевых администраторов, задача которых заключается в обеспечении безопасной и стабильной работы сети.
Коммутаторы используются для соединения компьютеров в локальных сетях (LAN) и передачи данных между ними. Коммутаторы отправляют кадры Ethernet на узловые устройства, которые идентифицируются по МАС-адресам сетевых плат.

В части 1 вам нужно построить топологию, состоящую из двух коммутаторов. В части 2 вам предстоит отправить эхо-запросы различным устройствам и посмотреть, как два коммутатора строят свои таблицы МАС-адресов.

**Примечание.** В лабораторной работе используются коммутаторы Cisco Catalyst 2960s с операционной системой Cisco IOS 15.2(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах.

**Примечание:** Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены в этом, обратитесь к инструктору.


## Необходимое оборудование и ПО
- •	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
- •	2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
- •	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- •	Кабели Ethernet, расположенные в соответствии с топологией
  **Примечание.** Интерфейсы Fast Ethernet на коммутаторах Cisco 2960 определяют тип подключения автоматически, поэтому между коммутаторами S1 и S2 можно использовать прямой кабель Ethernet. При использовании коммутатора Cisco другой модели может потребоваться перекрестный кабель Ethernet.


## РАБОТА

### Часть 1. Создание и настройка сети

#### Шаг 1. Подключение сети согласно топологии
- Соедините устройства кабелями Ethernet как показано на схеме
- Для связи между коммутаторами используйте порты F0/1
![2026-01-25_21-01](https://github.com/user-attachments/assets/f4964b9f-0b21-49d4-8ec7-94c1ad508265)

#### Шаг 2. Настройка узлов ПК
1. Назначьте PC-A IP-адрес: 192.168.1.1/24
```cisco
Cisco Packet Tracer PC Command Line 1.0
C:\>ipconfig

FastEthernet0 Connection:(default port)

  Connection-specific DNS Suffix..: 
  Link-local IPv6 Address.........: FE80::202:17FF:FE8C:D413
 IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.1
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0

C:\>
 ```  
3. Назначьте PC-B IP-адрес: 192.168.1.2/24
```cisco
Cisco Packet Tracer PC Command Line 1.0
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::260:2FFF:FE9E:7454
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.2
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0

C:\>
```
#### Шаг 3. Выполните инициализацию и перезагрузку коммутаторов

#### Шаг 4. Настройте базовые параметры каждого коммутатора

a.	Настройте имена устройств в соответствии с топологией.
b.	Настройте IP-адреса, как указано в таблице адресации.
c.	Назначьте cisco в качестве паролей консоли и VTY.
d.	Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.
```cisco
S1(config)#hostname S_1
S_1(config)#interfac
S_1(config)#interface vlan 1
S_1(config-if)#ip adress 192.168.1.11 255.255.255.0
                    ^
% Invalid input detected at '^' marker.
	
S_1(config-if)#ip address 192.168.1.11 255.255.255.0
S_1(config-if)#no shu
S_1(config-if)#no shutdown 
S_1(config-if)#exit
S_1(config)#line vty 0 15
S_1(config-line)#password cisco
S_1(config-line)#login
S_1(config-line)#exit
S_1(config)#enable secret class
S_1(config)#copy rurri
S_1(config)#copy runni
S_1(config)#copy running
S_1(config)#copy running-c
S_1(config)#copy running-con
S_1(config)#copy running-config startup-config
             ^
% Invalid input detected at '^' marker.
	
S_1(config)#copy running-config startup-config
             ^
% Invalid input detected at '^' marker.
	
S_1(config)#end
S_1#
%SYS-5-CONFIG_I: Configured from console by console

S_1#copy running-config startup-config
Destination filename [startup-config]? y
%Error copying nvram:y (Invalid argument)
S_1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S_1#
```
```cisco
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S_2
S_2(config)#conf VLAN 1 
             ^
% Invalid input detected at '^' marker.
	
S_2(config)#config VLAN 1 
             ^
% Invalid input detected at '^' marker.
	
S_2(config)#inter
S_2(config)#interface VLAN 1
S_2(config-if)# ip addres
S_2(config-if)# ip address 192.168.1.12 255.255.255.0
S_2(config-if)#exit
S_2(config)#line 0 15
%Error: Line 1 is not in async mode
S_2(config)#interface VLAN 1
S_2(config-if)# ip address 192.168.1.12 255.255.255.0
S_2(config-if)#no s
S_2(config-if)#no shu
S_2(config-if)#no shutdown 

S_2(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S_2(config-if)#exit
S_2(config)#line vty 0 15
S_2(config-line)#pass
S_2(config-line)#password cisco
S_2(config-line)#login
S_2(config-line)#exit
S_2(config)#enable
S_2(config)#enable secret class
S_2(config)#exit
S_2#
%SYS-5-CONFIG_I: Configured from console by console

S_2#copy runn
S_2#copy running-config start
S_2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S_2#
```

### Часть 2. Изучение таблицы МАС-адресов коммутатора
Как только между сетевыми устройствами начинается передача данных, коммутатор выясняет МАС-адреса и строит таблицу.

#### Шаг 1. Запишите МАС-адреса сетевых устройств.
a.	Откройте командную строку на PC-A и PC-B и введите команду ipconfig /all.
```cisco
C:\>ipconfig  /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0002.178C.D413
   Link-local IPv6 Address.........: FE80::202:17FF:FE8C:D413
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.1
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-68-B7-D3-B0-00-02-17-8C-D4-13
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Physical Address................: 00D0.5818.60E0
   Link-local IPv6 Address.........: ::

C:\>
```

```cisco
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0060.2F9E.7454
   Link-local IPv6 Address.........: FE80::260:2FFF:FE9E:7454
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.2
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-BC-7A-A1-13-00-60-2F-9E-74-54
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Physical Address................: 0002.16DB.4B58
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-BC-7A-A1-13-00-60-2F-9E-74-54
   DNS Servers.....................: ::
                                     0.0.0.0

C:\>
```
- Открытие окна командной строки Windows
> **❓ Вопрос:**
> Назовите физические адреса адаптера Ethernet.
>
>  **✅ Ответ:** MAC-адрес компьютера PC-A: 0002.178C.D413
>
>  **✅ Ответ:** MAC-адрес компьютера PC-B: 0060.2F9E.7454

b.	Подключитесь к коммутаторам S1 и S2 через консоль и введите команду show interface F0/1 на каждом коммутаторе.
```cisco
S_1>show interface F0/1
FastEthernet0/1 is up, line protocol is up (connected)
  Hardware is Lance, address is 00e0.f9c0.e501 (bia 00e0.f9c0.e501)
 BW 100000 Kbit, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s
  input flow-control is off, output flow-control is off
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     956 packets input, 193351 bytes, 0 no buffer
     Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 0 multicast, 0 pause input
     0 input packets with dribble condition detected
     2357 packets output, 263570 bytes, 0 underruns
     0 output errors, 0 collisions, 10 interface resets
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out

S_1>
```

```cisco
S_2>show interface F0/1
FastEthernet0/1 is up, line protocol is up (connected)
  Hardware is Lance, address is 000c.cfdc.5b01 (bia 000c.cfdc.5b01)
 BW 100000 Kbit, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s
  input flow-control is off, output flow-control is off
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     956 packets input, 193351 bytes, 0 no buffer
     Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 0 multicast, 0 pause input
     0 input packets with dribble condition detected
     2357 packets output, 263570 bytes, 0 underruns
     0 output errors, 0 collisions, 10 interface resets
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
```
> **❓ Вопрос:**
> Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).
> 
>  **✅ Ответ:** МАС-адрес коммутатора S1 Fast Ethernet 0/1: 00e0.f9c0.e501
> 
>  **✅ Ответ:** МАС-адрес коммутатора S2 Fast Ethernet 0/1: 000c.cfdc.5b01


#### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.

Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.

a.	Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.


b.	В привилегированном режиме EXEC введите команду show mac address-table и нажмите клавишу ввода.

*S2# show mac address-table* 

Даже если сетевая коммуникация в сети не происходила (т. е. если команда ping не отправлялась), коммутатор может узнать МАС-адреса при подключении к ПК и другим коммутаторам.
<img width="696" height="784" alt="2026-01-26_13-37-40" src="https://github.com/user-attachments/assets/5232f186-ef58-4176-b36d-389b35ce9736" />

> **❓ Вопрос:**  
> Записаны ли в таблице МАС-адресов какие-либо МАС-адреса?
>  **✅ Ответ:**
> Да, в таблице записаны 2 MAC-адреса.



> **❓ Вопрос:**  
> Какие МАС-адреса записаны в таблице?
>  **✅ Ответ:**

## Таблица MAC-адресов на S2

| MAC-адрес        | Тип     | Порт  | Устройство              | Примечание                                   |
|------------------|---------|-------|-------------------------|----------------------------------------------|
| `0002.178c.d413` | DYNAMIC | Fa0/1 | **PC-A**                | MAC-адрес PC-A (из `ipconfig /all`)          |
| `00e0.f9c0.e501` | DYNAMIC | Fa0/1 | **S1 (интерфейс F0/1)** | MAC-адрес S1 F0/1 (из `show interface f0/1`) |

> **❓ Вопрос:**  
> С какими портами коммутатора они сопоставлены и каким устройствам принадлежат?
> **✅ Ответ:**  
> Оба MAC-адреса сопоставлены с портом **Fa0/1** коммутатора S2:
> - `0002.178c.d413` принадлежит **PC-A**
> - `00e0.f9c0.e501` принадлежит **коммутатору S1** (интерфейс FastEthernet0/1)

> **❓ Вопрос:**  
> Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды `show mac address-table`?
> **✅ Ответ:**  
> Можно определить по номеру порта коммутатора, к которому подключено устройство:
> - Устройства на порту Fa0/18 — скорее всего PC-B (согласно топологии)
> - Устройства на порту Fa0/1 — коммутатор S1 и устройства за ним
> Однако без дополнительной информации невозможно точно определить, какой MAC-адрес принадлежит коммутатору, а какой — компьютеру.

> **❓ Вопрос:**  
> Работает ли это решение в любой ситуации?
> **✅ Ответ:**  
> Нет, это решение не работает в любой ситуации, потому что:
> Нельзя точно определить конкретное устройство за коммутатором
> Нельзя отличить MAC-адреса интерфейсов коммутаторов от MAC-адресов компьютеров
> При перемещении устройства на другой порт определение будет неверным
> Таблица MAC-адресов не показывает IP-адреса или имена устройств
> Для точного определения нужны дополнительные команды: `show cdp neighbors detail`, `show arp` и физическая проверка подключений.


#### Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

a.	В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter.
*S2# clear mac address-table dynamic*

b.	Снова быстро введите команду show mac address-table.

> **❓ Вопрос:**
> Указаны ли в таблице МАС-адресов адреса для VLAN 1?
>   **✅ Ответ:**


>  **❓ Вопрос:**
> Указаны ли другие МАС-адреса?
> **✅ Ответ:**

Через 10 секунд введите команду **show mac address-table** и нажмите клавишу ввода. 

> **❓ Вопрос:**
> Появились ли в таблице МАС-адресов новые адреса?
> **✅ Ответ:**


### Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

a.	На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

> **❓ Вопрос:**
> Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?
> **✅ Ответ:**
>

b.	Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

> **❓ Вопрос:**
> От всех ли устройств получены ответы?
>> **✅ Ответ:**
>
> Если нет, проверьте кабели и IP-конфигурации.


c.	Подключившись через консоль к коммутатору S2, введите команду show mac address-table.

> **❓ Вопрос:**
> Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса?
> **✅ Ответ:**
>


> **❓ Вопрос:**
> Если да, то какие адреса и устройства?
> **✅ Ответ:**
>

На компьютере PC-B откройте командную строку и еще раз введите команду **arp -a**.

> **❓ Вопрос:**
> Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?
> **✅ Ответ:**
>

### Вопрос для повторения
В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. 

> **❓ Вопрос:**
> Какие сложности могут возникнуть в крупных сетях?
> **✅ Ответ:**
>
