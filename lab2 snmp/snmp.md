![](https://github.com/Ketho/ADVSW/blob/master/lab2%20snmp/snmp.png)

## R1 - 1841
```lua
en
conf t
host R1
no ip domain-lookup
int s0/1/0
    ip addr 192.16.1.1 255.255.255.252
    no sh
int fa0/0
    ip addr 192.168.1.1 255.255.255.0
    no sh
router ospf 1
    router-id 1.1.1.1
    network 192.16.1.0  0.0.0.3 area 0
    network 192.168.1.0 0.0.0.3 area 0

!! snmp-server
snmp-server community banaan

!! netflow
int fa0/0
    ip flow ingress
    ip flow egress
    ex
ip flow-export destination 192.168.1.2 9996
ip flow-export version 9
end
```

## R2 - 1841
```lua
en
conf t
host R2
no ip domain-lookup
int s0/1/0
    ip addr 192.16.1.2 255.255.255.252
    no sh
int fa0/0
    ip addr 192.168.2.1 255.255.255.0
    no sh
router ospf 1
    router-id 2.2.2.2
    network 192.16.1.0  0.0.0.3 area 0
    network 192.168.2.0 0.0.0.3 area 0
end
```

## already configured
PC1:
- IP: `192.168.1.2`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.1.1`

PC2:
- IP: `192.168.2.2`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.2.1`
