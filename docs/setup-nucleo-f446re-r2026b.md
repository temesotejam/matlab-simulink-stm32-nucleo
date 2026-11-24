# NUCLEO-F446RE + MATLAB / Simulink R2026b セットアップ手順

この文書は、Windows 11 上で MATLAB / Simulink を使用し、NUCLEO-F446RE に Simulink モデルを書き込んで実行するまでの手順です。

最初の動作確認として、基板上の緑色 LED **LD2（PA5）** を点滅させます。

## 1. 使用機材

- STM32 Nucleo-64: **NUCLEO-F446RE**
- MCU: **STM32F446RETx（STM32F446xx）**
- オンボードデバッガ: **ST-LINK/V2-1**
- PC: Windows 11 Pro 64-bit
- USB ケーブル: データ通信対応 Mini-B USB ケーブル
- 接続: ボード上の ST-LINK 用 USB 端子 CN1

最初の試験では、モータ、サーボ、外部電源、外部センサを接続せず、ボード単体で確認します。

## 2. 使用ソフトウェア

今回確認した環境は次のとおりです。

| 製品 | 確認したバージョン |
|---|---|
| MATLAB | R2026b Update 1（26.2.0.3433840） |
| Simulink | R2026b |
| MATLAB Coder | R2026b |
| Simulink Coder | R2026b |
| Embedded Coder | R2026b |
| STM32 Microcontroller Blockset | R2026b |
| STM32CubeMX | 6.16.0 |
| STM32CubeProgrammer | 2.22.0 |
| GNU Tools for STM32 | `stm32setup` で導入 |
| ST-LINK ファームウェア | V2J49.M36 |

MATLAB の大学包括契約ライセンスでセットアップしました。ライセンス番号などの個人情報は記録しません。

### MATLAB 製品の確認

MATLAB コマンドウィンドウで実行します。

```matlab
version -release
ver
```

最終的に `ver` に MATLAB、Simulink、MATLAB Coder、Simulink Coder、Embedded Coder、STM32 Microcontroller Blockset が表示されることを確認します。

ライセンス利用可否は次で確認できます。

```matlab
license('test','MATLAB_Coder')
license('test','Real-Time_Workshop')
license('test','RTW_Embedded_Coder')
```

今回の環境では3つとも `1` でした。これはライセンス利用可否の確認であり、製品のインストール状態そのものを示すものではありません。

## 3. STM32 Hardware Setup

MATLAB コマンドウィンドウで起動します。

```matlab
stm32setup
```

今回のセットアップで行ったこと：

1. **STM32F4xx Based MCUs** を選択。
2. Armclang Compiler は任意項目のためスキップ。
3. STM32CubeMX を導入。
4. STM32CubeProgrammer を導入。
5. STM32CubeCLT は今回の LED 点滅・基本的な展開には不要な任意項目としてスキップ。
6. GNU Tools for STM32 をセットアップ。

Armclang を使用せず、GNU Arm Toolchain でビルドしました。実際のビルドログには `GNU Tools for STM32` と `arm-none-eabi-gcc` が記録されています。

## 4. NUCLEO-F446RE の USB / ジャンパ確認

USB ケーブルを抜いた状態で基板を確認します。

| 部品 | 今回確認した設定 |
|---|---|
| CN1 | ST-LINK 側 USB 端子を使用 |
| CN2 | 2個のジャンパを装着 |
| JP5 | U5V 側（USB 給電） |
| JP6（IDD） | ジャンパ装着 |
| JP1 | OFF |

基板上の電源 LED（LD3）が点灯することも確認します。ジャンパを変更するときは USB ケーブルを抜いてください。

## 5. STM32CubeMX プロジェクト

Simulink のモデル設定から次の場所を開きます。

`Target hardware resources → Build options → Create...`

作成したプロジェクト：

- Board: `NUCLEO-F446RE`
- MCU: `STM32F446RETx`
- IOC ファイル名: `nucleo_f446re_blink.ioc`

作成後、同じ Build options の `Launch...` から STM32CubeMX を開きます。

### 5.1 Pinout & Configuration

NUCLEO-F446RE の LD2 は PA5 に接続されています。

- Pin: `PA5`
- Mode: `GPIO_Output`

### 5.2 Project Manager

今回案内した設定：

| 項目 | 設定 |
|---|---|
| Do not generate the main() | 有効 |
| Generate under root | 無効 |

### 5.3 Advanced Settings

今回の画面で確認した Driver Selector：

| Peripheral | Driver |
|---|---|
| GPIO | LL |
| DMA | LL |
| RCC | LL |
| USART2 | LL |

Generated Function Calls の設定：

- `Generate Code`: 対象行で有効
- `Do Not Generate Function Call`: 無効（チェックなし）
- `Visibility (Static)`: 無効（チェックなし）

設定後は STM32CubeMX で `Ctrl + S` を押して保存し、STM32CubeMX を閉じて Simulink に戻ります。通常の Simulink ビルド時にコード生成・ビルドを行うため、初回設定時に STM32CubeMX の `GENERATE CODE` を別途押す必要はありません。

## 6. Simulink モデル

モデル名：

`nucleo_f446re_blink.slx`

### 6.1 Hardware Implementation

Hardware board は STM32F4 系の設定を選択します。

今回のモデル設定画面では `STM32F4xx Based` を使用しました。

### 6.2 Build options

今回使用した設定：

| 項目 | 設定 |
|---|---|
| Build action | Build, load and run |
| Runtime library | Reduced C |
| Floating-point ABI | hard |
| Connectivity mode | st-link |
| Connection port | SWD |
| Frequency | 100 kHz |
| Mode | Under reset |
| Reset mode | Hardware reset |
| Speed | Reliable |
| Auto detect board to download and run | 有効 |

最初は 4000 kHz / Normal / Software reset で接続エラーが発生しました。STM32CubeProgrammer で接続に成功した 100 kHz / Under reset / Hardware reset に合わせた後、Simulink からの実機展開に成功しました。

**注意：** `Erase all flash memory before programming` が有効な場合、書き込み前に MCU の Flash が消去されます。既存ファームウェアを残す必要がある場合は、この設定と書き込み内容を事前に確認してください。

### 6.3 Solver

| 項目 | 設定 |
|---|---|
| Type | Fixed-step |
| Solver | discrete (no continuous states) |
| Fixed-step size | 0.01 |
| Stop time | inf |

### 6.4 Pulse Generator

| 項目 | 設定 |
|---|---|
| Pulse type | Sample based |
| Amplitude | 1 |
| Period (number of samples) | 200 |
| Pulse width (number of samples) | 100 |
| Phase delay | 0 |
| Sample time | 0.01 |

100サンプル × 0.01秒 = 1秒なので、1秒 ON、1秒 OFF の周期になります。

### 6.5 Digital Port Write

STM32 Microcontroller Blockset の STM32F4 系ライブラリから Digital Port Write を配置します。

| 項目 | 設定 |
|---|---|
| Port name | GPIOA |
| Pin number | 5 |
| Access port pins as array | 無効 |

Pulse Generator の出力を Digital Port Write の入力へ接続します。

## 7. ビルド・実機書き込み

1. NUCLEO-F446RE を CN1 から PC に接続。
2. STM32CubeProgrammer など、ST-LINK を占有するアプリケーションを終了。
3. Simulink モデルを保存。
4. Hardware タブから `Build, Deploy & Start` を実行。
5. ビルド・書き込みの完了を確認。
6. LD2 が 1秒点灯 / 1秒消灯を繰り返すことを確認。

## 8. 初回ビルドで記録した結果

初回のコード生成・コンパイルログでは、次の結果が得られました。

| 項目 | 結果 |
|---|---:|
| Flash 使用量 | 5,600 bytes |
| Flash 容量 | 512 KB |
| RAM 使用量 | 1,696 bytes |
| RAM 容量 | 128 KB |

生成されたファイル：

- `nucleo_f446re_blink.elf`
- `nucleo_f446re_blink.hex`
- `nucleo_f446re_blink.bin`

初回はバイナリ生成後、ST-LINK 接続エラーで展開に失敗しました。接続問題を解消した後、Simulink からの実機書き込み・実行に成功しました。

## 9. 次の実験候補

- GPIO 入力（USER ボタン）
- GPIO 出力
- PWM 出力
- ADC 取得
- UART 通信
- I2C 通信
- PID 制御
- EKF / 状態推定

次の実験は LED 点滅モデルを基準として複製し、機能を一つずつ追加します。
