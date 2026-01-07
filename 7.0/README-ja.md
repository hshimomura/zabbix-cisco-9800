# zabbix-cisco-9800 (RESTCONF)
Cisco Catalyst 9800シリーズ無線コントローラー用のZabbixテンプレート (RESTCONF版)<br>
English version is [here](README.md)

## 目的
Cisco は IOS XE 17.18.2 から、レガシーかつ安全でない機能／プロトコルを段階的に廃止し、より安全な代替手段への移行を促しています。
Zabbix で使用する SNMPv2についても SNMPv3への移行が求められています。
SNMPv3は既にZabbixでサポートされており、従来のCisco Catalyst 9800 for SNMPでも動作しますが、暗号化通信であるSNMPv3への移行に伴い、情報取得速度が遅くなる症状がアクセスポイント数台の環境でも既に確認されています。またCisco はSNMPへの開発をほぼ停止しており、今後の監視系の項目はNETCONF/RESTCONF/gRPC等のストリーミングテレメトリの利用を推奨しています。
本zabbix template については、来る管理系トラフィック全暗号化の時代に備え、機能の大多数をRESTCONFに移行しました。一部のリアルタイム通知が必要な項目はZabbixでリアルタイムに受けられるストリーミングテレメトリの仕組みがないため、SNMP Trap を継続利用しています。

> [!NOTE]
> このテンプレートは無線LANの監視項目に特化しており、CPU utilization, Memory utilization, interface stats については、"Cisco IOS by SNMP" と同時に使用することを想定しています。

### 参考URL
Release Notes for Cisco Catalyst 9800 Series Wireless Controller, Cisco IOS XE 17.18.2
https://www.cisco.com/c/en/us/td/docs/wireless/controller/9800/17-18/release-notes/rn-17-18-2-9800.html


## 要件

- Cisco Catalyst 9800シリーズ ワイヤレスLANコントローラ
  - RESTCONFが有効化されていること (IOS XE 16.10以降推奨)
  - HTTP(S) サーバーが有効化されていること
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
```

## 使用方法

1. テンプレートをインポートします。
2. ホストのマクロを設定します。ユーザ名、パスワードは環境にあわせて変更してください。

| マクロ | 説明 | デフォルト値 |
| --- | --- | --- |
| `{$RESTCONF.URL}` | RESTCONFのベースURL。通常変更する必要はありません。 | `https://{HOST.CONN}/restconf/data` |
| `{$RESTCONF.USER}` | RESTCONF接続用ユーザー名 | `admin` |
| `{$RESTCONF.PASS}` | RESTCONF接続用パスワード | `Cisco123` |

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
| CPU Utilization | CPU使用率 (one-minute) | HTTP Agent |
| Memory Utilization | メモリ使用率 (Processor) | HTTP Agent |
| Rogue AP: Total Count | 検知されたRogue APの総数 | HTTP Agent |
| Rogue AP: Total Client Count | Rogue APに接続しているクライアント総数 | HTTP Agent |
| AP {#AP.NAME} ({#FREQ.NAME}): Channel Utilization | AP/周波数ごとのチャネル使用率 (CCA) | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): Client Count | AP/周波数ごとのクライアント数 | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): Noise Level | AP/周波数ごとのノイズレベル | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): RX Utilization | AP/周波数ごとの受信使用率 | Dependent |
| AP {#AP.NAME} ({#FREQ.NAME}): TX Utilization | AP/周波数ごとの送信使用率 | Dependent |
| SSID {#WLAN.NAME}: Client Count | SSIDごとのクライアント数 | Dependent |

### SNMP Trap
リアルタイム通知が必要なイベントはSNMP Trapで受信します。

| 名前 | 説明 | タイプ |
| --- | --- | --- |
| SNMP Trap - Channel Changed | APのチャネル変更トラップ (bsnAPCurrentChannelChanged) | SNMP Trap |
| SNMP Trap - RadarChannelDetected | DFSレーダー検知トラップ (bsnRadarChannelDetected) | SNMP Trap |
| SNMP Trap - AP disassociation | APの切断/再参加トラップ (bsnAPDisassociated / ciscoLwappApAssociated) | SNMP Trap |

## テスト環境
- Cisco Catalyst 9800-L wireless controller
- IOS XE 17.15.4d
