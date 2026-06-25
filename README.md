# 🤖 Autonomous Line Following Robot (Digital Logic Only)

![Digital Logic](https://img.shields.io/badge/Digital_Logic-%234CAF50.svg?style=for-the-badge)
![Robotics](https://img.shields.io/badge/Robotics-%23FF6F00.svg?style=for-the-badge)
![Proteus ISIS](https://img.shields.io/badge/Proteus_ISIS-%2300599C.svg?style=for-the-badge)
![No Microcontrollers](https://img.shields.io/badge/No_Microcontrollers-%23D32F2F.svg?style=for-the-badge)

![Robot Demo](path/to/your-demo.gif)

## 📌 Abstract & Objectives
The primary goal of this project was to develop a line-following robot for the "Ready to Race" division of the National Engineering Robotics Contest (NERC) 2024. The robot is designed to autonomously navigate complex intersections and obstacles. 
* This project strictly utilizes self-built digital logic circuits implemented on PCBs/Vero boards. 
* There is zero dependence on microcontrollers like Arduino. 
* The logic was meticulously constructed, simulated, and evaluated in Proteus ISIS to resolve routing problems before hardware implementation.

## 🛠️ List of Components
* **Logic Gates:** AND Gate (IC 7408), OR Gate (IC 7432), XOR Gate (IC 7486), NAND Gates.
* **Multiplexers/Demultiplexers:** 8-to-1 Multiplexers (Left and Right Mux), 2-to-1 Multiplexers (IC 74157), 1-to-4 Demultiplexer (IC 74155).
* **Counters:** 4-bit Asynchronous Ripple Counter (IC 7493).
* **Timers:** Timer (IC NE555), Multivibrator (IC 74123).
* **Actuation & Sensing:** IR Sensors, N20 Motors.
* **Passive Components:** Resistors, Potentiometers, Capacitors (100uF, 1uF).

---
## 🧠 Schematic Architecture & Subsystems

### 1. Line Following Integration
The core line tracking is processed via IR sensors.
* The raw sensor data is fed into a **NAND Gate of IR Sensors** to evaluate the presence of the line. 
* To seamlessly switch between default line-following behavior and hardcoded turns, the circuit utilizes **2x1 Muxes (74157) for Line Following Integration**. 
* An **AND Gate into the 2x1 Mux** acts as the trigger to exit standard line-following mode when a junction is reached.

### 2. Junction Counting & The "Garbage Value Timer"
To track the robot's location, the 7493 counter tallies the junctions. However, sensor bouncing caused false counts.
* **Garbage Value Timer:** A dedicated NE555 timer circuit was implemented to act as a debounce mechanism.
* **Garbage Value Pulse Generator:** An XOR gate is paired with this timer to create a clean, singular pulse that prevents the counter from registering "garbage values" or multiple falling edges from a single junction line.

### 3. Turning Circuitry & Multiplexers
To navigate the specific maze layout, two 8-to-1 multiplexers (**Left Mux** and **Right Mux**) serve as data selectors for left and right turns based on the counter's state.
* **1x4 Demux (74155):** Implemented specifically for "Efficient Turning Circuit" routing, drastically simplifying the logic needed to map the counter outputs to the motor states.

### 4. Turning Timers & Multivibrators (IC 74123)
When a turn is triggered, the motor must pause and then restart.
* **Pulse Generator for Turning Timer:** A 74123 Multivibrator is paired with an XOR gate to generate a crisp, momentary trigger pulse.
* **Turning Timer:** This pulse triggers a secondary NE555 monostable timer that temporarily deactivates the motor for the exact duration of the turn, allowing the robot to pivot cleanly before returning control to the 2x1 line-following muxes.

### 5. Motor Driver Stage
All logical outputs eventually route to the **Motor Driver IC (L293D)**, which bridges the gap between the low-current digital logic (which outputs only milliamperes) and the high-current demands of the N20 motors.

---
## 🔌 Hardware Implementation & Challenges

### Chassis Construction
* **Initial Design:** The first chassis was built using a 3mm acrylic sheet equipped with motors and a castor wheel. 
* **Final Design:** The 3mm sheet proved insufficiently sturdy and broke after a few days. It was successfully replaced with a robust 5mm thick acrylic sheet.

### Motor Driver Complications
* **Voltage Regulation:** Initial 12V battery tests burned out the left motor. A voltage regulator was added to drop the supply to a safe 5V.
* **Current Delivery:** The 2-to-1 multiplexer only outputted milliampere-level currents, which was insufficient to drive the right motor. 
* **The L293D Bridge:** The L293D IC was integrated as an intermediary between the 2-to-1 mux and the main L298N motor driver to provide the necessary amperage.
* **Speed Control:** Motors ran excessively fast, so Pulse-Width Modulation (PWM) was implemented using a low resistance potentiometer to easily dial in the speed.

### Sensor Calibration & Testing
* **Calibration:** IR sensors required meticulous tuning; sensitivity was adjusted over white and black surfaces until they accurately outputted a value of zero on the white line. 
* **Arena Terrain:** The highly reflective surfaces in the testing arena required constant recalibration. Furthermore, the terrain was strewn with rocks. The robot's tires frequently lodged in these rocks, causing incomplete turns and path deviations. Because of this environmental obstacle, the completion rate was limited to roughly 3 out of 10 attempts.

---
## 📷 Media & Schematics

### Breadboard Wiring
*(Insert your breadboard wiring images here)*
![Breadboard 1](path/to/breadboard-image.jpg)
Step-by-step logic testing was performed on the trainer board, utilizing LED displays to verify sensor output and counting mechanisms[cite: 3]. 

### Final Robot
*(Insert image of the final built robot here)*
![Final Robot](path/to/final-robot.jpg)
