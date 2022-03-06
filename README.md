# Configure Router-on-a-Stick Inter-VLAN Routing
map

![map](https://user-images.githubusercontent.com/99095235/156936532-dd59f9a1-2b8c-47b7-bf88-fe912ce69def.png)

ip address table

![ATable](https://user-images.githubusercontent.com/99095235/156936538-32eca4d2-b844-4612-9dac-5c1bbe7e717a.png)

vlan table

![TVlan](https://user-images.githubusercontent.com/99095235/156936556-0043a2b5-fec4-42c9-8e93-a87461f15696.png)

task

1. Создание сети и настройка основных параметров устройства
2. Создание VLAN и назначение портов коммутатора
3. Настройка магистрали 802.1Q между коммутаторами
4. Настройка маршрутизации между VLAN на маршрутизаторе
5. Проверка работы маршрутизации между VLAN

___________________________________________________________________________________________________________________________________________________________________________________

Базовая настройка R1 

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





