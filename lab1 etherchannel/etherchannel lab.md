## R1 - 2911
```lua
en
conf t
host R1
no ip domain-lookup
int g0/0/0
    ip addr 192.168.0.5 255.255.255.252
    no sh
int g0/0/1
    ip addr 192.168.0.9 255.255.255.252
    no sh
int lo0
    ip addr 10.0.0.1 255.255.255.0
router ospf 1
    router-id 1.1.1.1
    network 10.0.0.1     0.0.0.255 area 0
    network 192.168.0.4  0.0.0.3   area 0
    network 192.168.0.8  0.0.0.3   area 0
end
```

## DLS1 - 3650-24PS
```lua
en
conf t
host DLS1
no ip domain-lookup

!! routed port to R1
int g1/0/7
    no switchport
    ip addr 192.168.0.6  255.255.255.252
    no sh

!! L3 portchannel to DLS2
int range g1/0/3-4
    no switchport
    channel-group 1 mode on
int port-channel 1
    no switchport
    ip addr 192.168.0.1  255.255.255.252

!! portchannel to ALS1
int range g1/0/1-2
    channel-group 2 mode active
int port-channel 2
    switchport mode trunk

!! portchannel to ALS2
int range g1/0/5-6
    channel-group 3 mode active
int port-channel 3
    switchport mode trunk

!! inter-vlan routing
vlan 10
int vlan 10
    ip addr 192.168.10.1 255.255.255.0
ip routing

router ospf 1
    router-id 2.2.2.2
    network 192.168.0.0  0.0.0.3   area 0
    network 192.168.0.4  0.0.0.3   area 0
    network 192.168.10.0 0.0.0.255 area 0
end
```

## DLS2 - 3650-24PS
```lua
en
conf t
host DLS2
no ip domain-lookup

!! routed port to R1
int g1/0/7
    no switchport
    ip addr 192.168.0.10 255.255.255.252
    no sh

!! L3 portchannel to DLS1
int range g1/0/3-4
    no switchport
    channel-group 1 mode on
int port-channel 1
    no switchport
    ip addr 192.168.0.2 255.255.255.252

!! portchannel to ALS2
int range g1/0/1-2
    channel-group 2 mode active
int port-channel 2
    switchport mode trunk

!! portchannel to ALS1
int range g1/0/5-6
    channel-group 3 mode active
int port-channel 3
    switchport mode trunk

!! inter-vlan routing
vlan 20
int vlan 20
    ip addr 192.168.20.1 255.255.255.0
ip routing

router ospf 1
    router-id 3.3.3.3
    network 192.168.0.8  0.0.0.3   area 0
    network 192.168.0.0  0.0.0.3   area 0
    network 192.168.20.0 0.0.0.255 area 0
end
```

## ALS1 - 2960-24TT
```lua
en
conf t
host ALS1
no ip domain-lookup

!! portchannel to DLS1
int range fa0/1-2
    channel-group 1 mode active
int port-channel 1
    switchport mode trunk

!! portchannel to DLS2
int range fa0/5-6
    channel-group 2 mode active
int port-channel 2
    switchport mode trunk

int fa0/7
    switchport access vlan 10
end
```

## ALS2 - 2960-24TT
```lua
en
conf t
host ALS2
no ip domain-lookup

!! portchannel to DLS2
int range fa0/1-2
    channel-group 1 mode active
int port-channel 1
    switchport mode trunk

!! portchannel to DLS1
int range fa0/5-6
    channel-group 2 mode active
int port-channel 2
    switchport mode trunk

int fa0/7
    switchport access vlan 20
end
```
