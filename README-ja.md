# zabbix-cisco-9800
Cisco Catalyst 9800 シリーズ ワイヤレス LAN コントローラー用の Zabbix テンプレートリポジトリです。<br>
English version is [here](README.md)

Zabbix のバージョンおよび監視方式（SNMP vs RESTCONF）に合わせて、適切なバージョンのテンプレートを選択してください。

> [!NOTE]
> 本テンプレートは [Zabbix Community Templates](https://github.com/zabbix/community-templates/tree/main/Network_Devices/Cisco/template_cisco_catalyst_9800_snmp) に取り込まれました。


## バージョンと監視方式

| ディレクトリ | テンプレート説明 | 対象 Zabbix バージョン | 監視方式 |
| :--- | :--- | :--- | :--- |
| **[7.0](./7.0/README-ja.md)** | **推奨**: RESTCONF を利用した次世代テンプレート。<br>IOS XE 17.18.2 以降での SNMP 廃止方針に対応。**<br>2.4/5GHzに加えて6GHz帯の検出と監視を追加** | Zabbix 7.0 以降 | RESTCONF (メイン) + SNMP Trap |
| **[6.0](./6.0/README-ja.md)** | 従来の SNMP ベースのテンプレート。<br>既存環境の維持向け。 | Zabbix 6.0 以降 | SNMP |

### なぜ新しいテンプレートなのか？ (RESTCONF)

以下の理由から、**RESTCONF** テンプレートへの移行を強く推奨します。

- **パフォーマンスの大幅な向上**
    - SNMPv3 の暗号化処理はデバイスの CPU 負荷が高く、大規模なデータ取得（AP一覧の取得など）の際にタイムアウトが発生しやすい問題がありました。
    - **RESTCONF (HTTP Agent)** は軽量かつ効率的であり、数千台の AP を抱える大規模環境でも安定した監視が可能です。

- **将来への対応 (Future-Proofing)**
    - **IOS XE 17.18.2** 以降、Cisco はレガシープロトコルの廃止を積極的に進めています。
    - SNMP はレガシー扱いとなり、今後の機能拡張は Streaming Telemetry/API が中心となります。今移行することで、長期的なサポート性を確保できます。

- **より詳細でインテリジェントなデータ**
    - **6GHz / Wi-Fi 6E/7 対応**: MIB では困難だった 6GHz 帯の自動検出と識別に対応しています。
    - **スマートな AP マッピング**: アイテムのキーに MAC アドレスではなく実際の AP 名を使用するため、グラフやアラートが非常に読みやすくなります。
    - **可視性の深化**: HA のフェイルオーバー理由や、モビリティトンネルの状態など、よりリッチな情報を取得可能です。

- **信頼性の高いリアルタイム通知 (ハイブリッドアプローチ)**
    - メトリクスは RESTCONF へ移行しつつ、クリティカルなベンダー固有イベントには **SNMP Trap** を継続して採用しています。
    - Cisco ベンダー MIB 使用時に、その OID を正しく認識し処理できるようになりました。

> [!NOTE]
> 本テンプレートは無線LANの監視に特化しています。CPU/メモリ使用率、インターフェース統計、インベントリ情報などの一般的なシステム統計については、標準の "Cisco IOS by SNMP" テンプレートと併用して監視することを想定しています。

## 詳細情報

各バージョンの詳細なセットアップ方法や仕様については、それぞれのディレクトリ内の README を参照してください。

- [Zabbix 7.0 用 (RESTCONF 版) の詳細ドキュメント](./7.0/README-ja.md)
- [Zabbix 6.0 用 (SNMP 版) の詳細ドキュメント](./6.0/README-ja.md)

## スクリーンショット
![スクリーンショット1](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/4dd37fb6-fc9d-7e33-23aa-9928b1c4a85b.png)
![スクリーンショット2](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/fdc40126-9b34-8200-5ac5-6ea7dad9ecd6.png)

## ライセンス
本プロジェクトは GNU General Public License v3.0 の下でライセンスされています。詳細は [LICENSE](LICENSE) ファイルを参照してください。

## 著者
Hiroki Shimomura