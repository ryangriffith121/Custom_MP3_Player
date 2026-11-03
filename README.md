# Custom MP3 Player

A pocket-sized, cylindrical MP3 player built around an ESP32-S3. It's a hobby build inspired by the Sony NW-E507 "stick" Walkman. The player has a long bar display on one end and three rotating knobs for all of its controls.

> **Status:** Design phase. The parts list is final and the PCBs and housing are being designed. Firmware hasn't been started yet.

---

## Concept

The whole player is a single tube, about **40 mm in diameter** and **130–160 mm long**, split into four sections along its length:

```
 ┌──────────────────────────────┬────────┬────────┬────────┐
 │   ┌──────────────────────┐   │        │        │        │
 │   │   76×284 bar TFT     │   │ Knob 1 │ Knob 2 │ Knob 3 │
 │   └──────────────────────┘   │        │        │        │
 └──────────────────────────────┴────────┴────────┴────────┘
 |<──────────────────── 130–160 mm ────────────────────────>|
```

- **Display section:** a 2.25" color IPS "bar" screen behind a semi-translucent shell, like the NW-E507.
- **Three knob sections:** each knob is a large ring the full width of the tube. A gear on the inside of each ring drives a small rotary encoder mounted on the internal spine board. The encoders also have push switches, so the knob at the end works as the main button.
- **Internals:** a long "spine" PCB runs down the center of the tube. The 18650 cell sits next to it.

---

## Hardware

### Bill of materials

| Qty | Part | Role | Link |
|:---:|------|------|------|
| 1 | Adafruit ESP32-S3 Feather (4 MB Flash / 2 MB PSRAM) | Main microcontroller, USB-C, and LiPo charging | [Adafruit 5477](https://www.adafruit.com/product/5477) |
| 1 | 2.25" 76×284 color IPS bar TFT | Main display | [AliExpress](https://www.aliexpress.us/item/3256809009956989.html) |
| 3 | Bourns PEC11R-4015F-S0024 rotary encoder | Knob inputs (24 pulses/rev, push switch) | [DigiKey](https://www.digikey.com/en/products/detail/bourns-inc/PEC11R-4015F-S0024/4499668) |
| 1 | Adafruit TLV320DAC3100 I2S DAC | Audio output, headphone jack | [Adafruit 6309](https://www.adafruit.com/product/6309) |
| 1 | Adafruit Micro SD SPI/SDIO breakout (3 V) | Music storage | [Adafruit 4682](https://www.adafruit.com/product/4682) |
| 1 | 3.7 V 2200 mAh 18650 Li-ion cell | Power | [Adafruit 1781](https://www.adafruit.com/product/1781) |
| 1 | 20-pin IDC ribbon cable | Connects the spine board to the secondary board | [AliExpress](https://www.aliexpress.us/item/3256808902438744.html) |
| 1 | Spine board | Custom PCB | *Ordered from a PCB manufacturer* |
| 1 | Secondary board | Custom PCB | *Ordered from a PCB manufacturer* |
| 1 | Housing, knobs, and gears | Enclosure | *3D printed* |

The same list is in [`MP3 Player Final Parts list.txt`](MP3%20Player%20Final%20Parts%20list.txt).

### Architecture

```
                    ┌───────────────────────────────┐
   USB-C ─────────► │      ESP32-S3 Feather         │ ◄──── 18650 Li-ion
   (charge/flash)   │  (on the spine board)         │
                    └──┬──────────┬──────────┬──────┘
                       │ GPIO     │ I2S/I2C  │ SPI / SDIO
                       ▼          │          │
              3× PEC11R encoders  │          │        20-pin ribbon
              (gear-driven knobs) │          │ ─────────────────────────┐
                                  ▼          ▼                          ▼
                       TLV320DAC3100    microSD card          76×284 IPS TFT
                       → headphones                          (secondary board)
```

**Board split (planned):**

- **Spine board:** ESP32-S3 Feather and the three encoders. It runs the length of the knob sections.
- **Secondary board:** headphone jack/DAC, microSD, and the display. It connects to the spine board with the 20-pin ribbon cable.

---

## Firmware

Not started yet. Planned scope:

- [ ] Display driver and UI layout for the 76×284 bar screen
- [ ] Encoder input with debounced push switches
- [ ] Read the microSD and scan the music library
- [ ] MP3 decoding with I2S output to the TLV320DAC3100
- [ ] Playback controls, volume, and track browsing
- [ ] Battery level reading and power management

---

## Roadmap

- [x] Concept sketch and form factor (~40 mm × 130–160 mm)
- [x] Final parts selection
- [ ] Spine board and secondary board PCB design
- [ ] Housing, knob, and gear CAD
- [ ] Breadboard prototype (Feather + DAC + SD + display + encoders)
- [ ] Firmware
- [ ] PCB order and assembly
- [ ] Printed housing and final assembly

---

## Inspiration

The Sony NW-E507 is a stick-shaped digital music player from the mid-2000s. This project borrows its cylindrical body, inline controls, and translucent display window.

## Author

**Ryan Griffith**: [GitHub](https://github.com/ryangriffith121) · [LinkedIn](https://linkedin.com/in/ryan-l-griffith-g012108)
