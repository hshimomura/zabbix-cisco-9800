# zabbix-cisco-9800
Zabbix template repository for Cisco Catalyst 9800 Series Wireless LAN Controllers.<br>
Japanese version is [here](README-ja.md)

Please select the appropriate template version according to your Zabbix version and monitoring method (SNMP vs RESTCONF).

## Versions and Monitoring Methods

| Directory | Template Description | Target Zabbix Version | Monitoring Method |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README.md)** | **Recommended**: Next-generation template using RESTCONF.<br>Addresses the SNMP deprecation policy in IOS XE 17.18.2 and later. | Zabbix 7.0 or later | RESTCONF (Main) + SNMP Trap |
| **[6.0](./6.0/README.md)** | Traditional SNMP-based template.<br>For maintaining existing environments. | Zabbix 6.0 or later | SNMP |

> [!NOTE]
> This template specializes in Wireless LAN monitoring. It is designed to be used in conjunction with the standard "Cisco IOS by SNMP" template for generic system statistics, such as CPU/Memory utilization, interface traffic, and hardware inventory.

## Detailed Information

For detailed setup instructions and specifications for each version, please refer to the README in relevant directory.

- [Documentation for Zabbix 7.0 (RESTCONF version)](./7.0/README.md)
- [Documentation for Zabbix 6.0 (SNMP version)](./6.0/README.md)


## Screenshots
![Screenshot1](./screenshots/screenshot1.png)
![Screenshot2](./screenshots/screenshot2.png)

## License
This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

## Author
Hiroki Shimomura