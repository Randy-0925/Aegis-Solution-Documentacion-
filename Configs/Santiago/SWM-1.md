
enable
configure terminal
hostname SW-1
no ip domain-lookup

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

! --- EtherChannel hacia SW-2 (LACP) ---
interface range Ethernet0/1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 99,110,130,140,199
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 99,110,130,140,199

! --- Enlace hacia R-SANTIAGO (solo necesita la VLAN de transito) ---
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 99
 no shutdown

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
