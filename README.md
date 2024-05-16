# zabbix-cisco9800
Zabbix template for Cisco Catalyst 9800 Series Wireless Controllers

## What it is?
This is Zabbix template for [Cisco Catalyst 9800 Series Wireless Controllers](https://www.cisco.com/site/us/en/products/networking/wireless/wireless-lan-controllers/catalyst-9800-series/index.html).
It covers wireless monitoring like Wireless Client Count, AP Count, Radio ulization, Mobility tunnel status and High Availability status.
Please use "Cisco IOS by SNMP" template or others to get general IOS-XE status like CPU utilization, Memory usage or interface status.

The template use just OID and Standard MIB. No need to install vendor MIB.
Please refer to below MIBs to understand what infomration it uses.
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
- Cisco Catalyst 9800 Series Wireless Controller and supported Access Point
  - [Embdedded Wireless Controller For Catalyst Access Point](https://www.cisco.com/c/en/us/products/wireless/embedded-wireless-controller-on-catalyst-access-points/index.html) doesn't work because SNMP is not supported 
  - Other controllers including [Cisco Catalyst 9800-CL Wireless Controller for Cloud](https://www.cisco.com/c/en/us/products/collateral/wireless/catalyst-9800-cl-wireless-controller-cloud/nb-06-cat9800-cl-cloud-wirel-data-sheet-ctp-en.html) support SNMP
- Need IOS-XE 17.11 or later software image to get some of wireless specific SNMP OID
- SNNP and SNMP trap on Catalyst 9800 WLC

Here is sample configuration for SNMP
```
snmp-server community [SNMP COMMUNITY] RO
snmp-server location ["YOUR PHYSICAL ADDRESS"]
snmp-server enable traps wireless bsnAutoRF
snmp-server enable traps rf
snmp-server host [ZABBIX IP ADDRESS] version 2c [SNMP COMMUNITY]
```

## Monitoring items
- Access Point
  - AP Name: AIRESPACE-WIRELESS-MIB::bsnAPName
  - AP Channel Number (2.4GHz/5GHz): AIRESPACE-WIRELESS-MIB::bsnAPIfPhyChannelNumber
  - AP Channel Bandwidth (5GHz): CISCO-LWAPP-AP-MIB::cLAp11ChannelBandwidth
  - AP Channel Utilization (2.4GHz/5GHz): AIRESPACE-WIRELESS-MIB::bsnAPIfLoadChannelUtilization
  - AP Operation Status: AIRESPACE-WIRELESS-MIB::bsnAPOperationStatus
  - AP Serial Number: AIRESPACE-WIRELESS-MIB::bsnAPSerialNumber
  - AP Software Version: AIRESPACE-WIRELESS-MIB::bsnAPSoftwareVersion
  - AP Tx Power Level (2.4GHz/5GHz): AIRESPACE-WIRELESS-MIB::bsnAPIfPhyTxPowerLevel
- Current Number of AP: CISCO-LWAPP-AP-MIB::cLApGlobalAPConnectCount.0
- Number of APs Supported: CISCO-LWAPP-AP-MIB::cLApGlobalMaxApsSupported.0
- HA SSO status: CISCO-LWAPP-HA-MIB::cLHaPeerHotStandbyEvent
- Mobility Member
  - Mobility Member Tunnel Status (Control path): CISCO-LWAPP-MOBILITY-MIB::cLMobilityGroupMembersOperControlPathStatus
  - Mobility Member Tunnel Status (Data path): CISCO-LWAPP-MOBILITY-MIB::cLMobilityGroupMembersOperControlPathStatus
- Rouge AP Count: AIRESPACE-WIRELESS-MIB::bsnRogueAPDot11MacAddress
- Rogue Client Count: AIRESPACE-WIRELESS-MIB::bsnRogueClientDot11MacAddress
- SSID
  - SSID Administrative Status: AIRESPACE-WIRELESS-MIB::bsnDot11EssAdminStatus
  - SSID Number of Clients: AIRESPACE-WIRELESS-MIB::bsnDot11EssNumberOfMobileStations
- SNMP Trap
  - AP diassociation: AIRESPACE-WIRELESS-MIB::bsnAPDisassociated, CISCO-LWAPP-AP-MIB::ciscoLwappApAssociated
  - Channel Changed: AIRESPACE-WIRELESS-MIB::bsnAPCurrentChannelChanged
  - DFS Radar Detection: AIRESPACE-WIRELESS-MIB::bsnRadarChannelDetected

## Screenshots
![Screenshot1](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/4dd37fb6-fc9d-7e33-23aa-9928b1c4a85b.png)
![Screenshot2](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/fdc40126-9b34-8200-5ac5-6ea7dad9ecd6.png)

## Imprtant notes
Test is only done on small lab environment. On large environment, monitor CPU ulization of Wireless Controller in case SNMP consumes too much resources.

## Tested Environment
- Cisco IOS Software [Dublin], C9800 Software (C9800_IOSXE-K9), Version 17.12.3, RELEASE SOFTWARE (fc7)
  - C9800-L-F-K9
  - C9800-CL-K9
- Zabbix 6.0.29
