# Wet Dreams Guaranteed

#### Created by Vince Ly and Christian Santiago

---

## Project Overview

Experience an innovative wake-up solution designed to ensure you start your day with a splash. This autonomous alarm clock utilizes advanced sensors and a pressurized water system to locate sleepers and deliver a targeted wake-up call.

## System Overview

Our unique system combines state-of-the-art ultrasonic sensors and a pressurized water mechanism to locate and wake you effectively. 

![Project Assembly](/path-to-image/Assembly.png)

## Detailed Component Breakdown

### mBed LPC1768 (Controller)
Serves as the brain of our system, executing the software to control other components through its I/O ports. [More info](#).

### HiLetGo LM317 Voltage Regulators
These maintain stable voltage levels for the system, ensuring consistent performance and protecting the circuitry. [More info](#).

### Ultrasonic Distance Sensor HC-SR04
Detects the distance to the nearest object, critical in mapping the sleeper's position for targeted water delivery. [More info](#).

### DEVMO MG995 Continuous Servo Motors
Provide the mechanical actuation needed to adjust the water nozzle's aim with precision. [More info](#).

### GikFun Mini DC 6V-12V Water Diaphragm Pump
Pumps the water through the system, enabling the ejection mechanism to work effectively. [More info](#).

### JBTek 4 Channel DC 5V Relay Module
Allows for controlling high power devices, such as the water pump, with low voltage signals from the controller. [More info](#).

### SPST Momentary Pushbutton Switches
Used for user input, allowing for manual control over alarm settings.

### uLCD-144-G2 LCD Screen
Displays the system's status and provides an interface for user interaction.

![Final Project Schematic](/path-to-image/FinalProjectSchematic.png)

For the links marked with [#], please replace with the actual URLs where more information about the parts can be found. Ensure you provide the correct link references to detailed product pages or datasheets where readers can get more information about each component.



## Implementation

Step-by-step details on how we brought this project to life, including coding practices, circuit design, and assembly instructions.

## Conclusion and Future Work

We believe our system revolutionizes the morning routine. With potential expansions and user feedback, the possibilities are endless.

For a full list of components, please refer to the [Parts List](/path-to-document/Parts_List.pdf).

---

Feel free to fork, star, or contribute to our project. Let's change the way we wake up!

