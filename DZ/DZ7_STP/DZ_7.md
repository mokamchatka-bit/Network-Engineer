# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

## 1. Топология

<img width="908" height="317" alt="image" src="https://github.com/user-attachments/assets/75efeab9-f3ca-4fb4-8d3a-6b90fc1b8833" />


## 2. Таблица адресации

| Устройство | Интерфейс | IP-адрес    | Маска подсети |
|------------|-----------|-------------|---------------|
| S1         | VLAN 1    | 192.168.1.1 | 255.255.255.0 |
| S2         | VLAN 1    | 192.168.1.2 | 255.255.255.0 |
| S3         | VLAN 1    | 192.168.1.3 | 255.255.255.0 |

## 3. Цели

**Часть 1.** Создание сети и настройка основных параметров устройства



**Часть 2.** Выбор корневого моста

**Часть 3.** Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

**Часть 4.** Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

## 4. Общие сведения/сценарий

Избыточность позволяет увеличить доступность устройств в топологии сети за счёт устранения единой точки отказа. Избыточность в коммутируемой сети обеспечивается посредством использования нескольких коммутаторов или нескольких каналов между коммутаторами. Когда в проекте сети используется физическая избыточность, возможно возникновение петель и дублирование кадров.

Протокол Spanning-Tree (STP) был разработан как механизм предотвращения возникновения петель на 2-м уровне для избыточных каналов коммутируемой сети. Протокол STP обеспечивает наличие только одного логического пути между всеми узлами назначения в сети путем намеренного блокирования резервных путей, которые могли бы вызвать петлю.

В этой лабораторной работе команда **`show spanning-tree`** используется для наблюдения за процессом выбора протоколом STP корневого моста. Также вы будете наблюдать за процессом выбора портов с учетом стоимости и приоритета.

> **Примечание.** Используются коммутаторы Cisco Catalyst 2960s с Cisco IOS версии 15.0(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах.

> **Примечание.** Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору.

## 5. Необходимые ресурсы

- 3 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты
- Кабели Ethernet, расположенные в соответствии с топологией

## Часть 1. Создание сети и настройка основных параметров устройства

В части 1 вам предстоит настроить топологию сети и основные параметры коммутаторов.

1.  **Создайте сеть согласно топологии.**
    Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

2.  **Выполните инициализацию и перезагрузку коммутаторов.**

3.  **Настройте базовые параметры каждого коммутатора.**

    a.  Отключите поиск DNS.
    b.  Присвойте имена устройствам в соответствии с топологией.
    c.  Назначьте **`class`** в качестве зашифрованного пароля доступа к привилегированному режиму.
    d.  Назначьте **`cisco`** в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
    e.  Настройте `logging synchronous` для консольного канала.
    f.  Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
    g.  Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
    h.  Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
```cisco
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#loggin sy
S1(config-line)#loggin synchronous 
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#banner motd #Unauthorized access is prohibited DZ_STP_Maxim#
S1(config)#interf
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no sh

S1(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S1(config-if)#exit
S1(config)#copy running-config startup-config
            ^
% Invalid input detected at '^' marker.
	
S1(config)#
S1(config)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```

```cisco
Switch(config)#no ip domain-lookup
Switch(config)#hostname S2
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#logging synchronous
S2(config-line)#ex
% Ambiguous command: "ex"
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#banner motd #Unauthorized access is prohibited DZ_STP_Maxim#
S2(config)#interf
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.2 255.255.255.0
S2(config-if)#no shutdown

S2(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S2(config-if)#exit
S2(config)#end
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#
```
```cisco
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#no ip doma
Switch(config)#no ip domain-
Switch(config)#no ip domain-lookup
Switch(config)#hostname S3
S3(config)#enable secret class
S3(config)#line console 0
S3(config-line)#password cisco
S3(config-line)#lgin
                 ^
% Invalid input detected at '^' marker.
	
S3(config-line)#login
S3(config-line)#logging synchronous
S3(config-line)#exit
S3(config)#line vty 0 15
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#banner motd #Unauthorized access is prohibited DZ_STP_Maxim#
S3(config)#interface vlan 1
S3(config-if)#ip address 192.168.1.3 255.255.255.0
S3(config-if)#no shutdown

S3(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S3(config-if)#exit
S3(config)#end
S3#
%SYS-5-CONFIG_I: Configured from console by console

S3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S3#
```

5.  **Проверьте связь.**

    Проверьте способность коммутаторов обмениваться эхо-запросами.
<img width="957" height="599" alt="image" src="https://github.com/user-attachments/assets/267d6a6c-3435-4f3b-9204-7a8e1426aee5" />

    Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2? **ДА**

    Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3? **ДА**

    Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? **ДА**

    Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

## Часть 2. Определение корневого моста

Для каждого экземпляра протокола Spanning-Tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола Spanning-Tree, позволяя определить избыточные пути, которые следует заблокировать.

Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

1.  **Отключите все порты на коммутаторах.**
<img width="671" height="614" alt="image" src="https://github.com/user-attachments/assets/3f56afb7-c359-4b0c-811e-5d5dbb2b1a0f" />
<img width="699" height="597" alt="image" src="https://github.com/user-attachments/assets/69268a8b-8b67-4468-8240-bbc0541c144f" />
<img width="561" height="611" alt="image" src="https://github.com/user-attachments/assets/85db448e-f502-4a17-b4dd-6d06f7ba4727" />


2.  **Настройте подключенные порты в качестве транковых.**
<img width="1002" height="591" alt="image" src="https://github.com/user-attachments/assets/26a63de2-4a75-4575-a8bf-9cda8653859f" />

3.  **Включите порты F0/2 и F0/4 на всех коммутаторах.**

<img width="1012" height="640" alt="image" src="https://github.com/user-attachments/assets/30209334-cccd-4c58-9028-62118ce47593" />
<img width="459" height="361" alt="image" src="https://github.com/user-attachments/assets/73f386a7-3c67-446a-aa7b-ee9f41579a6c" />

4.  **Отобразите данные протокола Spanning-Tree.**

    Введите команду **`show spanning-tree`** на всех трех коммутаторах. Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. Расширенным идентификатором системы всегда является номер сети VLAN. В примере ниже все три коммутатора имеют равные значения приоритета идентификатора моста (32769 = 32768 + 1, где приоритет по умолчанию = 32768, номер сети VLAN = 1); следовательно, коммутатор с самым низким значением MAC-адреса становится корневым мостом (в примере — S2).

    **S1# `show spanning-tree`**
```cisco
   S1#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0001.96AD.321D
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p

```

**S2# `show spanning-tree`**
```cisco
    S2#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.BC38.C9E7
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p

S2#
```

**S3# `show spanning-tree`**

```cisco
    S3#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0000.0CB7.48D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Desg FWD 19        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p

S3#
```

    > **Примечание.** Режим STP по умолчанию на коммутаторе 2960 — протокол STP для каждой сети VLAN (PVST).

    **В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.**
<img width="614" height="348" alt="image" src="https://github.com/user-attachments/assets/d36a0fa6-859e-4ea6-a964-6726fc1b1a9e" />

S1 MAC: 0001.96AD.321D
S1 F0/1: отключен
S1 F0/2: Desg FWD
S1 F0/3: отключен
S1 F0/4: Root FWD


S2 MAC: 00D0.BC38.C9E7
S2 F0/1: отключен
S2 F0/2: Altn BLK
S2 F0/3: отключен
S2 F0/4: Root FWD

S3 MAC: 0000.0CB7.48D9 (корневой мост)
S3 F0/1: отключен
S3 F0/2: Desg FWD
S3 F0/3: отключен
S3 F0/4: Desg FWD


**С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.**

   **Какой коммутатор является корневым мостом?**  
**S3**

**Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?**  
Потому что у всех коммутаторов одинаковый приоритет идентификатора моста (32769), и решающим фактором стал наименьший MAC-адрес. MAC-адрес S3 (`0000.0CB7.48D9`) меньше, чем у S1 (`0001.96AD.321D`) и S2 (`00D0.BC38.C9E7`).

**Какие порты на коммутаторе являются корневыми портами?**  
- **S1:** FastEthernet 0/4  
- **S2:** FastEthernet 0/4  

**Какие порты на коммутаторе являются назначенными портами?**  
- **S1:** FastEthernet 0/2  
- **S3 (корневой мост):** FastEthernet 0/2 и FastEthernet 0/4  

**Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?**  
- **S2:** FastEthernet 0/2  

**Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?**  
В сегменте сети между S1 и S2 оба порта (S1 F0/2 и S2 F0/2) имеют одинаковую стоимость пути до корневого моста S3 — 19. Когда стоимость равна, STP сравнивает идентификаторы мостов (BID). BID S1 (`0001.96AD.321D`) меньше, чем BID S2 (`00D0.BC38.C9E7`), поэтому порт S1 F0/2 становится назначенным (Designated Port), а порт S2 F0/2 переводится в альтернативное состояние (Alternate Port) и блокируется для предотвращения петли.
## Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

Алгоритм протокола Spanning-Tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. Порт с более низкой стоимостью пути является предпочтительным. Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. В части 3 вам предстоит изменить стоимость порта, чтобы определить, какой порт будет заблокирован протоколом Spanning-Tree.

 **Шаг 1. Определите коммутатор с заблокированным портом.**

При текущей конфигурации только один коммутатор может содержать заблокированный протоколом STP порт. Выполните команду **`show spanning-tree`** на обоих коммутаторах некорневого моста.
    
**S2 (некорневой мост):**
- *Fa0/2 — Altn BLK  заблокирован*
- *Fa0/4 — Root FWD*

 **Шаг 2 Измените стоимость порта.**

Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. Уменьшите стоимость этого порта корневого моста до 18, выполнив команду **`spanning-tree vlan 1 cost 18`** режима конфигурации интерфейса.

<img width="533" height="580" alt="image" src="https://github.com/user-attachments/assets/be89ee2e-3956-4e2c-8791-8b5c8067a89a" />


4.  **Просмотрите изменения протокола Spanning-Tree.**

    Повторно выполните команду **`show spanning-tree`** на обоих коммутаторах некорневого моста. Обратите внимание, что ранее заблокированный порт (S1 -- F0/4) теперь является назначенным портом, и протокол Spanning-Tree теперь блокирует порт на другом коммутаторе некорневого моста (S3 -- F0/4).

    **S1# `show spanning-tree`**
```cisco
S1>en
Password: 
S1#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0001.96AD.321D
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Altn BLK 19        128.2    P2p

S1#
```

**S2# `show spanning-tree`**
```cisco
    S2#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        18
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.BC38.C9E7
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Root FWD 18        128.4    P2p

```
**S3# `show spanning-tree`**
```cisco
S3>en
Password: 
S3#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0000.0CB7.48D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Desg FWD 19        128.4    P2p
Fa0/2            Desg FWD 19        128.2    P2p

S3#
```

**Почему протокол Spanning-Tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?**
   
   *Изменение стоимости корневого порта на S2 с 19 до 18 уменьшило общую стоимость пути от S2 до корневого моста S3. В сегменте между S1 и S2 теперь S2 имеет более короткий путь к корню, поэтому его порт Fa0/2 становится назначенным, а порт S1 Fa0/2 — ааблокированным*

** Шаг 4. Удалите изменения стоимости порта.**

a.  Выполните команду **`no spanning-tree vlan 1 cost 18`** режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.

<img width="472" height="589" alt="image" src="https://github.com/user-attachments/assets/03aea162-a0aa-486c-b63d-d4d70df9747a" />

b.  Повторно выполните команду **`show spanning-tree`**, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

<img width="1012" height="846" alt="image" src="https://github.com/user-attachments/assets/36fa4d95-a9cd-4d81-89d9-1f43f7eaa3e0" />


## Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Значение приоритета по умолчанию — 128. STP объединяет приоритет порта с номером порта, чтобы разорвать связи. Наиболее низкие значения являются предпочтительными. В части 4 вам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

a.  **Включите порты F0/1 и F0/3 на всех коммутаторах.**

b.  Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду **`show spanning-tree`** на коммутаторах некорневого моста. Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

**S1# `show spanning-tree`**
```cisco
S1#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        19
             Port        3(FastEthernet0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0001.96AD.321D
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Altn BLK 19        128.4    P2p
Fa0/3            Root FWD 19        128.3    P2p
Fa0/1            Desg FWD 19        128.1    P2p
Fa0/2            Desg FWD 19        128.2    P2p

S1#
```

**S2# `show spanning-tree`**
```cisco
S2#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0000.0CB7.48D9
             Cost        19
             Port        3(FastEthernet0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.BC38.C9E7
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Altn BLK 19        128.1    P2p
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/3            Root FWD 19        128.3    P2p
Fa0/4            Altn BLK 19        128.4    P2p

S2#
```
**Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?**

*S1: FastEthernet 0/3*

*S2: FastEthernet 0/3*

**Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?**
 
 *Все пути до корневого моста S3 имеют одинаковую стоимость (19). Когда стоимость равна, STP сравнивает приоритет портов. Приоритет по умолчанию у всех портов одинаковый (128), поэтому выбирается порт с наименьшим номером. Прямые соединения S1 Fa0/3 и S2 Fa0/3 с S3 имеют меньшие номера портов, чем другие прямые соединения (Fa0/4), поэтому они становятся корневыми портами.*   

<img width="1057" height="598" alt="image" src="https://github.com/user-attachments/assets/ffd88e97-deb0-40d4-bae2-2bf802e790b1" />

## Ответы на вопросы для повторения

1. **Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?**  
   **Стоимость пути к корневому мосту (Root Path Cost).**  
   Порт с наименьшей стоимостью пути становится корневым портом (Root Port) на некорневом мосту.

2. **Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?**  
   **Идентификатор моста (Bridge ID — BID) моста-отправителя (Sender Bridge ID).**  
   Если стоимость пути одинакова, STP сравнивает BID соседних коммутаторов. Меньший BID означает, что порт на этом коммутаторе станет назначенным (Designated Port).

3. **Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?**  
   **Приоритет порта (Port Priority) и номер порта (Port ID).**  
   Если BID равны, STP сравнивает приоритет порта (по умолчанию 128). Если приоритеты равны, выбирается порт с наименьшим номером порта.
