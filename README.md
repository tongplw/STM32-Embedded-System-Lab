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
* Open Tera Term

```C
// Private variable
UART_HandleTypeDef huart2;


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
