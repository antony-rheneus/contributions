Weighted LAG

Weighted LAG member support added to SONIC based on SAI Attribute SAI_LAG_MEMBER_ATTR_WEIGHT
 
Sonic-utilities : CLI Changes
https://github.com/sonic-net/sonic-utilities/commit/6af58f86828f2cfff07c54d153a8f6a6acd55845
 
sonic-swss: RedisDB to SAI call
https://github.com/sonic-net/sonic-swss/commit/ba88069997ac588065d26a4bf0cb68e6aee98491
 
sonic-buildimage: For Yang-model change
https://github.com/sonic-net/sonic-buildimage/commit/e0103d70b5c4812dc8fa1bd95cfcf5d84e974adc
 
 
Design:
 
From SONIC Python CLI, weighted LAG configuration is added to CONFIG_DB.
TeamdMgr reads the portchannel/lag and its member configuration and updates to APP_DB.
Teamd doesn’t change any existing behaviour to Linux teamd LACP control protocol packets.
From APP_DB, orchagent reads the configuration, based on the schema, sends the appropriate Weighted LAG Attribute and its value to Syncd.
Syncd calls the respective SAI API set call to program the Lag weight in the TLx.
 
Caveats:
Current T5.0.2 is based on 1.15 SAI where WEIGHTED Lag SAI attribute is a custom attribute SAI_LAG_MEMBER_ATTR_CUSTOM_WEIGHT
In SAI 1.16, Weighted LAG attribute is Standard Attribute - SAI_LAG_MEMBER_ATTR_WEIGHT
 
 
Tested Version:
SONIC : 202411
iSAI : T5.0.2
 
 
Configuration:
 
Enable Weighted LAG support on a port channel
root@US-CSG-TL7-Wistron-13:~# config portchannel add PortChannel10 --weighted-lag=true
 
Configure the member with user define weight
root@US-CSG-TL7-Wistron-13:~# config portchannel member add PortChannel10 Ethernet128 --lag-weight 2
 
Configure the member with user define weight
root@US-CSG-TL7-Wistron-13:~# config portchannel member add PortChannel10 Ethernet64 --lag-weight 4
 
 
Show Command:
 
root@US-CSG-TL7-Wistron-13:~# show interfaces portchannel 
Flags: A - active, I - inactive, Up - up, Dw - Down, N/A - not available,
       S - selected, D - deselected, * - not synced
  No.  Team Dev       Protocol     Ports
-----  -------------  -----------  ----------------------------
   10  PortChannel10  LACP(A)(Up)  Ethernet128(S) Ethernet64(S)
 
Verbose option is newly added in show command to display the configured Weights
root@US-CSG-TL7-Wistron-13:~# show interfaces portchannel --verbose
Flags: A - active, I - inactive, Up - up, Dw - Down, N/A - not available,
       S - selected, D - deselected, * - not synced
  No.  Team Dev       Protocol     Ports
-----  -------------  -----------  --------------------------------------
   10  PortChannel10  LACP(A)(Up)  [w2] Ethernet128(S) [w4] Ethernet64(S)
 
 
root@US-CSG-TL7-Wistron-13:~# show interfaces status Ethernet64
  Interface            Lanes    Speed    MTU    FEC    Alias           Vlan    Oper    Admin    Type    Asym PFC
-----------  ---------------  -------  -----  -----  -------  -------------  ------  -------  ------  ----------
 Ethernet64  153,154,155,156     100G   9100     rs     Eth9  PortChannel10      up       up     N/A         N/A
Ethernet128  209,210,211,212     100G   9100     rs    Eth17  PortChannel10      up       up     N/A         N/A
 
 
Sairedis.rec
 
2025-06-26.01:30:53.691141|c|SAI_OBJECT_TYPE_LAG_MEMBER:oid:0x1b000000000456|SAI_LAG_MEMBER_ATTR_LAG_ID=oid:0x2000000000455|SAI_LAG_MEMBER_ATTR_PORT_ID=oid:0x100000000001c
2025-06-26.01:30:53.698064|s|SAI_OBJECT_TYPE_LAG_MEMBER:oid:0x1b000000000456|SAI_LAG_MEMBER_ATTR_CUSTOM_WEIGHT=2
 
2025-06-26.01:36:23.291508|c|SAI_OBJECT_TYPE_LAG_MEMBER:oid:0x1b000000000457|SAI_LAG_MEMBER_ATTR_LAG_ID=oid:0x2000000000455|SAI_LAG_MEMBER_ATTR_PORT_ID=oid:0x1000000000015
2025-06-26.01:36:23.299606|s|SAI_OBJECT_TYPE_LAG_MEMBER:oid:0x1b000000000457|SAI_LAG_MEMBER_ATTR_CUSTOM_WEIGHT=4
 
 
Ivmshell:
 
IVM-R>ifcs show lag
Total lag count: 1 
+-----------------------------------------------------------------------------+
|        lag | l3intf_handle |    type | member_count | direction | ref_count |
|-----------------------------------------------------------------------------|
| (lag:   1) |             0 | PRIMARY |            6 |     BIDIR |         0 |
+-----------------------------------------------------------------------------+
Total lag count: 1
 
 
IVM-R>ifcs show lag 1 members
+--------------------------+
|         member |    type |
|--------------------------|
| (sysport: 209) | Primary |
| (sysport: 209) | Primary |
| (sysport: 153) | Primary |
| (sysport: 153) | Primary |
| (sysport: 153) | Primary |
| (sysport: 153) | Primary |
+--------------------------+
 
 