# Switch Science MDBT53-1M nRF5340 Development Board

スイッチサイエンス製、Raytac MDBT53-1M 搭載 nRF5340 開発ボード用の nRF Connect SDK (NCS) HWMv2 ボード定義です。対象 SDK は **NCS v3.4.0** です。

## 配置

このリポジトリを、`boards/` を含むディレクトリ（`BOARD_ROOT`）配下へ次のように配置します。

```text
<BOARD_ROOT>/boards/ssci/mdbt53_dev_board/
```

例えば NCS を `C:\ncs` に配置している場合、ボード定義のパスは `C:\ncs\boards\ssci\mdbt53_dev_board` です。

## ボードターゲット

| 用途 | ボード名 |
| --- | --- |
| Application core (Secure) | `ssci_mdbt53_dev_board/nrf5340/cpuapp` |
| Application core (Non-Secure / TF-M) | `ssci_mdbt53_dev_board/nrf5340/cpuapp/ns` |
| Network core | `ssci_mdbt53_dev_board/nrf5340/cpunet` |

## ビルド例

`<BOARD_ROOT>` は `boards/` の親ディレクトリです。既存の生成物を使わないよう、初回または設定変更後は `-p always` を指定してください。

```powershell
# CPUAPP: LED 点滅
west build -p always -b ssci_mdbt53_dev_board/nrf5340/cpuapp zephyr/samples/basic/blinky -- -DBOARD_ROOT=<BOARD_ROOT>

# CPUAPP Non-Secure: TF-M を含む構成
west build -p always -b ssci_mdbt53_dev_board/nrf5340/cpuapp/ns zephyr/samples/basic/blinky -- -DBOARD_ROOT=<BOARD_ROOT>

# CPUNET: IPC Radio
west build -p always -b ssci_mdbt53_dev_board/nrf5340/cpunet nrf/applications/ipc_radio -- -DBOARD_ROOT=<BOARD_ROOT>
```

`cpuapp/ns` では TF-M と Non-Secure アプリを含む `tfm_merged.hex` がフラッシュ対象です。ビルド後に `zephyr/runners.yaml` の `hex_file: tfm_merged.hex` を確認してください。

## 入出力

- ユーザー LED: `P1.11`（Active Low、`led0`）
- ユーザーボタン: `P1.10`（プルアップ、Active Low、`sw0`）
- UART0: TX `P0.20`、RX `P0.21`、RTS `P0.19`、CTS `P0.22`、115200 bps

外部 USB-UART 変換器を使う場合は、TX/RX をクロス接続し、GND を共通にしてください。既定のコンソールはハードウェアフロー制御を有効化していないため、端末側はフロー制御なしに設定します。

## 注意事項

回路・ピン割り当ての一次資料は `doc/` の BOM、ネットリスト、商品情報です。ボード定義を変更する場合はこれらと照合してください。TF-M、パーティション、ピン、電源周辺の変更後は、クリーンビルドと実機確認を推奨します。
