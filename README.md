# MATLAB / Simulink + STM32 Nucleo

MATLAB / Simulink から STM32 マイコン向けの制御モデルを作成し、コード生成・ビルド・ST-LINK 経由の実機書き込みを行うための開発環境と手順を記録するリポジトリです。

## 動作確認対象

- ボード: **NUCLEO-F446RE**
- MCU: **STM32F446RETx / STM32F446xx**
- デバッガ: **ST-LINK/V2-1**
- テスト: Simulink から実機へ LED 点滅モデルを展開
- LED: **LD2（PA5）**
- 点滅: 1 秒 ON / 1 秒 OFF

## ダウンロードして実機で動かす

環境構築済みの PC では、次の ZIP をダウンロードして展開すれば、Simulink モデルと STM32CubeMX 設定をまとめて取得できます。

**[NUCLEO-F446RE LED 点滅プロジェクト一式（ZIP）](downloads/nucleo_f446re_blink_project_r2026b.zip)**

1. ZIP を展開し、内部のフォルダー構成を維持します。
2. `models/nucleo_f446re_blink.slx` を MATLAB / Simulink で開きます。
3. STM32CubeMX の `.ioc` ファイルが未解決の場合は、`Ctrl + E` → `Hardware Implementation` → `Target hardware resources` → `Build options` → `Browse...` から `cubemx/nucleo_f446re_blink.ioc` を指定します。
4. NUCLEO-F446RE の CN1（ST-LINK 側 Mini-B USB 端子）を PC に接続し、STM32CubeProgrammer など ST-LINK を使う別アプリを終了します。
5. Simulink の `Hardware` タブから **Build, Deploy & Start** を実行します。
6. 基板上の緑色 LED **LD2** が 1 秒 ON / 1 秒 OFF で点滅することを確認します。

ZIP 内のモデルは、今回の実機動作確認で使用したブロックと設定を保持し、CubeMX ファイルへの参照を PC 固有の絶対パスではなく相対パスにしています。

## 確認済み開発環境

| 項目 | バージョン・設定 |
|---|---|
| OS | Windows 11 Pro 64-bit |
| MATLAB | R2026b Update 1 |
| Simulink | R2026b |
| MATLAB Coder | R2026b |
| Simulink Coder | R2026b |
| Embedded Coder | R2026b |
| STM32 Microcontroller Blockset | R2026b |
| STM32CubeMX | 6.16.0 |
| STM32CubeProgrammer | 2.22.0 |
| コンパイラ | GNU Tools for STM32（`stm32setup` で導入） |
| ST-LINK ファームウェア | V2J49.M36 |

大学の包括契約ライセンスで利用した環境です。ライセンス番号、ライセンスファイル、個人の認証情報はリポジトリに保存しません。

## ドキュメント

- [NUCLEO-F446RE の環境構築・LED 点滅手順](docs/setup-nucleo-f446re-r2026b.md)
- [トラブルシューティングと解決記録](docs/troubleshooting.md)

## 初めて環境を構築する場合

1. まず [セットアップ手順書](docs/setup-nucleo-f446re-r2026b.md) の「0. 全体の作業順序」を読む。
2. 「画面・設定場所の早見表」で操作する画面を確認する。
3. セットアップ手順書を上から順に進める。
4. 接続エラーが発生した場合は [トラブルシューティング](docs/troubleshooting.md) を参照する。

STM32CubeMX Advanced Settings と Simulink Build options の画面配置図は `docs/images/` に保存しています。

## ディレクトリ構成

```text
.
├── README.md
├── .gitignore
├── downloads/
│   └── nucleo_f446re_blink_project_r2026b.zip
├── models/
│   └── README.md
├── cubemx/
│   └── README.md
└── docs/
    ├── setup-nucleo-f446re-r2026b.md
    ├── troubleshooting.md
    └── images/
        ├── build-options-layout.svg
        └── cubemx-advanced-settings-layout.svg
```

## 動作確認済みの範囲

1. MATLAB / Simulink と STM32 Microcontroller Blockset の導入
2. `stm32setup` による STM32F4 系ツールチェーンのセットアップ
3. STM32CubeMX で NUCLEO-F446RE / STM32F446RETx を設定
4. Simulink モデルから C コードを生成
5. GNU Arm Toolchain で ELF / HEX / BIN を生成
6. ST-LINK/V2-1 を介した STM32F446RE との SWD 接続
7. Simulink から実機への書き込み・実行
8. LD2（PA5）の 1 秒 ON / 1 秒 OFF の点滅

## プロジェクト ZIP の内容

- `models/nucleo_f446re_blink.slx`: Pulse Generator と STM32 Digital Port Write を使用した Simulink モデル
- `cubemx/nucleo_f446re_blink.ioc`: NUCLEO-F446RE / STM32F446RETx の CubeMX 設定
- `README.md`: ZIP の展開・実行手順
- `SHA256SUMS.txt`: モデルと IOC の SHA-256

## 注意

- このリポジトリは現在 **Public** です。大学のライセンス情報、パスワード、個人情報、秘密鍵、ライセンスファイルなどをコミットしないでください。
- MathWorks / ST のインストーラーやライセンス対象ソフトウェア本体は再配布せず、公式配布先を参照してください。
- このモデルでは、今回の初回実機書き込み時と同じく `Erase all flash memory before programming` が有効です。Flash 内に残したいプログラムやデータがある場合は、書き込み前に Build options で設定を確認してください。
- ZIP にコンパイル済み `.elf` / `.hex` / `.bin` は含めていません。Simulink が各 PC の GNU Toolchain でビルドし、ST-LINK 経由で書き込みます。
- ZIP には MATLAB / Simulink、Blockset、STM32CubeMX、STM32CubeProgrammer、GNU Toolchain 本体やライセンスは含まれません。
