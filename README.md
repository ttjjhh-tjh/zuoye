电控组第一次作业

板卡：STM32F103C8T6（LQFP48，64 KB Flash / 20 KB RAM），HSE = 8 MHz，系统时钟 = 72 MHz 工具：STM32CubeMX 6.18.1 + STM32Cube FW_F1 V1.8.7；Keil MDK 5.43（Arm Compiler 6.24） 接线：PA13/SWDIO、PA14/SWCLK、GND、3V3

1-1  LED 500ms 闪烁（HAL_Delay 版）
位置：1-1/Core/Src/main.c 的 while(1) 内
操作；通过HAL_Delay(500) 阻塞延时
现象；实现亮 500ms、灭 500ms（周期 1s）

1-2 LED 500ms 闪烁（TIM2 时基，非阻塞）
位置；1-2/Core/Src/main.c 的 main函数前，while(1)内，main函数后
操作；Cubemax配置时基TIM2(PSC=71,ARR=999) 通过中断里 Time++计时，同时主循环比较 Time - last >= 500够了翻转电平并更新last
现象；实现亮 500ms、灭 500ms（周期 1s）

1-3 EXTI 按键控制 LED（20ms 软件消抖）
位置；1-3/Core/Src/main.c 的 main函数前，while(1)内，main函数后
操作；同1-2配置时基TIM2(PSC=71、ARR=999)，并配置PA0 → GPIO_EXTI0(GPIO_MODE_IT_FALLING,GPIO_PULLUP),通过 keyFlag的值判断按钮状态，主循环判断 Time - lastKeyTime > 20(消抖) ，再读引脚判断电平高低
现象；实现按下亮灯

