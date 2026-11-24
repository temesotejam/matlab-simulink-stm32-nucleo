# トラブルシューティング記録

NUCLEO-F446RE と MATLAB / Simulink R2026b の初回接続時に発生した問題と、実際に解消した手順を記録します。

## 1. Simulink の展開エラー

### 症状

コード生成と GNU Tools によるコンパイルは成功するが、展開時に次のエラーが出る。

```text
No STM32 MCU connected.
```

### 確認できたこと

- C コード生成成功
- GNU Tools for STM32 によるコンパイル成功
- ELF / HEX / BIN 生成成功
- この時点では ST-LINK / STM32 への接続ができていなかった

この問題は制御モデルや C コードのコンパイルエラーではなく、デバッガ接続の問題だった。


## 2. USB ケーブル交換

最初の STM32CubeProgrammer では次の表示だった。

```text
No ST-LINK...
Error: No debug probe detected.
```

USB ケーブルをデータ通信対応のものに交換すると、ST-LINK のシリアル番号が表示されるようになった。

## 3. Windows での USB 認識確認

PowerShell で次を実行した。

```powershell
Get-PnpDevice -PresentOnly |
    Where-Object { $_.InstanceId -match 'VID_0483' } |
    Format-Table Status, Class, FriendlyName, InstanceId -AutoSize
```

確認結果：

| Status | Class | FriendlyName |
|---|---|---|
| OK | USBDevice | ST-Link Debug |
| OK | USB | USB 大容量記憶装置 |
| OK | USB | USB Composite Device |
| OK | Ports | STMicroelectronics STLink Virtual COM Port (COM20) |

Windows では ST-LINK の複合 USB デバイスと VCP が認識されていた。

## 4. ST-LINK 更新ツールでのエラー

STM32CubeProgrammer 付属の更新ツールでは、次のエラーが発生した。

```text
Error connecting to device ST-LINK/V2-1 (error 0x21)
```

その後、単体配布版の **ST-Link Upgrade 3.18.12** を使用すると ST-LINK と通信でき、ファームウェア情報を読み出せた。

STM32CubeProgrammer付属の更新画面では `error 0x21` が続いたが、単体版の更新画面では現在のファームウェアと更新先を表示できた。

更新画面では次の内容を確認した。

- Current Firmware: `V2J29.M18`
- Update to Firmware: `V2J49.M36`
- Device type: `STM32 Debug + Mass storage + VCP`

`Change Type` は有効にせず、元のデバイスタイプを維持して更新した。


### ファームウェア更新

| 項目 | 結果 |
|---|---|
| 更新前 | V2J29.M18 |
| 更新後 | V2J49.M36 |
| デバイスタイプ | STM32 Debug + Mass storage + VCP |

USB 機能タイプは変更せず、元の Debug + Mass storage + VCP を維持した。

## 5. ST-LINKファームウェア更新後の画面

更新後にSTM32CubeProgrammerでST-LINKのシリアル番号とファームウェアが表示された。

- ST-LINK firmware: `V2J49M36`
- Target voltage: `3.23 V`
- Board: `NUCLEO-F446RE`

ただし、この段階では最初の接続試行で `No STM32 target found` が表示された。ST-LINKのUSB認識と、STM32ターゲットへのSWD接続は別々に確認する必要がある。

## 6. STM32CubeProgrammer での SWD 接続

ファームウェア更新後、次の設定で STM32CubeProgrammer から接続できた。

| 項目 | 設定 |
|---|---|
| Interface | ST-LINK |
| Port | SWD |
| Frequency | 100 kHz |
| Mode | Under reset |
| Reset mode | Hardware reset |

接続成功後に確認できた情報：

| 項目 | 結果 |
|---|---|
| ST-LINK | 認識 |
| ST-LINK firmware | V2J49M36 |
| Board | NUCLEO-F446RE |
| Device | STM32F446xx |
| CPU | Cortex-M4 |
| Flash | 512 KB |
| Target voltage | 3.23 V |
| Flash read | 成功 |

メモリ読み出しログ：

```text
Address: 0x08000000
Size: 1024 Bytes
Data read successfully
```

これにより、ST-LINK から STM32F446RE への SWD 通信が正常に確立できることを確認した。

## 7. Simulink 側の接続設定

STM32CubeProgrammer で接続できた設定に合わせて、Simulink の Build options を次のように変更した。

- Connectivity mode: st-link
- Connection port: SWD
- Frequency: 100 kHz
- Mode: Under reset
- Reset mode: Hardware reset

その後、Simulink からの実機展開に成功した。

## 8. 今後同じ問題が起きた場合の確認順

### WindowsでのST-LINK認識を再確認するコマンド

PowerShell 5.1で次を実行する。

```powershell
Get-PnpDevice -PresentOnly |
    Where-Object { $_.InstanceId -match 'VID_0483' } |
    Format-Table Status, Class, FriendlyName, InstanceId -AutoSize
```

今回の結果では、次の4デバイスがすべて `Status = OK` だった。

- ST-Link Debug
- USB 大容量記憶装置
- USB Composite Device
- STMicroelectronics STLink Virtual COM Port

この結果はWindowsがUSB複合デバイスを認識していることを示すが、STM32ターゲットへのSWD接続成功を保証するものではない。


1. データ通信対応 USB ケーブルを使用しているか。
2. NUCLEO-F446RE の CN1（ST-LINK 側 USB）に接続しているか。
3. Windows デバイスマネージャーで ST-Link Debug が認識されているか。
4. STM32CubeProgrammer で ST-LINK のシリアル番号が表示されるか。
5. ターゲット電圧が読み取れるか。
6. CN2 の 2個のジャンパが装着されているか。
7. JP6（IDD）が装着されているか。
8. JP5 が U5V 側か。
9. ST-LINK ファームウェアの状態を確認する。
10. SWD 100 kHz / Under reset / Hardware reset で接続を試す。

**注意：** ファームウェア更新、Flash 消去、書き込みを行う前に、対象ボードと現在の接続状態を確認する。正常に動作しているボードの Flash を不用意に消去しない。
