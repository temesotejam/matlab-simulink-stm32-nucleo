# STM32CubeMX Projects

NUCLEO-F446RE の STM32CubeMX `.ioc` 設定ファイルを保存・配布するためのディレクトリです。

実際に使用した `nucleo_f446re_blink.ioc` は、リポジトリの次の配布 ZIP に含めています。

- [NUCLEO-F446RE LED 点滅プロジェクト一式（ZIP）](../downloads/nucleo_f446re_blink_project_r2026b.zip)

主な設定：

- Board: `NUCLEO-F446RE`
- MCU: `STM32F446RETx`
- LD2: `PA5 / GPIO_Output`
- Project Manager: `Do not generate the main()` 有効
- Project Manager: `Generate under root` 無効
- Driver Selector: GPIO / DMA / RCC / USART2 は LL

Simulink モデルから相対パスで参照されます。ZIP 内の `models/` と `cubemx/` のフォルダー構成を維持してください。
