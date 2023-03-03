![](https://github.com/Ketho/ADVSW/blob/master/lab3%20stp/stp.png)

- MLS2 can be used as redundant switch for inter-vlan routing by changing the gateway in:
    - PC10 to 192.168.10.2 
    - PC20 to 192.168.20.2
- Cannot config vlans separately on each MLS like in lab 1, would need layer 3 routing between MLS1 and MLS2.

## MLS1 - 3560-24PS
```lua
en
conf t
host MLS1
no ip domain-lookup
ip routing

!! trunks to MLS1, MLS2
int range fa0/2-3
    switchport trunk encapsulation dot1q
    switchport mode trunk

!! inter-vlan routing
vlan 10
int vlan 10
    ip addr 192.168.10.1 255.255.255.0
vlan 20
int vlan 20
    ip addr 192.168.20.1 255.255.255.0

spanning-tree mode rapid-pvst
spanning-tree vlan 10 root primary
end
copy running-config startup-config
```

## MLS2 - 3560-24PS
```lua
en
conf t
host MLS2
no ip domain-lookup
ip routing

!! trunks to MLS1, MLS2
int range fa0/2-3
    switchport trunk encapsulation dot1q
    switchport mode trunk

!! inter-vlan routing
vlan 10
int vlan 10
    ip addr 192.168.10.2 255.255.255.0
vlan 20
int vlan 20
    ip addr 192.168.20.2 255.255.255.0

spanning-tree mode rapid-pvst
spanning-tree vlan 20 root primary
end
copy running-config startup-config
```

## S1 - 2960-24TT IOS15
```lua
en
conf t
host S1
no ip domain-lookup

!! only allow PC10
int fa0/1
    switchport mode access
    switchport access vlan 10
    switchport port-security
    switchport port-security mac-address 000B.BE71.B8BB

int range fa0/2-3
    switchport mode trunk

!! prevent loop from rogue switch
int fa0/4
    spanning-tree bpduguard enable

spanning-tree mode rapid-pvst
spanning-tree portfast default
end
copy running-config startup-config
```

## S2 - 2960-24TT IOS15
```lua
en
conf t
host S2
no ip domain-lookup

int fa0/1
    switchport access vlan 20
int range fa0/2-3
    switchport mode trunk
spanning-tree mode rapid-pvst
spanning-tree portfast default
end
copy running-config startup-config
```

## hosts
PC10:
- IP: `192.168.10.10`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.10.1`

PC20:
- IP: `192.168.20.10`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.20.1`

## questions
- Q3. Alle switches dienen rpvst te draaien (controleer met "show spanning-tree" let op, het wordt rstp genoemd, is dit juist?)

Yes, since Rapid PVST+ is Cisco's enhancement of RSTP.

![](https://github.com/Ketho/ADVSW/blob/master/lab3%20stp/img1.png)

- Q4. Do "show spanning-tree interface fa0/1". Verklaar de entries in de tabel die voorkomen.
```lua
S1#sh sp int f0/1
Vlan             Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
VLAN0001         Desg BLK 19        128.1     P2p
VLAN0010         Desg FWD 19        128.1     P2p
```

- Q6. Configureer BPDUGuard op interface 0/4 van S1, test de werking met een Rogue switch, de poort dient te gaan
in de err-diabled state (check met "show interfaces status").
```lua
S1#sh int status
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    10         auto    auto  10/100BaseTX
Fa0/2                        connected    trunk      auto    auto  10/100BaseTX
Fa0/3                        connected    trunk      auto    auto  10/100BaseTX
Fa0/4                        err-disabled 1          auto    auto  10/100BaseTX
```

- Q7. Zorg ervoor dat als er een andere PC dan PC10 op Fa0/1 van S1 aangesloten worden dat de poort shutdown gaat.
Controleer met "show port-security" 

https://networklessons.com/switching/how-to-configure-port-security-on-cisco-switch

![](https://github.com/Ketho/ADVSW/blob/master/lab3%20stp/img2.png)
