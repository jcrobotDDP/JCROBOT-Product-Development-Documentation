# VCUmini User Manual

<img width="3100" height="2756" alt="image" src="https://github.com/user-attachments/assets/6c88e08b-7b97-4a5b-9a6e-8677b18ae15d" />

VCUmini User Manual V1.3

**Product Model:** VCUmini

---

**Language | 语言:** English  
**Firmware | 固件版本:** V1.1.3  
**Product Series | 产品系列:** VCUmini V1 and above

---

<details>
<summary><h2 style="display:inline; color: #4ECDC4">I. Product Introduction</h2></summary>

### 1.1 Product Description

1. Dedicated controller for chassis such as Warthog-01-mini, MA360, etc.
2. Supports string-based protocol and customisable operating modes.

---

### 1.2 Basic Parameters

| Category | Item | Description |
| :--- | :---: | :--- |
| **Peripheral Interfaces** | PWM output channels | 10 |
| | Output logic voltage | 3.3 V |
| | PWM input channels | 10 |
| | Input logic voltage | 3.3 V / 5 V |
| | CAN channels | 1 |
| | RS232 channels | 1 |
| | SBUS input channels | 1 |
| | USB serial port | 115200, 8 N 1 |
| | LTAOAA module | Supported |
| **Power Supply & Specifications** | Dimensions | 56 mm × 32 mm × 20 mm |
| | Supply voltage | V1: 7–30 V<br/>V2–V3: 5 V |

</details>

---

<details>
<summary><h2 style="display:inline; color: #4ECDC4">II. Usage Instructions</h2></summary>

### 2.1 Parameter Configuration

**Step 1:** Connect the module to the USB serial port using a Type‑C cable.  
**Step 2:** Send the command `"help"` via a serial terminal. The module will return its information as shown in Figure 2.1.

The module information is divided into two parts: **system information** and **operating modes**. The system information includes the hardware version, firmware version, and compilation date. The operating modes section lists the modes supported by the current firmware version; as the firmware is updated, available modes may also change accordingly.

<img width="660" height="325" alt="image" src="https://github.com/user-attachments/assets/b810e11a-a243-4d07-a3c9-dc648eb445a0" />

*Figure 2.1 – Module Information*

---

### 2.2 Mode Selection

Operating modes may vary between firmware versions. Please refer to the actual information returned by the module for the most accurate list of supported modes.

---

**Mode 1:**

SBUS to 10‑channel PWM output. X and Y channels are mixed. 10‑channel PWM output can also be controlled via RS232 protocol. SBUS has higher priority; when the remote controller is turned off, control switches to the RS232 protocol.

**RS232 protocol:** `{ 0x55 PWM1_H PWM1_L ... PWM10_H PWM10_L }` – 21 bytes total.

---

**Mode 2:**

C620 ESC speed closed‑loop control. Number of motor channels: 2. Rated speed: 7500 RPM. Supports SBUS, PWM, and CAN control methods.

- **SBUS mode:** X and Y channels are mixed; channel 9 switches the control mode.
- **PWM mode:** X and Y channels are mixed; channel 9 switches the control mode.

**Speed closed‑loop CAN protocol:**

- **ID:** 0x100
- **Transmission frequency:** Less than 200 Hz
- **Data format:**

| Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| SPEED1‑H | SPEED1‑L | SPEED2‑H | SPEED2‑L | 0x00 | 0x00 | 0x00 | 0x00 |

- **SPEEDx:** Unit: RPM; signed integer; range: -7500 to +7500.

---

**Mode 3:**

LTAOAA data processing module.

---

**Mode 4:**

C610 ESC speed closed‑loop control. Number of motor channels: 3. Rated speed: 7500 RPM. Supports SBUS and CAN control methods.

- **SBUS mode:** X and Y channels are mixed; channel 7 controls the swing arm; channel 9 switches the control mode.

**Speed closed‑loop CAN protocol:**

- **ID:** 0x100
- **Transmission frequency:** Less than 200 Hz
- **Data format:**

| Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| SPEED1‑H | SPEED1‑L | SPEED2‑H | SPEED2‑L | SPEED3‑H | SPEED3‑L | 0x00 | 0x00 |

- **SPEEDx:** Unit: RPM; signed integer; range: -7500 to +7500.

</details>

---

<details>
<summary><h2 style="display:inline; color: #4ECDC4">III. String Protocol</h2></summary>

### 3.1 Command List

**Table 3.1 – Command List**

| Command | Description |
| :--- | :--- |
| `help` | Print module information |
| `list` | Print parameter list |
| `config` | Print parameter configuration |
| `set` | Set a parameter. Format: `set_<parameter>=<value>` |
| `get` | Get a parameter. Format: `get_<parameter>` |
| `reboot` | Restart the device |

*Note: Carriage return and line feed (`\r\n`) are used as command terminators.*

---

### 3.2 Parameter List

**Table 3.2 – Parameter List**

| Parameter | Description |
| :--- | :--- |
| `mode` | Operating mode. A reboot is required for changes to take effect. |
| `axisX` | Remote controller X‑axis channel setting |
| `axisY` | Remote controller Y‑axis channel setting |

</details>

---

<details>
<summary><h2 style="display:inline; color: #4ECDC4">IV. Update Log</h2></summary>

**Table 4.1 – Update Log**

| Version | Date | Change Description |
| :---: | :---: | :--- |
| V1.0 | 2025.01.04 | 1. Initial release. |
| V1.1 | 2025.07.25 | 1. Revised the power supply description in Section 2.1.<br/>2. Changed the supply voltage to 24 V. |
| V1.2 | 2026.01.17 | 1. Revised the supply voltage description.<br/>2. Added parameters `axisX` and `axisY`.<br/>3. Added commands `list` and `config`. |
| V1.3 | 2026.06.29 | 1. Revised the description in Section 2.2. |

</details>

---

<details>
<summary><h2 style="display:inline; color: #4ECDC4">V. Further Information</h2></summary>

**Company:** Jichuang Robotics Intelligent Technology (Shandong) Co., Ltd.  

**Address:** Taishan Robot Maker Factory, No.72 Boyang Road, Taishan District, Tai'an City, Shandong Province, China  

**Email:**  ddpjcrobot@outlook.com  |  jcrobot@163.com

**Website:** [https://robot-chassis.com/](https://robot-chassis.com/) 

**Phone:**

 • +1 (327)208 2104

 • +1 (818)463 9273

 • 0538-6626018


</details>
