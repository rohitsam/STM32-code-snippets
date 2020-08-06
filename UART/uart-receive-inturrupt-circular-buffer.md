# UART-RECEIVE-INTERRUPT-CIRCULAR-BUFFER

1. This the UART-RECEIVE using INTERRUPT method in a circular buffer.

2. This is the USART IRQ handler.


```

volatile char downlink_buffer[DOWNLINK_BUFFER_SIZE];
volatile char received_data;
volatile uint8_t downlink_buffer_index,downlink_startbyte_index;

void USART2_IRQHandler(void)
{
  /* USER CODE BEGIN USART2_IRQn 0 */
	if((__HAL_UART_GET_IT(LoRa_UART_PORT, UART_IT_RXNE) != RESET) &&
										       (__HAL_UART_GET_IT_SOURCE(LoRa_UART_PORT, UART_IT_RXNE) != RESET))
			{
				received_data = (uint8_t)(huart2.Instance->RDR & 0x00FF);

				if(downlink_buffer_index > DOWNLINK_BUFFER_SIZE)
				{
					downlink_buffer_index = 0;
				}

				if(received_data == PACKET_START_BYTE)
				{
					packet_avail = true;
					downlink_startbyte_index = downlink_buffer_index;
					}

				downlink_buffer[downlink_buffer_index] = received_data;
				downlink_buffer_index++;

				__HAL_UART_SEND_REQ(LoRa_UART_PORT, UART_RXDATA_FLUSH_REQUEST);

			}
  /* USER CODE END USART2_IRQn 0 */
  HAL_UART_IRQHandler(&huart2);
  /* USER CODE BEGIN USART2_IRQn 1 */

  /* USER CODE END USART2_IRQn 1 */
}

```

3. USART TO interrupts must be enabled in the main.c


```
  __HAL_UART_CLEAR_OREFLAG(&huart2);
  __HAL_UART_ENABLE_IT(&huart2,UART_IT_RXNE);
  
``` 

***
 This snippet is for STM32's M0 families, It worked :tada: for me on stm32f103,stm32f030,stm32l0,stm32f070.
