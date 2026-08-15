! ############################################################
! CONFIGURacion R-SD (ROUTER PRINCIPAL - SANTO DOMINGO)
! ############################################################
enable
configure terminal
hostname R-SD

service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption

! --- 1. Credenciales y Acceso Remoto Seguro (SSH) ---
enable secret AEGIS-2026
username admin privilege 15 secret AEGIS-2026
no ip domain-lookup
ip domain-name aegis.com.do
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 2
crypto key generate rsa modulus 2048

! --- 2. PARCHE: Confianza para Bug DHCP en PNETLab ---
ip dhcp relay information trust-all

! --- 3. Exclusión de IPs (Gateways HSRP) ---
ip dhcp excluded-address 10.0.16.193 10.0.16.200
ip dhcp excluded-address 10.0.17.49 10.0.17.52
ip dhcp excluded-address 10.0.16.225 10.0.16.230
ip dhcp excluded-address 10.0.17.1 10.0.17.5
ip dhcp excluded-address 10.0.15.1 10.0.15.10
ip dhcp excluded-address 10.0.13.1 10.0.13.10
ip dhcp excluded-address 10.0.16.1 10.0.16.10    

! --- 4. DHCP Pools (Basado en el VLSM de Santo Domingo) ---
ip dhcp pool VLAN10_DirGeneral
 network 10.0.16.192 255.255.255.224
 default-router 10.0.16.193
 dns-server 10.0.10.10
 lease 7

ip dhcp pool VLAN20_RRHH
 network 10.0.17.48 255.255.255.240
 default-router 10.0.17.49
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN30_Cumplimiento
 network 10.0.16.224 255.255.255.224
 default-router 10.0.16.225
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN40_Soporte
 network 10.0.17.0 255.255.255.224
 default-router 10.0.17.1
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN50_Finanzas
 network 10.0.15.0 255.255.255.128
 default-router 10.0.15.1
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN60_Ventas
 network 10.0.13.0 255.255.255.0
 default-router 10.0.13.1
dns-server 8.8.8.8 8.8.4.4

ip dhcp pool VLAN70_Marketing
 network 10.0.16.0 255.255.255.128
 default-router 10.0.16.1
dns-server 8.8.8.8 8.8.4.4

! --- 5. ISAKMP / IPsec para el DMVPN (esto va ADEMÁS en el hub, ver sección 2) ---
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

! --- 5. Interfaz VPN (DMVPN HUB) ---
interface Tunnel1
 description TUNNEL HUB SANTO DOMINGO
 ip address 10.1.100.1 255.255.255.0
 ip mtu 1400
 tunnel source Ethernet0/0
 tunnel mode gre multipoint
 ip nhrp network-id 2026
 ip nhrp map multicast dynamic
 ip nhrp authentication AEGIS
 ip ospf network point-to-multipoint
 ip ospf 1 area 0
 tunnel protection ipsec profile AEGIS-PROFILE
 no shutdown

! --- 6. Configuración de Interfaces Físicas ---
interface Ethernet0/2
 description Conexion a Multicapa SWM-1
 ip address 10.255.255.1 255.255.255.252
 ip nat inside
 ip ospf 1 area 0
 duplex full
 no shutdown

interface Ethernet0/1
 description Conexion a Multicapa SWM-2
 ip address 10.255.255.5 255.255.255.252
 ip nat inside
 ip ospf 1 area 0
 duplex full
 no shutdown

interface Ethernet0/0
 description Enlace WAN hacia el ISP
 ip address 1.0.0.2 255.255.255.252
 ip nat outside
 no shutdown

! --- 7. Enrutamiento OSPF ---
ip route 0.0.0.0 0.0.0.0 1.0.0.1

router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Tunnel1
 network 10.0.0.0 0.255.255.255 area 10
 network 10.1.100.0 0.0.0.255 area 0
 default-information originate

! --- 8. Listas de Acceso y NAT ---
ip access-list extended VPN-TRAFFIC
 remark Trafico que debe ir por la VPN (No NATear)
 permit ip 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255

ip access-list extended NAT-INTERNAS
 remark Trafico hacia Internet
 deny ip 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255
 permit ip 10.0.0.0 0.255.255.255 any

ip nat inside source list NAT-INTERNAS interface Ethernet0/0 overload

! --- 9. Aseguramiento de Lineas y Banner Institucional ---
line con 0
 logging synchronous
 login local
 exec-timeout 5 0

line vty 0 4
 login local
 transport input ssh
 exec-timeout 5 0

banner motd #
*********************************************************************

AEGIS SOLUTIONS - SEDE SANTO DOMINGO
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
**************************************************************************
#
no ip http server
no ip http secure-server
end
write memory
