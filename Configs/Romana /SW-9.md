! ############################################################
! CONFIGURACION SW-9 - ACCESO - LA ROMANA
! ############################################################
enable
configure terminal

hostname SW-9

service timestamps debug datetime msec

service timestamps log datetime msec

service password-encryption

username admin privilege 15 secret AEGIS-2026

no ip domain-lookup

ip domain-name aegis.com.do

ip ssh version 2

crypto key generate rsa modulus 2048


! --- VLANs ---

vlan 210

 name DIR_REGIONAL
 
vlan 220

 name CONSULTORIA_TICONF CO
 
vlan 230

 name INFRAESTRUCTURA
 
vlan 99

 name NATIVA


exit


spanning-tree mode rapid-pvst


! --- EtherChannel hacia SW-3 (e0/1 - e0/2) ---

interface range Ethernet0/1 - 2

 description UPLINK-SW-3
 
 switchport trunk encapsulation dot1q
 
 switchport trunk allowed vlan 210,220,230,99
 
 switchport trunk native vlan 99
 
 switchport mode trunk
 
 switchport nonegotiate
 
 channel-group 1 mode active
 
 no shutdown
 

interface Port-channel1

 switchport trunk encapsulation dot1q
 
 switchport trunk allowed vlan 210,220,230,99
 
 switchport trunk native vlan 99
 
 switchport mode trunk
 
 switchport nonegotiate
 
 no shutdown
 

! --- Puertos de Acceso (Port-Security) ---


! PC Direccion Regional

interface Ethernet1/1

 switchport access vlan 210
 
 switchport mode access
 
 switchport nonegotiate
 
 switchport port-security
 
 switchport port-security maximum 2
 
 switchport port-security violation restrict
 
 switchport port-security mac-address sticky
 
 spanning-tree portfast edge
 
 spanning-tree bpduguard enable
 
 no shutdown
 

! PC Consultoria TI

interface Ethernet1/2

 switchport access vlan 220
 
 switchport mode access
 
 switchport nonegotiate
 
 switchport port-security
 
 switchport port-security maximum 2
 
 switchport port-security violation restrict
 
 switchport port-security mac-address sticky
 
 spanning-tree portfast edge
 
 spanning-tree bpduguard enable
 
 no shutdown
 

! PC Ing. de Infraestructura

interface Ethernet1/3

 switchport access vlan 230
 
 switchport mode access
 
 switchport nonegotiate
 
 switchport port-security
 
 switchport port-security maximum 2
 
 switchport port-security violation restrict
 
 switchport port-security mac-address sticky
 
 spanning-tree portfast edge
 
 spanning-tree bpduguard enable
 
 no shutdown

banner motd #
**************************************************************************
AEGIS-2026
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
*************************************************************************
#
end
