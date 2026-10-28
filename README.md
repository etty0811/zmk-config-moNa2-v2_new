# zmk-config-moNa2（DYA Studio 対応版）

moNa2（右手トラックボール・PAW3222）の ZMK ファームウェア設定です。[DYA Studio](https://studio.dya.cormoran.works/) に対応しており、ファームウェアを書き換えずにブラウザからキーマップ・マクロ・コンボ・トラックボール・接続先などを変更できます。

- ZMK: [cormoran/zmk `main+dya`](https://github.com/cormoran/zmk/tree/main%2Bdya)（Zephyr 4.1）
- ボード: `xiao_ble/nrf52840/zmk`（Seeed XIAO nRF52840）
- 右手側が central（PC・DYA Studio と通信する側）、左手側が peripheral
- ベース: [sayu-hub/zmk-config-moNa2-v2 `feature/dya-studio-support`](https://github.com/sayu-hub/zmk-config-moNa2-v2/tree/feature/dya-studio-support) に、自分のキーマップとトラックボール設定を移植

## DYA Studio でできること

| タブ | 内容 | 使用モジュール |
| --- | --- | --- |
| Keymap | キー割り当て・レイヤーの編集、押下キーの表示、ロータリーエンコーダーの割り当て | ZMK Studio / fast-keymap / input-stream / module-physical-layout / runtime-sensor-rotate |
| Macro / Combo | マクロ・コンボの作成と編集 | runtime-macro / runtime-combo |
| Trackball | カーソル速度・回転・軸反転・スクロール・オートマウス | runtime-input-processor |
| Mouse Gesture（拡張） | マウスジェスチャーの設定 | zmk-mouse-gesture / mouse-gesture-rpc |
| Connection | BLE プロファイル管理、接続先・OS ごとのデフォルトレイヤー | ble-management / default-layer / os-detection |
| Battery | バッテリー残量の履歴 | battery-history |
| Settings | 各種設定 | settings-rpc / custom-settings / zephyr-setting-expose |
| Troubleshooting | ファームウェア情報、再起動原因、キースイッチ診断、スタック使用量 | device-info / watchdog / kscan-diagnostics / devtool / studio-rpc-perf |

## 書き込み手順

ZMK のバージョンが大きく変わるため、初回は設定のリセットから行ってください。

1. GitHub の Actions から最新ビルドの `firmware` をダウンロードして展開する
2. 左右それぞれに `settings_reset.uf2` を書き込む（リセットボタンを 2 回押してドライブに入れる）
3. 左手側に `mona2_l.uf2`、右手側に `mona2_r.uf2` を書き込む
4. PC / スマホ側に残っている古い「mona2」のペアリングを削除し、ペアリングし直す

## DYA Studio の使い方

1. Chrome / Edge で <https://studio.dya.cormoran.works/> を開く
2. **右手側**を USB でつなぐ（または Bluetooth で接続する）
3. キーボードで Studio Unlock を実行する
   - 左親指の LANG2 キー（キー位置 26、`&lt 8 LANG2`）を押したまま Bluetooth レイヤーに入り、右手の H の位置のキー（キー位置 16、`&studio_unlock`）を押す
4. 編集して保存する

一定時間操作しない、または切断すると再びロックされます。

## トラックボールの初期設定

以前のファームウェアと同じ向き・量になるよう初期値を入れています。いずれも DYA Studio の Trackball タブから変更できます。

| processor | 役割 | 初期値 |
| --- | --- | --- |
| `mouse` | カーソル移動 | 等倍、X・Y 反転。オートマウスは無効（有効にするとレイヤー 7 MOUSE を 600ms 有効化） |
| `scroll` | レイヤー 2（scroll）の間だけカーソル移動をスクロールに変換 | 1/8 倍、270° 回転 + X 反転（以前の「XY 入れ替え → 反転」と同じ向き） |

## 補足

- コンボは `config/mona2.keymap` の `runtime_combo_defaults` に初期値として定義しています（M1 / M2 / M3 / win / mac / PCopen）。Combo タブに表示され、そのまま編集できます（最大 16 個）。Studio で変更したコンボは Studio 側の値が優先され、「Reset to Default」でこの初期値に戻ります。
- ロータリーエンコーダーはレイヤーごとに初期値を定義しています（基本はスクロール、scroll レイヤーは音量、windows2 は左右スクロール）。Studio で割り当てたレイヤーは Studio 側の値が優先されます。
- Web の Keymap Editor はコンボとエンコーダーの定義を扱えなくなります。コンボとエンコーダーは DYA Studio から変更してください。
- PAW3222 用のビルドのみ出力しています。PMW3610 の個体で使う場合は `build.yaml` から `cmake-args` の行を消すと PMW3610 版になります（DYA Studio の「トラックボールセンサー」タブは PMW3610 版のみ）。
- DYA Studio のプレビューに出るトラックボールとロータリーエンコーダーの位置は目安です（`mona2.dtsi` の `trackball_layout` / `left_encoder_layout`）。
- `config/west.yml` の各モジュールは `main` ブランチなどを追従します。上流の変更でビルドが通らなくなった場合は、動いていたコミットに `revision` を固定してください。
- DYA Studio 対応前の状態は `main` ブランチに残しています。

## 参考

- [DYA Studio 開発者ガイド](https://studio.dya.cormoran.works/developer-guide)
- [cormoran/zmk-config-dya-studio-sample](https://github.com/cormoran/zmk-config-dya-studio-sample)
- [sayu-hub/zmk-config-moNa2-v2](https://github.com/sayu-hub/zmk-config-moNa2-v2)
