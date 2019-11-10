# STM32-Embedded-System-Lab

This repository was created because the course ~~is so interesting~~ sucks.

## Table of contents
- [STM32-Embedded-System-Lab](#stm32-embedded-system-lab)
	- [Table of contents](#table-of-contents)
	- [Getting Started](#getting-started)
	- [TIM timer characteristics](#tim-timer-characteristics)
	- [GPIOx PinCode](#gpiox-pincode)
		- [LED Light Pin](#led-light-pin)
		- [Button Pin](#button-pin)
		- [Useful function in HAL library for GPIOs](#useful-function-in-hal-library-for-gpios)
	- [UART](#uart)
		- [Set up your ioc](#set-up-your-ioc)
		- [Useful statement to use with UART:](#useful-statement-to-use-with-uart)
	- [Interrupt](#interrupt)
		- [Button interrupt](#External-interrupt-via-Button)
			- [set up in ioc](#set-up-in-ioc)
			- [interrupt callback function](#interrupt-callback-function)
		- [Timer interrupt](#timer-interrupt)
			- [set up ioc](#set-up-ioc)
	- [Timer](#timer)
	- [PWM](#pwm)
	- [ADC](#adc)
	- [RTOS](#rtos)
- [Example of some labs](https://github.com/tongplw/STM32-Embedded-System-Lab/blob/master/example.md)

## Getting Started

You have to download STM32CubeIDE First. Then, select board STM32F4DISCOVERY and select debugger as J-LINK (OR ST-Link).

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

## GPIOx PinCode

### LED Light Pin

| LED | GPIOD_PD |
| ------------- | ------------- |
| Green (LD4)  |  PD12 	|
| Orange (LD3)  |  PD13	|
| Red (LD5)  | 	PD14 	|
| Blue (LD6)  | PD15 |

### Button Pin

| Button | GPIOA_PA |
| ------------- | ------------- |
| User button (blue button)  |  PA0 |


### Useful function in HAL library for GPIOs

x in GPIOx for A, B, C, D eg. "GPIOA", "GPIOD"

```C
// Read pin
// HAL_GPIO_ReadPin(GPIOx, GPIO_PIN_xx);
HAL_GPIO_ReadPin(GPIOD, GPIO_PIN_12); // read from PD12
HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0); // read from user button

// Write pin
// HAL_GPIO_WritePin(GPIOx, GPIO_PIN_xx, GPIO_PIN_SET/GPIO_PIN_RESET);
HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12, GPIO_PIN_SET); //turn on light

// Toggle on/off pin
// HAL_GPIO_TogglePin(GPIOx, GPIO_PIN_xx);
HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);	// toggle pin 12

// Delay
// HAL_Delay(uint32_t Delay);
HAL_Delay(1000); // delay for 1000 millisecond or 1 second
```

## GPIO Register

| Register | Responsibility |
----------------|--------------
| Port Mode Register ***(MODER)*** | configure the I/O direction mode (input/output/alternate/analog) |
| Port Mode Register ***(MODER)*** | configure the I/O direction mode (input/output/alternate/analog) |
| Output Type Register ***(TYPER)*** | configure the output type of the I/O port (push-pull/open-drain) |
| Output Speed Register ***(OSPEEDR)*** | configure the I/O output speed (2/25/50/100 MHz) |
| Pull-Up/Pull-Down Register ***(PUPDR)*** | configure the I/O pull-up or pull-down (no pull-up, pull-down/pull-up/pull-down) |
| Input Data Register ***(IDR)*** | contain the input value of the corresponding I/O port |
| Output Data Register ***(ODR)*** | can be read and written by software (ODR bits can be individually set and reset by writing to the BSRR) |
| Bit Set/Reset Register ***(BSRR)*** | can be used for atomic bit set/reset |
| Configuration Lock Register ***(LCKR)*** | used to lock the configuration of the port bits when a correct write sequence is applied to bit 16 |
| Alternate Function Low Register ***(AFRL)*** | configure alternate function I/Os |
| Alternate Function High Register ***(AFRH)*** | configure alternate function I/Os |

## Debounce

```C
int state = 0;
int push = 0;

if (state == 0) {
	
	...

	if (HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0) == HAL_OK && !push) {
		push = 1;
	}
	if (!HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0) == HAL_OK && push) {
		push = 0;
		state = 1;
	}
} else if (state == 1) {
	
	...

	
	if (HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0) == HAL_OK && !push) {
		push = 1;
	}
	if (!HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0) == HAL_OK && push) {
		push = 0;
		state = 0;
	}
}
```
* Another way
```C
if(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0)){
	while (HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0)) {

	}
}
```

## UART
**Universal Asynchronous Receiver and Transmitter**

### Set up your ioc

* Connectivity >> USART2 >> Mode (Asynchronous)
* Connect the wire from RX (UART) to TX (Board) and from TX (UART) to RX (Board)
* Connect the ET-MINI USE-TTL Board to Computer
* Open terminal (e.g. Tera Term)
* [Download Tera Term](https://osdn.net/projects/ttssh2/releases/)
* **_Very fricking important:_** set serial port to **115200** in Tera Term

### Useful statement to use with UART:

**Receive and print char one by one to terminal:**
```c
// Private variable
UART_HandleTypeDef huart2;	// program generated


// User code
while(1){

	// User variable
	char buffer[1];

	if (HAL_UART_Receive(&huart2, buffer, sizeof(buffer), HAL_MAX_DELAY) == HAL_OK){
		HAL_UART_Transmit(&huart2, buffer, sizeof(buffer), HAL_MAX_DELAY);
	}
}
```


**Print whole string at terminal:**

```C
// Private variable
UART_HandleTypeDef huart2;	// program generated

// User variable
int myValue;
char buffer[20];

while(1){

	// sprintf(char *, char format, arguments...)
	sprintf(buffer, "%d \r\n", myValue);	

	HAL_UART_Transmit(&huart2, &buffer, strlen(buffer), HAL_MAX_DELAY); // Transmit
}
```
## Interrupt

### External interrupt via Button
#### Set up in ioc
* System Core >> GPIO >> GPIO tab >> PA0 >> External Interrupt Mode with your prefered edge trigger detection
* System Core >> GPIO >> NVIC tab >> Enable EXTI line0 interrupt
* System Core >> NVIC >> NVIC tab >> set EXTI line0 interrupt preemptive priority to higher value (Higher number means lower priotity) 
* Note : if you can't change the priority value, change priority group to higher bits
#### Interrupt callback function
```c
// Write this function in your main.c 
// External interrupt/event controller (EXTI)
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin){
	
	... // do something

	HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_15);
	while (HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0)); // Debounce
    	HAL_Delay(100);
	__HAL_GPIO_EXTI_CLEAR_IT(GPIO_Pin);
}
```

### Timer interrupt
#### Set up ioc
* TIMx >> Set up PWM >> ([see PWM](#pwm))
* TIMx >> Parameter Setting >> Set up parameter([see Timer](#timer))
* TIMx >> auto-reload preload >> Enable
* TIMx >> NVIC Settings >> Enable TIM3 Global Interrupt or System Core >> NVIC >> Enable TIMx Global Interrupt

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim) {
	if (htim->Instance == TIM2) HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_14);
	if (htim->Instance == TIM3) HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);
}

int main(void)
{
	// Init()
	
	HAL_TIM_PWM_Start(&htim2, TIM_CHANNEL_1);
  	HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);
  	HAL_TIM_Base_Start_IT(&htim2);
  	HAL_TIM_Base_Start_IT(&htim3);
	while (1) { ... }
}
```
### Timer interrupt with LED
#### Set up ioc
* Pinout View >> Choose LED channel (PD12 to PD14) >> Change it to TIM4_CHx
* TIM4 >> Set up PWM >> ([see PWM](#pwm))
* TIM4 >> Parameter Setting >> Set up parameter([see Timer](#timer))
* LED will blink in the frequency that you have set.

## Timer

<p align="center">
  <img width="440" height="64" src="https://github.com/tongplw/STM32-Embedded-System-Lab/blob/master/res/formula.jpg">
</p>

* TIMx->CNT - Clock Counter (For ioc: Clock Configuration >> APB1 Timer clocks)
* TIMx->PSC - Prescaler Value (For ioc: TIMx >> Parameter Setting >> Prescaler)
* TIMx->ARR - Period Value (For ioc: TIMx >> Parameter Setting >> Counter Period)
* TIMx->CCRx - PWM for Channel x (For ioc: TIMx >> Parameter Setting >> PWM Generation Channel x >> Pulse)
* Tips : If you set APB1 Timer clocks = 16 MHz, Prescaler = 15999, you will be able to set Counter Period and Pulse in millisec.

### 

## PWM 
**Pulse Width Modulation**

* TIMx >> Clock Source >> Internal Clock
* TIMx >> Channelx >> PWM Generation CHx

```C
// Private variable
TIM_HandleTypeDef htim4; // program generated

// Don't forget to write this start method
HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_3);

while (1) {
	TIM4->CCR3 = myPulse; // If you want to change PWM
}
```

## ADC 
**Analog to Digital Converter**

* Select your prefered Pin (e.g. PC3, PC2) and set mode to ADCxIN_x (e.g. ADC1_IN13)
* Go to Analog >> ADCx >> check INx



```C
// Private variable
ADC_HandleTypeDef hadc1; // program generated

int adcValue;

while (1) {
	HAL_ADC_Start(&hadc1);
	if (HAL_ADC_PollForConversion(&hadc1, 1000000) == HAL_OK) {
		adcValue = HAL_ADC_GetValue(&hadc1);
    	}
}
```

## Serial Communication Interface

### SPI
**Serial Peripheral Interface Bus**

* Connectivity >> SPI2
	* For transmitter select `Transmit Only Master`
	* For receiver select `Receive Only Slave`
* Connect `SCK` and `MOSI`

``` C
// Transmitter
HAL_SPI_Transmit(&hspi1, "a", 1, HAL_MAX_DELAY);

// Receiver
HAL_SPI_Receive(&hspi1, "a", 1, HAL_MAX_DELAY);
```

### I2C
**Inter-Integrated Circuit Bus**

* Connectivity >> I2C1
	* Select `I2C`
* Connect `SLC` and `SDA`
* Set some `{address}`

``` C
// Transmitter
HAL_I2C_Master_Transmit(&hi2c1, {address}, "a", 1, HAL_MAX_DELAY);

// Receiver
HAL_I2C_Slave_Receive(&hi2c1, {address}, "a", 1, HAL_MAX_DELAY);
```

## RTOS
**Real Time Operating System**

- Read through [this link](https://os.mbed.com/handbook/CMSIS-RTOS) for additional infomation!
- Middleware >> FREERTOS >> Interface (CMSIS_1)

### RTOS Thread (Task)

- Task and Queue >> Task >> Add

``` C
...
void StartDefaultTask(void const * argument);
...

int main(void)
{
	...
	osThreadDef(defaultTask, StartDefaultTask, osPriorityNormal, 0, 128);
	defaultTaskHandle = osThreadCreate(osThread(defaultTask), NULL);
	osKernelStart();
	...
	while (1) { ... }
}

void StartDefaultTask(void const * argument)
{
	/* USER CODE BEGIN */ 
	
	/* USER CODE END */ 
}
```

### RTOS Timer

- Config parameters >> software timer definitions >> USE_TIMERS (Enabled)
- Timers and Semaphores >> Timers >> Add

``` C
...
osTimerId myTimer01Handle;
void Callback01(void const * argument);
...

int main(void)
{
	...
	osTimerDef(myTimer01, Callback01);
	myTimer01Handle = osTimerCreate(osTimer(myTimer01), osTimerPeriodic, NULL);
	osTimerStart(myTimer01Handle, 25);
	...
    while (1) { ... }
}

void Callback01(void const * argument)
{
  /* USER CODE BEGIN Callback01 */

  /* USER CODE END Callback01 */
}
```

### Semaphore
```C
osSemaphoreWait(myBinarySem01Handle, 10000);

osSemaphoreRelease(myBinarySem01Handle);
```


### TODO: Message Queue/Mailbox


## Acknowledgement

- Thank you for all of contributors including me ([Tong](https://github.com/tongplw), [Pin](https://github.com/BackgroundBoy), [Charn](https://github.com/ctkgit) and [Terk](https://github.com/terktanac)).
- Thanks to all CP44 friends for help gathering all quizes and skill-tests.
