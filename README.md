# zabbix-cisco-9800
Zabbix template repository for Cisco Catalyst 9800 Series Wireless LAN Controllers.<br>
Japanese version is [here](README-ja.md)

Please select the appropriate template version according to your Zabbix version and monitoring method (SNMP vs RESTCONF).

## Versions and Monitoring Methods

| Directory | Template Description | Target Zabbix Version | Monitoring Method |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README.md)** | **Recommended**: Next-generation template using RESTCONF.<br>Addresses the SNMP deprecation policy in IOS XE 17.18.2 and later. **<br>Includes detection and monitoring for the 6GHz band, extending existing 2.4/5GHz support** | Zabbix 7.0 or later | RESTCONF (Main) + SNMP Trap |
| **[6.0](./6.0/README.md)** | Traditional SNMP-based template.<br>For maintaining existing environments. | Zabbix 6.0 or later | SNMP |

### Why new template? (RESTCONF)

Transitioning to the **RESTCONF** template is strongly recommended for the following reasons:

- **Significantly Improved Performance**
    - SNMPv3 encryption incurs heavy CPU load on devices, often causing timeouts during large-scale data retrieval (e.g., retrieving AP lists).
    - **RESTCONF (HTTP Agent)** is lightweight and efficient, enabling stable monitoring even in large environments with thousands of APs.

- **Future-Proofing**
    - Starting with **IOS XE 17.18.2**, Cisco is aggressively deprecating legacy protocols.
    - SNMP is becoming legacy, and future feature expansion will focus on Streaming Telemetry/API. Migrating now ensures long-term supportability.

- **More Detailed & Intelligent Data**
    - **6GHz / Wi-Fi 6E/7 Support**: Automatically detects and distinguishes 6GHz bands, which is difficult with standard MIBs.
    - **Smart AP Mapping**: Uses actual AP Names for item keys instead of MAC addresses, making graphs and alerts much easier to read.
    - **Deeper Visibility**: Provides richer insights into High Availability (HA) failover reasons and Mobility tunnel statuses.

    - **Reliable Real-time Alerting (Hybrid Approach)**
    - While migrating metrics to RESTCONF, the template wisely retains **SNMP Traps** for critical vendor-specific events.
    - Now correctly recognizes and resolves OIDs when using Cisco Vendor MIBs.

> [!NOTE]
> This template specializes in Wireless LAN monitoring. It is designed to be used in conjunction with the standard "Cisco IOS by SNMP" template for generic system statistics, such as CPU/Memory utilization, interface traffic, and hardware inventory.

## Detailed Information

For detailed setup instructions and specifications for each version, please refer to the README in relevant directory.

- [Documentation for Zabbix 7.0 (RESTCONF version)](./7.0/README.md)
- [Documentation for Zabbix 6.0 (SNMP version)](./6.0/README.md)


## Screenshots
![Screenshot1](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/4dd37fb6-fc9d-7e33-23aa-9928b1c4a85b.png)
![Screenshot2](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/fdc40126-9b34-8200-5ac5-6ea7dad9ecd6.png)

## License
This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

## Author
Hiroki Shimomura