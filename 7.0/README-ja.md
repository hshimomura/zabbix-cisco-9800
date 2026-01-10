# zabbix-cisco-9800 (RESTCONF)
Cisco Catalyst 9800シリーズ無線コントローラー用のZabbixテンプレート (RESTCONF版)<br>
English version is [here](README.md)

## 目的
IOS XE 17.18.2 以降、Cisco は「Secure by Design」の理念に基づき、レガシーかつ安全でない機能やプロトコルを段階的に廃止し、よりセキュアな代替手段への移行を推進しています。 この取り組みの一環として SNMPv2 から SNMPv3 への移行が推奨されています。しかし、SNMPv3 の暗号化オーバーヘッドは負荷が高く、アクセスポイント数台程度の環境であってもデータ取得のパフォーマンスが著しく低下することが確認されています。その結果、Zabbix 標準の snmp timeout（3秒）を超過するケースも散見されます。また、Cisco は新機能の開発において SNMP よりもストリーミングテレメトリを優先しており、今後の監視手法として NETCONF/RESTCONF/gRPC 等の移行を推奨しています。

このような管理トラフィックの暗号化への対応とパフォーマンス対策として、本テンプレートではデータ収集機能の大部分を RESTCONF に移行しました。ただし、現在の Zabbix はストリーミングテレメトリのリアルタイム受信にネイティブ対応していないため、即時通知が必要なアラートについては引き続き SNMP Trap を使用するハイブリッド構成としています。

> [!NOTE]
> 本テンプレートは無線LANの監視に特化しています。CPU/メモリ使用率、インターフェース統計、インベントリ情報などの一般的なシステム統計については、標準の "Cisco IOS by SNMP" テンプレートと併用して監視することを想定しています。

### 参考URL
Release Notes for Cisco Catalyst 9800 Series Wireless Controller, Cisco IOS XE 17.18.2
https://www.cisco.com/c/en/us/td/docs/wireless/controller/9800/17-18/release-notes/rn-17-18-2-9800.html


## 要件

- Cisco Catalyst 9800シリーズ ワイヤレスLANコントローラ
  - RESTCONFが有効化されていること (IOS XE 16.10以降推奨)
  - HTTPS サーバーが有効化されていること
- Zabbix 7.0以降

## 設定方法 (Cisco Catalyst 9800 WLC)

Cisco Catalyst 9800 WLCでRESTCONFとHTTPSサーバーを有効にし、適切な権限を持つユーザーを作成してください。

```cisco
configure terminal
 ip http secure-server
 restconf
 username [USER] privilege 15 secret [PASSWORD]
end
```

SNMP Trapを使用する場合の設定例 (SNMPv3推奨):
```cisco
! Trapの有効化
snmp-server enable traps wireless bsnAutoRF
snmp-server enable traps rf

! SNMPv3 グループとユーザーの作成 (AuthPrivモード)
snmp-server group [SNMPv3 Group] v3 priv
snmp-server user [SNMPv3 Username] [SNMPv3 Group] v3 auth sha [AUTH_PASS] priv aes 128 [PRIV_PASS]

! Trap送信先の設定
snmp-server host [ZABBIX IP ADDRESS] version 3 priv [SNMPv3 Username]

! EngineIDの確認
#show snmp engineID
Local SNMP engineID: 800000090300F4BD9E59254C
Remote Engine ID          IP-addr    Port

```

> [!NOTE]
> ZabbixがデバイスからのSNMPv3 Trapを受け入れられるようにするには、Zabbixドキュメントを参照して `snmptrapd` を設定してください。
> 以下の設定は例です。EngineIDと認証情報は、実際のデバイス設定と一致させてください。
```shell
# snmptrapd.conf
createUser -e [SNMP engineID] [SNMPv3 Username] SHA [AUTH_PASS] AES [PRIV_PASS]
authuser log,execute [SNMPv3 Username]

```
https://www.zabbix.com/documentation/current/en/manual/config/items/itemtypes/snmptrap


## 使用方法

1. テンプレートをインポートします。
2. ホストのマクロを設定します。ユーザ名、パスワードは環境にあわせて変更してください。

| マクロ | 説明 | デフォルト値 |
| --- | --- | --- |
| `{$RESTCONF.URL}` | RESTCONFのベースURL。通常変更する必要はありません。 | `https://{HOST.CONN}/restconf/data` |
| `{$RESTCONF.USER}` | RESTCONF接続用ユーザー名 | `admin` |
| `{$RESTCONF.PASS}` | RESTCONF接続用パスワード | `adminpassword` |

## ディスカバリルール

| 名前 | 説明 | タイプ | 更新間隔 |
| --- | --- | --- | --- |
| Mobility Node Discovery | モビリティグループメンバーを検出し、トンネル状態を監視します。 | Dependent | 0 (Master itemに従う) |
| Radio Slot Discovery | APのラジオスロットを検出し、AP名と周波数帯(2.4/5/6GHz)を動的にマッピングします。<br>MACアドレスではなくAP名でアイテムを作成します。 | Script | 1h |
| WLAN SSID Discovery | SSID (WLANプロファイル) を検出し、クライアント数を監視します。 | Dependent | 0 (Master itemに従う) |

## アイテム

### HTTP Agent / RESTCONF
RESTCONF経由で取得される主なメトリクスです。

| 名前 | 説明 | タイプ |
| --- | --- | --- |
| HA Local State | HAのローカルステータス (Active/Standbyなど) | Dependent |
| HA Peer State | HAのピアステータス | Dependent |
| HA Last Switchover Reason | 最後のスイッチオーバー理由 | Dependent |
| Mobility Node {#NODE.IP}: Link Status | モビリティトンネルステータス (up/down) | Dependent |
| Total Joined AP Count | 参加AP総数 | HTTP Agent |
| Rogue AP: Total Count | 検知されたRogue APの総数 | HTTP Agent |
| Rogue AP: Total Client Count | Rogue APに接続しているクライアント総数 | HTTP Agent |
| AP {#AP.NAME} ({#FREQ.NAME}): Operating Channel | AP/周波数ごとのオペレーティングチャネル | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): Channel Utilization | AP/周波数ごとのチャネル使用率 (CCA) | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): Client Count | AP/周波数ごとのクライアント数 | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): Noise Level | AP/周波数ごとのノイズレベル | Dependent |
| SSID {#WLAN.NAME}: Client Count | SSIDごとのクライアント数 | Dependent |

### SNMP Trap
リアルタイム通知が必要なイベントはSNMP Trapで受信します。

| 名前 | 説明 | タイプ |
| --- | --- | --- |
| SNMP Trap - Channel Changed | APのチャネル変更トラップ (bsnAPCurrentChannelChanged) | SNMP Trap |
| SNMP Trap - RadarChannelDetected | DFSレーダー検知トラップ (bsnRadarChannelDetected) | SNMP Trap |
| SNMP Trap - AP disassociation | APの切断/再参加トラップ (bsnAPDisassociated / ciscoLwappApAssociated) | SNMP Trap |

## スクリーンショット
ホストをZabbixに追加し、テンプレートを適用してください。
環境に合わせてテンプレートのマクロを変更してください。

![Screenshot3](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/6a9d5994-9d81-4a1d-85e2-231876ec0f9a.png)
![Screenshot4](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/385067/5770181e-5df3-44ba-972b-9b5ff86acf93.png)


## テスト環境
- Cisco Catalyst 9800-L wireless controller
- IOS XE 17.15.4d
- Zabbix 7.0.22