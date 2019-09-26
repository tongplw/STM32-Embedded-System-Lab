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
| TIM7  |	|
| TIM12 |	|
| TIM13 |	|
| TIM14 |	|

## Might Be Useful

### UART (Universal Asynchronous Receiver and Transmitter)

* Connectivity >> USART2 >> Mode (Asynchronous)
* Connect the wire from RX to TX and from TX to RX
* Connect the ET-MINI USE-TTL Board to Computer
* Open terminal (e.g. Tera Term)
* [Tera Term](https://osdn.net/projects/ttssh2/releases/)
* ***Very fricking important:*** set serial port to **115200**

#### Useful statement to use UART:
```C
// Private variable
UART_HandleTypeDef huart2;

// transmit and print at terminal
int myValue;
char buffer[20];
sprintf(buffer, "%d \r\n", myValue);
HAL_UART_Transmit(&huart2, &buffer, strlen(buffer), 1000000);
```

### PWM (Pulse Width Modulation)

* Set Clock Source > Internal Clock
* Set Channel > PWM Generation CHX

```C
// Private variable
TIM_HandleTypeDef htim4;


HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_3);
while (1) {
	TIM4->CCR3 = myPulse;
}
```

### ADC (Analog to Digital Converter)

* Go to Analog >> ADC1 >> (Check) IN1



```C
// Private variable
ADC_HandleTypeDef hadc1;
uint32_t ADCValue;

while (1) {
	HAL_ADC_Start(&hadc1);
	if (HAL_ADC_PollForConversion(&hadc1, 1000000) == HAL_OK) {
		myVariable = HAL_ADC_GetValue(&hadc1);
    	}
}
```

### GPIO LED light Pinn
| LED | PD |
| ------------- | ------------- |
| Green(LD4)  |  PD12 	|
| Orange(LD3)  |  PD13	|
| Red(LD5)  | 	PD14 	|
| Blue(LD6)  | PD15 |

#### Useful function in HAL library for GPIOs

x in GPIOx for A,B,C,D eg. "GPIOA", "GPIOD"

| Description | Function |  Example  |
| ------------- | ------------- | ------------- |
| Read  pin  | `HAL_GPIO_ReadPin(GPIOx,Pin);`  | `HAL_GPIO_ReadPin(GPIOD, PD12); # read from PD12` |
| Write pin | `HAL_GPIO_WritePin(GPIOx, Pin, GPIO_PIN_SET/GPIO_PIN_RESET);` | `HAL_GPIO_WritePin(GPIOD, PD12, GPIO_PIN_SET); # turn on light` |  
| Toggle on/off pin  | `HAL_GPIO_TogglePin(GPIOx, Pin);` | `HAL_GPIO_TogglePin(GPIOD, PD12);` |

###
