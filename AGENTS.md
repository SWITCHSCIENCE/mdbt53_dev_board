# リポジトリ運用ガイド

## 目的と対象 SDK

本リポジトリは、スイッチサイエンス製 MDBT53-1M 搭載 nRF5340 開発ボード用の nRF Connect SDK（NCS）ボード定義を作成・保守する。対象は **NCS v3.4.0** とする。HWMv2 の構成とマルチコア対象は `raytac_mdbt53_db_40` を基準にするが、ピン・周辺回路の定義は必ず本ボードの `doc/` を根拠にする。NCS、Zephyr、nRF5340 の仕様・ビルド方法を確認する際は、必ず Nordic MCP を最初に利用する。MCP で情報を取得できない場合を除き、SDK ツリーや Web を直接検索しない。

## 構成

リポジトリ直下は現行の HWMv2 ボード定義であり、`<BOARD_ROOT>/boards/ssci/mdbt53_dev_board/` に配置する。`board.yml` がボードと SoC を定義し、`*_nrf5340_cpuapp.dts`、`*_cpuapp_ns.dts`、`*_cpunet.dts` が各ターゲットの DeviceTree 定義である。`reference/ncs-v2.4.0/ssci_mdbt53_dev_board/` は v2.4.0 向けの旧ボード定義であり、参照専用とする。`doc/` には BOM、ネットリスト、商品情報があり、回路・部品・ピン変更時の一次資料として扱う。

## 静的確認

ビルド、フラッシュ、および実機での動作確認はユーザーが行う。エージェントはこれらを実行せず、変更対象のファイルと設定の整合性を静的に確認する。ユーザーが実施する確認用コマンドは、次の形式を案内する。

```powershell
west build -b ssci_mdbt53_dev_board/nrf5340/cpuapp <アプリのパス> -- -DBOARD_ROOT=<BOARD_ROOT>
west build -b ssci_mdbt53_dev_board/nrf5340/cpuapp/ns <アプリのパス> -- -DBOARD_ROOT=<BOARD_ROOT>
west build -b ssci_mdbt53_dev_board/nrf5340/cpunet <アプリのパス> -- -DBOARD_ROOT=<BOARD_ROOT>
west flash -d <ビルドディレクトリ>
```

`<BOARD_ROOT>` は `boards/` を直接含むディレクトリである。DeviceTree、Kconfig、パーティション定義の変更では、参照先、識別子、include 順、YAML 構文、対応する `cpuapp`、`cpuapp/ns`、`cpunet` 定義を確認する。ユーザーがビルドする場合は、既存成果物を再利用せずクリーンなビルドディレクトリを使用する。専用テストスイートは確認できないため、実機検証の要否と対象周辺機能を変更内容に明記する。

## 記法と変更方針

C は既存の Zephyr スタイルに合わせ、タブでインデントし、関数・変数は `snake_case` とする。既存の SPDX ヘッダーは維持し、コメントにはハードウェア上の根拠を記す。DTS のラベルは小文字を使用する。Kconfig では `config` のシンボル名を大文字の `BOARD_*` とし、設定値の参照には `CONFIG_` 接頭辞を用いる。YAML のインデント、DTS の include 順、既存のボード名パターンを崩さない。

## レビューと安全性

コミットは `board: CPUAPP のピン設定を修正` のように短い命令形で、1 件のハードウェア上の変更に限定する。PR には対象コア、回路上の影響、静的確認の内容、ユーザーが実施すべきビルド・実機検証を記載する。ピン、電源、初期化優先度、パーティション、セキュリティドメインの変更は、BOM・ネットリスト・商品情報と照合し、実機検証対象として明示する。
