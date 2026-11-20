# STM32F103C8T6 第一次作业

电控组 32 阶段培训 · 第一次作业（GPIO / EXTI）

## 开发环境

| 项目 | 版本 |
|---|---|
| 代码生成 | STM32CubeMX 6.18.1 |
| 固件库 | STM32Cube FW_F1 V1.8.7 |
| IDE | Keil MDK 5.43（Arm Compiler 6.24） |
| 芯片 | STM32F103C8T6（LQFP48，64KB Flash / 20KB RAM） |

## 硬件接线

| 信号 | 引脚 | 接线 |
|---|---|---|
| LED | PA5 | PA5 → 220Ω 电阻 → LED 长脚，LED 短脚 → GND |
| 按键 | PA0 | PA0 → 按键一端，按键另一端 → GND（内部上拉，下降沿触发） |
| 下载 | SWD | ST-Link 3V3 / SWDIO / SWCLK / GND → 板端 4 针 |

## 任务与实现

### 任务 1：LED 500ms 闪烁（HAL_Delay 版）

- 位置：`GPIO/Core/Src/main.c` 的 `while (1)` 内
- 思路：循环里翻转 LED，再用 `HAL_Delay(500)` 阻塞等待
- 现象：亮 500ms、灭 500ms（一个完整周期 1s）
- 说明：HAL_Delay 阻塞期间 CPU 空转，按键无响应 —— 这正是任务 2 要解决的问题

### 任务 2：LED 500ms 闪烁（不用 HAL_Delay / HAL_GetTick）

> TODO：完成后补充

- 时基：TIM2，PSC = 71、ARR = 999 → 1ms 中断，`msCnt` 计数
- 思路：主循环比较 `msCnt - last >= 500`，够了就翻转并更新 `last`
- 关键点：中断里只做 `msCnt++`；变量加 `volatile`；用减法比较（计数器回绕也正确）

### 任务 3：EXTI 按键控制 LED（20ms 软件消抖）

> TODO：完成后补充

- 配置：PA0 → GPIO_EXTI0，内部上拉，下降沿触发，NVIC 使能 EXTI line0
- 思路：`HAL_GPIO_EXTI_Callback` 里只记时间戳/置标志；主循环判断距上次有效动作 ≥ 20ms 才翻转
- 关键点：中断里不做延时；标志用完要清

### 选做

> TODO：完成情况填写（短按长按切换常亮/快闪 5Hz；双击翻转）

## 目录说明

```
1/
├─ README.md
├─ .gitignore
└─ GPIO/                  CubeMX 工程根目录
   ├─ GPIO.ioc            CubeMX 配置文件
   ├─ Core/               main.c / gpio.c / tim.c / 中断入口
   ├─ Drivers/            HAL 库 + CMSIS
   └─ MDK-ARM/            Keil 工程（GPIO.uvprojx）
```

## 说明

- 工程由 CubeMX 生成，业务代码全部写在 `USER CODE BEGIN/END` 区域内
- 提交内容只含源码与工程配置，编译产物（Objects / *.axf / *.hex / *.uvguix.*）已通过 `.gitignore` 排除
