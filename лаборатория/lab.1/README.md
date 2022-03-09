# Configure Router-on-a-Stick Inter-VLAN Routing
map

![map](https://user-images.githubusercontent.com/99095235/156936532-dd59f9a1-2b8c-47b7-bf88-fe912ce69def.png)

ip address table

![ATable](https://user-images.githubusercontent.com/99095235/156936538-32eca4d2-b844-4612-9dac-5c1bbe7e717a.png)

vlan table

![TVlan](https://user-images.githubusercontent.com/99095235/156936556-0043a2b5-fec4-42c9-8e93-a87461f15696.png)

task  
```
1. Создание сети и настройка основных параметров устройства
2. Создание VLAN и назначение портов коммутатора
3. Настройка магистрали 802.1Q между коммутаторами
4. Настройка маршрутизации между VLAN на маршрутизаторе
5. Проверка работы маршрутизации между VLAN'
```
___________________________________________________________________________________________________________________________________________________________________________________

Базовая настройка R1:
```
Router>en  
Router#conf t  
Router(config)#hostname R1  
R1(config)#no ip domain-lookup  
R1(config)#enable password class  
R1(config)#line console 0  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config-line)#exit  
R1(config)#line vty 0 4  
R1(config-line)#password cisco  
R1(config-line)#login  
R1(config-line)#exit  
R1(config)#service password-encryption  
R1(config)#banner motd "unauthorized access is prohibited"  
R1(config)#exit  
R1#clock set 20:00:00 6 MARCH 2022  
R1#wr
```
Преднастройка S1 и S2:

```
Switch>en  
Switch#conf t  
Switch(config)#hostname S1  
S1(config)#no ip domain-lookup  
S1(config)#enable password class  
S1(config)#line console 0  
S1(config-line)#password cisco  
S1(config-line)#login  
S1(config-line)#exit  
S1(config)#line vty 0 4  
S1(config-line)#password cisco  
S1(config-line)#login  
S1(config-line)#exit  
S1(config)#service password-encryption  
S1(config)#banner motd "unauthorized access is prohibited"  
S1(config)#exit  
S1#clock set 20:07:00 6 MARCH 2022  
S1#wr   
```
Настройка VLAN S1:

```
S1#conf t  
S1(config)#vlan 3  
S1(config-vlan)#name Managment  
S1(config-vlan)#vlan 4  
S1(config-vlan)#name Operation  
S1(config-vlan)#vlan 7  
S1(config-vlan)#name ParkingLoot  
S1(config-vlan)#vlan 8  
S1(config-vlan)#name Native  
S1(config-vlan)#exit  
S1(config)#int e0/1  
S1(config-if)#switchport mode access  
S1(config-if)#switchport access vlan 3  
S1(config-if)#exit  
S1(config-if)#no shutdown  
S1(config-if)#exit  
S1(config)#int range e0/2-3  
S1(config-if-range)#switchport trunk encapsulation dot1q  
S1(config-if-range)#switchport mode trunk  
S1(config-if-range)#switchport trunk allowed vlan 3,4,7  
S1(config-if-range)#switchport trunk native vlan 8  
S1(config-if-range)#exit  
S1(config-if)#int e0/0  
S1(config-if)#switchport mode access  
S1(config-if)#switchport access vlan 7  
S1(config-if)#no shutdown   
S1(config-if)#exit  
S1(config)#ip default-gateway 192.168.3.1  
S1(config)#int vlan 3  
S1(config-if)#ip address 192.168.3.11 255.255.255.0  
S1(config-if)#no sh  
S1(config-if)#do sh vl brief  

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active
3    Managment                        active    Et0/1
4    Operation                        active
7    ParkingLoot                      active    Et0/0
8    Native                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
  
S1(config-if)#do wr mem
```
Настройка VLAN S2:

```
S2#conf t  
S2(config)#vlan 3  
S2(config-vlan)#name Managment  
S2(config-vlan)#vlan 4  
S2(config-vlan)#name Operation  
S2(config-vlan)#vlan 7  
S2(config-vlan)#name ParkingLot  
S2(config-vlan)#vlan 8 
S2(config-vlan)#name Native  
S2(config-vlan)#int e0/3  
S2(config-if)#switchport trunk encapsulation dot1q  
S2(config-if)#switchport mode trunk  
S2(config-if)#switchport trunk allowed vlan 3,4,7  
S2(config-if)#switchport trunk native vlan 8  
S2(config-if)#no shutdown  
S2(config-if)#exit  
S2(config)#int e0/2  
S2(config-if)#switchport mode access  
S2(config-if)#switchport access vlan 4  
S2(config-if)#exit  
S2(config)#int range e0/0-1  
S2(config-if-range)#switchport mode access  
S2(config-if-range)#switchport access vlan 7  
S2(config-if-range)#shutdown  
S2(config-if-range)#exit  
S2(config)#ip default-gateway 192.168.4.1  
S2(config)#int vlan 3  
S2(config-if)#  
S2(config-if)#ip add  
S2(config-if)#ip address 192.168.3.12 255.255.255.0  
S2(config-if)#no sh  
S2(config-if)#do sh vl br  

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active
3    Managment                        active
4    Operation                        active    Et0/2
7    ParkingLot                       active    Et0/0, Et0/1
8    Native                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
S2(config-if)#do wr mem

```
Конфигурация R1:

```
R1#conf t  
R1(config)#int e0/0.3  
R1(config-subif)#encapsulation dot1Q 3  
R1(config-subif)#ip address 192.168.3.1 255.255.255.0  
R1(config-subif)#ex  
R1(config)#int e0/0.4  
R1(config-subif)#encapsulation dot1Q 4  
R1(config-subif)#ip address 192.168.4.1 255.255.255.0  
R1(config-subif)#ex  
R1(config)#int e0/0.8  
R1(config-subif)#ex  
R1(config)#int e0/0  
R1(config-if)#no sh  
R1(config-if)#do wr mem  
```
Проверка маршрутизации между VLAN

PC1

![3](https://user-images.githubusercontent.com/99095235/156939012-149487d0-5c7e-4895-967b-f6210c495a15.png)

PC2

![4](https://user-images.githubusercontent.com/99095235/156939073-1941cd8c-93df-47cd-943e-4e6f27ec60ac.png)







