# Reflow Oven Controller

#### Project Specifications

*Brief description:*  Reflow oven controller system equipped with a user interface comprised of an LCD screen and push buttons which allowed for the pre-set reflow temperature and time profile to be customized. Several [F38x microcontrollers](http://imgur.com/fpRxDN6) were assembled and soldered with the system; they were used in the [line following robot car project](https://github.com/hannahvsawiuk/Line-Following-Robot-Car) described in another repository.

*Subjects:* timers, [interrupts and service routines](http://users.ece.utexas.edu/~valvano/Volume1/E-Book/C12_Interrupts.htm), signal processing, 8051 assembly, [Mealy machine](https://en.wikipedia.org/wiki/Mealy_machine), [pulse width modulation]((https://learn.sparkfun.com/tutorials/pulse-width-modulation)), timer and interrupt prioritization, [analog to digital conversion](https://www.google.ca/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwiYnq3BjuzTAhUC9GMKHd0xCCgQjRwIBw&url=https%3A%2F%2Fwww.nutaq.com%2Fblog%2Fanalog-digital-%25E2%2580%2593-part-2-conversion-process&psig=AFQjCNGV3rRhnGQ_iyBCpZaaDk4g1qbj1Q&ust=1494738897585891), [reflow soldering](https://www.compuphase.com/electronics/reflowsolderprofiles.htm), [macro files](https://www.tutorialspoint.com/assembly_programming/assembly_macros.htm)

*Languages:* [8051 Assembly]( http://www.keil.com/support/man/docs/is51/) for the main code, and Python for [temperature strip chart](https://www.dropbox.com/s/nt0qm0uwofr5zlu/Capture.PNG?dl=0) generation

*Tip:* Since assembly is a low-level language, the use of macro files and include files should be employed to decrease redundancy and improve clarity of the code. They are also incredibly useful during the debugging processing.

*IDE:* [CrossIDE](http://crosside.software.informer.com/)

*Microcontrollers:* [Atmel AT89LP52]( http://www.atmel.com/images/doc3709.pdf)

*Electronic components:* SSR box, [thermocouples]( http://www.thermometricscorp.com/thertypk.html),[operational amplifiers]( http://www.analog.com/media/en/technical-documentation/data-sheets/OP07.pdf), [potentiometer](http://www.resistorguide.com/potentiometer/), LCD screen, [analog to digital converter (ADC)]( https://cdn-shop.adafruit.com/datasheets/MCP3008.pdf), [voltage inverter]( http://www.ti.com/lit/ds/symlink/tl7660.pdf)

*Schematics and diagrams:*</br>
<a href="https://www.dropbox.com/s/8inusqbrt4eife4/circuit1.jpg?dl=0" target="_blank">Circuit photo</a> </br>
Circuit schematics: [1](https://www.dropbox.com/s/4hfuhjtyy643e6z/circuit_diagram2.png?dl=0) [2](https://www.dropbox.com/s/4gagmzkupb865ab/circuit_diagram1.png?dl=0) </br>
[Hardware block diagram](https://www.dropbox.com/s/dxfalct1wr18iq8/Hardware%20Block%20Diagram.png?dl=0) </br>
[Software block diagram](https://www.dropbox.com/s/04m8l1wg80gv9jt/Software%20block%20diagram.png?dl=0)

*Detailed description:* 
###### User Interface and Safety Features: 
The experience working with the system inspired a user-friendly interface for the controller. With an LCD and three pushbuttons, an intuitive, cursor-controlled display was created to select and modify the parameters, including reflow time and temperature. With the pushbuttons, the user could navigate between menus, move the cursor, and accurately increment parameters. While this system increased the ease of use for the operator, it also ensured safety, as the selectable values for these parameters were bounded to prevent unsafe behavior. In addition, 8 a pushbutton allowed the user to manually abort the process at any time; further, an automatic process abortion would occur if the temperature within the oven was over 235°C at any point to ensure the PCB’s would not burn. Also, a process abortion occurred if the temperature was lower than 50°C after one minute. This was put in place to ensure that the thermocouples were placed in the oven properly. 

###### Power Supply and Modulation: 
[Pulse width modulation](https://learn.sparkfun.com/tutorials/pulse-width-modulation) was used to control the power and as a result the temperature of the oven. The pulse width of the output pin signal (input to the SSR box that the oven was plugged into) was determined by the current state of the Mealy machine. Then, the value was assigned in a timer interrupt which reloaded every millisecond. 

##### Reflow Process Controller:
To control the reflow process, a Mealy Machine was used. The inputs to the system were the time and temperature parameters. Once the process begun, the appropriate state information was displayed on the LCD screen. To begin, the oven was preheated by setting the pulse width to 100% of the [duty cycle](https://www.google.ca/url?sa=i&rct=j&q=&esrc=s&source=imgres&cd=&cad=rja&uact=8&ved=0ahUKEwjXkKW1iuzTAhUY9WMKHW5xAQcQjRwIBw&url=https%3A%2F%2Flearn.sparkfun.com%2Ftutorials%2Fpulse-width-modulation%2Fduty-cycle&psig=AFQjCNGkqpQFrvcZ776lDA8NEu3qP_2fcA&ust=1494737814815260). Then, until the soak temperature was achieved, the system remained in preheat. Once the temperature was reached, the soak state would be entered. In soak, the pulse width is set to 40%, and the system remains in soak until the soak time has been completed. The, the process transitions into ramp, which sets the pulse width to 100% until the oven reaches the set reflow temperature and enters the reflow state. Within reflow, the pulse width is decreased to 20%. The process remains in reflow until the reflow time is over. Next, the power is shut off and the process enters cool down states which indicate when the oven is cool enough to open and when the process is finished. The system also indicates with sounds when state transitions occur, when the oven is in cooldown, and when the process has been completed.

##### Op-Amp Gain:
The gain and offset of the operation amplifier was chosen specifically to accurately convert the thermocouple’s voltage readings into temperature values to be received by the controller. Knowing that the maximum voltage that the analog-to-digital converter could safely receive was 5.5 V, appropriate values for the resistors were chosen to ensure that the highest temperature readings would still be safely under that maximum voltage threshold. To achieve the specific value, a potentiometer was used 
to offset the output voltage, since it is an adjustable resistance. 

##### Temperature Reading Validation:
To validate the temperature, a separate temperature device was used to measure room temperature and the results were compared with the thermocouple system. Additionally, the system was validated by completing several tests where the temperature computed by the system was compared to the conversions done from voltages taken with a voltmeter at certain increments

Note: the analog voltages were converted to digital values and then fed into a pin on the AT89 microcontroller, which were converted into temperature values within the code. The temperature is an input to the Mealy machine. An external ADC was necessary since the AT89 did not have one built in.

### [Full Report](https://www.dropbox.com/s/f4rwnuwflpyrxwa/Final%20Report%201.pdf?dl=0) 
