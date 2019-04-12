##Hydration Station Documentation

##Hardware 









##Software 

 
	Class to get values from a HX711 Load Cell amplifier with scaling and taring
	Pin PD_SCK (clockPin) and DOUT (dataPin) are used for data retrieval, input selection, 
	gain selection and power down controls.  The HX711 outputs data on the the dataPin
	and the Pi controls the clock signal on the clock pin. 
	
	When the Pi holds the clockPin high for greater than 60 ms, the HX711 goes into low power mode, 
	holding the dataPin high.
	
	When the Pi sets the dataPin low, the HX711 wakes up, but does not have new data ready until 0.5 seconds
	after turning on. 
	
	
	When output data are not ready for retrieval, digital output pin DOUT is high.
	       _                                                         _
	____| |_____________________________________| |_________________________
	with clock pin held low, (not reading any data) data pin pulses are 100 usec, frequency is 11.2 Hz (90 msec spacing)
	each high-to-low transution indicates a new value is available from the HX711
	
	The data pin goes high when data has been read, and only goes low when new data is available
	___      _   _         _     ______________________
	   |____| |_| |_____| |__|                                 |__Data Pin
	
	
	___|||||||||||||||||||||||||________________________|| Clock Pin
	
	
	By applying 25~27 positive clock pulses at the clock pin, data is shifted out from the data output pin.
	Each clock pulse shifts out one bit, starting with the MSB bit first, until all 24 bits are shifted out.
	The 25th pulse at clock input will pull data pin back to high.
	Input and gain selection is controlled by adding a number of extra input clock pulses to the train
	after the data is collected
	clock Pulses   	Input channel   Gain
	25               		A              	128
	26               		B              	32
	27               		A              	64
	This code always runs the HX711 with high gain, input channel A, by using 25 pulses. 
	This maps onto a pulsedThread train with 25 pulses with 1 us delay and duration time
		
	Data is 24 bit two's-complement differential signal
	min value is -8388608, max value is 8388607
