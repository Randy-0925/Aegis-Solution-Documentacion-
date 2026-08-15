enable
configure terminal
hostname SW-2
no ip domain-lookup
ip routing

vlan 99
 name TRANSITO_WAN
vlan 110
 name CENTRODEDATOS
vlan 130
 name VENTASCORP
vlan 140
 name ADMINISTRACION
vlan 199
 name SERVIDORES
exit

! --- EtherChannel hacia SW-1 (LACP) ---
interface range Ethernet0/0-1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 99,110,130,140,199
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 99,110,130,140,199

! --- Puertos de acceso ---
interface Ethernet0/2
 description Server Linux (DHCP-DNS-NFS-RADIUS-FTP)
 switchport mode access
 switchport access vlan 199
 no shutdown

interface Ethernet1/0
 description PC Centro de Datos
 switchport mode access
 switchport access vlan 110
 no shutdown

interface Ethernet1/1
 description PC Ventas
 switchport mode access
 switchport access vlan 130
 no shutdown

interface Ethernet1/3
 description PC Administracion
 switchport mode access
 switchport access vlan 140
 no shutdown

interface Ethernet0/3
 description PC Corporativa
 switchport mode access
 switchport access vlan 130
 no shutdown

! --- SVIs (gateways) + relay DHCP hacia el server ---
interface Vlan199
 ip address 10.0.10.1 255.255.254.0
 ip helper-address 10.0.10.2
 no shutdown

interface Vlan140
 ip address 10.0.12.1 255.255.255.0
 ip helper-address 10.0.10.2
 no shutdown

interface Vlan110
 ip address 10.0.14.1 255.255.255.0
 ip helper-address 10.0.10.2
 no shutdown

interface Vlan130
 ip address 10.0.17.33 255.255.255.240
 ip helper-address 10.0.10.2
 no shutdown

interface Vlan99
 description ENLACE L3 HACIA R-SANTIAGO
 ip address 10.0.19.2 255.255.255.252
 no shutdown

! --- OSPF (area 30) ---
router ospf 1
 router-id 3.3.3.3
 passive-interface default
 no passive-interface Vlan99
 network 10.0.10.0 0.0.1.255 area 30
 network 10.0.12.0 0.0.0.255 area 30
 network 10.0.14.0 0.0.0.255 area 30
 network 10.0.17.32 0.0.0.15 area 30
 network 10.0.19.0 0.0.0.3 area 30

enable secret AEGIS-2026
username admin privilege 15 secret AEGIS-2026
line con 0
 logging synchronous
 exec-timeout 5 0
line vty 0 4
 login local
 transport input ssh
 exec-timeout 5 0
end
write memory
