! ############################################################
! CONFIGURACION ROUTER - LA ROMANA (SPOKE DMVPN)
! ############################################################
enable
configure terminal
hostname ROMANA

service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption

enable secret AEGIS-2026
username admin privilege 15 secret AEGIS-2026
no ip domain-lookup
ip domain-name aegis.com.do
ip ssh version 2
crypto key generate rsa modulus 2048

! --- Parche para Bug DHCP en PNETLab ---
ip dhcp relay information trust-all

! --- Exclusión de IPs (Gateways según VLSM) ---
ip dhcp excluded-address 10.0.8.1
ip dhcp excluded-address 10.0.15.129
ip dhcp excluded-address 10.0.16.129

! --- DHCP Pools (VLSM La Romana) ---
ip dhcp pool VLAN220_CONSULTORIA
 network 10.0.8.0 255.255.254.0
 default-router 10.0.8.1
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN230_INFRAESTRUCTURA
 network 10.0.15.128 255.255.255.128
 default-router 10.0.15.129
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN210_DIR_REGIONAL
 network 10.0.16.128 255.255.255.192
 default-router 10.0.16.129
dns-server 8.8.8.8 8.8.4.4

! --- Parche DHCP relay (bug PNETLab) ---
ip dhcp relay information trust-all

! --- ISAKMP / IPsec para el DMVPN ---
crypto isakmp policy 10
 encr aes 256
 hash sha256
 authentication pre-share
 group 14
 lifetime 3600
crypto isakmp key AEGIS-2026-VPN address 0.0.0.0 0.0.0.0

crypto ipsec transform-set AEGIS-TS esp-aes 256 esp-sha256-hmac
 mode transport

crypto ipsec profile AEGIS-PROFILE
 set transform-set AEGIS-TS

! --- Interfaz VPN (DMVPN Spoke) ---
interface Tunnel1
 description TUNNEL SPOKE LA ROMANA
 ip address 10.1.100.3 255.255.255.0
 ip mtu 1400
 tunnel source Ethernet0/0
 tunnel mode gre multipoint
 ip nhrp network-id 2026
 ! Apuntando a la IP publica y privada del Hub SD
 ip nhrp map 10.1.100.1 1.0.0.2
 ip nhrp map multicast 1.0.0.2
 ip nhrp nhs 10.1.100.1
 ip nhrp authentication AEGIS
 ip ospf network point-to-multipoint
 ip ospf 1 area 0
 tunnel protection ipsec profile AEGIS-PROFILE
 no shutdown

! --- Enrutamiento Estático (Salida a Internet) ---
ip route 0.0.0.0 0.0.0.0 1.0.0.9

! --- Interfaz WAN ---
interface Ethernet0/0
 description ENLACE WAN ISP
 ip address 1.0.0.10 255.255.255.252
 ip nat outside
 no shutdown

! --- Interfaz LAN Física ---
interface Ethernet0/1
 description TRUNK HACIA SW-3
 no ip address
 duplex full
 no shutdown

! --- Subinterfaces (Router-on-a-Stick) ---
interface Ethernet0/1.220
 encapsulation dot1Q 220
 ip address 10.0.8.1 255.255.254.0
 ip nat inside
 ip ospf 1 area 30

interface Ethernet0/1.230
 encapsulation dot1Q 230
 ip address 10.0.15.129 255.255.255.128
 ip nat inside
 ip ospf 1 area 30

interface Ethernet0/1.210
 encapsulation dot1Q 210
 ip address 10.0.16.129 255.255.255.192
 ip nat inside
 ip ospf 1 area 30

! --- OSPF Multi-Área ---
router ospf 1
 router-id 4.4.4.4
 passive-interface default
 no passive-interface Tunnel1
 network 10.1.100.0 0.0.0.255 area 0

! --- Listas de Acceso (VPN y NAT con el VLSM corregido) ---
ip access-list extended VPN-TRAFFIC
 permit ip 10.0.8.0 0.0.1.255 10.0.0.0 0.255.255.255
 permit ip 10.0.15.128 0.0.0.127 10.0.0.0 0.255.255.255
 permit ip 10.0.16.128 0.0.0.63 10.0.0.0 0.255.255.255

ip access-list extended NAT-INTERNAS
 deny ip 10.0.8.0 0.0.1.255 10.0.0.0 0.255.255.255
 deny ip 10.0.15.128 0.0.0.127 10.0.0.0 0.255.255.255
 deny ip 10.0.16.128 0.0.0.63 10.0.0.0 0.255.255.255
 permit ip 10.0.8.0 0.0.1.255 any
 permit ip 10.0.15.128 0.0.0.127 any
 permit ip 10.0.16.128 0.0.0.63 any

! --- NAT ---
ip nat inside source list NAT-INTERNAS interface Ethernet0/0 overload

! --- Aseguramiento de Lineas y Banner ---
line con 0
 logging synchronous
 login local
 exec-timeout 5 0

line vty 0 4
 login local
 transport input ssh
 exec-timeout 5 0

banner motd #
**************************************************************************
AEGIS-2026
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
*************************************************************************
#
no ip http server
no ip http secure-server
end
write memory
