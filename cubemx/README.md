# STM32CubeMX Projects

STM32CubeMX の `.ioc` 設定ファイルを保存するディレクトリです。

NUCLEO-F446RE の初回 LED 点滅プロジェクト：

```text
nucleo_f446re_blink.ioc
```

主な設定：

- Board: NUCLEO-F446RE
- MCU: STM32F446RETx
- LD2: PA5 / GPIO_Output

現時点では実際の `.ioc` ファイルは未登録です。STM32CubeMX で保存したファイルをこのディレクトリに追加してください。

CubeMX が生成したファイル群のうち、再現・ビルドに必要なものは内容を確認してから管理対象に追加します。ライセンス対象のツール本体や個人情報は登録しません。
