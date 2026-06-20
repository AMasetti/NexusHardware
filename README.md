# NexusHardware

CAD files, STLs, GCode, and datasheets for the Nexus Robotics hardware. Reference-only — no firmware or code lives here.

Part of the [NexusRobotics](https://github.com/AMasetti/NexusRobotics) monorepo.

---

## Robots

### Optimus Biped Robot

Active development target. 8-DOF biped with MG995 leg servos and Futaba S3003 arm servos.

| Component | Part | Notes |
|---|---|---|
| MCU | ESP32-C3 Super Mini | Firmware in [NexusFirmware](https://github.com/AMasetti/NexusFirmware) |
| Servo driver | PCA9685 16-ch PWM | I2C @ 0x40 |
| IMU | MPU6050 | I2C @ 0x68 |
| Leg servos ×8 | MG995 | PWM 600–2650 µs |
| Arm servos ×6 + hip yaw | Futaba S3003 | PWM 900–2100 µs |

Leg geometry: femur 90mm → knee → tibia 90mm → ankle → foot 30mm.

### Tini Biped (Archived)

Earlier biped prototype. Superseded by Optimus. Files kept for reference.

---

## File structure

```
NexusHardware/
├── Optimus Biped Robot/
│   ├── STL/          # Print-ready STL files for all body parts
│   └── STEP/         # Source CAD (editable in Fusion 360, FreeCAD, etc.)
├── Tini Biped (Archived project)/
│   ├── 20240402/     # Iteration by date — STL + GCode
│   ├── 20240405/
│   ├── 20240413/
│   ├── 20240419/
│   ├── 20240422/
│   ├── leg-assemblies/  # Full leg assembly GCode
│   └── misc/            # Loose parts from early iterations
├── cad resources/
│   ├── pca9685/      # PCA9685 CAD model (STEP, STL, SKP, OBJ)
│   ├── power/        # LM2596 step-down, battery holder, I2C controller (STEP)
│   └── raspberry-pi-zero-w/  # Pi Zero W CAD model (STEP, IGS, STL)
└── datasheets/       # Component datasheets
```

---

## Printing notes

- All STLs are sized for FDM printing in PLA or PETG.
- GCode files in `Tini Biped/` were sliced for a specific printer — re-slice from STL for your setup.
- STEP files are the editable source; modify these if you need to adjust tolerances.

---

## MuJoCo meshes

The STL files from `Optimus Biped Robot/STL/` are mirrored in [NexusSimulation](https://github.com/AMasetti/NexusSimulation) under `Optimus Full/meshes/` for use in the MuJoCo simulation. If you update a part, update both.
