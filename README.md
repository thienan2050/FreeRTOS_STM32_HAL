# Redirect printf() to USB Virtual COM Port on STM32F103C8T6 MCU using STM32CubeMX

Enable printf() function to work with USB Virtual COM Port (STM32F103C8T6).

## STM32CubeMX

Generate Source Code for USB
1. Choose the suitable chip.
2. System Core -> SYS -> Debug : Serial Wire | Timebase Source : TIM4
3. System Core -> RCC -> High Speed Clock (HSE) : Crystal/Ceramic Resonator
4. System Core -> NVIC -> System tick timer : 15 | Time base : TIM4 global interrupt
5. Connectivity -> USB -> Device(FS)
6. Middleware -> USB_DEVICE -> Class For FS IP : Communication Device Class (Virtual Port Com)

## Edit Source Code
# main.c
1. #include <stdio.h>
2. Add the code below between /* USER CODE BEGIN 4 */
```C
int _write(int file, char *ptr, int len) 
{ 
    CDC_Transmit_FS((uint8_t*) ptr, len); 
    return len; 
}
```
That's all! Now you can use printf() anywhere you want. Let's try.

## NOTICE!
Sometimes, printf() displays an incorrect value. Digging a bit, I found out that if you delay a little bit after CDC_Transmit_FS(), then everything works fine, so:

```C
int _write(int file, char *ptr, int len) 
{ 
    CDC_Transmit_FS((uint8_t*) ptr, len); 
    HAL_Delay(1);
    return len; 
}
```


