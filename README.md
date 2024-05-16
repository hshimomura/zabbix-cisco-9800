# zabbix-cisco9800
Zabbix template for Cisco Catalyst 9800 Series Wireless Controllers

## What it is?
This is a Zabbix template for [Cisco Catalyst 9800 Series Wireless Controllers](https://www.cisco.com/site/us/en/products/networking/wireless/wireless-lan-controllers/catalyst-9800-series/index.html).
It covers wireless monitoring like Wireless Client Count, AP Count, Radio utilization, Mobility tunnel status, and High Availability status.
Please use the "Cisco IOS by SNMP" template or others to get general IOS-XE status like CPU utilization, Memory usage, or interface status.


## Requiements
- Cisco Catalyst 9800 Series Wireless Controller and supported Access Point
  - [Embdedded Wireless Controller For Catalyst Access Point](https://www.cisco.com/c/en/us/products/wireless/embedded-wireless-controller-on-catalyst-access-points/index.html) doesn't work [^1] 
  - Other controllers including [Cisco Catalyst 9800-CL Wireless Controller for Cloud](https://www.cisco.com/c/en/us/products/collateral/wireless/catalyst-9800-cl-wireless-controller-cloud/nb-06-cat9800-cl-cloud-wirel-data-sheet-ctp-en.html) support SNMP
- IOS-XE 17.11 or later software image to get many wireless SNMP OIDs like AIRESPACE-WIRELESS-MIB::bsnDot11EssNumberOfMobileStations
- SNNP and SNMP trap on Catalyst 9800 WLC
[^1]: Embedded Wireless Controller (EWC) does not support SNMP and does not implement the SNMP MIBs of Cisco Catalyst 9800 Series Wireless Controllers, although EWC might respond to some of the object identifiers (OIDs). [Configuration guide](https://www.cisco.com/c/en/us/td/docs/wireless/controller/ewc/17-6/config-guide/ewc_cg_17_6/new_configuration_model.html)

Here is a sample configuration for SNMP
```
snmp-server community [SNMP COMMUNITY] RO
snmp-server location ["YOUR PHYSICAL ADDRESS"]
snmp-server enable traps wireless bsnAutoRF
snmp-server enable traps rf
snmp-server host [ZABBIX IP ADDRESS] version 2c [SNMP COMMUNITY]
```

## Monitoring items
| Monitoring Item  |SNMP MIBs |
| ------------- | ------------- |
| AP Name  | AIRESPACE-WIRELESS-MIB::bsnAPName  |
| AP Channel Number (2.4GHz/5GHz) | AIRESPACE-WIRELESS-MIB::bsnAPIfPhyChannelNumber |
| AP Channel Bandwidth (5GHz) | CISCO-LWAPP-AP-MIB::cLAp11ChannelBandwidth|
| AP Channel Utilization (2.4GHz/5GHz) | AIRESPACE-WIRELESS-MIB::bsnAPIfLoadChannelUtilization|
| AP Operation Status | AIRESPACE-WIRELESS-MIB::bsnAPOperationStatus|
| AP Serial Number | AIRESPACE-WIRELESS-MIB::bsnAPSerialNumber|
| AP Software Version | AIRESPACE-WIRELESS-MIB::bsnAPSoftwareVersion|
| AP Tx Power Level (2.4GHz/5GHz) | AIRESPACE-WIRELESS-MIB::bsnAPIfPhyTxPowerLevel|
| Current Number of AP | CISCO-LWAPP-AP-MIB::cLApGlobalAPConnectCount.0|
| Number of APs Supported | CISCO-LWAPP-AP-MIB::cLApGlobalMaxApsSupported.0|
| HA SSO status | CISCO-LWAPP-HA-MIB::cLHaPeerHotStandbyEvent |
| Mobility Member Status (Control) | CISCO-LWAPP-MOBILITY-MIB::cLMobilityGroupMembersOperControlPathStatus |
| Mobility Member Status (Data) | CISCO-LWAPP-MOBILITY-MIB::cLMobilityGroupMembersOperControlPathStatus |
| Rouge AP Count | AIRESPACE-WIRELESS-MIB::bsnRogueAPDot11MacAddress |
| Rogue Client Count | AIRESPACE-WIRELESS-MIB::bsnRogueClientDot11MacAddress |
| SSID Administrative Status | AIRESPACE-WIRELESS-MIB::bsnDot11EssAdminStatus |
| SSID Number of Clients | AIRESPACE-WIRELESS-MIB::bsnDot11EssNumberOfMobileStations |
| AP diassociation | AIRESPACE-WIRELESS-MIB::bsnAPDisassociated, CISCO-LWAPP-AP-MIB::ciscoLwappApAssociated |
| Channel Changed | AIRESPACE-WIRELESS-MIB::bsnAPCurrentChannelChanged |
| DFS Radar Detection | AIRESPACE-WIRELESS-MIB::bsnRadarChannelDetected |

The template uses just OID and Standard MIB. No need to install vendor MIB. Please refer to the below MIBs to understand each SNMP MIB.
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

## Screenshots
![Screenshot1](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/4dd37fb6-fc9d-7e33-23aa-9928b1c4a85b.png)
![Screenshot2](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/fdc40126-9b34-8200-5ac5-6ea7dad9ecd6.png)

## Tested Environment
- Cisco IOS Software [Dublin], C9800 Software (C9800_IOSXE-K9), Version 17.12.3, RELEASE SOFTWARE (fc7)
  - C9800-L-F-K9
  - C9800-CL-K9
- Zabbix 6.0.29

> [!IMPORTANT]
The test is only done in small lab environments. In a large environment, monitor the CPU utilization of the Wireless Controller in case SNMP consumes too many resources.
