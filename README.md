# NexusHardware

CAD, print-ready STLs, bill of materials, wiring and datasheets for the Optimus biped robot. Reference-only — no firmware or code lives here.

Related repos: [NexusFirmware](https://github.com/AMasetti/NexusFirmware) · [NexusSimulation](https://github.com/AMasetti/NexusSimulation) · [NexusFuturespace](https://github.com/AMasetti/NexusFuturespace)

---

## Optimus Biped Robot

8-DOF biped with MG995 leg servos and Futaba S3003 arm servos.

| Component | Part | Notes |
|---|---|---|
| MCU | ESP32-C3 Super Mini | Firmware in [NexusFirmware](https://github.com/AMasetti/NexusFirmware) |
| Servo driver | PCA9685 16-ch PWM | I2C @ 0x40 |
| IMU | MPU6050 | I2C @ 0x68 |
| Leg servos ×8 | MG995 | PWM 600–2650 µs |
| Arm servos ×6 + hip yaw | Futaba S3003 | PWM 900–2100 µs |

Leg geometry: femur 90mm → knee → tibia 90mm → ankle → foot 30mm.

Full parts list in [`BOM.csv`](optimus-biped-robot/BOM.csv); pinout and power distribution in [`wiring.md`](optimus-biped-robot/wiring.md).

---

## File structure

```
NexusHardware/
├── optimus-biped-robot/
│   ├── STL/          # Print-ready STL files for all body parts
│   ├── STEP/         # Source CAD (editable in Fusion 360, FreeCAD, etc.)
│   ├── BOM.csv       # Bill of materials
│   └── wiring.md     # Wiring and power distribution
└── datasheets/       # Component datasheets
```

---

## Printing notes

- All STLs are sized for FDM printing in PLA or PETG.
- STEP files are the editable source; modify these if you need to adjust tolerances.

---

## MuJoCo meshes

Lower-resolution copies of the STLs live in [NexusSimulation](https://github.com/AMasetti/NexusSimulation) under `optimus/urdf/full/meshes/` for the MuJoCo model. If you update a part, re-export both.
