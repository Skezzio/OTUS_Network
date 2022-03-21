# Lab - Implement DHCPv4
### Топология:

![image](https://user-images.githubusercontent.com/99095235/159233203-911522cc-a970-446a-8ba7-f148d019d793.png)

### Таблица адресации:

![image](https://user-images.githubusercontent.com/99095235/159233284-829b3428-a17a-4fe7-9250-75e5c72d24b1.png)

### Таблица VLAN:

![image](https://user-images.githubusercontent.com/99095235/159233488-7e19f531-74ac-4079-85d2-f6108275675a.png)


#### Цели:
1. Создание сети и настройка основных параметров устройства
2. Настройка и проверка двух серверов DHCPv4 на маршрутизаторе R1
3. Настройка и проверка ретрансляции DHCP на маршрутизаторе R2
___________________________________________________________________

#### Настройка основных параметров для R1/R2

R1:
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
R1#clock set 10:22:00 21 MARCH 2022
R1#wr mem
```
R2:
```
Router>en
Router#conf t
Router(config)#hostname R2
R2(config)#no ip domain-lookup
R2(config)#enable password class
R2(config)#line console 0
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 4
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption
R2(config)#banner motd "unauthorized access is prohibited"
R2(config)#exit
R2#clock set 10:24:00 21 MARCH 2022
R2#wr mem
```
#### Настройка маршрутизации между VLAN на R1
```
R1(config)#int e0/1
R1(config-if)#no sh
R1(config-if)#exit
R1(config)#int e0/1.100
R1(config-subif)#ip ad
R1(config-subif)#ip ad
R1(config-subif)#ip add
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#description Clients
R1(config-subif)#exit
R1(config)#int e0/1.200
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip address 192.168.2.1 255.255.255.224
R1(config-subif)#description Management
R1(config-subif)#exit
R1(config)#int e0/1.1000
R1(config-subif)#encapsulation dot1Q 1000
R1(config-subif)#description Native
R1(config-subif)#exit
R1(config)#do sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                unassigned      YES unset  administratively down down
Ethernet0/1                unassigned      YES unset  up                    up
Ethernet0/1.100            192.168.1.1     YES manual up                    up
Ethernet0/1.200            192.168.2.1     YES manual up                    up
Ethernet0/1.1000           unassigned      YES unset  up                    up
Ethernet0/2                unassigned      YES unset  administratively down down
Ethernet0/3                unassigned      YES unset  administratively down down
```
#### Настройка e0/0-1 на R2, и настройка статической маршрутизации для обоих маршрутизаторов.

R2:
```
R2(config)#int e0/1
R2(config-if)#ip address 192.168.3.1 255.255.255.240
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#int e0/0
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no sh
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#wr mem
```
R1:
```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
R1(config)#exit
R1#ping 192.168.3.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R1#wr mem
```
#### Настройка основных параметров для каждого коммутатора.

S1:
```
Switch>en
Switch#conf t
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#enable password class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exi
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd "unauthorized access is prohibited"
S1(config)#exit
S1#clock set  10:50:00 21 MARCH 2022
S1#wr mem
```
S2
```
Switch>en
Switch#conf t
Switch(config)#hostname S2
S2(config)#no ip domain-lookup
S2(config)#enable password class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd "unauthorized access is prohibited"
S2(config)#exit
S2#clock set 10:53:00 21 MARCH 2022
S2#wr mem
```
#### Создание VLAN на S1/S2.

S1:
```
S1#conf t
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200
S1(config-vlan)#name Managements
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#int vlan 200
S1(config-if)#ip address 192.168.2.2 255.255.255.224
S1(config-if)#no sh
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.2.1
S1(config)#int range 0/2-3
S1(config-if-range)#switchport mode access
S1(config-if-range)#sh
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#exit
```
S2:
```
S2#conf t
S2(config)#int vlan 1
S2(config-if)#ip address 192.168.3.2 255.255.255.240
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.3.1
S2(config)#exit
S2(config)#int range e0/2-3
S2(config-if-range)#switchport mode access
S2(config-if-range)#sh
S2(config-if-range)#exit
S2(config)#int e0/0
S2(config-if)#switchport mode access
S2(config-if)#switchport access vlan 1
S2(config-if)#exit
```

#### Настройка e0/0-1 на S1.
```
S1(config)#int e0/0
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 100
S1(config-if)#exit
S1(config)#int e0/1
S1(config-if)#switchport trunk encapsulation dot1q
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#do wr mem
```
#### Настройка двух серверов DHCPv4 на R1
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp excluded-address 192.168.3.1 192.168.3.5
R1(config)#ip dhcp pool pool1
R1(dhcp-config)#network 192.168.1.0
R1(dhcp-config)#domain-name ccna-lab.co
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#exit
R1(config)#ip dhcp pool pool2
R1(dhcp-config)#network 192.168.3.0
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#default-router 192.168.3.1
R1(dhcp-config)#exit
R1(config)#wr mem
```
Попытка получить IP-адрес от DHCP на ПК-A
```
VPCS> dhcp
IP 192.168.1.6/24 GW 192.168.1.1
VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 192.168.1.6/24
GATEWAY     : 192.168.1.1
DNS         :
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 217779, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:05
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=0.411 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=0.599 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=0.526 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=0.604 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=0.635 ms

```
#### Настройка DHCP-helper-address на R2.
```
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#exit
R2(config)#wr mem
```
Попытка получить IP-адрес от DHCP на ПК-B
```
VPCS> dhcp
IP 192.168.3.6/24 GW 192.168.3.1
VPCS> sh ip

NAME        : VPCS[1]
IP/MASK     : 192.168.3.6/24
GATEWAY     : 192.168.3.1
DNS         :
DHCP SERVER : 10.0.0.1
DHCP LEASE  : 217649, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:06
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS> ping 10.0.0.1

84 bytes from 10.0.0.1 icmp_seq=1 ttl=254 time=0.457 ms
84 bytes from 10.0.0.1 icmp_seq=2 ttl=254 time=0.707 ms
84 bytes from 10.0.0.1 icmp_seq=3 ttl=254 time=0.775 ms
84 bytes from 10.0.0.1 icmp_seq=4 ttl=254 time=0.776 ms
84 bytes from 10.0.0.1 icmp_seq=5 ttl=254 time=0.619 ms

```
