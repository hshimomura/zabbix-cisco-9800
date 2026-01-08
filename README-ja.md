# zabbix-cisco-9800
Cisco Catalyst 9800 シリーズ ワイヤレス LAN コントローラー用の Zabbix テンプレートリポジトリです。<br>
English version is [here](README.md)

Zabbix のバージョンおよび監視方式（SNMP vs RESTCONF）に合わせて、適切なバージョンのテンプレートを選択してください。

## バージョンと監視方式

| ディレクトリ | テンプレート説明 | 対象 Zabbix バージョン | 監視方式 |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README-ja.md)** | **推奨**: RESTCONF を利用した次世代テンプレート。<br>IOS XE 17.18.2 以降での SNMP 廃止方針に対応。 | Zabbix 7.0 以降 | RESTCONF (メイン) + SNMP Trap |
| **[6.0](./6.0/README-ja.md)** | 従来の SNMP ベースのテンプレート。<br>既存環境の維持向け。 | Zabbix 6.0 以降 | SNMP |

> [!NOTE]
> 本テンプレートは無線LANの監視に特化しています。CPU/メモリ使用率、インターフェース統計、インベントリ情報などの一般的なシステム統計については、標準の "Cisco IOS by SNMP" テンプレートと併用して監視することを想定しています。

## 詳細情報

各バージョンの詳細なセットアップ方法や仕様については、それぞれのディレクトリ内の README を参照してください。

- [Zabbix 7.0 用 (RESTCONF 版) の詳細ドキュメント](./7.0/README-ja.md)
- [Zabbix 6.0 用 (SNMP 版) の詳細ドキュメント](./6.0/README-ja.md)

## スクリーンショット
![スクリーンショット1](../screenshots/screenshot1.png)
![スクリーンショット2](../screenshots/screenshot2.png)