

#### Created by Vince Ly and Christian Santiago

---

## Project Overview

Experience an innovative wake-up solution designed to ensure you start your day with a splash. This autonomous alarm clock utilizes advanced sensors and a pressurized water system to locate sleepers and deliver a targeted wake-up call.

## System Overview

Our unique system combines state-of-the-art ultrasonic sensors and a pressurized water mechanism to locate and wake you effectively. 

![Project Assembly](fullProject.jpg)




## Detailed Component Breakdown

![](mbed2.png)
### mBed LPC1768 (Controller)
Serves as the brain of our system, executing the software to control other components through its I/O ports. [More info](https://os.mbed.com/platforms/mbed-LPC1768/).



![](voltageRegulator.jpg)
### HiLetGo LM317 Voltage Regulators
These maintain stable voltage levels for the system, ensuring consistent performance and protecting the circuitry. [More info](https://www.amazon.com/gp/product/B07VJDPZ2L/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1).



![](ultrasonicSensor.jpg)
### Ultrasonic Distance Sensor HC-SR04
Detects the distance to the nearest object, critical in mapping the sleeper's position for targeted water delivery. [More info](https://www.sparkfun.com/products/15569).



![](servoMotor.jpg)
### DEVMO MG995 Continuous Servo Motors
Provide the mechanical actuation needed to adjust the water nozzle's aim with precision. [More info](https://www.amazon.com/gp/product/B07X3S6FM2/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1).



![](waterPump.jpg)
### GikFun Mini DC 6V-12V Water Diaphragm Pump
Pumps the water through the system, enabling the ejection mechanism to work effectively. [More info](https://www.amazon.com/gp/product/B0744FWNFR/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1).



![](relayModule.jpg)
### JBTek 4 Channel DC 5V Relay Module
Allows for controlling high power devices, such as the water pump, with low voltage signals from the controller. [More info](https://www.amazon.com/gp/product/B00KTEN3TM/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1).



![](pushbuttons.jpeg)
### SPST Momentary Pushbutton Switches
Used for user input, allowing for manual control over alarm settings. [More info](https://os.mbed.com/users/4180_1/notebook/pushbuttons/).



![](uLCD.png)
### uLCD-144-G2 LCD Screen
Displays the system's status and provides an interface for user interaction. [More info](https://os.mbed.com/users/4180_1/notebook/ulcd-144-g2-128-by-128-color-lcd/).



![Final Project Schematic](FinalProjectSchematic.png)


[Watch our project demo](https://www.youtube.com/watch?v=fC7QabEXuDo).


## Implementation

Step-by-step details on how we brought this project to life, including coding practices, circuit design, and assembly instructions.

![](initial_frame.JPG)
##  Step 1: Framework
We had spare 6mm acrylic on hand that can be acquired through local hardware stores such as Ace and Home Depot, with a large piece we were able to make the base, roof, and front panel of the alarm clock using clear cast acrylic and a few U-bolts.

![](initialElectronics.JPG)
##  Step 2: Electronics
From there we mounted the voltage reduction circuit to power the entire project externally which caused a few issues when wanting to use the relay. We got weird behavior where if we did not power the LPC1768 with the 5V USB supply the entired mBed would freeze up and not post our initial GUI for the display. We decided to just use a 5V LM317 to power the peripherals of the LCD and Ultrasonic and also made a 6V LM317 to supply the relay board, servos, and diaphram pump mentioned earlier.

The LCD was mounted using velcro tape in order to troubleshoot more readily around the frame. Along with the pushbuttons being placed on the same panel. These pushbuttons use the mBed's 3.3 volt output to configure logic active high which was set internally in the program.
Following the schematic the servos, ultrasonic, and LCD can be wired using appropriate extensions whenever needed.

![](Assembly.png)
##  Step 3: Linear Rail System
For moving in the X and Y axis direction we decided to model and 3D print a linear rail system using our own carriage design. This design utilizes two servos that would mount directly to the carriage and allow the utlrasonic sensor to move freely along a grid that will be configured within the software. 
![](gearRail3dprint.JPG)
SUPER IMPORTANT: Use a high quality printer when printing these parts as we ran into a LOT of clearance issues that required a large amount of grinding, cutting, and filing to even move the Y-axis linear rail that fits into the T-slot. Be wary of this or even take a simpler route with using extruded aluminum with T-slot bolts.

![](carriage2.JPG)
##  Step 4: Attaching all 3D parts together
When placing the rails and carriages a lot of holes need to be drilled into the frame. We used a 5.5mm drill bit and 5mm bolts to piece everything together while using 3 inch long #10-32 to hold the carriage together. The servos were mounted to the carriages afterwards. IMPORTANT: Tighten the servos only with the driving gears compressed onto the teeth to allow for smooth operation when moving the linear rails in X and Y direction.
It should be noted that the acrylic mount that is shown in the figure is due to the entire top piece of our carriage snapping off. We were able to get around this by laser cutting a new servo mount panel and directly bolting it into the excess extrusion that did not snap off. Also to note is the T-rail is completely mauled. That is because of the terrible printing quality on the linear rails and the T-slot itself. We abandoned the design and decided to just use the pressure from the drive gear to hold the linear rail down which made it quite wobbly, but functional.

![](ultrasonicMount.JPG)
##  Step 5: Extra Parts Attachment to Frame
The pump, ultrasonic sensor, water tank (old candle case), and relay board were all bolted on or adhered on to secure a firm and functional product. Fuel line would have worked perfect for the diaphragm pump but we only had oversized pneumatic hose on hand so we used that with some hose clamps and were able to yield a pretty could result although not a perfect seal. This will drastically impact the intensity at which the water can shoot out so it is definitely important to keep a good seal and get a proper size. For the ultrasonic sensor we drilled through-holes on the end of the Y-axis plate which turned out absolutely perfect and fully functional which then led us straight into coding after following the schematic.

![](SoftwareUML.png)
##  Step 6: Software
The program follows a pretty simple but tedious format in which in main() after a large instantiation of objects there is a constant displayTime() and checkAlarm(). Both of these have conditionals that would activate the alarm routine in which a grid matrix with a predefined resolution is created and populated with ultrasonic values at each x and y value essentially "mapping" out the entire area with distance values. If we follow the assumption that the sleeper is the closest to the sensor (and does not have the fluffiest pillow known to man) then the routine of finding the lowest value and sending the pipe to that x,y coordinate works perfectly! As also shown through the demo.

## Conclusion and Future Work

We believe our system revolutionizes the morning routine. With potential expansions and user feedback, the possibilities are endless.

For a full list of components, please refer to the [Parts List](Parts_List.pdf).

---

Feel free to fork, star, or contribute to our project. Let's change the way we wake up!

