# USER-CODE-TO-READ-AND-WRITE-FROM-FLASH-MEMORY

In most cases we do require to save config parameters to some memory.
Since Most stm32 controllers doesn't have any internal eeprom, we can make use of the available Flash to write the data.

1. > save_data(uint32_t Address,uint32_t data) 
takes 32bit address and data as args


```
#include "stm32f0xx_hal.h"


void save_data(uint32_t Address,uint32_t data){

    HAL_FLASH_Unlock();
	FLASH_EraseInitTypeDef EraseInitStruct;
	EraseInitStruct.TypeErase = FLASH_TYPEERASE_PAGES;
	EraseInitStruct.PageAddress = Address;
	EraseInitStruct.NbPages = 1;

	uint32_t PageError;
	if (HAL_FLASHEx_Erase(&EraseInitStruct, &PageError) != HAL_OK)		//Erase the Page Before a Write Operation
			return HAL_ERROR;

	HAL_Delay(50);
	HAL_FLASH_Program(FLASH_TYPEPROGRAM_WORD,Address,(uint64_t)data);
	HAL_Delay(50);
	HAL_FLASH_Lock();


}

```

2. > read_data(uint32_t Address)
takes the Address and returns the data at that address.

```
uint32_t read_data(uint32_t Address){

	__IO uint32_t read_data = *(__IO uint32_t *)Address;
	return (uint32_t)read_data;
}

```

***
Must work with all Families of stm32 :tada: