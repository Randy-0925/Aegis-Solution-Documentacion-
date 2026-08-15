! ############################################################
! CONFIGURACION SWM-1 - MULTILAYER PRINCIPAL - SANTO DOMINGO
! ############################################################
enable
configure terminal

hostname SWM-1
ip routing
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption

! --- Credenciales ---
enable secret AEGIS-2026
username admin privilege 15 secret AEGIS-2026

no ip domain-lookup
ip domain-name aegis.com.do
ip ssh version 2
ip ssh time-out 60
crypto key generate rsa modulus 2048

! --- Creacion de VLANs ---
vlan 10
 name DIR_GENERAL
vlan 20
 name RRHH
vlan 30
 name CUMPLIMIENTO
vlan 40
 name SOPORTE_TEC
vlan 50
 name FINANZAS
vlan 60
 name VENTAS
vlan 70
 name MARKETING
vlan 99
 name NATIVA
vlan 500
 name TRONCALES_MGMT
exit

! --- Parche de Compatibilidad DHCP (IOL/PNETLab) ---
no ip dhcp snooping
no ip dhcp snooping information option

! --- Spanning Tree (SWM-1 es el Root Bridge Principal) ---
spanning-tree mode rapid-pvst
spanning-tree extend system-id
spanning-tree vlan 10,20,30,40,50,60,70,99,500 priority 24576

! --- EtherChannel hacia SWM-2 (e0/1, e0/2, e0/3) ---
interface range Ethernet0/1 - 3
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,40,50,60,70,99,500
 switchport trunk native vlan 99
 switchport mode trunk
 switchport nonegotiate
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,40,50,60,70,99,500
 switchport trunk native vlan 99
 switchport mode trunk
 switchport nonegotiate
 no shutdown

! --- Enlace Enrutado hacia R-SD (e0/0) ---
interface Ethernet0/0
 description Conexion a Router R-SD
 no switchport
 ip address 10.255.255.2 255.255.255.252
 duplex full
 ip ospf 1 area 0
 no shutdown

! --- Enlaces Troncales hacia Switches de Acceso (e1/0 - e1/2) ---
interface range Ethernet1/0 - 2
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30,40,50,60,70,99,500
 switchport trunk native vlan 99
 switchport mode trunk
 switchport nonegotiate
 no shutdown

! --- Interfaces Virtuales (SVIs) y HSRP (Prioridad 120) ---
interface Vlan10
 description Gateway DIR_GENERAL
 ip address 10.0.16.194 255.255.255.224
 ip helper-address 10.255.255.1
 standby version 2
 standby 10 ip 10.0.16.193
 standby 10 priority 120
 standby 10 preempt
 no shutdown

interface Vlan20
 description Gateway RRHH
 ip address 10.0.17.50 255.255.255.240
 ip helper-address 10.255.255.1
 standby version 2
 standby 20 ip 10.0.17.49
 standby 20 priority 120
 standby 20 preempt
 no shutdown

interface Vlan30
 description Gateway CUMPLIMIENTO
 ip address 10.0.16.226 255.255.255.224
 ip helper-address 10.255.255.1
 standby version 2
 standby 30 ip 10.0.16.225
 standby 30 priority 120
 standby 30 preempt
 no shutdown

interface Vlan40
 description Gateway SOPORTE_TEC
 ip address 10.0.17.2 255.255.255.224
 ip helper-address 10.255.255.1
 standby version 2
 standby 40 ip 10.0.17.1
 standby 40 priority 120
 standby 40 preempt
 no shutdown

interface Vlan50
 description Gateway FINANZAS
 ip address 10.0.15.2 255.255.255.128
 ip helper-address 10.255.255.1
 standby version 2
 standby 50 ip 10.0.15.1
 standby 50 priority 120
 standby 50 preempt
 no shutdown

interface Vlan60
 description Gateway VENTAS
 ip address 10.0.13.2 255.255.255.0
 ip helper-address 10.255.255.1
 standby version 2
 standby 60 ip 10.0.13.1
 standby 60 priority 120
 standby 60 preempt
 no shutdown

interface Vlan70
 description Gateway MARKETING
 ip address 10.0.16.2 255.255.255.128
 ip helper-address 10.255.255.1
 standby version 2
 standby 70 ip 10.0.16.1
 standby 70 priority 120
 standby 70 preempt
 no shutdown

! --- OSPF ---
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface Ethernet0/0
 network 10.0.0.0 0.255.255.255 area 10

! --- Acceso y Seguridad ---
line con 0
 logging synchronous
 login local
 exec-timeout 5 0
line vty 0 4
 login local
 transport input ssh
 exec-timeout 5 0

banner motd #
*******************************************************
AEGIS SOLUTIONS - SEDE SANTO DOMINGO
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
*******************************************************
#
end
write memory
