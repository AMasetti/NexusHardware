# Optimus — Wiring Reference

## ESP32-C3 Super Mini GPIO Pinout

| GPIO | Function | Connected to |
|------|----------|--------------|
| GPIO 8 | I2C SDA | PCA9685 SDA, MPU6050 SDA |
| GPIO 9 | I2C SCL | PCA9685 SCL, MPU6050 SCL |
| USB-C | Flash / Monitor | Host PC (arduino-cli upload / serial monitor) |

All other GPIOs unused. I2C bus runs at 400 kHz (`I2C_FREQ_HZ 400000`).

---

## I2C Bus Topology

Both the PCA9685 and MPU6050 share the same I2C bus (SDA=GPIO8, SCL=GPIO9).

```
ESP32-C3
  GPIO 8 (SDA) ──┬── PCA9685 SDA  (addr 0x40)
                 └── MPU6050 SDA  (addr 0x68)

  GPIO 9 (SCL) ──┬── PCA9685 SCL
                 └── MPU6050 SCL

  3.3V ──────────┬── PCA9685 VCC (logic)
                 └── MPU6050 VCC

  GND ───────────┬── PCA9685 GND
                 └── MPU6050 GND
```

Add 4.7 kΩ pull-up resistors from SDA and SCL to 3.3V if not present on the module boards.

---

## PCA9685 Channel Map

PWM frequency: 50 Hz. All angles measured from servo center (90° = halt).

### Leg Servos — MG995 (600–2650 µs)

| Ch | Joint | Firmware name | `set_inverse` | Halt° | Notes |
|----|-------|---------------|:-------------:|------:|-------|
| 0  | R ankle_roll | `r_ankle_roll` | No | 90 | |
| 1  | R knee | `r_knee` | No | 80 | |
| 2  | R hip_pitch | `r_hip_pitch` | **Yes** | 100 | Inverted axis |
| 3  | R hip_roll | `r_hip_roll` | **Yes** | 100 | Inverted axis |
| 12 | L hip_roll | `l_hip_roll` | No | 80 | |
| 13 | L hip_pitch | `l_hip_pitch` | No | 90 | |
| 14 | L knee | `l_knee` | **Yes** | 90 | Inverted axis |
| 15 | L ankle_roll | `l_ankle_roll` | **Yes** | 70 | Inverted axis |

Channels 11 are unused.

`set_inverse=Yes` means the servo is mechanically mirrored — the firmware negates the angle before writing the pulse.

**Ankle constraint:** `ankle_roll = −hip_roll` always (keeps foot flat). Do not break this in firmware.

### Arm Servos — Futaba S3003 (900–2100 µs)

| Ch | Joint | Firmware name | Dir | T-pose° |
|----|-------|---------------|:---:|--------:|
| 4  | R shoulder forward/backward | `r_shoulder_fb` | +1 | 90 |
| 5  | R shoulder lateral elevation | `r_shoulder_lat` | +1 | 90 |
| 6  | R forearm lateral elevation | `r_forearm_lat` | +1 | 90 |
| 7  | L shoulder forward/backward | `l_shoulder_fb` | +1 | 90 |
| 8  | L shoulder lateral elevation | `l_shoulder_lat` | +1 | 90 |
| 9  | L forearm lateral elevation | `l_forearm_lat` | +1 | 90 |
| 10 | Hip yaw (waist rotation) | `hip_yaw` | +1 | 90 |

All arm servos are at their mechanical center (1500 µs) in T-pose. No trim offsets required.

---

## Power Rail Diagram

```
LiPo 2S 7.4V
     │
     ├── DC-DC Buck (→ 5V) ─── ESP32-C3 5V pin  (logic)
     │                     └── PCA9685 VCC servo rail
     │
     └── PCA9685 V+ (servo power)
              │
              ├── ch 0–3, 12–15  → MG995 ×8  (legs)
              └── ch 4–10        → Futaba S3003 ×7  (arms + hip yaw)
```

**Servo power and logic power are separated at the PCA9685 board:**
- `VCC` pin = 3.3V or 5V logic supply (from ESP32-C3 or buck converter)
- `V+` screw terminal = servo power supply (direct from LiPo or 5V regulated)

MG995 stall current is ~1.2A per servo; 8 servos simultaneous stall ≈ 9.6A peak.
Use wire gauge appropriate for expected current (≥22 AWG for servo rails, ≥18 AWG for main lipo lead).

---

## Servo Tuning

Per-servo trim offsets are in `optimus/firmware/include/config.h` under `SERVO_OFFSET_DEG_*`.
Adjust these to compensate for mechanical misalignment after assembly.
Run `make compile-upload` after any change and verify with `make monitor`.

---

## Datasheets

See [`hardware/datasheets/`](../datasheets/README.md) for PDFs: ESP32-C3, PCA9685, MPU6050, MG995.
