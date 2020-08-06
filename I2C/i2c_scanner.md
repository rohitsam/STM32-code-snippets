# I2C SCANNER

This is the first function I would be calling, This scanner test will confirm.

* The Wiring COnnection 
* The Sensor Itself.



```
uint8_t scan_devices(void){
	uint8_t i=0;
	for(i=0;i<0xFF;i++){
	if(HAL_I2C_IsDeviceReady(&I2C_PORT,i,5,200) == HAL_OK)
		return i;

	}
	return 0xFF;
}



```

***


Enjoy :tada: