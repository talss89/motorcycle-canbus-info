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

Headlight main / DRL and horn is also on bytes 0-3, but I forgot to note the bits. Will update.

> :thinking: **Possibility**
> 
> From the wiring diagram, it seems that the switchgear -> chassis ECM is via LIN. This could explain why 2 bits are used for each button state. Perhaps this is so the chassis ECM can detect tri state - `On / Off / Fault`.

#### Byte 1

- *Bit 1:* Horn (Set inactive)
- *Bit 2:* Horn (Set active)
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

### 0x600 [8 Bytes] - Tyre Pressure Monitoring System (TPMS)

Thank you to `T800XC` on the forum at https://www.tiger-explorer.com for this information.

- *Byte 1:* **Tyre** `00` = Front Tyre, `10` = Rear Tyre 
- *Byte 2:* **Pressure (PSI * 5)** `val / 5 == psi`
- *Byte 3:* **Temperature (ºC + 50)** `val - 50 = ºC`
- *Byte 4(4:7)* **Status** `0` = Stable, `2` = Rising Pressure, `3` = Falling Pressure
- *Byte 4(0:3)* **Status** `0` = Battery OK, `7` = No Battery Signal, `8` = Low Battery
- *Byte 5:* **Unused** `00`
- *Byte 6+7 (uint16_t):* **Data Counter** Increments on each transmission, reset to `0x0000` when temperature or pressure changes
- *Byte 8(4:7)* **Fault Status** `0` = OK, `4` = No Comms, `8` = Leak
- *Byte 8(0:3)* **Fault Status** `A` = TPMS Warning Light On, `C` = TPMS Warning Light Off

> If psi increases, stays level or decreases, need to change status byte high nibble to suit.
 0x0n = stable, 0x2n = decrease, 0x3n = increase, 0x4n = ??
>
> Data counter resets to 0x0000 whenever the psi or temperature values change.
>
> When the TPMS warning light 'ON' status data is sent, the light turns on immediately. To disable the warning light, two or more consecutive 'OFF' data are required.
> 
> When the warning light changes state, the sensor (F / R) causing the change sends its data twice in succession instead of waiting its turn in the CAN train.
> 
> CAN ID '600' data is sent at ~500ms intervals, alternating between '600 00' and '600 10' for front and rear.
> 
> If there's no data from the sensors (e.g. they've not woken), the following default data values are sent, also showing the data count incrementing...
> 
> `600 00 01 01 07 00 01 00 4A`
> 
> `600 10 01 01 07 00 01 00 4A`
> 
> `600 00 01 01 07 00 02 00 4A`
> 
> `600 10 01 01 07 00 02 00 4A`
> 
> `600 00 01 01 07 00 03 00 4A`
> 
> `600 10 01 01 07 00 03 00 4A`
> 
> `600 00 01 01 07 00 04 00 4A`
> 
> `600 10 01 01 07 00 04 00 4A`
> 
> `600 00 01 01 07 00 05 00 4A`
> 
> `600 10 01 01 07 00 05 00 4A`
> 
