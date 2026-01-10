# Лабораторная работа: Базовая настройка коммутатора
https://github.com/mokamchatka-bit/Network-Engineer/blob/main/DZ/DZ1/README.md#%D0%BE%D0%BF%D0%B8%D1%81%D0%B0%D0%BD%D0%B8%D0%B5

## Описание
Лабораторная работа посвящена основам настройки коммутатора Cisco 2960. В ходе работы вы научитесь:
- Проверять заводские настройки коммутатора
- Настраивать базовые параметры устройства (имя, пароли, баннеры)
- Назначать IP-адрес для удалённого управления через SVI (VLAN 1)
- Настраивать доступ через консоль и Telnet
- Проверять связность сети с помощью утилиты ping
- Удалённо управлять коммутатором через Telnet

## Топология
<img width="498" height="150" alt="image" src="https://github.com/user-attachments/assets/e7adc0b0-6008-40e9-9d7a-172d8348658f" />

PC-A (192.168.1.10/24) --- F0/6 --- S1 (192.168.1.2/24)

(консольное подключение через Console порт)


## Таблица адресации
| Устройство | Интерфейс   | IP-адрес / маска      |
|------------|-------------|-----------------------|
| S1         | VLAN 1      | 192.168.1.2 /24      |
| PC-A       | NIC         | 192.168.1.10 /24     |

## Необходимое оборудование и ПО
- 1 коммутатор Cisco 2960 (Cisco IOS 15.2(2) lanbasek9 или аналогичный)
- 1 ПК с Windows
- Программа эмуляции терминала (Tera Term, PuTTY)
- Консольный кабель (RJ-45 – DB9/DB25/USB)
- Кабель Ethernet (витая пара)

## Части лабораторной работы
1. **Проверка конфигурации коммутатора по умолчанию**
2. **Настройка базовых параметров устройства**
3. **Проверка сетевых подключений**

## Основные шаги настройки
## Часть 1. Проверка конфигурации коммутатора по умолчанию

### Шаг 1. Создание сети

a.	Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.  
   *Примечание:* При использовании Netlab отключите интерфейс F0/6 на коммутаторе S1. Это имеет такой же эффект, как отсоединение компьютера PC-A от коммутатора S1

b. 	Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.

> **❓ Вопрос:**  
> Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?
>
>  **✅ Ответ:**  
> Потому что по умолчанию коммутатор не имеет настроенныхдля удалённого доступа. Telnet или SSH требуют предварительной настройки сетевых параметров, которая возможна только через консольное подключение.

### Шаг 2. Проверка настроек коммутатора по умолчанию

На данном этапе вам нужно проверить такие параметры коммутатора по умолчанию, как текущие настройки коммутатора, данные IOS, свойства интерфейса, сведения о VLAN и флеш-память.
Все команды IOS коммутатора можно выполнять из привилегированного режима. Доступ к привилегированному режиму нужно ограничить с помощью пароля, чтобы предотвратить неавторизованное использование устройства — через этот режим можно получить прямой доступ к режиму глобальной конфигурации и командам, используемым для настройки рабочих параметров. Пароли можно будет настроить чуть позже.
К привилегированному набору команд относятся команды пользовательского режима, а также команда configure, при помощи которой выполняется доступ к остальным командным режимам. Введите команду enable, чтобы войти в привилегированный режим EXEC.

a. Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду enable, чтобы войти в привилегированный режим EXEC.

Откройте окно конфигурации

Обратите внимание, что измененная в конфигурации строка будет отражать привилегированный режим EXEC.
Убедитесь, что на коммутаторе находится пустой файл конфигурации по умолчанию, с помощью команды show running-config привилегированного режима EXEC. Если конфигурационный файл был предварительно сохранен, его нужно удалить. В зависимости от модели коммутатора и версии IOS ваша конфигурация может слегка отличаться. Тем не менее, настроенных паролей или IP-адресов в конфигурации быть не должно. Выполните очистку настроек и перезагрузите коммутатор, если ваш коммутатор имеет настройки, отличные от настроек по умолчанию.

- Войти в привилегированный режим на коммутаторе:
  
Switch> enable 
Switch#

- Проверьте текущую конфигурацию коммутатора:
  
Switch# show running-config

```cisco
Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
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
line con 0
!
line vty 0 4
login
line vty 5 15
login
!
end
```
b.	Изучите текущий файл running configuration:

> **❓ Вопрос:**  
> Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?
>
> **✅ Ответ:**  
> 24 интерфейса FastEthernet.

> **❓ Вопрос:**  
> Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?
>
> **✅ Ответ:**  
> 2 интерфейса Gigabit Ethernet.

> **❓ Вопрос:**  
Каков диапазон значений, отображаемых в vty-линиях?
>
> **✅ Ответ:**   
> Диапазоны: `line vty 0 4` и `line vty 5 15`
> 
> ```cisco
> Switch# show running-config | section line vty
> line vty 0 4
> login
> line vty 5 15
> login
> ```

c.	Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM).
> **❓ Вопрос:**  
> Почему появляется это сообщение?
> 
> **✅ Ответ:**
> 
> ```cisco
> Switch#show startup-config
> startup-config is not present
> ```
> На коммутаторе нет сохранённой конфигурации

d.	Изучите характеристики SVI для VLAN 1:

Switch# show interface vlan 1
```cisco
Vlan1 is administratively down, line protocol is down
Hardware is CPU Interface, address is 0001.c7a6.7e29 (bia 0001.c7a6.7e29)
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out
 ```
> **❓ Вопрос:**  
Назначен ли IP-адрес сети VLAN 1?
>
> **✅ Ответ:**   
> нет

> **❓ Вопрос:**  
Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.
>
> **✅ Ответ:**   
> 0001.c7a6.7e29

> **❓ Вопрос:**  
Данный интерфейс включен?
>
> **✅ Ответ:**   
>ВЫКЛЮЧЕН 
> 
> ```cisco
> interface Vlan1
> no ip address
> shutdown
> ```

e.	Изучите IP-свойства интерфейса SVI сети VLAN 1:

Switch# show ip interface

> **❓ Вопрос:**  
Какие выходные данные вы видите?
>
> **✅ Ответ:**
> VLAN 1  неактивен
> ```cisco
> Switch#show ip interface
> Vlan1 is administratively down, line protocol is down
> Internet protocol processing disabled
> ```
f.	Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК:

*Примечание: При использовании Netlab включите интерфейс F0/6 на коммутаторе S1.*

g.	Изучите сведения о версии ОС Cisco IOS на коммутаторе:

```cisco
S1-max#show ver
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

ROM: Bootstrap program is C2960 boot loader
BOOTLDR: C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)

Switch uptime is 39 minutes
System returned to ROM by power-on
System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
Processor board ID FOC1010X104
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
2 Gigabit Ethernet interfaces
The password-recovery mechanism is enabled.

64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address : 00:01:C7:A6:7E:29
Motherboard assembly number : 73-10390-03
Power supply part number : 341-0097-02
Motherboard serial number : FOC10093R12
Power supply serial number : AZS1007032H
Model revision number : B0
Motherboard revision number : B0
Model number : WS-C2960-24TT-L
System serial number : FOC1010X104
Top Assembly Part Number : 800-27221-02
Top Assembly Revision Number : A0
Version ID : V02
CLEI Code Number : COM3L00BRA
Hardware Board Revision Number : 0x01


Switch Ports Model SW Version SW Image
------ ----- ----- ---------- ----------
* 1 26 WS-C2960-24TT-L 15.0(2)SE4 C2960-LANBASEK9-M

Configuration register is 0xF

S1-max#
```
> **❓ Вопрос:**  
> Под управлением какой версии ОС Cisco IOS работает коммутатор?
>
> **✅ Ответ:**   
> Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE

> **❓ Вопрос:**  
> Как называется файл образа системы?
>
> **✅ Ответ:**
>
> System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"

h.	Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A:

Switch# show interface f0/6 
```cisco
S1-max#show interfaces f0/6
FastEthernet0/6 is up, line protocol is up (connected)
Hardware is Lance, address is 00d0.bae8.3806 (bia 00d0.bae8.3806)
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
```
> **❓ Вопрос:**  
> Интерфейс включен или выключен?
>
> **✅ Ответ:**
> ВЫКЛЮЧЕН FastEthernet0/6 is up, line protocol is up (connected)
>
> **❓ Вопрос:**
> Что нужно сделать, чтобы включить интерфейс?
>
> **✅ Ответ:**
> VLAN 1  неактивен
```cisco
S1-max#conf t
Enter configuration commands, one per line. End with CNTL/Z.
S1-max(config)#
S1-max(config)#interface f0/6
S1-max(config-if)#no shutdown 
S1-max(config-if)#exit
S1-max(config)#exit
```

> **❓ Вопрос:**
> Какой MAC-адрес у интерфейса?
>
> **✅ Ответ:**
> Hardware is Lance, address is 00d0.bae8.3806 (bia 00d0.bae8.3806)

> **❓ Вопрос:**
> Какие настройки скорости и дуплекса заданы в интерфейсе?
>
> **✅ Ответ:**
> Full-duplex, 100Mb/s

i.	Изучите флеш-память:


S1-max# show flash:
S1-max# dir flash:
```cisco

S1-max#
S1-max#show fl
S1-max#show flash: 
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)
S1-max#dir flash
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)
S1-max#dir flash:
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)
S1-max#
```
> **❓ Вопрос:**
> Какое имя присвоено образу Cisco IOS?
>
> **✅ Ответ:**
> 2960-lanbasek9-mz.150-2.SE4.bin
