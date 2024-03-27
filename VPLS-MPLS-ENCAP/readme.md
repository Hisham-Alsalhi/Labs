

- VPLS Using MPLS encap between R2,R4,R5
- R2 and R4 link is shut , also R4 and R5 is shut.
- Tracing the traffic if we ping from R1 loopback 1.1.1.1 to R7 loopback 7.7.7.7
- From R1 
```
R1#show ip route 7.7.7.7
Routing entry for 7.7.7.7/32
  Known via "ospf 1", distance 110, metric 2, type intra area
  Last update from 10.0.0.7 on GigabitEthernet0/0, 00:04:06 ago
  Routing Descriptor Blocks:
  * 10.0.0.7, from 7.7.7.7, 00:04:06 ago, via GigabitEthernet0/0
      Route metric is 2, traffic share count is 1
R1#show ip arp | sec 10.0.0.7
Internet  10.0.0.7                4   5254.0000.002f  ARPA   GigabitEthernet0/0
```


- From R2 
```
R2#show bridge-domain 
Bridge-domain 2 (4 ports in all)
State: UP                    Mac learning: Enabled
Aging-Timer: 300 second(s)
Maximum address limit: 65536
    GigabitEthernet3 service instance 1
    GigabitEthernet4 service instance 1
    vfi VPLS1 neighbor 4.4.4.4 3
    vfi VPLS1 neighbor 5.5.5.5 3
   AED MAC address    Policy  Tag       Age  Pseudoport
   0   5254.0000.0045 forward dynamic   299  GigabitEthernet3.EFP1
   0   5254.0000.002F forward dynamic   299  VPLS1.404012 <============
   0   5254.0000.0049 forward dynamic   291  GigabitEthernet4.EFP1
   0   5254.0000.0033 forward dynamic   296  VPLS1.404013


R2#show mpls l2transport vc detail 
Local interface: VFI VPLS1 vfi up
  Interworking type is Ethernet
  Destination address: 4.4.4.4, VC ID: 3, VC status: up
    Output interface: Gi1, imposed label stack {22 27}  <============
    Preferred path: not configured  
    Default path: active
    Next hop: 20.2.3.3
  Create time: 00:37:32, last status change time: 00:37:17
    Last label FSM state change time: 00:37:17
  Signaling protocol: LDP, peer 4.4.4.4:0 up
    Targeted Hello: 2.2.2.2(LDP Id) -> 4.4.4.4, LDP is UP
    Graceful restart: not configured and not enabled
    Non stop routing: not configured and not enabled
    Status TLV support (local/remote)   : enabled/supported
      LDP route watch                   : enabled
      Label/status state machine        : established, LruRru
      Last local dataplane   status rcvd: No fault
      Last BFD dataplane     status rcvd: Not sent
      Last BFD peer monitor  status rcvd: No fault
      Last local AC  circuit status rcvd: No fault
      Last local AC  circuit status sent: No fault
      Last local PW i/f circ status rcvd: No fault
      Last local LDP TLV     status sent: No fault
      Last remote LDP TLV    status rcvd: No fault
      Last remote LDP ADJ    status rcvd: No fault
    MPLS VC labels: local 29, remote 27 
    Group ID: local n/a, remote 0
    MTU: local 1500, remote 1500
    Remote interface description: 
  Sequencing: receive disabled, send disabled
  Control Word: On (configured: autosense)
  SSO Descriptor: 4.4.4.4/3, local label: 29
  Dataplane:
    SSM segment/switch IDs: 16395/12294 (used), PWID: 1
  VC statistics:
    transit packet totals: receive 669, send 470
    transit byte totals:   receive 77950, send 60725
    transit packet drops:  receive 201, seq error 0, send 0


R2#show mpls forwarding-table 
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
16         24         19.19.19.19/32   0             Gi1        20.2.3.3    
17         16         6.6.6.6/32       0             Gi1        20.2.3.3    
18         26         5.5.5.5/32       0             Gi1        20.2.3.3    
19         22         4.4.4.4/32       0             Gi1        20.2.3.3   <=========
20         Pop Label  3.3.3.3/32       0             Gi1        20.2.3.3    
21         17         20.6.19.0/24     0             Gi1        20.2.3.3    
22         18         20.5.6.0/24      0             Gi1        20.2.3.3    
23         25         20.5.19.0/24     0             Gi1        20.2.3.3    
24         23         20.4.5.0/24      0             Gi1        20.2.3.3    
25         19         20.4.6.0/24      0             Gi1        20.2.3.3    
26         Pop Label  20.3.6.0/24      0             Gi1        20.2.3.3    
27         Pop Label  20.3.4.0/24      0             Gi1        20.2.3.3    
28         21         20.2.4.0/24      0             Gi1        20.2.3.3    
29         No Label   l2ckt(1)         82034         none       point2point 
30         No Label   l2ckt(2)         48522         none       point2point 
```


- From R3
```
R3#show mpls forwarding-table 
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
16         Pop Label  6.6.6.6/32       0             Gi0/0      20.3.6.6    
17         Pop Label  20.6.19.0/24     0             Gi0/0      20.3.6.6    
18         Pop Label  20.5.6.0/24      0             Gi0/0      20.3.6.6    
19         Pop Label  20.4.6.0/24      0             Gi0/1      20.3.4.4    
           Pop Label  20.4.6.0/24      0             Gi0/0      20.3.6.6    
20         Pop Label  2.2.2.2/32       145868        Gi0/2      20.2.3.2    
21         Pop Label  20.2.4.0/24      0             Gi0/1      20.3.4.4    
22         Pop Label  4.4.4.4/32       90481         Gi0/1      20.3.4.4  <========  
23         22         20.4.5.0/24      0             Gi0/0      20.3.6.6    
24         23         19.19.19.19/32   0             Gi0/0      20.3.6.6    
25         24         20.5.19.0/24     0             Gi0/0      20.3.6.6    
26         25         5.5.5.5/32       86311         Gi0/0      20.3.6.6 
```


- From R4 

```
Local interface: VFI VPLS1 vfi up
  Interworking type is Ethernet
  Destination address: 2.2.2.2, VC ID: 3, VC status: up
    Output interface: Gi2, imposed label stack {20 29}
    Preferred path: not configured  
    Default path: active
    Next hop: 20.3.4.3
  Create time: 00:42:09, last status change time: 00:42:09
    Last label FSM state change time: 00:42:09
  Signaling protocol: LDP, peer 2.2.2.2:0 up
    Targeted Hello: 4.4.4.4(LDP Id) -> 2.2.2.2, LDP is UP
    Graceful restart: not configured and not enabled
    Non stop routing: not configured and not enabled
    Status TLV support (local/remote)   : enabled/supported
      LDP route watch                   : enabled
      Label/status state machine        : established, LruRru
      Last local dataplane   status rcvd: No fault
      Last BFD dataplane     status rcvd: Not sent
      Last BFD peer monitor  status rcvd: No fault
      Last local AC  circuit status rcvd: No fault
      Last local AC  circuit status sent: No fault
      Last local PW i/f circ status rcvd: No fault
      Last local LDP TLV     status sent: No fault
      Last remote LDP TLV    status rcvd: No fault
      Last remote LDP ADJ    status rcvd: No fault
    MPLS VC labels: local 27, remote 29 
    Group ID: local n/a, remote 0
    MTU: local 1500, remote 1500
    Remote interface description: 
  Sequencing: receive disabled, send disabled
  Control Word: On (configured: autosense)
  SSO Descriptor: 2.2.2.2/3, local label: 27 <==============
  Dataplane:
    SSM segment/switch IDs: 8196/8194 (used), PWID: 1
  VC statistics:
    transit packet totals: receive 518, send 521
    transit byte totals:   receive 64764, send 61975
    transit packet drops:  receive 2, seq error 0, send 0
	
	
R4#show bridge-domain 
Bridge-domain 2 (3 ports in all)
State: UP                    Mac learning: Enabled
Aging-Timer: 300 second(s)
Maximum address limit: 65536
    GigabitEthernet5 service instance 1
    vfi VPLS1 neighbor 2.2.2.2 3
    vfi VPLS1 neighbor 5.5.5.5 3
   AED MAC address    Policy  Tag       Age  Pseudoport
   0   5254.0000.0045 forward dynamic   296  VPLS1.404011
   0   5254.0000.002F forward dynamic   298  GigabitEthernet5.EFP1
   0   5254.0000.0049 forward dynamic   295  VPLS1.404011
   0   5254.0000.0033 forward dynamic   295  VPLS1.404012
   
   
R4#show bridge-domain 
Bridge-domain 2 (3 ports in all)
State: UP                    Mac learning: Enabled
Aging-Timer: 300 second(s)
Maximum address limit: 65536
    GigabitEthernet5 service instance 1
    vfi VPLS1 neighbor 2.2.2.2 3
    vfi VPLS1 neighbor 5.5.5.5 3
   AED MAC address    Policy  Tag       Age  Pseudoport
   0   5254.0000.0045 forward dynamic   296  VPLS1.404011
   0   5254.0000.002F forward dynamic   298  GigabitEthernet5.EFP1
   0   5254.0000.0049 forward dynamic   295  VPLS1.404011
   0   5254.0000.0033 forward dynamic   295  VPLS1.404012
```