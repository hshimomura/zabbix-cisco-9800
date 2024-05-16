# zabbix-cisco9800
Zabbix template for Cisco Catalyst 9800 Series Wireless Controllers

## What it is?
This is Zabbix template for Cisco Catalyst 9800 Series Wireless Controllers.
It covers wireless monitoring like Client Count, AP Count, Radio ulization, Mobility status and High Availability status.
Please use "Cisco IOS by SNMP" template or others to get general IOS-XE status like CPU utilization, Memory usage or interface status.

The template use just OID and Standard MIB. No need to install vendor MIB.
Please refer to below MIBs to understand what infomration it's using.
- AIRESPACE-REF-MIB.my
- AIRESPACE-WIRELESS-CAPABILITY.my
- AIRESPACE-WIRELESS-MIB.my
- CISCO-LWAPP-AP-MIB.my
- CISCO-LWAPP-DOT11-MIB.my
- CISCO-LWAPP-RF-MIB.my
- CISCO-LWAPP-TC-MIB.my
- CISCO-LWAPP-WLAN-MIB.my
- CISCO-SMI.my
- CISCO-TC.my
- ENTITY-MIB.my

[https://github.com/cisco/cisco-mibs](https://github.com/cisco/cisco-mibs)

## Requiements
- Need IOS-XE 17.11 or later software image to get some of wireless specific SNMP OID
- SNNP and SNMP trap on Catalyst 9800 WLC

Here is sample configuration for SNMP
```
snmp-server community public RO
snmp-server location "YOUR PHYSICAL ADDRESS"
snmp-server enable traps snmp authentication linkdown linkup coldstart warmstart
snmp-server enable traps wireless bsnAutoRF
snmp-server enable traps rf
snmp-server host [SNMPSERVER IP ADDRESS] version 2c public
```

## Monitoring items
- Current Number of AP
- Number of APs Supported
- Rouge AP Count
- Rogue Client Count
- Access Point
  - AP Name
  - AP Channel Number (2.4GHz/5GHz)
  - AP Channel Bandwidth (5GHz)
  - AP Channel Utilization (2.4GHz/5GHz)
  - AP Operation Status
  - AP Tx Power Level (2.4GHz/5GHz)
  - AP Serial Number
  - AP Software Version
- SSID
  - SSID Administrative Status
  - SSID Number of Clients 
- Mobility Member
  - Mobility Status (Control/Data path)
- HA SSO status
- SNMP Trap
  - Channel Changed
  - DFS Radar Detected
  - AP diassociation

## Tested Environment
Tested on IOS-XE 17.12.3 and Zabbix 6.0.29 LTS.
