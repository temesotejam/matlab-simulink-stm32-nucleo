# Simulink Models

NUCLEO-F446RE の LED 点滅モデルを保存・配布するためのディレクトリです。

実際に使用したモデル `nucleo_f446re_blink.slx` は、リポジトリの次の配布 ZIP に含めています。

- [NUCLEO-F446RE LED 点滅プロジェクト一式（ZIP）](../downloads/nucleo_f446re_blink_project_r2026b.zip)

モデルの仕様：

- 出力先: NUCLEO-F446RE の LD2（PA5）
- ブロック: Pulse Generator → STM32 Microcontroller Blockset / Digital Port Write
- 周期: 2 秒（1 秒 ON / 1 秒 OFF）
- Sample time: 0.01 秒
- Build action: Build, load and run

Simulink のコード生成キャッシュやビルド出力（`slprj/`、`*_ert_rtw/`、`*.slxc` など）は原則としてコミットしません。
