# PRINTF()-implement-for-UART

1. To be copied in the header file


```
#include <stdarg.h>
#include <stdint.h>
#include <stdio.h>
void vprint(const char *fmt, va_list argp);
void Debug_tx(const char *fmt, ...);

```

2. To be copied in the source file.


```
#include "usart.h"
#include "uart_debug.h"
#define PRINTF_UART hlpuart1

void vprint(const char *fmt, va_list argp)
{
    char string[200];
    if(0 < vsprintf(string,fmt,argp)) // build string
    {
        HAL_UART_Transmit(&PRINTF_UART, (uint8_t*)string, strlen(string), 0xffffff); // send message via UART
    }
}
void Debug_tx(const char *fmt, ...) // custom printf() function
{
    va_list argp;
    va_start(argp, fmt);
    vprint(fmt, argp);
    va_end(argp);
}
```



