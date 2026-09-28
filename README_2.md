FEC Config in DPB port breakout CLI

https://github.com/sonic-net/sonic-utilities/pull/3908


fix Dynamic Port breakout CLI to pass the FEC config from the user during the PORT create.
Changes are done in SONIC CLI script.
 
Existing:
config interface breakout Ethernet248  "4x25G[10G](4)"  -f
  Interface            Lanes    Speed    MTU    FEC    Alias    Vlan    Oper    Admin    Type    Asym PFC
-----------  ---------------  -------  -----  -----  -------  ------  ------  -------  ------  ----------
Ethernet248              233      25G   9100    N/A  Eth32-1  routed    down     down     N/A         N/A
Ethernet249              234      25G   9100    N/A  Eth32-2  routed    down     down     N/A         N/A
Ethernet250              235      25G   9100    N/A  Eth32-3  routed    down     down     N/A         N/A
Ethernet251              236      25G   9100    N/A  Eth32-4  routed    down     down     N/A         N/A
 
Sairedies.rec
2025-05-28.09:08:58.351408|C|SAI_OBJECT_TYPE_PORT||oid:0x1000000000472|SAI_PORT_ATTR_HW_LANE_LIST=1:236|SAI_PORT_ATTR_SPEED=25000
 
 
New:
config interface breakout Ethernet248  "4x25G[10G](4)" rs  -f
  Interface            Lanes    Speed    MTU    FEC    Alias    Vlan    Oper    Admin    Type    Asym PFC
-----------  ---------------  -------  -----  -----  -------  ------  ------  -------  ------  ----------
Ethernet248              233      25G   9100     rs  Eth32-1  routed    down     down     N/A         N/A
Ethernet249              234      25G   9100     rs  Eth32-2  routed    down     down     N/A         N/A
Ethernet250              235      25G   9100     rs  Eth32-3  routed    down     down     N/A         N/A
Ethernet251              236      25G   9100     rs  Eth32-4  routed    down     down     N/A         N/A
 
 
Sairedis.rec
2025-05-28.09:00:03.318430|C|SAI_OBJECT_TYPE_PORT||oid:0x1000000000459|SAI_PORT_ATTR_HW_LANE_LIST=1:235|SAI_PORT_ATTR_SPEED=25000|SAI_PORT_ATTR_FEC_MODE=SAI_PORT_FEC_MODE_RS
 
 
 

