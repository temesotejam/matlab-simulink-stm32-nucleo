# MATLAB / Simulink + STM32 Nucleo

MATLAB / Simulink から STM32 マイコン向けの制御モデルを作成し、コード生成・ビルド・ST-LINK 経由の実機書き込みを行うための開発環境と手順を記録するリポジトリです。

## 現在の動作確認対象

- ボード: **NUCLEO-F446RE**
- MCU: **STM32F446RETx / STM32F446xx**
- デバッガ: **ST-LINK/V2-1**
- 確認したテスト: Simulink で作成した LED 点滅モデルの実機展開
- LED: **LD2（PA5）**

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
| コンパイラ | GNU Tools for STM32（stm32setup で導入） |
| ST-LINK ファームウェア | V2J49.M36 |

大学の包括契約ライセンスで利用した環境です。ライセンス番号、ライセンスファイル、個人の認証情報はリポジトリに保存しません。

## ドキュメント

- [NUCLEO-F446RE の環境構築・LED 点滅手順](docs/setup-nucleo-f446re-r2026b.md)
- [トラブルシューティングと解決記録](docs/troubleshooting.md)

## 初めて再現する場合

1. まず [セットアップ手順書](docs/setup-nucleo-f446re-r2026b.md) の「0. 全体の作業順序」を読む。
2. 「画面・設定場所の早見表」で操作する画面を確認する。
3. セットアップ手順書を上から順に進める。
4. 接続エラーが発生した場合は [トラブルシューティング](docs/troubleshooting.md) を参照する。

STM32CubeMX Advanced Settingsの画面配置図は `docs/images/` に保存しています。

## ディレクトリ構成

```text
.
├── README.md
├── .gitignore
├── docs/
│   ├── setup-nucleo-f446re-r2026b.md
│   ├── troubleshooting.md
│   └── images/
│       └── cubemx-advanced-settings-layout.svg
├── models/
│   └── README.md
└── cubemx/
    └── README.md
```

## 動作確認済みの範囲

1. MATLAB / Simulink と STM32 Microcontroller Blockset の導入
2. `stm32setup` による STM32F4 系ツールチェーンのセットアップ
3. STM32CubeMX で NUCLEO-F446RE / STM32F446RETx を設定
4. Simulink モデルから C コードを生成
5. GNU Arm Toolchain で ELF / HEX / BIN を生成
6. ST-LINK/V2-1 を介した STM32F446RE との SWD 接続
7. Simulink から実機への書き込み・実行

## 実際のモデルファイルについて

現時点では、動作確認に使用した `.slx` と `.ioc` の実ファイルはまだこのリポジトリに登録していません。後から以下に追加してください。

- Simulink モデル: `models/nucleo_f446re_blink.slx`
- STM32CubeMX 設定: `cubemx/nucleo_f446re_blink.ioc`

実ファイルを追加するまでは、ドキュメントに記載した設定値を再現手順として利用してください。

## 注意

このリポジトリは現在 **Public** です。大学のライセンス情報、パスワード、個人情報、秘密鍵、ライセンスファイルなどをコミットしないでください。また、MathWorks / ST のインストーラーやライセンス対象ソフトウェア本体は再配布せず、公式配布先を参照してください。
