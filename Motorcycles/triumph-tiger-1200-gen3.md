# 2018 - 2021 Triumph Tiger 1200

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
- Electronic Steering Lock (this also has an additional non-canbus connection to the Chassis ECM, and Dashboard - possible safety lockout?)

## CAN IDs

### 0x210 [? Bytes] - Brake Pressure & ?

Source: `ABS Modulator`

---

### 0x500 [? Bytes] - Brake Light & Noisy Data

Source: `Chassis ECM`

Byte 4, bit 3 appears to go high when either brake activates brake light. Suggest that module controlling brake LED is listening to this packet.

---

### 0x567 [8 Bytes] - Handlebar Controls (0-3) & ? (4-8) 

Source: `Chassis ECM`

The buttons seem to have 2 bits (or a *crumb*) allocated. They shift left on press, or 1 == inactive, 2 == pressed.

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
- *Bit 4:* Rider Heated Seaat (Set active)
- *Bit 5:* Mode (Set inactive)
- *Bit 6:* Mode (Set active)
- *Bit 7:* Unknown
- *Bit 8:* Unknown

---
