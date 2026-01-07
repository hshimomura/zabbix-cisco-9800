# zabbix-cisco-9800
Zabbix template repository for Cisco Catalyst 9800 Series Wireless LAN Controllers.<br>
Japanese version is [here](README-ja.md)

Zabbix template repository for Cisco Catalyst 9800 Series Wireless LAN Controllers.
Please select the appropriate template version according to your Zabbix version and monitoring method (SNMP vs RESTCONF).

## Versions and Monitoring Methods

| Directory | Template Description | Target Zabbix Version | Monitoring Method |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README.md)** | **Recommended**: Next-generation template using RESTCONF.<br>Addresses the SNMP deprecation policy in IOS XE 17.18 and later. | Zabbix 7.0 or later | RESTCONF (Main) + SNMP Trap |
| **[6.0](./6.0/README.md)** | Traditional SNMP-based template.<br>For maintaining existing environments. | Zabbix 6.0 or later | SNMP |

> [!NOTE]
> This template specializes in wireless LAN monitoring items. For CPU utilization, Memory utilization, and interface stats, it is intended to be used in conjunction with "Cisco IOS by SNMP".

## Detailed Information

For detailed setup instructions and specifications for each version, please refer to the README in relevant directory.

- [Documentation for Zabbix 7.0 (RESTCONF version)](./7.0/README.md)
- [Documentation for Zabbix 6.0 (SNMP version)](./6.0/README.md)
