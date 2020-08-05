# Jump-to-user-code

stm32 Custom Bootloaders always requires you this little peice of code snippet for jumping from 
your bootloader code to the application code.

## BOOTLOADER CODE

### WHAT CODE DOES?

1.pointing the function pointer to the reset handler of the user application.

```
	uint32_t reset_handler_add = *((volatile uint32_t *) (FLASH_SECTOR2_BASE_ADDRESS+4)); 
	void (*app_reset_handler)(void) = (void*) reset_handler_add;
```
2.Disable RCC, set it to default (after reset) settings Internal clock, no PLL, etc
```
	HAL_RCC_DeInit();
	HAL_DeInit();
```

3.Disabling Systick timer and loading it with the default values.

```
	SysTick->CTRL = 0x0;
	SysTick->LOAD=0;
	SysTick->VAL=0;
```

4. Remapping the vector table offset

`	SCB->VTOR = FLASH_SECTOR2_BASE_ADDRESS;`

5. Setting the MSP value and calling the reset handler of the the user-application.

```
	uint32_t msp_value = *((volatile uint32_t *)FLASH_SECTOR2_BASE_ADDRESS);
	__set_MSP(msp_value);
	app_reset_handler();
```


```
#define FLASH_SECTOR2_BASE_ADDRESS 0x08008000

void jump_to_user_app1(void)
{
    
	uint32_t reset_handler_add = *((volatile uint32_t *) (FLASH_SECTOR2_BASE_ADDRESS+4)); 
	void (*app_reset_handler)(void) = (void*) reset_handler_add;
	HAL_RCC_DeInit();
	HAL_DeInit();
	SysTick->CTRL = 0x0;
	SysTick->LOAD=0;
	SysTick->VAL=0;
	SCB->VTOR = FLASH_SECTOR2_BASE_ADDRESS;
	uint32_t msp_value = *((volatile uint32_t *)FLASH_SECTOR2_BASE_ADDRESS);
	__set_MSP(msp_value);
	app_reset_handler();
	
}

```


## USER CODE

1. Change the *Vector Table base offset field* in the system_stm32f4xx.c file with the first 2 bytes of the new flash sector.

`#define VECT_TAB_OFFSET  0x8000 `

2. Change the origin address flash memory in the linker script of your user-application priject.

`FLASH (rx)      : ORIGIN = 0x08008000, LENGTH = 512K`




[^1]: This snippet is for STM32's M0+ families, It worked fine for me on stm32f411.
[^2]: I will update the code for other family aswell.


                                   










