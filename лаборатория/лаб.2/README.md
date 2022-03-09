# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

Топология:

![1](https://user-images.githubusercontent.com/99095235/157195380-97548005-5872-4271-8e90-7ede93131527.png)

Таблица адресации:  

![2](https://user-images.githubusercontent.com/99095235/157195625-feab4a47-5d76-4c20-89eb-2ea3f07f8117.png)

Цели: 

```
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
```
___________________________________________________________________________________________________________________________________________________________________________________

Настройка базовыx параметров коммутаторов S1,S2 и S3

S1
```
Switch>en
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#enable password class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#loggin synchronous
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#banner motd "unauthorized access is prohibited"
S1(config)#int vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no sh
S1(config-if)#exit
S1(config)#do wr mem
S1(config)#ex
```
S2
```
Switch>en
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S2
S2(config)#enable password class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#loggin synchronous
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#banner motd "unauthorized access is prohibited"
S2(config)#int vlan 1
S2(config-if)#ip address 192.168.1.2 255.255.255.0
S2(config-if)#no sh
S2(config-if)#exit
S2(config)#do wr mem
```
S3
```
Switch>en
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S3
S3(config)#enable password class
S3(config)#line console 0
S3(config-line)#password cisco
S3(config-line)#loggin synchronous
S3(config-line)#login
S3(config-line)#exit
S3(config)#line vty 0 4
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#banner motd "unauthorized access is prohibited"
S3(config)#int vlan 1
S2(config-if)#ip address 192.168.1.3 255.255.255.0
S2(config-if)#no sh
S2(config-if)#exit
S2(config)#do wr mem
```

Проверка связи между коммутаторами 
```
S1:

S1#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms

S2:

S2#ping 192.168.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms

S3:

S3#ping 192.168.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S3#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```
show spanning-tree:
```
S1:

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.1000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/2               Desg FWD 100       128.3    Shr


S2:

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    Shr
Et0/2               Desg FWD 100       128.3    Shr

S3:

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr
Et0/2               Root FWD 100       128.3    Shr
```
Выбор root switch:

Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. 
Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. 
Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.
```
S1 - Priority 32769, Address aabb.cc00.1000 - стал ROOT SWITCH
S2 - Priority 32769, Address aabb.cc00.2000
S3 - Priority 32769, Address aabb.cc00.3000
```
Выбор путей spanning-tree

Алгоритм протокола spanning-tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. 
Порт с более низкой стоимостью пути является предпочтительным. 
Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными.
```
S1 - root, Et0/0 и Et0/2 port desg  
S2 - Et0/0 port root, Et0/2 port desg
S3 - Et0/0 port altn, Et0/2 port root
```
![3](https://user-images.githubusercontent.com/99095235/157225020-9f68ec99-4afb-4fdc-a7f0-af48a06b6f2c.png)

Наблюдение за процессом выбора протоколом STP после включения резервных каналов

Cводка по spanning-tree :

```
S1:
nterface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et0/2               Desg FWD 100       128.3    Shr
Et0/3               Desg FWD 100       128.4    Shr

S2:
nterface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    Shr
Et0/1               Altn BLK 100       128.2    Shr
Et0/2               Desg FWD 100       128.3    Shr
Et0/3               Desg FWD 100       128.4    Shr

S3:
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr
Et0/1               Altn BLK 100       128.2    Shr
Et0/2               Root FWD 100       128.3    Shr
Et0/3               Altn BLK 100       128.4    Shr
```
![4](https://user-images.githubusercontent.com/99095235/157244014-37b4547d-5e51-4ed0-b3ee-087dfadc22ea.png)

Измение стоимости пути и влияние на маршрут

S3:
```
S3(config)#int e0/3
S3(config-if)#spanning-tree cost 19
```
![5](https://user-images.githubusercontent.com/99095235/157246056-764f12d8-62d7-4f82-93ab-2e658c65be92.png)

Ранее был root port - e0/2 т.к он являлся в приоритетней нежели e0/3 при значении cost 100.
После изменения стоимости e0/3 на 19, приоритет портов ушел и перешел на приоритет стоимости(cost) и пошло перестроение STA

