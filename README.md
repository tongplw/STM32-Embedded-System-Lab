# STM32-Embedded-System-Lab
This repository was created because the course sucks.

## Getting Started

You have to download STM32CubeIDE First. Then, select board STM32F4DISCOVERY and select debugger as J-LINK.

[See Getting Started in Lab1](https://github.com/tongplw/STM32-Embedded-System-Lab/blob/master/Lab1%20Basic%20MCU%20development%20on%20STM32/Lab1-Main.pdf)

## TIM timer characteristics

Each TIM uses different clock source frequency according to the table provided below.

| APB1 Domain | APB2 domain |
| ------------- | ------------- |
| TIM2  | TIM1 	|
| TIM3  | TIM8 	|
| TIM4  | TIM9 	|
| TIM5  | TIM10 |
| TIM6  | TIM11 |
| TIM7  | 		|
| TIM12 | 		|
| TIM13 | 		|
| TIM14 | 		|

You can set the clock frequency by going to "Clock Configuration" and place value in the box as shown below.

<p align="center">
  <img src="https://github.com/tongplw/STM32-Embedded-System-Lab/blob/master/resource/APB1%20APB2.jpg">
</p>
