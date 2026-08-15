enable
configure terminal
hostname R-PUERTOPLATA

service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption

! --- Credenciales y SSH ---
enable secret AEGIS-2026
username admin privilege 15 secret AEGIS-2026
no ip domain-lookup
ip domain-name aegis.com.do
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 2
crypto key generate rsa modulus 2048

! --- Parche DHCP relay (bug PNETLab) ---
ip dhcp relay information trust-all

! --- Exclusiones DHCP (gateways) ---
ip dhcp excluded-address 10.0.6.1
ip dhcp excluded-address 10.0.17.65
ip dhcp excluded-address 10.0.4.1
ip dhcp excluded-address 10.0.2.1
ip dhcp excluded-address 10.0.0.1

! --- Pools DHCP ---
ip dhcp pool VLAN310_DFIR
 network 10.0.6.0 255.255.254.0
 default-router 10.0.6.1
 dns-server 10.0.10.2 8.8.8.8
 lease 7

ip dhcp pool VLAN320_MALWARE
 network 10.0.17.64 255.255.255.240
 default-router 10.0.17.65
 dns-server 10.0.10.2 8.8.8.8
 lease 7

ip dhcp pool VLAN330_THREATINTEL
 network 10.0.4.0 255.255.254.0
 default-router 10.0.4.1
 dns-server 10.0.10.2 8.8.8.8
 lease 7

ip dhcp pool VLAN340_REDTEAM
 network 10.0.2.0 255.255.254.0
 default-router 10.0.2.1
 dns-server 10.0.10.2 8.8.8.8
 lease 7

ip dhcp pool VLAN350_SOC
 network 10.0.0.0 255.255.254.0
 default-router 10.0.0.1
 dns-server 10.0.10.2 8.8.8.8
 lease 7

! --- ISAKMP / IPsec (DMVPN spoke, mismos parametros que el resto de sedes) ---
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
 description TUNNEL SPOKE PUERTO PLATA
 ip address 10.1.100.4 255.255.255.0
 ip mtu 1400
 tunnel source Ethernet0/0
 tunnel mode gre multipoint
 ip nhrp network-id 2026
 ip nhrp map 10.1.100.1 1.0.0.2
 ip nhrp map multicast 1.0.0.2
 ip nhrp nhs 10.1.100.1
 ip nhrp authentication AEGIS
 ip ospf network point-to-multipoint
 ip ospf 1 area 0
 tunnel protection ipsec profile AEGIS-PROFILE
 no shutdown

! --- Interfaces físicas ---
interface Ethernet0/0
 description ENLACE WAN ISP
 ip address 1.0.0.14 255.255.255.252
 ip nat outside
 duplex auto
 no shutdown

interface Ethernet0/1
 description TRUNK HACIA SW-5
 no ip address
 duplex full
 no shutdown

interface Ethernet0/1.310
 encapsulation dot1Q 310
 ip address 10.0.6.1 255.255.254.0
 ip nat inside
 ip ospf 1 area 40

interface Ethernet0/1.320
 encapsulation dot1Q 320
 ip address 10.0.17.65 255.255.255.240
 ip nat inside
 ip ospf 1 area 40

interface Ethernet0/1.330
 encapsulation dot1Q 330
 ip address 10.0.4.1 255.255.254.0
 ip nat inside
 ip ospf 1 area 40

interface Ethernet0/1.340
 encapsulation dot1Q 340
 ip address 10.0.2.1 255.255.254.0
 ip nat inside
 ip ospf 1 area 40

interface Ethernet0/1.350
 encapsulation dot1Q 350
 ip address 10.0.0.1 255.255.254.0
 ip nat inside
 ip ospf 1 area 40

! --- Enrutamiento estático + OSPF (area 40) ---
ip route 0.0.0.0 0.0.0.0 1.0.0.13

router ospf 1
 router-id 5.5.5.5
 passive-interface default
 no passive-interface Ethernet0/1.310
 no passive-interface Ethernet0/1.320
 no passive-interface Ethernet0/1.330
 no passive-interface Ethernet0/1.340
 no passive-interface Ethernet0/1.350
 no passive-interface Tunnel1
 network 10.0.0.0 0.0.1.255 area 40
 network 10.0.2.0 0.0.1.255 area 40
 network 10.0.4.0 0.0.1.255 area 40
 network 10.0.6.0 0.0.1.255 area 40
 network 10.0.17.64 0.0.0.15 area 40
 network 10.1.100.0 0.0.0.255 area 0
 default-information originate

! --- ACL y NAT ---
ip access-list extended VPN-TRAFFIC
 remark Trafico que debe ir por la VPN (no NATear)
 permit ip 10.0.0.0 0.0.1.255 10.0.0.0 0.255.255.255
 permit ip 10.0.2.0 0.0.1.255 10.0.0.0 0.255.255.255
 permit ip 10.0.4.0 0.0.1.255 10.0.0.0 0.255.255.255
 permit ip 10.0.6.0 0.0.1.255 10.0.0.0 0.255.255.255
 permit ip 10.0.17.64 0.0.0.15 10.0.0.0 0.255.255.255

ip access-list extended NAT-INTERNAS
 remark Trafico hacia Internet
 deny ip 10.0.0.0 0.0.1.255 10.0.0.0 0.255.255.255
 deny ip 10.0.2.0 0.0.1.255 10.0.0.0 0.255.255.255
 deny ip 10.0.4.0 0.0.1.255 10.0.0.0 0.255.255.255
 deny ip 10.0.6.0 0.0.1.255 10.0.0.0 0.255.255.255
 deny ip 10.0.17.64 0.0.0.15 10.0.0.0 0.255.255.255
 permit ip 10.0.0.0 0.0.1.255 any
 permit ip 10.0.2.0 0.0.1.255 any
 permit ip 10.0.4.0 0.0.1.255 any
 permit ip 10.0.6.0 0.0.1.255 any
 permit ip 10.0.17.64 0.0.0.15 any

ip nat inside source list NAT-INTERNAS interface Ethernet0/0 overload

! --- Líneas y banner ---
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
AEGIS SOLUTIONS - SEDE PUERTO PLATA
ADVERTENCIA: ACCESO RESTRINGIDO.
Toda actividad en este dispositivo esta siendo monitorizada.
*********************************************************************
#
no ip http server
no ip http secure-server
end
write memory
