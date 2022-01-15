
# About

This is a small guide that contains some information about drones/quadcopters that I collected during my internship.

Here, the "Hexatron" refers to the product that was being designed. It was a hexapod base with elements of quadcopter integrated into it.

Its purpose was to navigate tough terrains where it was difficult for the humans to do so.

NOTE: The idea for Hexatron wasn't originally mine. I was just tasked to work on the drone part.


<p align="center">
  <img src="Images\Hexatron.jpg" />
</p>

<p align="center">
  Hexatron (The arrowhead represents the front)
</p>


# Basic components for a Drone

    -> Flight Controller

    -> Brushless DC Motors (4, 6, 8)

    -> ESCs (Electronic Speed Controller) (4, 6, 8)

    -> Propellers (4, 6. 8)

    -> Receiver and Transmitter

    -> Batteries (Multiple batteries in parallel maybe required due to Hexatron's weight)

    -> Frame/Platform (Quadcopter, Hexacopter or Octocopter)

    -> FPV System with Camera, Goggles, Gimbal, etc.

# Algorithms for Processing IMU Data

Complementary Filter:
    
    https://www.pieter-jan.com/node/11

Madgwick Filter: 
    
    https://x-io.co.uk/open-source-imu-and-ahrs-algorithms/ 

Kalman Filter:

    https://thekalmanfilter.com/kalman-filter-explained-simply/

The Complementary Filter is fastest but least accurate, Madgwick is in the middle ground, while Kalman Filter is computationally heavy and but most accurate and noise free.

# Changing the Drone's Position

The processed and noise free data is then fed into a PID Controller that determines the error or the difference between the current position of the Drone and the required position as requested by the pilot. 

The result from PID Controller is then translated into speed and direction data of the Brushless Motors, and then applied to those motors to 
change the Drone's position. 

The IMU then takes the new position measurement data of the Drone and applies a de-noising filter, which is then passed onto the PID controller to 
calculate the positional difference, and then finally applied to motors using the Motor Controllers.

Good video demonstrating PID Control:

    https://www.youtube.com/watch?v=qKy98Cbcltw

# Processing Loop

<p align="center">
  <img src="Images\Processing_Loop.png" />
</p>

<p align="center">
  Processing Loop Block Diagram
</p>

A feedback loop is created in the Drone system which consists mainly of the Flight Controller, IMU Sensors, PID Controller, Motor Controllers and can also include a Flight Computer for tasks involving: 

    -> Data Logging from different Sensors

    -> Image and Video Capturing

    -> Path Planning Using Set Coordinates

    -> Mission Planning

    -> Image Processing using AI and ML, etc.

This feedback loop in the Flight Controller must run at high speed (at least 1kHz) so that the Drone is stable all the times and doesn't get off the desired path of its pilot. That's why a good Flight Controller is necessary part of a Drone. 

# Why Not Just Use A Flight Computer Only (Raspberry Pi)?

One might think of just using the Flight Computer (e.g. Raspberry Pi) for all the tasks performed by the Flight Controller. But the problem arises 
when high reaction speed and good stability is required from the Drone. That is because the Flight Computer is not directly able to communicate
with the motors, sensors and PID controllers directly since it has an OS layer. This reduces the operating speed of the Drone. A Flight Computer may also be running other heavy tasks such as Image Processing which can easily slow down the CPU, thus de-stabilizing the Drone.

A Flight Controller on the other hand, doesn't have an OS, and it is able to to communicate and control the IMU Sensor and Motor Controller directly at a set speed. This way the Drone is able to stay in air and hover without falling to the ground due to de-stabilization.

Good video on this topic: 
    
    https://www.youtube.com/watch?v=PlKeFj5teo4

# Improvising the Hexatron into a Drone

Since Hexatron already uses a computer (Raspberry Pi) for communication and control, we only need to add in a Flight Controller to convert Hexatron 
into a Drone. The Raspberry Pi can act as the Flight Controller for the Drone.

<p align="center">
  <img src="Images\Block_Diagram.png" />
</p>

<p align="center">
  Hexapod Base + Quadcopter Drone Block Diagram
</p>

The next problem is the weight constraint. Hexatron already weighs about 5 kg (~10 pounds) without including the battery.
One of the main reason for its heavy weight are the multiple servos used for operating the 6 legs. Since we cant remove the legs and crawling ability of the Hexatron, we have to implement the Drone such that the propellers and the motors are able to lift this weight into the air. This area is still under research.

## Interesting comment from a user on Reddit

    "An octocopter would be easier (and possibly cheaper) for that much load capacity. For a 2kg airframe 
    
    which is a bit optimistic including batteries, you'd want about 14kg max thrust for the best handling, 
    
    so 1.75 per corner. You'd want somewhere in the region of 500W to 600W per motor to do that. 80A speed 
    
    controllers, then some big ass batteries to power it all, 3 x 5000mAh 3S 45c wired in parallel would be 
    
    enough to get you about 5-10 minutes of flight without being overloaded. As for the propellers, you're 
    
    probably looking at 14" or so depending on the KV rating of the motors you get."

# Important Drone Data in a Spreadsheet (not mine)

https://docs.google.com/spreadsheets/d/1cPdofZWCfkSS_foDwRs3u5FUe5eYlWKzW_TypwBiWTE/edit?usp=sharing

# Thrust/Weight Ratios Basics

First, there are two ways to measure the force applied by the Drone and the force of gravity. The most common is:

    F = m*a = m*g

But the in the Drone communities, the equation is simplified to:

    F = a = g

That is, the acceleration due to gravity is ignored since it stays the almost same on Earth surface and also the Drone isn't going to fly at very high altitudes either.

So, "F = m" is used for comparing thrust output of a Drone, which is in kilograms (kg).

Note: "Dry Weight" of a Drone means the weight excluding the battery's weight.

As for the Thrust/Weight ratio for a Drone, it needs to be at least higher than 1:1, preferably 2:1 for comfortable flying power. The ratio also needs to account for 
some future weight increase.

For calculating TWR, we need to first make an estimate for the Drone's weight (mass). Then as stated above, we need to produce thrust double of the Drone's weight. 
The thrust can then be approximately calculated by choosing some four DC Brushless Motors (for a quadcopter) and making sure that their combined thrust is equal to or greater than twice the weight of the Drone.

This will result in a TWR of 2:1 for our Drone.

# TWR for Hexatron

Hexatron's weight is estimated to be around 5 kg.

Since we definitely want to have a TWR of at least 2:1, that means we need to have double the thrust as compared to the weight.

Thus, to make Hexatron fly, 10 kg of thrust will be required, or in official terms:

    Weight of Hexatron = m x g = 5 x 9.8 = 49 N

    Thrust = Weight x 2 = 49 x 2 = 98 N

Since the Hexatron will incorporate a quadcopter design, therefore four motors will be used.

These four motors will divide the total required thrust of 98 N between them, that is:

    98 / 4 = 24.5 N

OR 

    10 kg / 4 = 2.5 kg

Hence thrust force of 2.5 kg (or 24.5 N) will be required from each one of the four motors to successfully lift Hexatron into the air.

For comparison, a common motor used in drone (specs: 2207/1000KV) has a thrust of approximately 0.7 kg, or 6.86 N. This means we would require much bigger motors to lift Hexatron with its current weight of 5 kg (excluding the weight of the motors, battery, etc).

# DC Brushless Motor Dimensions and KV Ratings

Motors dimensions are represented with four digits; where the first two are for the motor stator's width and the last two digits tells us about its height.

For Example: 

    "2212" would mean 22 mm width and 12 mm height for motor's stator.

The KV Rating of a motor gives us the max RPM at the supplied voltage. The standard KV Rating is for RPM at 1 volts applied to the motor.

Therefore, for 2400 KV Rating, the max RPM at:

    1 volt will be 2400 RPM

    11.1 volts will be (2400 x 11.1 =) 26540 RPM

    14 volts will be (2400 x 14 = ) 33600 RPM

# Propellers

Propeller specs are typically represented as "1045" where as the first two digits tells the diameter of the propellers' blade, and the second digits are for the pitch of the propeller.

Another way for writing propeller specs might be in this format:

    Diameter Size x Pitch x Number of Blades

For example: 

    "10x4.5x2" would mean a propeller with a diameter of 10 inch, pitch of 4.5 inch and with 2 blades.

# Communication with Hexatron

When the Drone ability is added to the Hexatron, a Flight Controller will also be added. This Flight Controller combined with RF modules such as LoRa Modules can used for controller the whole Hexatron robot. 

For the Drone mode, the remote controller will communicate directly with the Flight Controller. When the Crawling mode is to be used, the remote controller can transmit a message to the Flight Controller to also involve the Flight Computer (Raspberry Pi in Hexatron's case). Now the Flight Controller will just receive control commands and then pass it on to the Flight Computer to move the Hexatron on ground using its six legs. During this time, the Drone mode would be disengaged.

This way, the latency issue for controlling the Hexatron can be eliminated. This is because now LoRa Module is being used for communication with the robot instead of WiFi. This will also greatly increase the operating range of Hexatron (up to several kilometers).

# Wireless Tx/Rx Modules

For wireless remote access to Hexatron, radio modules such as XBee 3 Pro Modules can be used. Two modules will be required; one for the Hexatron and and for the PC/Laptop running the remote control software. 

These XBee modules have a max range of 2 miles (around 3 km) which is enough for the operating range of Hexatron.  These modules also have a antenna connector for adding an RP-SMA Antenna. Xbee is full duplex so data can be sent and received at the same time.

For sending and receiving the data, UART pins are used either directly or in the form of USB. This greatly simplifies the implementation of these modules in a given project.
For configuring these modules, such as setting their destination addresses, etc XCTU software is used on the Windows/Mac systems, while on Raspberry Pi (Linux), since the XCTU isn't supported, other tools and libraries such as "python-xbee" and "libxbee" can be used to communicate with the XBee Module.

Note: To connect the XBee Module to a USB port, a serial to USB (and vice versa) converter board is used. An example of this is the "XBee Explorer USB" available from SparkFun. The XBee Module can be inserted into this adapter, and since this adapter has a USB connector, a USB cable can be used to connect it to the PC/Laptop or Raspberry Pi.

# Choosing a FPV System

<p align="center">
  <img src="Images\FPV_Systems.png" />
</p>

<p align="center">
  Guidelines for choosing a FPV System
</p>

# Antenna Types

<p align="center">
  <img src="Images\Antenna_Types.png" />
</p>

<p align="center">
  Some Common Antenna Types
</p>

Different types of antennas have their own advantages:

    Linear -> Simple and good but can introduce signal noise

    Helical -> Omnidirectional, much better option as compared to Linear antennas

    Patch -> Directional antennas, provides a long range

Best option is to use a combination of two antennas, such as:

    Linear + Helical

    Patch + Helical

The antenna selection applies to both FPV Camera Tx/Rx and the Remote Control Tx/Rx.

# Some Useful Links for the Components

Pixhawk Flight Controller: 

    https://docs.px4.io/master/en/flight_controller/pixhawk4.html

Navio2 Flight Controller:

    https://navio2.emlid.com/

DC Brushless Motors (0.7 kg Thrust):

    https://www.banggood.com/4X-Racerstar-Racing-Edition-2212-BR2212-920KV-2-4S-Brushless-Motor-For-350-380-400-Frame-Kit-p-1083198.html?rmmds=detail-left-hotproducts&cur_warehouse=CN

DC Brushless Motors (2.8 kg Thrust):

    https://robu.in/product/emax-mt3515-650kv-bldc-motor-ccw-original/

ESCs (Electronic Speed Controllers):

    https://www.banggood.com/Wholesale-XXD-HW30A-30A-Brushless-Motor-ESC-For-Airplane-Quadcopter-p-50621.html?rmmds=stop_sale_viewalsoview&cur_warehouse=CN

XBee 3 Pro Module - RP-SMA Antenna:

    https://www.sparkfun.com/products/15131

SparkFun XBee Explorer USB:

    https://www.sparkfun.com/products/11812

Tx/Rx Module:

    https://www.getfpv.com/happymodel-expresslrs-es900tx-es900rx-long-range-combo.html

Propellers:

    https://www.banggood.com/Gemfan-1045-10x4_5-10-Inch-Carbon-Nylon-CW-or-CCW-Propeller-EPP-for-RC-Drone-FPV-Racing-Multi-Rotor-p-961067.html?rmmds=detail-bottom-alsobought&cur_warehouse=CN

<p align="center">
  = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = = =
</p>