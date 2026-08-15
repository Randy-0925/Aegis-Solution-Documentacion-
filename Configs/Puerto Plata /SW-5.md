
enable
configure terminal
hostname SW-5
no ip domain-lookup

vlan 310
 name DFIR
vlan 320
 name MALWARE
vlan 330
 name THREATINTEL
vlan 340
 name REDTEAM
vlan 350
 name SOC
exit

! --- Hacia el router ---
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 310,320,330,340,350
 no shutdown

! --- Hacia sw-31 (TH, Red Team, SOC) ---
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 310,320,330,340,350
 no shutdown

! --- Hacia sw-30 (DFIR, Malware) ---
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 310,320,330,340,350
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
