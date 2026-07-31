[![Panasonic](https://img.shields.io/badge/Panasonic-FP--XH-blue)](https://industrial.panasonic.com)
[![Documentation](https://img.shields.io/badge/Manual-WUMC--FPXHCOM-orange)](https://industrial.panasonic.com)

> **⚠️ 重要法律及安全提示：请务必完整阅读以下条款**
> 1. **非官方文档声明**：本文档为第三方基于公开技术资料整理的深度解析与知识共享文章，**并非**松下电器（Panasonic）或其附属公司（松下神视电子（苏州）有限公司）发布的官方文档。本文档不构成任何形式的官方技术建议、指导或授权。
> 2. **版权声明**：本文档引用的《FP-XH系列用户手册》插图、技术规格、系统寄存器定义等内容，其**全部版权、商标权及相关知识产权均归松下神视电子（苏州）有限公司所有**。严禁将本文档用于任何商业盈利或侵犯松下合法权益的目的。
> 3. **技术时效性声明**：本文档基于《FP-XH系列用户手册（通信篇）》（手册编号：WUMC-FPXHCOM-01，发行日期：2014年6月）撰写。实际产品的规格、软件版本、系统寄存器代码可能因厂商后续的**产品改良和技术升级而变更，恕不另行通知**。请**务必以松下官网（http://industrial.panasonic.com）发布的最新版本官方手册为准**。
> 4. **使用风险与责任免责**：工业自动化控制涉及高压电、精密机械及高危环境。**本文档中任何文字、图表、代码及接线图解，均不构成对任何具体项目的适用性、安全性或可靠性的担保或承诺**。因不恰当的接线、系统寄存器错误配置、通信干扰、未遵循安全规范或直接套用本教程，进而导致的设备损坏、财产损失、生产事故甚至严重人身伤害，**文档原作者、整理者及发布平台（包括但不限于GitHub）均不承担任何法律、经济及连带责任**。
> 5. **务必遵守安全操作规程**：在安装、运行、保养或检查FP-XH系列及任何相关外围设备之前，请务必**完整阅读并严格遵守松下官方《用户手册》中的“安全注意事项”**。任何在通电状态下进行的接线、插件安装或拆卸操作，均可能引发严重的触电或设备短路事故。

---

# 🔌 Panasonic FP-XH Series PLC Communication Mastery

[![Panasonic](https://img.shields.io/badge/Panasonic-FP--XH-blue)](https://industrial.panasonic.com)
[![Protocol Support](https://img.shields.io/badge/Protocol-PLC%20Link%20%7C%20MEWTOCOL%20%7C%20MODBUS%20%7C%20General-green)](https://github.com)
[![Documentation](https://img.shields.io/badge/Manual-WUMC--FPXHCOM-orange)](https://industrial.panasonic.com)

> **A Comprehensive Technical Deep Dive into FP-XH Serial & Ethernet Communication**
> Based on official user manual (WUMC-FPXHCOM). Covers hardware architecture, wiring schematics, system register configuration, protocol logic (PLC Link, MEWTOCOL, MODBUS RTU, General Communication), and troubleshooting.

---

## 📚 Table of Contents

- [1. Core Architecture & Hardware](#-1-core-architecture--hardware)
- [2. Physical Layer Wiring: The "Vascular" System](#-2-physical-layer-wiring-the-vascular-system)
  - [2.1 Port Terminals & Wiring Diagram](#21-port-terminals--wiring-diagram)
- [3. System Register Configuration](#-3-system-register-configuration)
- [4. In-depth Protocol Analysis](#-4-in-depth-protocol-analysis)
  - [4.1 PLC Link (MEWNET-W0)](#41-plc-link-mewnet-w0)
  - [4.2 MEWTOCOL Master/Slave](#42-mewtocol-masterslave)
  - [4.3 MODBUS RTU Master/Slave](#43-modbus-rtu-masterslave)
  - [4.4 General Communication (Raw Data)](#44-general-communication-raw-data)
- [5. Diagnostic & Troubleshooting Guide](#-5-diagnostic--troubleshooting-guide)
- [6. Technical Specifications Quick Reference](#-6-technical-specifications-quick-reference)

---

## 🔥 1. Core Architecture & Hardware

The **FP-XH Series** is renowned for its flexible expansion capabilities. It offers a built-in standard **COM.0 port (RS-232C)** and **removable Serial Communication Cassettes** for flexible industrial networking.

### 📦 Communication Cassettes Overview

| Ordering Code | Interface Options | Channels | Mapping (Slot 1 / Slot 2) | Isolation |
| :--- | :--- | :--- | :--- | :--- |
| **AFPX-COM1** | RS-232C | 1 (5-Wire) | COM.1 / COM.3 | Non-insulated |
| **AFPX-COM2** | RS-232C | 2 (3-Wire) | COM.1, COM.2 / COM.3, COM.4 | Non-insulated |
| **AFPX-COM3** | RS-485 / RS-422 | 1 | COM.1 / COM.3 | **Insulated** |
| **AFPX-COM4** | RS-485 + RS-232C | 1 + 1 | COM.1(485), COM.2(232) / COM.3(485), COM.4(232) | Insulated (485 only) |
| **AFPX-COM5** | **Ethernet** + RS-232C | 1 + 1 | COM.1(ETH), COM.2(232) / COM.3(ETH), COM.4(232) | Non-insulated |
| **AFPX-COM6** | RS-485 | 2 | COM.1, COM.2 / COM.3, COM.4 | **Insulated** |

> [!WARNING]
> - **C14 Models** can only mount **1** communication cassette.
> - **C30/C40/C60 Models** can mount up to **2** cassettes.
> - When installing a Function Cassette (e.g., Analog AFPX-AD2) + Communication Cassette, **Mount the Communication Cassette ON TOP of the Function Cassette**.

### ⚙️ Port Function Restrictions

*   **PLC Link** functionality is **only** available on the built-in **COM.0** OR the first slot **COM.1** (can't use both simultaneously).
*   **COM.4** supports **only MEWTOCOL-COM** communication.
*   **COM.4 inherits COM.3 parameters at Power-ON**, but can be changed dynamically via `SYS1` instruction after RUN.

---

## 📡 2. Physical Layer Wiring: The "Vascular" System

### 📌 General Wiring Specifications
- **Stripping length**: `7 mm`
- **Cable**: Twisted-pair, 0.08 mm² ~ 1.00 mm² (AWG 28~16)
- **Tightening Torque**: Strictly `0.22 N·m ~ 0.25 N·m`
- **Screwdriver**: Dedicated precision flathead (Blade width 0.4×2.5mm)

> [!WARNING]
> **Clockwise rotation is MANDATORY**. If you tighten counter-clockwise, the terminal plate will lift, causing contact failure and intermittent communication drops. If you make a mistake, disconnect and re-insert.

### 2.1 Port Terminals & Wiring Diagram

#### 🔹 AFPX-COM1 (RS-232C 5-Wire)
- **Terminals**: `SD` (Tx), `RD` (Rx), `RS` (RTS), `CS` (CTS), `SG` (GND).
- **Crucial warning**: If using 3-wire connection (without hardware flow control), **You MUST short `RS` and `CS` terminals on the PLC side**. Otherwise, the PLC cannot detect the `CS=ON` (Clear to Send) state and will refuse to transmit.
- Wiring: `SD` ↔ `RD`, `RD` ↔ `SD`, `SG` ↔ `SG`.

#### 🔹 AFPX-COM3 / COM4 / COM6 (RS-485/RS-422)
- **SW1-SW4 DIP Switches** (Crucial for 485/422):
  - **SW1, SW2, SW3**: Set **ALL to ON** for RS-485; **ALL to OFF** for RS-422.
  - **SW4 (Terminating Resistor)**:
    - RS-485: **Turn ON ONLY at the PHYSICAL END** of the network segment.
    - RS-422: **Turn ON** (Always enabled).
- Wiring: `S+` ↔ `S+`, `S-` ↔ `S-`, `R+` ↔ `R+`, `R-` ↔ `R-`.
- Use **Shielded Twisted-Pair** cable, ground the shield at **ONE SINGLE END**.

#### 🔹 AFPX-COM5 (Ethernet)
- Provides MDI/MDI-X auto-crossover. Use CAT5/UTP cable.
- **Hardware Initialization**: There is a physical Init. Switch. If network parameters (IP/DHCP) are corrupted, set the switch `ON`, power cycle, then **return switch to `OFF`**.

---

## 🧩 3. System Register Configuration

Configuration is performed via **FPWIN GR** software (`Option` -> `PLC System Register Setting`).

| System Reg. | Parameter | Range | Default | Note |
| :--- | :--- | :--- | :--- | :--- |
| **No.410** | Station No. (Unit No.) | 1 - 99 | 1 | Must be unique in the network |
| **No.412** | Communication Mode | 0-4 | Computer Link | Options: General, PC(PLC) Link, MODBUS RTU |
| **No.413** | Transmission Format | - | 8, Odd, 1 | Data Bits, Parity, Stop Bits. |
| **No.414** | End/Start Codes | - | CR / None | CR+LF, ETX, STX |
| **No.415** | Baud Rate | - | 9600 bps | Max 230400 bps |
| **No.420** | RX Buffer Start Address (DT) | 40960 - 65532 | - | Define the memory region for General Comm Rx |
| **No.421** | RX Buffer Capacity (Words) | 0 - 2048 | 2048 | |
| **No.424** | End Judging Time (x0.01ms) | 0 - 10000 | 100 (1ms) | Time to wait after last char to judge frame end |

> [!NOTE]
> In MODBUS RTU mode, End Code is determined by **3.5 character idle time**, not by `CR` or `ETX`.

---

## 🔬 4. In-depth Protocol Analysis

### 4.1 PLC Link (MEWNET-W0)

Ideal for connecting multiple **Panasonic FP series** PLCs. Data is shared via **Link Relays (L)** and **Link Registers (LD)**.

*   **Topology**: RS-485 Multi-drop.
*   **Max Stations**: 16 units.
*   **Data Scope**: 1,008 points (L), 128 words (LD).

#### 📐 Response Time Calculation
> The refresh cycle **T_MAX** is critical for performance. The formula (per manual 4.3):
> `T_MAX = Ts1 + Ts2 + ... + Tsn + Tlt + Tso + Tlk`
>
> *   `Ts`: Time per station = Scan Time + (Transmission time per byte × Total bytes).
> *   `Tlk`: Link join time, which increases significantly if there are empty/unpowered stations in the 1~16 sequence.

**Calculation Example (Max 16 Stations, 5ms scan time):**
Calculated **T_MAX ≈ 198.44 ms** (Stable network). If there is **1 missing station**, this skyrockets to **~593.43 ms**!
> [!TIP]
> To maintain system responsiveness, **ALWAYS assign station numbers sequentially starting at 1**, and ensure no planned empty stations are left powered off.

---

### 4.2 MEWTOCOL Master/Slave

#### 🖥️ Master Mode (PLC initiates)
- **Read Data**: `F146 RECV` instruction.
- **Write Data**: `F145 SEND` instruction.

**Programming Key Point**:
```ladder
// Must check "SEND/RECV Executable Flag" before triggering
|---[ R913C ]-------------------[ F146 RECV, ... ]---|
```
If `R913C` is OFF, previous transaction is still active. Do not trigger `SEND`/`RECV` commands, or you will get a communication collision.

#### 📟 Slave Mode (PLC acts as server)
- **PLC program logic is NOT required** for the communication processing.
- The HMI or PC sends the MEWTOCOL command, and the FP-XH automatically parses the request and responds.

#### 🔎 Protocol Frame Structure (`%01#RCS...`)
- `Start`: `%` (ASCII H25)
- `Station`: `01` (2 digit ASCII)
- `Command`: `RCS`, `RD`, `WD`, etc.
- `BCC (Block Check Code)`: XOR from Start char to end of Text. Convert result to ASCII. Can be omitted with `**`.
- `End`: `CR` (H0D).

---

### 4.3 MODBUS RTU Master/Slave

**Universal Industrial Protocol**.

#### 🔗 PLC Memory Mapping (Crucial!)
| MODBUS Ref (Hex) | MODBUS Address | FP-XH Device |
| :--- | :--- | :--- |
| `000001` (0x0000) | Coil 0 | **Y0** (Output) |
| `040001` (0x0000) | Holding Reg 4x | **DT0** (Data Reg) |
| `030001` (0x0000) | Input Reg 3x | **WL0** (Link Reg) |

#### ⏱️ MODBUS RTU Frame Timing
MODBUS RTU is binary, frame end is detected by **3.5 character idle time**.
- At `9600 bps`, idle time ≈ **3.3 ms**.
- At `115200 bps`, idle time ≈ **0.3 ms**.
> [!WARNING]
> If you are using generic RS-232/485 adapters on the PC side, ensure the transmission packet interval is larger than 3.5 char time, otherwise the PLC will truncate the frame and return CRC/Format errors.

---

### 4.4 General Communication (Raw Data)

Used for connecting bar-code scanners, printers, or proprietary protocols with custom ASCII/Hex commands.

#### 📤 Sending Data (`F159 MTRN`)
1.  Store data into `DT` buffers. First word = Byte count.
2.  Execute `F159 MTRN, Start_DT, Byte_Count, COM_Port`.
3.  **Automatic padding**: PLC appends Start/End codes configured in System Reg. Do not include `STX`, `CR`, `ETX` in your user data buffer.

#### 📥 Receiving Data
- **Detection Flag**: `R913A` (COM.1 Rx Complete) turns `ON` when frame ends.
- **Read & Reset**:
    1.  Move RX buffer data to user memory (`F10 BKMV`).
    2.  **Crucial Next Step**: Execute `F159 MTRN, Dummy, K0, COM_Port`.
    > [!TIP]
    > The `K0` send instruction acts as a "Clear Receive Buffer" command, resetting the write pointer and turning `R913A` `OFF` to prepare for the next frame.

---

## 🛠️ 5. Diagnostic & Troubleshooting Guide

### 💥 RS-232C Communication Errors
*   **Check CS Signal**: Is RS shorted to CS (3-wire mode)? If using 5-wire, is remote device asserting CTS?
*   **Check Wiring**: Is `SD` connected to remote `RD`? Did you swap Tx/Rx? Is SG connected?
*   **System Registers**: Verify Baud rate, Parity bits, Data bits match EXACTLY.

### 📡 RS-485 / RS-422 Communication Errors
*   **Termination Resistors**: `SW4` ON for terminal stations ONLY. Removing termination resistors on intermediate nodes is mandatory.
*   **Cable Polarity**: `+` connected to `+`, `-` connected to `-`. If polarity is reversed, all stations will be unable to receive.
*   **Cable Type**: **NEVER mix** shielded twisted pair with standard unshielded flat cables.
*   **Baud/Distance Trade-off**: See section 6. `230400 bps` + 99 stations needs cable length < 300m.

### 🌐 Ethernet (AFPX-COM5) Communication Errors
*   **LINK/ACT LED OFF**: Physical layer failure. Check LAN cable and switch power.
*   **ERR LED ON**:
    *   `IP Address Duplication`: Change IP to a unique one.
    *   `DHCP Error`: DHCP server inaccessible. Check Router.
*   **ERR LED OFF but can't talk**: Check COM settings in System Register, verify IP and subnet mask.

---

## 📊 6. Technical Specifications Quick Reference

### 📈 Communication Speed vs Distance (RS-485/RS-422)

*   `38400 bps` or lower: Max **1200 m**.
*   `115200 bps`: Max **1000 m** (if < 99 nodes).
*   `230400 bps`: Max **700 m** (if < 20 nodes). For 99 nodes at high speed, keep length to **< 200~300 m** for stable communication.

### 🔢 Key Special Relays (User Program critical)

| Relay | Port | Function | Meaning |
| :--- | :--- | :--- | :--- |
| `R913C` | COM.1 | Send/Recv Executable | `ON`: Ready. `OFF`: Communication Busy. |
| `R913A` | COM.1 | General Rx Complete | `ON`: Received a data frame. |
| `R913B` | COM.1 | General Tx Complete | `ON`: Transmission finished. |
| `R9138` | COM.1 | Comm. Error | `ON`: Framing/Parity/Overrun error. |
| `DT90124`| COM.1 | Send/Recv Error Code | Contains error code if `R913D` is ON. |

> [!IMPORTANT]
> Because communication status flags can change within a single PLC scan, it is **highly recommended to latch** the flag status into an internal relay at the top of the program before performing conditional logic.

---

## 🎯 Final Words

The Panasonic FP-XH series provides a comprehensive toolkit for industrial communication, ranging from easy multi-PLC data sharing via `PLC Link` to highly flexible third-party connectivity via `MODBUS RTU` or `Raw Data`. By strictly adhering to physical wiring standards, understanding the system register mapping, and utilizing the correct high-level instructions (`F145`, `F146`, `F159`), you can achieve a highly stable and robust automation network in your factory floor.

For detailed instruction parameters, please refer to the official `FP Series Programming Manual` (ARCT1F353C) alongside this guide.

---
*Documentation generated based on Panasonic FP-XH User Manual (Communication Edition) WUMC-FPXHCOM.*
```
