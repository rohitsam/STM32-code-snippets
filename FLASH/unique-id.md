# READING UNIQUE DEVICE ID


The unique device identifier is ideall suited:
* for use as serial numbers (for example USB string serial numbers or other end applications)
* for use as security keys in order to increase the security of code in Flash memory while using and combining this unique ID with software cryptographic primitives and protocols before programming the internal Flash memory
* to activate secure boot processes, etc.

The 96-bit unique device identifier provides a reference number which is unique for any device and in any context. These bits can never be altered by the user.The 96-bit unique device identifier can also be read in single bytes/half-words/words 
in different ways and then be concatenated using a custom algorithm.

Unique ID consists of 3 parts:

    1. X and Y coordinates on the wafer expressed in BCD format
    2. lot number
    3. wafer number

Funtioins for the U_ID [^1]
1. Struct to save the U_ID 
```
#include <stdint.h>

struct u_id {
    uint16_t off0;
    uint16_t off2;
    uint32_t off4;
    uint32_t off8;
};
```
2. Reading the Values

```
#define MMIO16(addr)  (*(volatile uint16_t *)(addr))
#define MMIO32(addr)  (*(volatile uint32_t *)(addr))
#define U_ID          0x1ffff7e8

/* Read U_ID register */
void uid_read(struct u_id *id)
{
    id->off0 = MMIO16(U_ID + 0x0); 
    id->off2 = MMIO16(U_ID + 0x2);
    id->off4 = MMIO32(U_ID + 0x4);
    id->off8 = MMIO32(U_ID + 0x8);
}
```
3. Function to compare the values

```
#include <stdbool.h>

/* Returns true if IDs are the same */
bool uid_cmp(struct u_id *id1, struct u_id *id2)
{
    return id1->off0 == id2->off0 &&
           id1->off2 == id2->off2 &&
           id1->off4 == id2->off4 &&
           id1->off8 == id2->off8;
}

```




U_ID register address in different lines of stm32

|Device line | Starting address|
|------------ | ----------------|
| F0,F3 | 0x1FFFF7AC |
| F1 	  | 0x1FFFF7E8 |
| F2,F4 |	0x1FFF7A10 |
| F7 	  | 0x1FF0F420 |
| L0 	  | 0x1FF80050 |



***
Enjoy :tada:

[^1][Stack over flow](https://stackoverflow.com/questions/29323699/how-to-find-the-device-id-of-a-stm32f103ret)