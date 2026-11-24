# Simulink Models

動作確認に使用した Simulink モデルを保存するディレクトリです。

初回 LED 点滅モデルは次の名前で保存する想定です。

```text
nucleo_f446re_blink.slx
```

このモデルは NUCLEO-F446RE の LD2（PA5）を 1秒 ON / 1秒 OFF で点滅させるものです。

現時点では実際の `.slx` ファイルは未登録です。MATLAB から保存したファイルをこのディレクトリに追加してください。

Simulink のコード生成キャッシュやビルド出力（`slprj/`、`*_ert_rtw/`、`*.slxc` など）は原則としてコミットしません。
