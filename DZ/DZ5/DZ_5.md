# *Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH*

# Топология
<img width="665" height="139" alt="image" src="https://github.com/user-attachments/assets/0f5a301d-a36f-42ff-b122-c53efc4ff4cc" />


# Таблица адресации

| Устройство | Интерфейс | IP-адрес     | Маска подсети   | Шлюз по умолчанию |
|------------|-----------|--------------|-----------------|-------------------|
| R1         | G0/0/1    | 192.168.1.1  | 255.255.255.0   | ---               |
| S1         | VLAN 1    | 192.168.1.11 | 255.255.255.0   | 192.168.1.1       |
| PC-A       | NIC       | 192.168.1.3  | 255.255.255.0   | 192.168.1.1       |

В этой таблице показана адресация для устройства, интерфейса, IP-адрес, маска подсети, префикс и шлюз по умолчанию.

# Задачи

Часть 1. Настройка основных параметров устройства

Часть 2. Настройка маршрутизатора для доступа по протоколу SSH

Часть 3. Настройка коммутатора для доступа по протоколу SSH

Часть 4. SSH через интерфейс командной строки (CLI) коммутатора

# Общие сведения/сценарий

Раньше для удаленной настройки сетевых устройств в основном применялся протокол Telnet. Однако он не обеспечивает шифрование информации, передаваемой между клиентом и сервером, что позволяет анализаторам сетевых пакетов перехватывать пароли и данные конфигурации.

Secure Shell (SSH) — это сетевой протокол, устанавливающий безопасное подключение с эмуляцией терминала к маршрутизатору или иному сетевому устройству. Протокол SSH шифрует все сведения, которые поступают по сетевому каналу, и предусматривает аутентификацию удаленного компьютера. Протокол SSH все больше заменяет Telnet — именно его выбирают сетевые специалисты в качестве средства удаленного входа в систему. SSH чаще всего используется для входа на удаленное устройство и выполнения команд. Но это может также передавать файлы по связанным протоколам SFTP или SCP.

Чтобы протокол SSH мог работать, на сетевых устройствах, взаимодействующих между собой, должна быть настроена поддержка SSH. В этой лабораторной работе необходимо включить SSH-сервер на маршрутизаторе, после чего подключиться к этому маршрутизатору, используя ПК с установленным клиентом SSH. В локальной сети подключение обычно устанавливается с помощью Ethernet и IP.

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

**Примечание**: Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены, обратитесь к инструктору.

# Необходимые ресурсы

- 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
- 1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
- 1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1. Настройка основных параметров устройства

В части 1 потребуется настроить топологию сети и основные параметры, такие как IP-адреса интерфейсов, доступ к устройствам и пароли на маршрутизаторе.

### Шаг 1. Создайте сеть согласно топологии.

### Шаг 2. Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.

### Шаг 3. Настройте маршрутизатор.

Откройте окно конфигурации

a. Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
b. Войдите в режим конфигурации.
c. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
d. Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
e. Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
f. Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
g. Зашифруйте открытые пароли.
h. Создайте баннер, который предупреждает о запрете несанкционированного доступа.
i. Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.
j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```cisco
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostn
Router(config)#hostname R1
R1(config)#no ip doma
R1(config)#no ip domam
R1(config)#no ip don
R1(config)#no ip domain
R1(config)#no ip domain-lookup
R1(config)#enable  secret class
R1(config)#login
% Incomplete command.
R1(config)#lin
R1(config)#line con
R1(config)#line console 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 15
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#ser
R1(config)#service passw
R1(config)#service password-encryption 
R1(config)#banner motd #ZAPRET OT MAXIM#
R1(config)#it
R1(config)#inter
R1(config)#interface g0/0/1
R1(config-if)#ip ad
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shut
R1(config-if)#no shutdown 
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
exit
R1(config)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console
copy ru
R1#copy running-config st
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
### Шаг 4. Настройте компьютер PC-A.

a. Настройте для PC-A IP-адрес и маску подсети.
b. Настройте для PC-A шлюз по умолчанию.
<img width="437" height="499" alt="2026-02-14_00-37" src="https://github.com/user-attachments/assets/7c428603-b85d-42e8-841c-dfd0ae950162" />


### Проверьте подключение к сети.

Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.
<img width="463" height="259" alt="2026-02-14_00-49" src="https://github.com/user-attachments/assets/5020c7c2-e14f-4828-8f55-5c53dcaac8a4" />

Закройте окно настройки.

## Часть 2. Настройка маршрутизатора для доступа по протоколу SSH

Подключение к сетевым устройствам по протоколу Telnet сопряжено с риском для безопасности, поскольку вся информация передается в виде открытого текста. Протокол SSH шифрует данные сеанса и обеспечивает аутентификацию устройств, поэтому для удаленных подключений рекомендуется использовать именно этот протокол. В части 2 вам нужно настроить маршрутизатор для приема соединений SSH по линиям VTY.

### Шаг 1. Настройте аутентификацию устройств.

При генерации ключа шифрования в качестве его части используются имя устройства и домен. Поэтому эти имена необходимо указать перед вводом команды **crypto key**.

Откройте окно конфигурации

a. Задайте имя устройства.

b. Задайте домен для устройства.
*R1(config)#ip domain-name local.domain*

### Шаг 2. Создайте ключ шифрования с указанием его длины.

*R1(config)#crypto key generate rsa general-keys modulus 1024*

### Шаг 3. Создайте имя пользователя в локальной базе учетных записей.

Настройте имя пользователя, используя **admin** в качестве имени пользователя и **Adm1nP @55** в качестве пароля.

*R1(config)# username admin secret Adm1nP@55*

### Шаг 4. Активируйте протокол SSH на линиях VTY.

a. Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды **transport input**.
```cisco
R1(config)#line vty 0 15
R1(config-line)# transport input ssh
R1(config-line)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#write memory 
Building configuration...
[OK]
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip ssh ve
R1(config)#ip ssh version 2
R1(config)#END
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#line vty 0 15
R1(config-line)# transport input telnet
R1(config-line)#
```
b. Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

### шАГ 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```cisco
R1#write memory 
Building configuration...
[OK]
R1#
```
### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.

a. Запустите Tera Term с PC-A.

b. Установите SSH-подключение к R1. Use the username **admin** and password **Adm1nP@55**. У вас должно получиться установить SSH-подключение к R1.
<img width="486" height="628" alt="2026-02-14_01-32" src="https://github.com/user-attachments/assets/29afa99b-e7e4-409a-9134-b614a8cdbc7f" />

## Часть 3. Настройка коммутатора для доступа по протоколу SSH

В части 3 вам предстоит настроить коммутатор для приема подключений по протоколу SSH, а затем установить SSH-подключение с помощью программы Tera Term.

### Шаг 1._Настройте основные параметры коммутатора.

Откройте окно конфигурации

a. Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.
b. Войдите в режим конфигурации.
c. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
d. Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
e. Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
f. Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
g. Зашифруйте открытые пароли.
h. Создайте баннер, который предупреждает о запрете несанкционированного доступа.
i. Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.
j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```cisco
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#no ip domain-lookup 
Switch(config)#en
Switch(config)#ena
Switch(config)#enable sec
Switch(config)#enable secret class
Switch(config)#line con
Switch(config)#line console 0
Switch(config-line)#password cisco
Switch(config-line)#login
Switch(config-line)#exit
Switch(config)#line vty 0 15
Switch(config-line)#password cisco
Switch(config-line)#login
Switch(config-line)#exit
Switch(config)#ser
Switch(config)#service paass
Switch(config)#service paassword-e
Switch(config)#service password-encryption
Switch(config)#banner motd #ZAPRET OT MAXIM_S1#
Switch(config)#interface vlan 1
Switch(config-if)#p address 192.168.1.11 255.255.255.0
% Ambiguous command: "p address 192.168.1.11 255.255.255.0"
Switch(config)#no shutdown
                   ^
% Invalid input detected at '^' marker.
	
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.1.11 255.255.255.0
Switch(config-if)#no shutdown

Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

Switch(config-if)#exit
Switch(config)#ip default-gateway 192.168.1.1
Switch(config)#end
Switch#
%SYS-5-CONFIG_I: Configured from console by console

Switch#wr
Switch#write m
Switch#write memory 
Building configuration...
[OK]
Switch#
```
### Шаг 2. Настройке коммутатора для соединения по протоколу SSH.

Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

a. Настройте имя устройства, как указано в таблице адресации.
b. Задайте домен для устройства.
c. Создайте ключ шифрования с указанием его длины.
d. Создайте имя пользователя в локальной базе учетных записей.
e. Активируйте протоколы Telnet и SSH на линиях VTY.
f. Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.
```cisco
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#host na
Switch(config)#hostnaame S1
                     ^
% Invalid input detected at '^' marker.
	
Switch(config)#hostname S1
S1(config)#i[p dom
S1(config)#ip domain-name local.domain
S1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: S1.local.domain

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:53:28.492: %SSH-5-ENABLED: SSH 1.99 has been enabled
S1(config)#username admin secret Adm1nP@55
S1(config)#line vty 0 15
S1(config-line)#transport input ssh
S1(config-line)#login local
S1(config-line)#ex
% Ambiguous command: "ex"
S1(config-line)#exit
S1(config)#ip ssh version 2
S1(config)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#
S1#
S1#write memory
Building configuration...
[OK]
S1#
```
### Шаг 3. Установите соединение с коммутатором по протоколу SSH.

Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.

#### Вопрос:

Удалось ли вам установить SSH-соединение с коммутатором?
<img width="627" height="841" alt="2026-02-14_02-44" src="https://github.com/user-attachments/assets/8882ba5c-55e0-4c7e-a50e-632b345e5359" />



## Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора

Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.

### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.

Используйте вопросительный знак (**?**), чтобы отобразить варианты параметров для команды **ssh**.

S1# **ssh?**
-c Select encryption algorithm
-l Log in using this user name
-m Select HMAC algorithm
-o Specify options
-p Connect to this port
-v Specify SSH Protocol Version
-vrf Specify vrf name
WORD IP-адрес или имя хоста удаленной системы

### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.

a. Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду **--l admin**. Это позволит вам войти в систему под именем **admin**. При появлении приглашения введите в качестве пароля **Adm1nP@55**

S1# **ssh -l admin 192.168.1.1**
Password:
Authorized Users Only!
R1>

b. Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш **Ctrl**+**Shift**+**6**. Отпустите клавиши **Ctrl**+**Shift**+**6** и нажмите **x**. Отображается приглашение привилегированного режима EXEC коммутатора.
R1>
S1#
c. Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.
S1#
[Resuming connection 1 to 192.168.1.1 ... ]
R1>
d. Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду **exit**.
R1# **exit**
[Connection to 192.168.1.1 closed by foreign host]
S1#

#### Вопрос:
Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?
*По умолчанию используется SSHv2. Версию можно указать ключом -v при подключении.*

<img width="599" height="835" alt="2026-02-14_03-05" src="https://github.com/user-attachments/assets/c06a11f5-2db5-4343-9358-34cf257b8929" />

# Вопрос для повторения

Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?
*Необходимо создать локальную базу учётных записей на устройстве с помощью команды **username** и настроить линии VTY на аутентификацию по локальной базе командой **login local.***
```cisco
username maxim1 secret class
username maxim2 secret cisco
line vty 0 15
 login local
 transport input ssh
```
