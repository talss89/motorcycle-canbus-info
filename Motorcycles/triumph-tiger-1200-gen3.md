# 2017 - 2021 Triumph Tiger 1200 Gen 3

Incomplete list, these are just sniffing notes as I work on the bus.

## Bus Architecture

The bike has a single bus. The following physical modules are connected to it:

- Chassis ECM
- Engine ECM
- Instruments / Dash
- Reserved Connector (under seat, for OEM bluetooth module)
- OBD2 Diagnostic Connector
- ABS Modulator
- Inertial Measurement Unit (IMU)
- Front Light Unit
- Electronic Steering Lock

## CAN IDs

Lower IDs will win arbitration in the event of a collision. Systems that are safety-critical and/or require low latency in response to bus inputs (eg. ABS Modulator) should have a lower ID than say, handlebar controls.

### 0x210 [? Bytes] - Brake Pressure & ?

Source: `ABS Modulator`

---

### 0x500 [? Bytes] - Brake Light & Noisy Data

Source: `?`

Byte 4, bit 3 appears to get set when brake light is activated.

---

### 0x567 [8 Bytes] - Handlebar Controls (0-3) & ? (4-8) 

Source: `Chassis ECM`

The buttons each are allocated 2 bits (or a *crumb*). They shift left on press, or 1 == inactive, 2 == pressed.

Headlight main / DRL is also on bytes 0-3, but I forgot to note the bits. Will update.

> :thinking: **Possibility**
> 
> From the wiring diagram, it seems that the switchgear -> chassis ECM is via LIN. This could explain why 2 bits are used for each button state. Perhaps this is so the chassis ECM can detect tri state - `On / Off / Fault`.

#### Byte 1

- *Bit 1:* Unknown
- *Bit 2:* Unknown
- *Bit 3:* Joystick Press (Set inactive)
- *Bit 4:* Joystick Press (Set active)
- *Bit 5:* Indicator / Turn Signal Left (Set inactive)
- *Bit 6:* Indicator / Turn Signal Left (Set active)
- *Bit 7:* Fog Light Button (Set inactive)
- *Bit 8:* Fog Light Button (Set active)

#### Byte 2

- *Bit 1:* Joystick Left (Set inactive)
- *Bit 2:* Joystick Left (Set active)
- *Bit 3:* Joystick Right (Set inactive)
- *Bit 4:* Joystick Right (Set active)
- *Bit 5:* Unknown
- *Bit 6:* Unknown
- *Bit 7:* Home (Set active)
- *Bit 8:* Home (Set inactive)

#### Byte 3

- *Bit 1:* Joystick Up (Set inactive)
- *Bit 2:* Joystick Up (Set inactive)
- *Bit 3:* Indicator / Turn Signal Cancel (Set inactive)
- *Bit 4:* Indicator / Turn Signal Cancel (Set active)
- *Bit 5:* Headlight Pass / Flash (Set inactive)
- *Bit 6:* Headlight Pass / Flash (Set active)
- *Bit 7:* Joystick Down (Set inactive)
- *Bit 8:* Joystick Down (Set active)

#### Byte 4

- *Bit 1:* Indicator / Turn Signal Right (Set inactive)
- *Bit 2:* Indicator / Turn Signal Right (Set active)
- *Bit 3:* Rider Heated Seat (Set inactive)
- *Bit 4:* Rider Heated Seat (Set active)
- *Bit 5:* Mode (Set inactive)
- *Bit 6:* Mode (Set active)
- *Bit 7:* Unknown
- *Bit 8:* Unknown

---
