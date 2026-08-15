
! ############################################################
! CONFIGURACION SW-3 - DISTRIBUCION - LA ROMANA
! ############################################################
enable
configure terminal

hostname SW-3
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
 name CONSULTORIA_TI
vlan 230
 name INFRAESTRUCTURA
vlan 99
 name NATIVA
exit

spanning-tree mode rapid-pvst
spanning-tree extend system-id
spanning-tree vlan 210,220,230,99 priority 24576

! --- Trunk hacia el Router (e0/0) ---
interface Ethernet0/0
 description UPLINK-ROUTER-ROMANA
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 210,220,230,99
 switchport trunk native vlan 99
 switchport mode trunk
 switchport nonegotiate
 duplex auto
 no shutdown

! --- EtherChannel hacia SW-9 (e0/1 - e0/2) ---
interface range Ethernet0/1 - 2
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

banner motd #
**************************************************************************
AEGIS-2026
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
*************************************************************************
#
end
write memory

