# JC-VCU-02 Produvt Guide and Software
<img width="5000" height="3485" alt="image" src="https://github.com/user-attachments/assets/d2cd12a3-d72c-4e5b-85a5-e436fec665f5" />
<font style="color:rgb(0,0,0);"></font>

---

**Firmware Version**: V4\.21\.0

**JcrobotsVCU Configuration Tool Version**: V3\.16\.0

**Applicable Product Series**: JC\-VCU\-02 V1\.4 and above

---
## I. Product Introduction

### 1.1 Overview
<img width="3840" height="2160" alt="image" src="https://github.com/user-attachments/assets/0ef0b5cb-461f-40e6-8aab-4b7f6ccca722" />

The Vehicle Control Unit (VCU) is designed to provide a complete low‑level control platform for special-purpose vehicles (inspection, reconnaissance, firefighting, education, etc.), facilitating subsequent upper‑level development (SLAM, ROS).

### 1.2 Product Features

(1) The VCU supports custom CAN and ModBus protocols for chassis development; the VCU does **not** provide any source code.

(2) The VCU’s two CAN ports are dedicated separately to the user and to chassis devices:  
- **CAN1** is the user port, allowing users to develop chassis control based on the CAN protocol.  
- **CAN2** is the drive port, responsible for communication with internal CAN devices on the chassis.  
The two CAN buses operate independently, so users need not worry about ID conflicts.

(3) VCU functions are customisable – optional sensors include gas sensors, gyroscopes, temperature & humidity sensors, GPS, obstacle avoidance, etc.

(4) Supports SBUS, CAN, Modbus‑RTU (RS232), Modbus‑TCP (LAN) protocols *<font color="#DF2A3F">(Ethernet module is optional, requires separate purchase)</font>*.

(5) Users can customise the function of each channel of the RC transmitter via the VCU tuning software (requires connecting the receiver’s SBUS signal line to the VCU).

(6) The VCU integrates kinematic models for differential drive, Ackermann steering, four‑wheel independent steering, etc.

(7) Power inputs are protected against reverse polarity and overvoltage.

(8) Supports 2‑channel current signal and 2‑channel voltage signal detection.

(9) 10 NPN digital inputs; 10 Mosfet outputs (12V).

(10) Operating temperature: -40℃ ~ +85℃.

(11) <font style="color:black;">Rated power (excluding Mosfet switches and 5V output): 1.3W.</font>

---
### 1.3 Chassis Controller (VCU) Pin Definitions
<img width="1974" height="824" alt="image" src="https://github.com/user-attachments/assets/77c8b45e-433a-49a0-a88e-b18e8b0c1303" />

**Chassis Controller (VCU) Port Definition Table 1**

| No. | Terminal Definition | Description |
| :---: | :---: | --- |
| 1 | RS232 | 1. Modbus‑RTU communication interface;<br/>2. Parameter tuning;<br/>3. Firmware upgrade. |
| 2 | CAN1 (H1 L1 G1) | CAN interface open to the user. |
| 3 | CAN2 (H2 L2 G2) | Interface for communication with other CAN devices on the chassis. |
| 4 | 485‑1 (A1 B1) | RS‑485 bus 1, connects to obstacle‑avoidance sensors, pan‑tilt cameras, etc. |
| 5 | 485‑2 (A2 B2) | RS‑485 bus 2, connects to battery BMS, temperature & humidity sensors, gas detectors, etc. |
| 6 | RUN | Running indicator LED. |
| 7 | RXD | Reception indicator – flashes when the VCU receives a valid command. |
| 8 | LAN | Modbus‑TCP communication interface. <font color="#DF2A3F">Note: Ethernet module is optional, requires separate purchase.</font> |

---

**Chassis Controller (VCU) Port Definition Table 2**

| **VCU Port P1** | | | | | | |
| --- | --- | --- | --- | --- | --- | --- |
| **No.** | Pin1 | Pin3 | Pin5 | Pin7 | Pin9 | Pin11 |
| **Name** | 5V | SBUS‑IN | Current sense 1<br/>AIN1 | Voltage sense 1<br/>AIN3 | Emergency stop<br/>DIN1 | Front bump<br/>DIN3 |
| **No.** | Pin2 | Pin4 | Pin6 | Pin8 | Pin10 | Pin12 |
| **Name** | GND | GND/NC | Current sense 2<br/>AIN2 | Voltage sense 2<br/>AIN4 | Parking brake<br/>DIN2 | Rear bump<br/>DIN4 |
| **No.** | Pin13 | Pin15 | Pin17 | | | |
| **Name** | DIN5 | DIN7 | DIN9 | | | |
| **No.** | Pin14 | Pin16 | Pin18 | | | |
| **Name** | DIN6 | DIN8 | DIN10 | | | |
| **VCU Port P2** | | | | | | |
| **No.** | Pin1 | Pin3 | | | | |
| **Name** | 12V/5V | TTL TX | | | | |
| **No.** | Pin2 | Pin4 | | | | |
| **Name** | GND | TTL RX | | | | |
| **VCU Port P3** | | | | | | |
| **No.** | Pin1 | Pin3 | Pin5 | Pin7 | Pin9 | |
| **Name** | 5V | Reserved | Reserved | Reserved | Reserved | |
| **No.** | Pin2 | Pin4 | Pin6 | Pin8 | Pin10 | |
| **Name** | GND | Reserved | Reserved | Reserved | Reserved | |
| **VCU Port P4** | | | | | | |
| **No.** | Pin1 | Pin3 | Pin5 | Pin7 | Pin9 | Pin11 |
| **Name** | DOUT1 | DOUT2 | DOUT3 | DOUT4 | DOUT5 | DOUT6 |
| **No.** | Pin2 | Pin4 | Pin6 | Pin8 | Pin10 | Pin12 |
| **Name** | GND | GND | GND | GND | GND | GND |
| **No.** | Pin13 | Pin15 | Pin17 | Pin19 | | |
| **Name** | DOUT7 | DOUT8 | DOUT9 | DOUT10 | | |
| **No.** | Pin14 | Pin16 | Pin18 | Pin20 | | |
| **Name** | GND | GND | GND | GND | | |

*Note: P2_Pin1 provides 12V (for hardware V1.4) or 5V (for hardware V1.5 and above).*

---

## II. Controller CAN Protocol

### 2.1 Default Factory Configuration

(1) CAN 2.0B specification;  
(2) Standard frames, data frames;  
(3) Bit rate: 500 kbps;  
(4) All messages follow Intel format – low byte first, high byte later.

---

### 2.2 Command Transmission (Downlink)

The downlink protocol includes motion control command frames and auxiliary control command frames. Detailed contents are as follows:

| Motion Control Command, ID: 0x200 | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity / translational speed | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity / rotational speed | 0.001 | 0 | rad/s | signed |  |
| 4..5 | Front wheel angle | 0.1 / 1 | 0 | deg | signed | For 2200 chassis: steering gear angle;<br/>For 4400 chassis: inner wheel steering angle;<br/>Not valid for differential chassis. |
| 6..7 | Reserved |  |  |  |  |  |

| Motion Control Command, ID: 0x200 – Independent Control Mode | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Left side speed | 1 | 0 | rpm | signed |  |
| 2..3 | Right side speed | 1 | 0 | rpm | signed |  |
| 4..5 | Left side steering angle | 0.1 | 0 | deg | signed | Not valid for differential chassis. |
| 6..7 | Right side steering angle | 0.1 | 0 | deg | signed | Not valid for differential chassis. |

---

| Function Control Command, ID: 0x1FF (V2.0~V2.3, older versions) | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Motion mode | - | 0 | - | unsigned | 1: Ackermann<br/>2: Pivot turn (angular velocity only)<br/>3: Crab steering |
| 1 | Function control | - | 0 | - | unsigned | bit0: Emergency stop (1=on, 0=off)<br/>bit1: Parking brake<br/>bit2: Obstacle avoidance<br/>bit3: Following |
| 2..3 | Switch control | - | 0 | - | unsigned | bit0: Switch 1 (1=on, 0=off)<br/>bit1: Switch 2<br/>bit2: Switch 3<br/>...... |
| 4 | Reserved |  |  |  |  | fill 0x00 |
| 5 | Reserved |  |  |  |  | fill 0x00 |
| 6 | Reserved |  |  |  |  | fill 0x00 |
| 7 | Reserved |  |  |  |  | fill 0x00 |

---

| Function Control Command, ID: 0x1FF (current) | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Motion mode | - | - | - | unsigned | 1: Default<br/>2: Pivot turn (multi‑wheel‑multi‑steer only)<br/>3: Crab steering (multi‑wheel‑multi‑steer only)<br/>4: Independent control<br/>Others: invalid |
| 1 | Emergency stop | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 2 | Parking brake | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 3 | Ultrasonic obstacle avoidance | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 4 | Braking | 1 | 0 | % | unsigned | Range: 1~100%<br/>0xFF: brake released<br/>Others: invalid |
| 5 | Battery high‑voltage enable (custom) | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 6 | Driver enable | - | - | - | unsigned | 1: Enable, 2: Disable<br/>Others: invalid |
| 7 | Lidar obstacle avoidance | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |

---

| Switch Control Command, ID: 0x1FE | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 1 | Mosfet1 | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 2 | Mosfet2 | - | - | - | unsigned |  |
| 3 | Mosfet3 | - | - | - | unsigned |  |
| 4 | Mosfet4 | - | - | - | unsigned |  |
| 5 | Mosfet5 | - | - | - | unsigned |  |
| 6 | Mosfet6 | - | - | - | unsigned |  |
| 7 | Mosfet7 | - | - | - | unsigned |  |
| 8 | Mosfet8 | - | - | - | unsigned |  |

---

| Switch Control Command, ID: 0x1FD | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Mosfet9 | - | - | - | unsigned | 1: On, 2: Off<br/>Others: invalid |
| 1 | Mosfet10 | - | - | - | unsigned |  |
| 2..7 | Reserved |  |  |  |  |  |

---

### 2.3 Data Reporting (V2.0, older version)

#### 2.3.1 General Data

General data reporting covers information common to most chassis, including battery data, VCU faults and status bits, odometer, etc.

| Battery Information Report, ID: 0x201, Period: 1000ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Bus voltage | 0.1 | 0 | V | unsigned |  |
| 2..3 | Bus current | 0.1 | 0 | A | signed |  |
| 4 | Battery max temperature | 1 | 0 | ℃ | signed |  |
| 5 | SOC | 1 | 0 | % | unsigned | Remaining capacity |
| 6 | SOH | 1 | 0 | % | unsigned | State of health |

| Chassis Status Flags Report, ID: 0x202, Period: 1000ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | VCU status | - | 0 | - | unsigned | Refer to 4.9 VCU Device Status |
| 2..3 | Chassis fault | - | 0 | - | unsigned | Refer to 4.10 Chassis Fault Status |
| 4..5 | Battery status | - | 0 | - | unsigned | Refer to 4.11 Battery Status |
| 6..7 | Safety status | - | 0 | - | unsigned | Refer to 4.12 Safety Status |

| Odometer Information Report, ID: 0x208, Period: 1000ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Odometer value | 1 | 0 | m | unsigned |  |

---

#### 2.3.2 Two‑wheel Differential Chassis

| Two‑wheel Differential Motor Information Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 2 | 0.01 | 0 | A | signed |  |

| Two‑wheel Differential Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

---

#### 2.3.3 Four‑wheel Differential Chassis

| Four‑wheel Differential Motor Speeds Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor speed 3 | 1 | 0 | rpm | signed |  |
| 6..7 | Motor speed 4 | 1 | 0 | rpm | signed |  |

| Four‑wheel Differential Motor Currents Report, ID: 0x204, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 2..3 | Motor current 2 | 0.01 | 0 | A | signed |  |
| 4..5 | Motor current 3 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 4 | 0.01 | 0 | A | signed |  |

| Four‑wheel Differential Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

---

#### 2.3.4 Single‑drive Ackermann Chassis

| Single‑drive Ackermann Motor Information Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor current 1 | 0.01 | 0 | A | signed |  |

| Single‑drive Ackermann Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Steering angle | 0.1 | 0 | deg | signed |  |

---

#### 2.3.5 Dual‑drive Ackermann Chassis

| Dual‑drive Ackermann Motor Information Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 2 | 0.01 | 0 | A | signed |  |

| Dual‑drive Ackermann Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Steering angle | 0.1 | 0 | deg | signed |  |

---

#### 2.3.6 Four‑wheel‑independent‑steering Chassis

| Four‑wheel‑independent‑steering Motor Speeds Report, ID: 0x203, Period: 33ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor speed 3 | 1 | 0 | rpm | signed |  |
| 6..7 | Motor speed 4 | 1 | 0 | rpm | signed |  |

| Four‑wheel‑independent‑steering Motor Currents Report, ID: 0x204, Period: 33ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 2..3 | Motor current 2 | 0.01 | 0 | A | signed |  |
| 4..5 | Motor current 3 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 4 | 0.01 | 0 | A | signed |  |

| Four‑wheel‑independent‑steering Steering Angles Report, ID: 0x205, Period: 33ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering angle 1 | 0.1 | 0 | deg | signed |  |
| 2..3 | Steering angle 2 | 0.1 | 0 | deg | signed |  |
| 4..5 | Steering angle 3 | 0.1 | 0 | deg | signed |  |
| 6..7 | Steering angle 4 | 0.1 | 0 | deg | signed |  |

| Four‑wheel‑independent‑steering Steering Motor Currents Report, ID: 0x206, Period: 33ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering motor current 1 | 0.01 | 0 | A | signed |  |
| 2..3 | Steering motor current 2 | 0.01 | 0 | A | signed |  |
| 4..5 | Steering motor current 3 | 0.01 | 0 | A | signed |  |
| 6..7 | Steering motor current 4 | 0.01 | 0 | A | signed |  |

| Four‑wheel‑independent‑steering Motion Information Report, ID: 0x207, Period: 33ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

---

### 2.4 Data Reporting (V2.1, older version)

#### 2.4.1 General Data

| Battery Information Report, ID: 0x201, Period: 500ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Bus voltage | 0.01 | 0 | V | unsigned |  |
| 2..3 | Bus current | 0.01 | 0 | A | signed |  |
| 4 | Battery max temperature | 1 | 0 | ℃ | signed |  |
| 5 | Battery min temperature | 1 | 0 | ℃ | signed |  |
| 6 | SOC | 1 | 0 | % | unsigned |  |
| 7 | SOH | 1 | 0 | % | unsigned |  |

| Chassis Status Flags Report, ID: 0x202, Period: 500ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Function status | - | 0 | - | unsigned | See 4.1 Function Status |
| 1 | Operating mode | - | 0 | - | unsigned | See 4.2 Operating Mode |
| 2 | Lighting status | - | 0 | - | unsigned | See 4.3 Lighting Status |
| 3 | Device status | - | 0 | - | unsigned | See 4.4 Device Status |
| 4 | Collision status | - | 0 | - | unsigned | See 4.5 Collision Status |
| 5 | Obstacle avoidance status | - | 0 | - | unsigned | See 4.6 Obstacle Avoidance Status |
| 6..7 | Battery status | - | 0 | - | unsigned | See 4.7 Battery Status |

| Odometer Report, ID: 0x208, Period: 500ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Odometer | 1 | 0 | m | unsigned |  |

| Driver Fault Codes Report, ID: 0x209, Period: 500ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Driver fault code 1 | - | 0 | - | unsigned | See 4.8 Driver Fault Codes |
| 1 | Driver fault code 2 | - | 0 | - | unsigned | |
| 2 | Driver fault code 3 | - | 0 | - | unsigned | |
| 3 | Driver fault code 4 | - | 0 | - | unsigned | |
| 4 | Driver fault code 5 | - | 0 | - | unsigned | |
| 5 | Driver fault code 6 | - | 0 | - | unsigned | |
| 6 | Driver fault code 7 | - | 0 | - | unsigned | |
| 7 | Driver fault code 8 | - | 0 | - | unsigned | |

---

#### 2.4.2 Two‑wheel Differential Chassis

| Two‑wheel Differential Motor Info, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 2 | 0.01 | 0 | A | signed |  |

| Two‑wheel Differential Motion Info, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

| Two‑wheel Differential Wheel Odometer Report, ID: 0x20A, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed |  |
| 4..7 | Wheel odometer 2 | 0.0001 | 0 | m | signed |  |

---

#### 2.4.3 Four‑wheel Differential Chassis

*(Similar to 2.4.2 but with 4 motors; reports ID 0x20B for wheel odometers 3 & 4. We'll keep concise.)*

| Four‑wheel Differential Motor Speeds, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor speed 3 | 1 | 0 | rpm | signed |  |
| 6..7 | Motor speed 4 | 1 | 0 | rpm | signed |  |

| Four‑wheel Differential Motor Currents, ID: 0x204, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 2..3 | Motor current 2 | 0.01 | 0 | A | signed |  |
| 4..5 | Motor current 3 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 4 | 0.01 | 0 | A | signed |  |

| Four‑wheel Differential Motion Info, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

| Four‑wheel Differential Wheel Odometer (1&2), ID: 0x20A, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed |  |
| 4..7 | Wheel odometer 2 | 0.0001 | 0 | m | signed |  |

| Four‑wheel Differential Wheel Odometer (3&4), ID: 0x20B, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| 0..3 | Wheel odometer 3 | 0.0001 | 0 | m | signed |  |
| 4..7 | Wheel odometer 4 | 0.0001 | 0 | m | signed |  |

---

#### 2.4.4 Single‑drive Ackermann

| Motor Info, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor current 1 | 0.01 | 0 | A | signed |  |

| Motion Info, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Steering angle | 0.1 | 0 | deg | signed |  |

| Wheel Odometer, ID: 0x20A, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed |  |

---

#### 2.4.5 Dual‑drive Ackermann

| Motor Info, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Motor current 1 | 0.01 | 0 | A | signed |  |
| 6..7 | Motor current 2 | 0.01 | 0 | A | signed |  |

| Motion Info, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Steering angle | 0.1 | 0 | deg | signed |  |

| Wheel Odometer (1&2), ID: 0x20A, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed |  |
| 4..7 | Wheel odometer 2 | 0.0001 | 0 | m | signed |  |

---

#### 2.4.6 Four‑wheel‑independent‑steering

| Four-wheel-independent-steering Motor Speeds Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed | |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed | |
| 4..5 | Motor speed 3 | 1 | 0 | rpm | signed | |
| 6..7 | Motor speed 4 | 1 | 0 | rpm | signed | |


| Four-wheel-independent-steering Motor Currents Report, ID: 0x204, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor current 1 | 0.01 | 0 | A | signed | |
| 2..3 | Motor current 2 | 0.01 | 0 | A | signed | |
| 4..5 | Motor current 3 | 0.01 | 0 | A | signed | |
| 6..7 | Motor current 4 | 0.01 | 0 | A | signed | |


| Four-wheel-independent-steering Steering Angles Report, ID: 0x205, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering angle 1 | 0.1 | 0 | deg | signed | |
| 2..3 | Steering angle 2 | 0.1 | 0 | deg | signed | |
| 4..5 | Steering angle 3 | 0.1 | 0 | deg | signed | |
| 6..7 | Steering angle 4 | 0.1 | 0 | deg | signed | |


| Four-wheel-independent-steering Steering Motor Currents Report, ID: 0x206, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering motor current 1 | 0.01 | 0 | A | signed | |
| 2..3 | Steering motor current 2 | 0.01 | 0 | A | signed | |
| 4..5 | Steering motor current 3 | 0.01 | 0 | A | signed | |
| 6..7 | Steering motor current 4 | 0.01 | 0 | A | signed | |


| Four-wheel-independent-steering Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed | |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed | |


| Four-wheel-independent-steering Wheel Odometer Information Report, ID: 0x20A, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed | |
| 4..7 | Wheel odometer 2 | 0.0001 | 0 | m | signed | |


| Four-wheel-independent-steering Wheel Odometer Information Report, ID: 0x20B, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 3 | 0.0001 | 0 | m | signed | |
| 4..7 | Wheel odometer 4 | 0.0001 | 0 | m | signed | |

---

### 2.5 Data Reporting (V2.2, older version)

#### 2.5.1 Chassis General Data

General data reporting covers information common to most chassis, including battery data, VCU status bits, odometer, driver fault codes, etc.

| Battery Information Report, ID: 0x201, Period: 500ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Bus voltage | 0.01 | 0 | V | unsigned | |
| 2..3 | Bus current | 0.01 | 0 | A | signed | |
| 4 | Battery max temperature | 1 | 0 | °C | signed | |
| 5 | Battery min temperature | 1 | 0 | °C | signed | |
| 6 | SOC | 1 | 0 | % | unsigned | Remaining capacity |
| 7 | SOH | 1 | 0 | % | unsigned | State of health |


| Chassis Status Flags Report, ID: 0x202, Period: 500ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Function status | - | 0 | - | unsigned | See 4.1 Function Status |
| 1 | Operating mode | - | 0 | - | unsigned | See 4.2 Operating Mode |
| 2 | Lighting status | - | 0 | - | unsigned | See 4.3 Lighting Status |
| 3 | Device status | - | 0 | - | unsigned | See 4.4 Device Status |
| 4 | Collision status | - | 0 | - | unsigned | See 4.5 Collision Status |
| 5 | Obstacle avoidance status | - | 0 | - | unsigned | See 4.6 Obstacle Avoidance Status |
| 6..7 | Battery status | - | 0 | - | unsigned | See 4.7 Battery Status |


| Odometer Information Report, ID: 0x208, Period: 500ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Distance travelled | 1 | 0 | m | unsigned | Saved on power‑off |


| Driver Fault Information Report, ID: 0x209, Period: 500ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Driver fault code 1 | - | 0 | - | unsigned | See 4.8 Driver Fault Codes |
| 1 | Driver fault code 2 | - | 0 | - | unsigned | |
| 2 | Driver fault code 3 | - | 0 | - | unsigned | |
| 3 | Driver fault code 4 | - | 0 | - | unsigned | |
| 4 | Driver fault code 5 | - | 0 | - | unsigned | |
| 5 | Driver fault code 6 | - | 0 | - | unsigned | |
| 6 | Driver fault code 7 | - | 0 | - | unsigned | |
| 7 | Driver fault code 8 | - | 0 | - | unsigned | |


#### 2.5.2 Chassis Motion Data

| Motor Speeds Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor speed 1 | 1 | 0 | rpm | signed | |
| 2..3 | Motor speed 2 | 1 | 0 | rpm | signed | |
| 4..5 | Motor speed 3 | 1 | 0 | rpm | signed | |
| 6..7 | Motor speed 4 | 1 | 0 | rpm | signed | |


| Motor Currents Report, ID: 0x204, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Motor current 1 | 0.01 | 0 | A | signed | |
| 2..3 | Motor current 2 | 0.01 | 0 | A | signed | |


| Steering Angles Report, ID: 0x205, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering angle 1 | 0.1 | 0 | deg | signed | |
| 2..3 | Steering angle 2 | 0.1 | 0 | deg | signed | |
| 4..5 | Steering angle 3 | 0.1 | 0 | deg | signed | |
| 6..7 | Steering angle 4 | 0.1 | 0 | deg | signed | |


| Steering Motor Currents Report, ID: 0x206, Period: 20ms | | | | | | |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering motor current 1 | 0.01 | 0 | A | signed | |
| 2..3 | Steering motor current 2 | 0.01 | 0 | A | signed | |

---

### 2.6 Data Reporting (current)

#### 2.6.1 Chassis General Data

| Battery Information Report, ID: 0x201, Period: 1000ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Bus voltage | 0.01 / 0.1 | 0 | V | unsigned | Accuracy depends on battery model |
| 2..3 | Bus current | 0.01 / 0.1 | 0 | A | signed | Accuracy depends on battery model |
| 4 | Battery max temperature | 1 | 0 | ℃ | signed |  |
| 5 | Battery min temperature | 1 | 0 | ℃ | signed |  |
| 6 | SOC | 1 | 0 | % | unsigned | Remaining battery power |
| 7 | SOH | 1 | 0 | % | unsigned | performance status |

| Chassis Status Flags Report, ID: 0x202, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Function status | - | 0 | - | unsigned | See 4.1 |
| 1 | Operating mode | - | 0 | - | unsigned | See 4.2 |
| 2 | Lighting status | - | 0 | - | unsigned | See 4.3 |
| 3 | Device status | - | 0 | - | unsigned | See 4.4 |
| 4 | Collision & Lidar obstacle avoidance status | - | 0 | - | unsigned | See 4.5 |
| 5 | Ultrasonic obstacle avoidance status | - | 0 | - | unsigned | See 4.6 |
| 6..7 | Battery status | - | 0 | - | unsigned | See 4.7 |

| Odometer Report, ID: 0x208, Period: 1000ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Total distance travelled | 1 | 0 | m | unsigned | Saved on power‑off |

| Driver Fault Codes Report, ID: 0x209, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0 | Drive motor fault code 1 | - | 0 | - | unsigned | See 4.8 |
| 1 | Drive motor fault code 2 | - | 0 | - | unsigned | |
| 2 | Drive motor fault code 3 | - | 0 | - | unsigned | |
| 3 | Drive motor fault code 4 | - | 0 | - | unsigned | |
| 4 | Steering motor fault code 1 | - | 0 | - | unsigned | |
| 5 | Steering motor fault code 2 | - | 0 | - | unsigned | |
| 6 | Steering motor fault code 3 | - | 0 | - | unsigned | |
| 7 | Steering motor fault code 4 | - | 0 | - | unsigned | |

| Analog Signal Detection Report, ID: 0x20C, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Current signal 1 | 0.01 | 0 | mA | unsigned |  |
| 2..3 | Current signal 2 | 0.01 | 0 | mA | unsigned |  |
| 4..5 | Voltage signal 1 | 0.01 | 0 | V | unsigned |  |
| 6..7 | Voltage signal 2 | 0.01 | 0 | V | unsigned |  |

| Digital Input & MOS Switch Status Report, ID: 0x20D, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Digital input status | - | 0 | - | unsigned | bitx=1: NPN signal detected<br/>bitx=0: no signal<br/>x=0~9 |
| 2..3 | MOS switch status | - | 0 | - | unsigned | bitx=1: MOS on<br/>bitx=0: MOS off<br/>x=0~9 |
| 4..7 | Reserved |  |  |  |  |  |

---

#### 2.6.2 Chassis Motion Data

| Motor Speeds Report, ID: 0x203, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Drive motor speed 1 | 1 | 0 | rpm | signed |  |
| 2..3 | Drive motor speed 2 | 1 | 0 | rpm | signed |  |
| 4..5 | Drive motor speed 3 | 1 | 0 | rpm | signed |  |
| 6..7 | Drive motor speed 4 | 1 | 0 | rpm | signed |  |

| Motor Currents Report, ID: 0x204, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Drive motor current 1 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 2..3 | Drive motor current 2 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 4..5 | Drive motor current 3 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 6..7 | Drive motor current 4 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |

| Steering Angles Report, ID: 0x205, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering angle 1 | 0.1 | 0 | deg | signed |  |
| 2..3 | Steering angle 2 | 0.1 | 0 | deg | signed |  |
| 4..5 | Steering angle 3 | 0.1 | 0 | deg | signed |  |
| 6..7 | Steering angle 4 | 0.1 | 0 | deg | signed |  |

| Steering Motor Currents Report, ID: 0x206, Period: 100ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Steering motor current 1 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 2..3 | Steering motor current 2 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 4..5 | Steering motor current 3 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |
| 6..7 | Steering motor current 4 | 0.01/0.1/1 | 0 | A | signed | Accuracy depends on driver model |

| Motion Information Report, ID: 0x207, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..1 | Linear velocity | 0.001 | 0 | m/s | signed |  |
| 2..3 | Angular velocity | 0.001 | 0 | rad/s | signed |  |

| Wheel Odometer (1&2), ID: 0x20A, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 1 | 0.0001 | 0 | m | signed | |
| 4..7 | Wheel odometer 2 | 0.0001 | 0 | m | signed | |

| Wheel Odometer (3&4), ID: 0x20B, Period: 20ms | | | | | | |
| :---: | --- | --- | --- | --- | --- | --- |
| Byte | Signal Description | Resolution | Offset | Unit | Data Type | Value Description |
| 0..3 | Wheel odometer 3 | 0.0001 | 0 | m | signed | |
| 4..7 | Wheel odometer 4 | 0.0001 | 0 | m | signed | |

---

### 2.7 DBC File Download

**DBC File Download:** *(Please open the link in a browser or app)*  
[jcrobots_vcu02_canbus_v2.2a.7z](https://www.yuque.com/attachments/yuque/0/2024/7z/35413540/1727771370317-52a2e6be-13a7-482f-ba2f-72d9385c2cff.7z)

---

## III. Controller RS232 Protocol

### 3.1 Overview

- Baud rate: 115200
- Data bits: 8
- Stop bits: 1
- Parity: None

---

### 3.2 Command Codes and Communication Data Description

#### 3.2.1 Command Code: 03H – Read N words

Example: Read 2 consecutive words from VCU address 03E9H.

| Table 3.1 Master Request Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 03H |
| Read Data Address High | 03H |
| Read Data Address Low | E9H |
| Data Count High (in words) | 00H |
| Data Count Low (in words) | 02H |
| CRC Low | 14H |
| CRC High | 0CH |

| Table 3.2 Slave Response Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 03H |
| Byte Count | 04H |
| First Word Data High | 00H |
| First Word Data Low | C8H |
| Second Word Data High | 00H |
| Second Word Data Low | 00H |
| CRC Low | 0DH |
| CRC High | 0DH |

---

#### 3.2.2 Command Code: 06H – Write single word

Example: Write 200 (00C8H) to address 03E9H.

| Table 3.3 Master Request Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 06H |
| Write Data Address High | 03H |
| Write Data Address Low | E9H |
| Data High | 00H |
| Data Low | C8H |
| CRC Low | 58H |
| CRC High | 5BH |

| Table 3.4 Slave Response Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 06H |
| Write Data Address High | 03H |
| Write Data Address Low | E9H |
| Data High | 00H |
| Data Low | C8H |
| CRC Low | 58H |
| CRC High | 5BH |

---

#### 3.2.3 Command Code: 10H

**Function:** Write N words, where N ≥ 2 and N must be even.

For example, to write 200 (000000C8H) and 0 (0000H) to VCU memory addresses 03E9H and 03EAH respectively, the frame structure is described as follows:

| Table 3.5 Master Request Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 10H |
| Write Data Address High | 03H |
| Write Data Address Low | E9H |
| Data Count High (in words) | 00H |
| Data Count Low (in words) | 02H |
| Byte Count | 04H |
| First Word Data High | 00H |
| First Word Data Low | C8H |
| Second Word Data High | 00H |
| Second Word Data Low | 00H |
| CRC Low | B2H |
| CRC High | F7H |

| Table 3.6 Slave Response Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 10H |
| Write Data Address High | 03H |
| Write Data Address Low | E9H |
| Data Count High (in words) | 00H |
| Data Count Low (in words) | 02H |
| CRC Low | 91H |
| CRC High | CFH |

---

### 3.3 Communication Frame Error Checking Method

#### 3.3.1 CRC Check Method

The RTU frame format is used, and the frame includes a frame error detection field calculated using the CRC method. The CRC field checks the entire content of the frame.

The CRC field consists of two bytes, containing a 16‑bit binary value. It is calculated by the transmitting device and appended to the frame. The receiving device recalculates the CRC of the received frame and compares it with the value in the received CRC field. If the two CRC values are not equal, a transmission error has occurred.

CRC starts by loading 0xFFFF into a register, then a process is called that processes six or more consecutive bytes in the frame with the current register value. Only the 8 data bits of each character are valid for CRC; start bits, stop bits, and parity bits are ignored.

During CRC generation, each 8‑bit byte is XORed with the register contents. The result is shifted toward the least significant bit (LSB), and the most significant bit (MSB) is filled with zero. The LSB is extracted and checked: if the LSB is 1, the register is XORed with a preset value; if the LSB is 0, no XOR is performed. This process is repeated eight times. After the final bit (the 8th) is processed, the next 8‑bit byte is again XORed with the current register value. The final register value is the CRC value after all bytes in the frame have been processed.

This CRC calculation method follows the internationally standard CRC checking rules. When developing a CRC algorithm, users may refer to relevant standard CRC algorithms to implement a fully compliant CRC calculation program.

---

### 3.4 Exception Response

When the slave device responds, it uses the function code field and the fault address field to indicate whether the response is a normal response (no error) or whether an error has occurred (referred to as an exception response). For a normal response, the slave returns the corresponding function code and data address or sub‑function code. For an exception response, the slave returns a code identical to the normal code, but with the most significant bit set to logic 1.

For example, a request from the master to the slave to read a set of servo drive function code address data would generate the following function code:

**00000011** (hexadecimal **03H**)

For a normal response, the slave responds with the same function code. For an exception response, it returns:

**10000011** (hexadecimal **83H**)

In addition to the function code being modified due to the exception error, the slave also returns a one‑byte exception code that defines the cause of the exception.

After receiving an exception response, the master application typically retransmits the message or changes the command according to the corresponding fault.

For example: Writing the value 1000 to VCU data address 03E4H returns an error message because address 03E4H does not exist.

---

| Table 3.7 Slave Exception Response Frame | |
| :--- | :---: |
| Field | Value |
| ADDR | 06H |
| CMD | 90H |
| Exception Code | 02H |
| CRC Low | 7CH |
| CRC High | 00H |


| Table 3.8 Exception Code Definitions| | |
| :---: | :--- | :--- |
| Code | Name | Description |
| 01H | Illegal Function | The function code received from the master is not an allowed operation. This may be because the function code is only applicable to newer devices and is not implemented in this device. |
| 02H | Illegal Data Address | The data address requested by the master is not an allowed address; the combination of register address and the number of transmitted bytes is invalid; or the firmware version is too low. |
| 03H | Illegal Data Value | The received data value exceeds the valid range for that address, resulting in an invalid parameter change. |
| 11H | CRC Error | The CRC check value in the frame sent by the master does not match the CRC value calculated by the slave. |

---

### 3.5 MODBUS Register Definitions

#### 3.5.1 Real‑time Status Registers (V2.0, older version)

| Register | Address (dec) | Description | Supported Function Codes | Remarks |
| :---: | :---: | :--- | :---: | :--- |
| R0.00 | 4000 | Bus voltage | 03H | Unit: 0.1V |
| R0.01 | 4001 | Bus current | 03H | Unit: 0.1A |
| R0.02 | 4002 | Battery temperature | 03H | Unit: °C |
| R0.03 | 4003 | SOC (remaining capacity) | 03H | Unit: % |
| R0.04 | 4004 | SOH (state of health) | 03H | Unit: % |
| R0.05 | 4005 | VCU device status | 03H | See 4.9 VCU Device Status |
| R0.06 | 4006 | Chassis fault status | 03H | See 4.10 Chassis Fault Status |
| R0.07 | 4007 | Battery status | 03H | See 4.11 Battery Status |
| R0.08 | 4008 | Safety status | 03H | See 4.12 Safety Status |
| R0.09 | 4009 | Linear velocity | 03H | Unit: 0.001m/s |
| R0.10 | 4010 | Angular velocity | 03H | Unit: 0.001rad/s |
| R0.11 | 4011 | Motor speed 1 | 03H | Unit: rpm |
| R0.12 | 4012 | Motor speed 2 | 03H | Unit: rpm |
| R0.13 | 4013 | Motor speed 3 | 03H | Unit: rpm |
| R0.14 | 4014 | Motor speed 4 | 03H | Unit: rpm |
| R0.15 | 4015 | Drive motor current 1 | 03H | Unit: 0.01A |
| R0.16 | 4016 | Drive motor current 2 | 03H | Unit: 0.01A |
| R0.17 | 4017 | Drive motor current 3 | 03H | Unit: 0.01A |
| R0.18 | 4018 | Drive motor current 4 | 03H | Unit: 0.01A |
| R0.19 | 4019 | Steering angle 1 | 03H | Unit: deg |
| R0.20 | 4020 | Steering angle 2 | 03H | Unit: deg |
| R0.21 | 4021 | Steering angle 3 | 03H | Unit: deg |
| R0.22 | 4022 | Steering angle 4 | 03H | Unit: deg |
| R0.23 | 4023 | Steering motor current 1 | 03H | Unit: 0.01A |
| R0.24 | 4024 | Steering motor current 2 | 03H | Unit: 0.01A |
| R0.25 | 4025 | Steering motor current 3 | 03H | Unit: 0.01A |
| R0.26 | 4026 | Steering motor current 4 | 03H | Unit: 0.01A |
| R0.27 | 4027 | Absolute encoder count 1 low | 03H | |
| R0.28 | 4028 | Absolute encoder count 1 high | 03H | |
| R0.29 | 4029 | Absolute encoder count 2 low | 03H | |
| R0.30 | 4030 | Absolute encoder count 2 high | 03H | |
| R0.31 | 4031 | Absolute encoder count 3 low | 03H | |
| R0.32 | 4032 | Absolute encoder count 3 high | 03H | |
| R0.33 | 4033 | Absolute encoder count 4 low | 03H | |
| R0.34 | 4034 | Absolute encoder count 4 high | 03H | |
| R0.35 | 4035 | Odometer low | 03H | Unit: m |
| R0.36 | 4036 | Odometer high | 03H | Unit: m |

---

#### 3.5.2 Real‑time Status Registers (V2.1, older version)

| Register | Address (dec) | Description | Supported Function Codes | Remarks |
| :---: | :---: | :--- | :---: | :--- |
| R0.00 | 4000 | Bus voltage | 03H | Unit: 0.01V |
| R0.01 | 4001 | Bus current | 03H | Unit: 0.01A |
| R0.02 | 4002 | Battery max temperature | 03H | Unit: °C |
| R0.03 | 4003 | Battery min temperature | 03H | Unit: °C |
| R0.04 | 4004 | SOC (remaining capacity) | 03H | Unit: % |
| R0.05 | 4005 | SOH (state of health) | 03H | Unit: % |
| R0.06 | 4006 | Function status | 03H | See 4.1 Function Status |
| R0.07 | 4007 | Operating mode | 03H | See 4.2 Operating Mode |
| R0.08 | 4008 | Lighting status | 03H | See 4.3 Lighting Status |
| R0.09 | 4009 | Device status | 03H | See 4.4 Device Status |
| R0.10 | 4010 | Collision status | 03H | See 4.5 Collision Status |
| R0.11 | 4011 | Obstacle avoidance status | 03H | See 4.6 Obstacle Avoidance Status |
| R0.12 | 4012 | Battery status | 03H | See 4.7 Battery Status |
| R0.13 | 4013 | Linear velocity | 03H | Unit: 0.001m/s |
| R0.14 | 4014 | Angular velocity | 03H | Unit: 0.001rad/s |
| R0.15 | 4015 | Motor speed 1 | 03H | Unit: rpm |
| R0.16 | 4016 | Motor speed 2 | 03H | Unit: rpm |
| R0.17 | 4017 | Motor speed 3 | 03H | Unit: rpm |
| R0.18 | 4018 | Motor speed 4 | 03H | Unit: rpm |
| R0.19 | 4019 | Drive motor current 1 | 03H | Unit: 0.01A |
| R0.20 | 4020 | Drive motor current 2 | 03H | Unit: 0.01A |
| R0.21 | 4021 | Drive motor current 3 | 03H | Unit: 0.01A |
| R0.22 | 4022 | Drive motor current 4 | 03H | Unit: 0.01A |
| R0.23 | 4023 | Steering angle 1 | 03H | Unit: 0.1deg |
| R0.24 | 4024 | Steering angle 2 | 03H | Unit: 0.1deg |
| R0.25 | 4025 | Steering angle 3 | 03H | Unit: 0.1deg |
| R0.26 | 4026 | Steering angle 4 | 03H | Unit: 0.1deg |
| R0.27 | 4027 | Steering motor current 1 | 03H | Unit: 0.01A |
| R0.28 | 4028 | Steering motor current 2 | 03H | Unit: 0.01A |
| R0.29 | 4029 | Steering motor current 3 | 03H | Unit: 0.01A |
| R0.30 | 4030 | Steering motor current 4 | 03H | Unit: 0.01A |
| R0.31 | 4031 | Absolute encoder count 1 low | 03H | |
| R0.32 | 4032 | Absolute encoder count 1 high | 03H | |
| R0.33 | 4033 | Absolute encoder count 2 low | 03H | |
| R0.34 | 4034 | Absolute encoder count 2 high | 03H | |
| R0.35 | 4035 | Absolute encoder count 3 low | 03H | |
| R0.36 | 4036 | Absolute encoder count 3 high | 03H | |
| R0.37 | 4037 | Absolute encoder count 4 low | 03H | |
| R0.38 | 4038 | Absolute encoder count 4 high | 03H | |
| R0.39 | 4039 | Driver fault code 1 | 03H | See 4.8 Driver Fault Codes |
| R0.40 | 4040 | Driver fault code 2 | 03H | |
| R0.41 | 4041 | Driver fault code 3 | 03H | |
| R0.42 | 4042 | Driver fault code 4 | 03H | |
| R0.43 | 4043 | Driver fault code 5 | 03H | |
| R0.44 | 4044 | Driver fault code 6 | 03H | |
| R0.45 | 4045 | Driver fault code 7 | 03H | |
| R0.46 | 4046 | Driver fault code 8 | 03H | |
| R0.47 | 4047 | Odometer low | 03H | Unit: m |
| R0.48 | 4048 | Odometer high | 03H | Unit: m |

---

#### 3.5.3 Real‑time Status Registers (current version)

| Register | Address (dec) | Description | Supported Function Codes | Remarks |
| :---: | :---: | :--- | :---: | :--- |
| R0.00 | 4000 | Bus voltage | 03H | Unit: 0.01V / 0.1V (accuracy depends on battery model) |
| R0.01 | 4001 | Bus current | 03H | Unit: 0.01A / 0.1A (accuracy depends on battery model) |
| R0.02 | 4002 | Battery max temperature | 03H | Unit: °C |
| R0.03 | 4003 | Battery min temperature | 03H | Unit: °C |
| R0.04 | 4004 | SOC (remaining capacity) | 03H | Unit: % |
| R0.05 | 4005 | SOH (state of health) | 03H | Unit: % |
| R0.06 | 4006 | Function status | 03H | See 4.1 Function Status |
| R0.07 | 4007 | Operating mode | 03H | See 4.2 Operating Mode |
| R0.08 | 4008 | Lighting status | 03H | See 4.3 Lighting Status |
| R0.09 | 4009 | Device status | 03H | See 4.4 Device Status |
| R0.10 | 4010 | Collision & lidar obstacle avoidance status | 03H | See 4.5 Collision & Lidar Obstacle Avoidance Status |
| R0.11 | 4011 | Ultrasonic obstacle avoidance status | 03H | See 4.6 Ultrasonic Obstacle Avoidance Status |
| R0.12 | 4012 | Battery status | 03H | See 4.7 Battery Status |
| R0.13 | 4013 | Linear velocity | 03H | Unit: 0.001m/s |
| R0.14 | 4014 | Angular velocity | 03H | Unit: 0.001rad/s |
| R0.15 | 4015 | Drive motor speed 1 | 03H | Unit: rpm |
| R0.16 | 4016 | Drive motor speed 2 | 03H | Unit: rpm |
| R0.17 | 4017 | Drive motor speed 3 | 03H | Unit: rpm |
| R0.18 | 4018 | Drive motor speed 4 | 03H | Unit: rpm |
| R0.19 | 4019 | Drive motor current 1 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.20 | 4020 | Drive motor current 2 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.21 | 4021 | Drive motor current 3 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.22 | 4022 | Drive motor current 4 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.23 | 4023 | Steering angle 1 | 03H | Unit: 0.1deg |
| R0.24 | 4024 | Steering angle 2 | 03H | Unit: 0.1deg |
| R0.25 | 4025 | Steering angle 3 | 03H | Unit: 0.1deg |
| R0.26 | 4026 | Steering angle 4 | 03H | Unit: 0.1deg |
| R0.27 | 4027 | Steering motor current 1 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.28 | 4028 | Steering motor current 2 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.29 | 4029 | Steering motor current 3 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.30 | 4030 | Steering motor current 4 | 03H | Unit: 0.01A / 0.1A / A (accuracy depends on driver model) |
| R0.31 | 4031 | Wheel odometer 1 low | 03H | Unit: 0.0001m |
| R0.32 | 4032 | Wheel odometer 1 high | 03H | |
| R0.33 | 4033 | Wheel odometer 2 low | 03H | Unit: 0.0001m |
| R0.34 | 4034 | Wheel odometer 2 high | 03H | |
| R0.35 | 4035 | Wheel odometer 3 low | 03H | Unit: 0.0001m |
| R0.36 | 4036 | Wheel odometer 3 high | 03H | |
| R0.37 | 4037 | Wheel odometer 4 low | 03H | Unit: 0.0001m |
| R0.38 | 4038 | Wheel odometer 4 high | 03H | |
| R0.39 | 4039 | Drive driver fault code 1 | 03H | See 4.8 Driver Fault Codes |
| R0.40 | 4040 | Drive driver fault code 2 | 03H | |
| R0.41 | 4041 | Drive driver fault code 3 | 03H | |
| R0.42 | 4042 | Drive driver fault code 4 | 03H | |
| R0.43 | 4043 | Steering driver fault code 1 | 03H | |
| R0.44 | 4044 | Steering driver fault code 2 | 03H | |
| R0.45 | 4045 | Steering driver fault code 3 | 03H | |
| R0.46 | 4046 | Steering driver fault code 4 | 03H | |
| R0.47 | 4047 | Total distance travelled low | 03H | Unit: m (saved on power‑off) |
| R0.48 | 4048 | Total distance travelled high | 03H | |
| R1.02 | 4202 | Digital input status | 03H | bitx=1: NPN signal detected; bitx=0: no signal (x=0~9) |
| R1.03 | 4203 | MOS switch status | 03H | bitx=1: MOS on; bitx=0: MOS off (x=0~9) |
| R1.04 | 4204 | Current signal 1 | 03H | Unit: 0.01mA |
| R1.05 | 4205 | Current signal 2 | 03H | Unit: 0.01mA |
| R1.06 | 4206 | Voltage signal 1 | 03H | Unit: 0.01V |
| R1.07 | 4207 | Voltage signal 2 | 03H | Unit: 0.01V |

---

#### 3.5.4 Chassis Control Registers

| Register | Address (dec) | Description | Supported Function Codes | Remarks |
| :---: | :---: | :--- | :---: | :--- |
| P0.00 | 1040 | Linear / translational velocity | 06H, 10H | Unit: 0.001m/s |
| P0.01 | 1041 | Angular / rotational velocity | 06H, 10H | Unit: 0.001rad/s |
| P0.02 | 1042 | Front wheel angle | 06H, 10H | Unit: 0.1deg / 1deg<br/>For 2200 chassis: steering gear angle;<br/>For 4400 chassis: inner wheel steering angle;<br/>Not valid for differential chassis. |
| P0.03 | 1043 | Reserved | 06H, 10H | |
| P0.00\(^{(1)}\) | 1040 | Left side speed | 06H, 10H | Unit: rpm |
| P0.01\(^{(1)}\) | 1041 | Right side speed | 06H, 10H | Unit: rpm |
| P0.02\(^{(1)}\) | 1042 | Left side steering angle | 06H, 10H | Unit: 0.1deg<br/>Not valid for differential chassis. |
| P0.03\(^{(1)}\) | 1043 | Right side steering angle | 06H, 10H | Unit: 0.1deg<br/>Not valid for differential chassis. |
| P0.04 | 1044 | Motion mode | 06H, 10H | 1: Default<br/>2: Pivot turn (multi‑wheel‑multi‑steer only)<br/>3: Crab steering (multi‑wheel‑multi‑steer only)<br/>4: Independent control<br/>Others: invalid |
| P0.05 | 1045 | Function control | 06H, 10H | bit0: Emergency stop (1=on, 0=off)<br/>bit1: Parking brake<br/>bit2: Ultrasonic obstacle avoidance<br/>bit3: Lidar obstacle avoidance |
| P0.06 | 1046 | Switch control | 06H, 10H | bit0: Switch 1 (1=on, 0=off)<br/>bit1: Switch 2<br/>bit2: Switch 3<br/>...... |
| P0.07 | 1047 | Braking | 06H, 10H | Unit: %, valid range 0~100 |
| P0.08 | 1048 | Battery high‑voltage enable (custom) | 06H, 10H | 1: On, 2: Off; others: invalid |
| P0.09 | 1049 | Driver enable | 06H, 10H | 1: Enable, 2: Disable; others: invalid |

> \(^{(1)}\) These registers are used only when P0.04 is set to **4 (Independent Control Mode)**.

---

Here is the professional English translation of **Chapter 4 – Status Descriptions** (Sections 4.1 through 4.12), including all tables and explanatory text.

---

## IV. Status Descriptions

### 4.1 Function Status

| Table 4.1 VCU Function Status | | | | | | | |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Bit7 | Bit6 | Bit5 | Bit4 | Bit3 | Bit2 | Bit1 | Bit0 |
| Lidar obstacle avoidance | Driver enable | Battery HV enable (reserved) | Braking | Following | Ultrasonic obstacle avoidance | Parking brake | Emergency stop |

---

### 4.2 Operating Mode

| Table 4.2 VCU Operating Mode | |
| :---: | :--- |
| Code | Description |
| 1 | CAN / RS232 / LAN (protocol mode) |
| 2 | SBUS mode |
| 3 | VRF mode |
| 4 | PWM‑RTK mode |
| 5 | OWK mode |
| 6 | UWB‑AOA mode |
| 7 | XDL3 mode |
| 255 | Custom mode |

---

### 4.3 Lighting Status

| Table 4.3 VCU Lighting Status | | | | | | | |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Bit7 | Bit6 | Bit5 | Bit4 | Bit3 | Bit2 | Bit1 | Bit0 |
| (reserved) | (reserved) | (reserved) | Brake light | Right turn signal | Left turn signal | Warning light | Headlight |

---

### 4.4 Device Status

| Table 4.4 VCU Device Status | | | | | | | |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Bit7 | Bit6 | Bit5 | Bit4 | Bit3 | Bit2 | Bit1 | Bit0 |
| (reserved) | (reserved) | (reserved) | (reserved) | (reserved) | (reserved) | CAN2 bus fault | CAN1 bus fault |

---

### 4.5 Collision & Lidar Obstacle Avoidance Status

| Table 4.5 VCU Collision & Lidar Obstacle Avoidance Status| | | | | | | |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Bit7 | Bit6 | Bit5 | Bit4 | Bit3 | Bit2 | Bit1 | Bit0 |
| (reserved) | Zone 3 triggered | Zone 2 triggered | Zone 1 triggered | (reserved) | (reserved) | Rear bump triggered | Front bump triggered |

---

### 4.6 Ultrasonic Obstacle Avoidance Status

| Table 4.6 VCU Ultrasonic Obstacle Avoidance Status | | | | | | | |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Bit7 | Bit6 | Bit5 | Bit4 | Bit3 | Bit2 | Bit1 | Bit0 |
| Sensor 8 triggered | Sensor 7 triggered | Sensor 6 triggered | Sensor 5 triggered | Sensor 4 triggered | Sensor 3 triggered | Sensor 2 triggered | Sensor 1 triggered |

---

### 4.7 Battery Status (V2.0 / older)

| Table 4.7 Battery Status | | | |
| :---: | :---: | :---: | :---: |
| Status flags (1 = active, 0 = inactive) |
| Low Byte Bit0 | Discharge MOS switch | High Byte Bit0 | Low‑temperature protection |
| Low Byte Bit1 | Charge MOS switch | High Byte Bit1 | Overtemperature protection |
| Low Byte Bit2 | Differential pressure protection | High Byte Bit2 | Discharging |
| Low Byte Bit3 | xx | High Byte Bit3 | Charging |
| Low Byte Bit4 | xx | High Byte Bit4 | Discharge overcurrent / short‑circuit |
| Low Byte Bit5 | xx | High Byte Bit5 | Charge overcurrent |
| Low Byte Bit6 | xx | High Byte Bit6 | Overall / cell undervoltage |
| Low Byte Bit7 | xx | High Byte Bit7 | Overall / cell overvoltage |

---

### 4.8 Driver Fault Codes

| Table 4.8 Driver Fault Codes| |
| :---: | :--- |
| Fault Code | Description |
| 0 | Device not used |
| 1 | Not enabled |
| 2 | Overvoltage |
| 3 | Motor hardware overcurrent |
| 4 | EEPROM writing in progress |
| 5 | Undervoltage |
| 6 | Active braking state |
| 7 | Software overcurrent protection |
| 8 | Invalid parameter settings |
| 9 | Incorrect operating mode setting |
| 10 | Interlock fault in hybrid drive mode |
| 11 | Driver overtemperature protection |
| 12 | Motor Hall sensor fault |
| 13 | Current sensor hardware fault |
| 14 | Motor overtemperature protection |
| 15 | Encoder fault |
| 16 | Motor stalled |
| 17 | Motor overspeed |
| 18 | MOSFET fault |
| 101 ~ 200 | Driver‑defined fault codes |
| 254 | Driver not detected |
| 255 | No fault |

---

### 4.9 VCU Device Status Description

The VCU device status is automatically reported via CAN commands. The data type is **uint16_t**. Bits 0–15 each correspond to a status; a value of 1 indicates the condition is active, 0 indicates inactive.

| Table 4.9 VCU Device Status Description| | | |
| :---: | :--- | :---: | :--- |
| Bit | Description | Bit | Description |
| Bit0 | Emergency stop | Bit8 | CAN / RS232 |
| Bit1 | Parking brake | Bit9 | SBUS mode |
| Bit2 | Obstacle avoidance | Bit10 | VRF mode |
| Bit3 | Following | Bit11 | Manual mode |
| Bit4 | Braking | Bit12 | Reserved |
| Bit5 | VRF fault | Bit13 | Reserved |
| Bit6 | Reserved | Bit14 | Reserved |
| Bit7 | Reserved | Bit15 | Reserved |

---

### 4.10 Chassis Fault Status Description

The VCU fault status is automatically reported via CAN commands. The data type is **uint16_t**. Bits 0–15 each correspond to a fault; a value of 1 indicates the fault is active, 0 indicates inactive.

| Table 4.10 VCU Fault Status Description| | | |
| :---: | :--- | :---: | :--- |
| Bit | Description | Bit | Description |
| Bit0 | Driver 1 fault | Bit8 | Motor 1 communication fault |
| Bit1 | Driver 2 fault | Bit9 | Motor 2 communication fault |
| Bit2 | Driver 3 fault | Bit10 | Motor 3 communication fault |
| Bit3 | Driver 4 fault | Bit11 | Motor 4 communication fault |
| Bit4 | Driver 5 fault | Bit12 | Motor 5 communication fault |
| Bit5 | Driver 6 fault | Bit13 | Motor 6 communication fault |
| Bit6 | Driver 7 fault | Bit14 | Motor 7 communication fault |
| Bit7 | Driver 8 fault | Bit15 | Motor 8 communication fault |

---

### 4.11 Battery Status Description

| Table 4.11 Battery Status Description| | | |
| :---: | :--- | :---: | :--- |
| Status flags (1 = active, 0 = inactive) |
| Low Byte Bit0 | Discharge MOS switch | High Byte Bit0 | Low temperature |
| Low Byte Bit1 | Charge MOS switch | High Byte Bit1 | Overtemperature |
| Low Byte Bit2 | xx | High Byte Bit2 | Discharging |
| Low Byte Bit3 | xx | High Byte Bit3 | Charging |
| Low Byte Bit4 | xx | High Byte Bit4 | Discharge overcurrent / short‑circuit |
| Low Byte Bit5 | xx | High Byte Bit5 | Charge overcurrent |
| Low Byte Bit6 | xx | High Byte Bit6 | Undervoltage |
| Low Byte Bit7 | xx | High Byte Bit7 | Overvoltage |

---

### 4.12 Safety Status Description

| Table 4.12 Safety Status Description| | | | 
| :---: | :--- | :---: | :--- |
| Bit | Description | Bit | Description |
| Bit0 | Chassis tilted forward | Bit8 | Obstacle sensor 1 triggered |
| Bit1 | Chassis tilted backward | Bit9 | Obstacle sensor 2 triggered |
| Bit2 | Chassis tilted left | Bit10 | Obstacle sensor 3 triggered |
| Bit3 | Chassis tilted right | Bit11 | Obstacle sensor 4 triggered |
| Bit4 | Front collision triggered | Bit12 | Obstacle sensor 5 triggered |
| Bit5 | Rear collision triggered | Bit13 | Obstacle sensor 6 triggered |
| Bit6 | Reserved | Bit14 | Obstacle sensor 7 triggered |
| Bit7 | Reserved | Bit15 | Obstacle sensor 8 triggered |

---

## V. JcrobotsVCU Operation

### 5.1 Connection
Use a USB‑to‑RS232 cable. The software automatically searches and connects to the VCU. Alternatively, click the tool assistant at the lower right.

<img width="936" height="563" alt="image" src="https://github.com/user-attachments/assets/d33bc054-7323-4f75-8cae-45efe0ea1bcb" />

Figure 5.1 Function Selection Interface

---

### 5.2 Parameter Configuration
<img width="936" height="564" alt="image" src="https://github.com/user-attachments/assets/2924b37f-fb60-4a64-9f6b-56975f128b5b" />

Figure 5.2 Parameter list interface

---

**Function Descriptions:**

- **Import file:** Import previously saved chassis parameters.
- **Export file:** Save the currently configured chassis parameters locally for future use.
- **Read parameters:** Read all configuration information from the VCU.
- **Write parameters:** Write all parameter values to the VCU.
- **Firmware ID:** Used to distinguish between standard chassis firmware and custom chassis firmware.
- **Firmware version:** The version number of the VCU program.
- **Hardware version:** The version number of the VCU hardware circuit.
- **Update firmware:** Update the VCU program.

**Special Parameters:**

- **error:** The parameter value is outside the valid range.
- **Invalid:** Invalid parameter, indicating that this parameter has been deprecated.

<img width="936" height="562" alt="image" src="https://github.com/user-attachments/assets/f2fb3c97-8ae3-41a7-9143-ac4e43c2bd1f" />

Figure 5.3 Parameter setting interface

Double-click the parameter item to be modified; the "Detailed Parameter Window" will appear, allowing you to change the set value.

If you wish to modify only a single parameter, click the **"Save"** button – that parameter will be written directly to the VCU. If you click the **"OK"** button, the parameter value will be temporarily stored in the host software parameter list and not written immediately.

---

### 5.3 Data Monitoring & Runtime
<img width="936" height="564" alt="image" src="https://github.com/user-attachments/assets/8a8282ee-1a06-450f-ae4b-714503300a03" />

Figure 5.4 Data monitoring interface

- **Drive‑by‑wire section:** Directly operate the chassis via the host software.
- **Battery section:** Displays common battery information and status bits.
- **Chassis status:** Includes function status, operating mode, lighting status, device status, collision status, obstacle avoidance status, driver fault status, etc.
- **Drive motor section:** Displays motor current, motor speed, and wheel odometer values.
- **Steering motor section:** Displays steering motor current and wheel steering angles.
- **Chassis data:** Shows linear velocity, angular velocity, total distance travelled, and movement status during chassis motion.

---

### 5.4 Firmware Update
**Update Procedure:**

1. After the VCU is properly connected, click the **"Update Firmware"** button; the software will automatically navigate to the firmware update interface, as shown in Figure 5.5.
2. Open the serial port.
3. Load the firmware file (supports .jcbin and .bin formats).
4. Download the firmware.
5. After completion, click **"Return"**.
<img width="936" height="564" alt="image" src="https://github.com/user-attachments/assets/db665f9f-f451-43fe-8a78-0ffd16ba28c5" />

Figure 5.5 Program update interface

---

### 5.5 Software Download
*(Links provided in original)*

---

## VI. ROS1 & ROS2 Configuration

### 6.1 Overview
The VCU‑ROS package subscribes to `/cmd_vel` and translates it into CAN/RS232 commands. It also processes battery data, chassis status, encoders, odometer, and publishes `/vcu_info` topic.
- ROS1 version: noetic
- ROS2 version: humble
- Ubuntu: 22.04 LTS

### 6.2 Communication Devices
Supported devices:
1. CANalyst‑II (Linux edition)
2. CANable2.0
3. Native SocketCAN
4. USB‑RS232

### 6.3 Ubuntu USB Device Permissions and Mapping
Udev rules are provided for each device (idVendor/idProduct specific). For example, for CANalyst‑II: `SUBSYSTEMS=="usb", ATTRS{idVendor}=="04d8", ATTRS{idProduct}=="0053", GROUP="users", MODE="0777"`.

For CANable2.0: `KERNEL=="ttyACM*", ATTRS{idVendor}=="16d0", ATTRS{idProduct}=="117e", MODE:="0777", SYMLINK+="CANable2"`.

For USB‑RS232 (PL2303GT): similar.

After editing, reload udev or reboot.

### 6.4 ROS1 Package Compilation
- Place `jcrobots_vcu_rclcpp` and `serial` into the ROS1 workspace `src`.
- Configure dynamic library path for CANalyst‑II (create .conf file in `/etc/ld.so.conf.d/` pointing to `/home/user/controlcan/libcontrolcan.so`, then `sudo ldconfig`).
- Install serial library (`make && make install` inside serial folder).
- Then `catkin_make`.

### 6.5 ROS2 Package Compilation
- Place `jcrobots_vcu_rclcpp` and `vcu_topic_interface` into ROS2 workspace `src`.
- Configure CANalyst‑II library as above.
- Install serial‑ros2 library (copy folder, cmake, make, sudo make install).
- `colcon build`.

### 6.6 ROS1 Package Execution
Run: `rosrun jcrobots_vcu_cpp <filename>` where filename:
- `chassis_control_canalyst` for CANalyst‑II
- `chassis_control_socketCAN` for CANable2.0/SocketCAN (run `./canable2_init.sh` first)
- `chassis_control_rs232` for USB‑RS232

Demo programs: `demo_information.cpp`, `demo_control.cpp`.

### 6.7 ROS2 Package Execution
Similar, using `ros2 run jcrobots_vcu_rclcpp <filename>`.

### 6.8 Nodes
The main node `chassis_control_xxx`:
- Subscribes to `/cmd_vel` (Twist) and `/vcu_control` (Control message) – contains sports_mode, function, mosfet.
- Publishes `/vcu_info` (Information) with all battery, status, motor, odometer fields as described.

### 6.9 Common Issues
- `'struct can_frame' has no member named 'len8_dlc'`: use `can_dlc` instead of `len`.
- `open device error!`: check CANalyst‑II Linux support or udev permissions.
- Shared library error: verify dynamic link configuration and run `ldconfig`.
- ROS2 foxy/humble coexistence: set environment variables or remove one.

### 6.10 ROS Development Package Download
*(Links provided in original)*

---

## VII. Controller LAN Protocol (Modbus‑TCP)

### 7.1 Overview
Standard Modbus‑TCP with function codes 03, 06, 10. Register definitions are identical to Modbus‑RTU. Ethernet module is optional.

### 7.2 Function Codes and Data Description
Examples for read holding registers (0x03), write single (0x06), write multiple (0x10) – with MBAP headers (transaction ID, protocol ID, length, unit ID).

### 7.3 Ethernet Module Configuration
Use DTU configuration tool:
1. Connect PC and VCU via Ethernet (direct or same LAN).
2. Open DTU tool, search device.
3. Set mode to ModbusTCP‑>RTU.
4. Set gateway, IP, port.
5. Write parameters and reboot.

*(Tool download link provided)*

---

## VIII. Version History

| Firmware Version | Protocol Version |
| :---: | :---: |
| V4.0.0 ~ V4.8.x | V2.0 |
| V4.9.x ~ V4.10.x | V2.1 |
| V4.11.x ~ V4.13.x | V2.2 |
| V4.14.x ~ V4.20.x | V2.3 |

---

## IX. Update Log

| Version | Date | Changes |
| :---: | :---: | --- |
| V2.0 | 2022.11.24 | Initial release |
| V2.1 | 2023.12.11 | Steering resolution 1→0.1; modified 0x201,0x202; added 0x209,0x20A,0x20B; changed periods; etc. |
| V2.2 | 2024.04.19 | Unified CAN report format; MODBUS registers changed to wheel odometer. |
| V2.3 | 2024.11.22 | Changed periods of 0x202,0x209,0x204,0x206 to 100ms; 0x201,0x208 to 1000ms; added rated power; etc. |
| V2.4 | 2025.07.21 | Updated pin definitions for V1.5+; modified current descriptions; software V3.14.0. |
| V2.5 | 2025.11.01 | Mosfet switches moved to 0x1FE/0x1FD; separate control bytes for emergency, parking, ultrasonic, lidar; added brake, HV enable, driver enable parameters; new OWK and UWB‑AOA modes. |
| V2.6 | 2026.01.16 | Added Chapter VII – LAN Protocol. |
| V2.7 | 2026.04.13 | Added Independent Control motion mode; renamed Ackermann to Default; added lidar zones to collision status. |
| V2.8 | 2026.06.26 | Added CAN reports 0x20C and 0x20D; added MODBUS registers 4202~4207. |

---

## X. Further Information

**Company:** 极创机器人智能科技（山东）有限公司 (Jichuang Robotics Intelligent Technology (Shandong) Co., Ltd.)  
**Address:** Taishan Robot Maker Factory, No.72 Boyang Road, Taishan District, Tai'an City, Shandong Province, China  
**Email:** jcrobot@163.com  
**Website:** [www.jcrobots.com](http://www.jcrobots.com/)

**PDF version download** (as of July 15, 2024. Please refer to the web version for updates).

---
