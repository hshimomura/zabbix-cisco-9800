# zabbix-cisco-9800
Cisco Catalyst 9800 シリーズ ワイヤレス LAN コントローラー用の Zabbix テンプレートリポジトリです。<br>
English version is [here](README.md)

Cisco Catalyst 9800 シリーズ ワイヤレス LAN コントローラー用の Zabbix テンプレートリポジトリです。
Zabbix のバージョンおよび監視方式（SNMP vs RESTCONF）に合わせて、適切なバージョンのテンプレートを選択してください。

## バージョンと監視方式

| ディレクトリ | テンプレート説明 | 対象 Zabbix バージョン | 監視方式 |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README-ja.md)** | **推奨**: RESTCONF を利用した次世代テンプレート。<br>IOS-XE 17.18 以降での SNMP 廃止方針に対応。 | Zabbix 7.0 以降 | RESTCONF (メイン) + SNMP Trap |
| **[6.0](./6.0/README-ja.md)** | 従来の SNMP ベースのテンプレート。<br>既存環境の維持向け。 | Zabbix 6.0 以降 | SNMP |

> [!NOTE]
> このテンプレートは無線LANの監視項目に特化しており、CPU utilization, Memory utilization, interface stats については、"Cisco IOS by SNMP" と同時に使用することを想定しています。

## 詳細情報

各バージョンの詳細なセットアップ方法や仕様については、それぞれのディレクトリ内の README を参照してください。

- [Zabbix 7.0 用 (RESTCONF 版) の詳細ドキュメント](./7.0/README-ja.md)
- [Zabbix 6.0 用 (SNMP 版) の詳細ドキュメント](./6.0/README-ja.md)
