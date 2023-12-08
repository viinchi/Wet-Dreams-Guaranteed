

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

```
#include "mbed.h"
#include "rtos.h"
#include "wave_player.h"
#include "uLCD_4DGL.h"
#include "ultrasonic.h"
#include "Matrix.h"
#include "PinDetect.h"
#include "Servo.h"

//Variables
int alarmHour = 10;  // Default alarm time: 07:00 AM
int alarmMinute = 39;
int resolution = 10;
int minDistance = 10000; // Initialize with maximum possible value
int minX = 0;
int minY = 0;    
//servoStepSize needs 7s to go across whole x axis rail
float XservoStepSize = 6.5/resolution;
float YservoStepSize = 7.0/resolution;
// Temp Distance Storage for Ultrasonic
int currDistance = 0;
// Status to activate the locate and wake up routine
bool status = false;
//Pushbutton Declarations
PinDetect pb1(p23); 
PinDetect pb2(p24);
PinDetect pb3(p25);

//Definiton for the x and y servos, ultrasonic sensor, and matrix grid
Servo xServo(p21);
Servo yServo(p22);
Matrix m1(resolution, resolution);
uLCD_4DGL uLCD(p28, p27, p29); // TX, RX, RESET
// Relay Extra for appropriate usage
DigitalOut relay(p26, 1); 
Mutex lcd_mutex;

void dist(int distance) {
    currDistance = distance;
}

ultrasonic eyes(p6, p7, .1, 1, &dist);

void pb1_hit_callback (void) {
    // Increment alarm minute
    alarmMinute = (alarmMinute + 1) % 60;
}

void pb2_hit_callback (void) {
    // Increment alarm Hour
    alarmHour = (alarmHour + 1) % 24;
}

void pb3_hit_callback (void) {
    // Activate and Deactivate Alarm
    status = !status;
}

void findFace() {
    // Servo movement of x and y direction that calculates all ultrasonic values
    for (int y = 1; y < resolution; y++) {
        for (int x = 1; x < resolution; x++) {

            // Move x-axis forward for duration XservoStepSize then stop
            xServo.write(0.2);
            wait(XservoStepSize);
            xServo.write(0.5);
            wait(0.2);

            // Pings in and out the Ultrasonic to update currDistance Value
            eyes.checkDistance();
            m1.add(x, y, currDistance);

            // Display Ultrasonic Distance
            lcd_mutex.lock();
            uLCD.text_width(1); // Set text width
            uLCD.text_height(1); // Set text height
            uLCD.locate(0,14);
            uLCD.printf("Dist: %d cm  ", currDistance);
            lcd_mutex.unlock();
            wait(0.2);

        }

        // Move xServo back to starting position
        xServo.write(0.8);
        wait(XservoStepSize * resolution);
        xServo.write(0.5);
        wait(0.2);

        // Move yServo over one matrix tick
        yServo.write(0.2);
        wait(YservoStepSize);
        yServo.write(0.5);
        wait(0.2);

    }
    yServo.write(0.8);
    wait(YservoStepSize*resolution);
    yServo.write(0.5);
    wait(1);
}

void wakeUp() {

    // Iterate through the matrix to find the minimum distance
    for (int y = 1; y < resolution; y++) {
        for (int x = 1; x < resolution; x++) {
            int distanceM = m1.getNumber(x, y);
            if (distanceM < minDistance) {
                minDistance = distanceM;
                minX = x;
                minY = y;
            }
        }
    }

    xServo.write(0.2);
    wait(XservoStepSize*minX);
    xServo.write(0.5);
    wait(0.2);

    yServo.write(0.2);
    wait(YservoStepSize*minY - YservoStepSize);
    yServo.write(0.5);
    wait(3);

    relay = !relay;
    wait(10);
    relay = !relay;
    wait(0.5);

    xServo.write(0.8);
    wait(XservoStepSize*minX - YservoStepSize);
    xServo.write(0.5);
    wait(0.2);

    yServo.write(0.8);
    wait(YservoStepSize*minY - YservoStepSize);
    yServo.write(0.5);
    wait(0.2);
}

void displayTime() {
    lcd_mutex.lock();
    time_t currentTime = time(NULL);
    struct tm *localTime = localtime(&currentTime);

    // Set font size for current time
    uLCD.text_width(2); // Double the text width
    uLCD.text_height(2); // Double the text height
    // Display current time
    uLCD.color(BLUE);
    uLCD.locate(0,0);
    uLCD.printf("%02d:%02d:%02d\n", localTime->tm_hour, localTime->tm_min, localTime->tm_sec);

    // Display "Alarm" text larger and centered
    uLCD.text_width(2); // Set text width
    uLCD.text_height(2); // Set text height
    uLCD.color(WHITE);
    const char* alarmText = "Alarm";
    uLCD.locate(0, 3); // Adjust vertical position
    uLCD.printf("%s ", alarmText); // Space added after "Alarm"

    // Display alarm status next to "Alarm"
    if (status) {
        uLCD.color(GREEN);
        uLCD.printf("ON_");
    } else {
        uLCD.color(RED);
        uLCD.printf("OFF");
    }

    // Display alarm time
    uLCD.color(WHITE); // Reset color to white
    uLCD.locate(0,5); // Adjust vertical position for alarm time
    uLCD.printf("%02d:%02d\n", alarmHour, alarmMinute);

    lcd_mutex.unlock();
    wait(0.2); // Update interval
}

void checkAlarm() {
    time_t currentTime = time(NULL);
    struct tm *localTime = localtime(&currentTime);

    if (localTime->tm_hour == alarmHour && localTime->tm_min == alarmMinute && status == true) {
        // Trigger alarm routine
        findFace();
        wait(0.5);
        wakeUp();
        wait(0.5);
        //m1.Clear();
        //wait(0.5);
    }
}

int main() {
    set_time(1661510400);

    pb1.mode(PullDown);
    pb2.mode(PullDown);
    pb3.mode(PullDown);
    //Wait for mode assertion to take place
    wait(.01);
    // Setup Interrupt callback functions for a pb hit
    pb1.attach_deasserted(&pb1_hit_callback);
    pb2.attach_deasserted(&pb2_hit_callback);
    pb3.attach_deasserted(&pb3_hit_callback);
    // Start sampling pb inputs using interrupts
    pb1.setSampleFrequency();
    pb2.setSampleFrequency();
    pb3.setSampleFrequency();

    while (1) {
        eyes.startUpdates();
        displayTime();
        checkAlarm();
    }
}
```

[Watch our project demo](https://www.youtube.com/watch?v=fC7QabEXuDo).

## Conclusion and Future Work

We believe our system revolutionizes the morning routine. With potential expansions and user feedback, the possibilities are endless.
However, that does not mean we do not see the need for a large amount of improvements. One would be using a lot higher quality printer to make all the parts and avoid all the extra fabrication we had to do due to the time crunch we were under. Printing took up 4-5 days which is where we lost a lot of time but we were able to make it work. If anything a possible redesign of how the X and Y axis rails could save a lot of time if unable to access a high quality printer that doesn't warp parts. 
Better fitting and hoses can be used but if using what we used ourselves we still got the results we wanted that can be seen through the dmeo video. 

## Hardware improvements for future
Due to resolution quality of 3D printer a lot of pieces did not come out perfect. Initially in the design the linear rails are T-slot driven which were extremely warped post 3-day print.​

Another issue that arose in terms of hardware was the need for a larger LCD display as the screen to body ratio was extremely large.​

An addition of pushbuttons can further this project to have more functionality for a better user experience.​

Swap out small pump for air compressor? :)

## Software Improvements for Future
Currently the software does not have a way to change the current time due to time constraints and lack of pushbuttons. ​

The LCD (due to not being programmed in a way to utilize threads) stops updating the time when scanning the area which can also be improved.​

The scanning routing when populating the matrix can be changed to be continuous instead of repeatedly stopping and starting to save time.​

Need to swap out ultrasonic sensor for something more precise as the sensor is not as accurate as the internet says per our tests on a variety of objects.


For a full list of components, please refer to the [Parts List](Parts_List.pdf).

---

Feel free to fork, star, or contribute to our project. Let's change the way we wake up!

