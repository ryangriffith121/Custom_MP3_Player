# Custom MP3 Player

A pocket-sized, cylindrical MP3 player built around an ESP32-S3. It's a hobby build inspired by the Sony NW-E507 "stick" Walkman. It has a long color bar display and three rotating knobs for all of its controls.

> **Status:** Design phase. The parts list is final, and a SolidWorks mock assembly of the housing and internals is in progress. The PCBs and firmware haven't been started yet.

---

## Concept

The whole player is a single tube, about **40 mm in diameter** and **130–160 mm long**:

```
 ┌──────────────────────────────┬────────┬────────┬────────┐
 │   ┌──────────────────────┐   │        │        │        │
 │   │   76×284 bar TFT     │   │ Knob 1 │ Knob 2 │ Knob 3 │
 │   └──────────────────────┘   │        │        │        │
 └──────────────────────────────┴────────┴────────┴────────┘
 |<──────────────────── 130–160 mm ────────────────────────>|
```

- **Display section:** a 2.25" 76×284 color IPS "bar" screen behind a semi-translucent shell, like the NW-E507.
- **Three ring knobs:** the knobs sit in a row next to the display. Each one is a large ring the full width of the tube. A gear on the inside of each ring drives a small rotary encoder inside the body.
- **Internals:** a long "spine" PCB runs down the center of the tube. The 18650 cell sits next to it.

Which knob controls volume and which controls tracks hasn't been decided yet.

---

## Hardware

### Bill of materials

| Qty | Part | Role | Link |
|:---:|------|------|------|
| 1 | Adafruit ESP32-S3 Feather (4 MB Flash / 2 MB PSRAM) | Main MCU: USB-C, LiPo charger, MAX17048 fuel gauge | [Adafruit 5477](https://www.adafruit.com/product/5477) |
| 1 | 2.25" 76×284 color IPS bar TFT (SPI) | Main display | [AliExpress](https://www.aliexpress.us/item/3256809009956989.html) |
| 3 | Bourns PEC11R-4015F-S0024 rotary encoder | Knob inputs: no detents, 15 mm shaft, push switch, 24 pulses/rev | [DigiKey](https://www.digikey.com/en/products/detail/bourns-inc/PEC11R-4015F-S0024/4499668) |
| 1 | Adafruit TLV320DAC3100 I2S DAC | Audio DAC and headphone amp, with onboard headphone jack | [Adafruit 6309](https://www.adafruit.com/product/6309) |
| 1 | Adafruit Micro SD SPI/SDIO breakout (3 V) | Music storage | [Adafruit 4682](https://www.adafruit.com/product/4682) |
| 1 | 3.7 V 2200 mAh 18650 Li-ion cell (protected) | Power | [Adafruit 1781](https://www.adafruit.com/product/1781) |
| 2 | 10-pin (2×5) IDC ribbon cable | Spine board ↔ secondary board | [AliExpress](https://www.aliexpress.us/item/3256808902438744.html) |
| 2 | 2×5 SMD male pin header | Ribbon connectors on the PCBs | [AliExpress](https://www.aliexpress.us/item/3256805859910212.html) |
| 1 | Spine board | Custom PCB | *Ordered from a PCB manufacturer* |
| 1 | Secondary board | Custom PCB | *Ordered from a PCB manufacturer* |
| 1 | Housing, knobs, and gears | Enclosure | *3D printed* |

The same list is in [`MP3 Player Final Parts list.txt`](MP3%20Player%20Final%20Parts%20list.txt).

**Why these parts:**

- **The 5477 Feather variant:** the planned MP3 decoder library needs PSRAM, and the similar-looking Feather ESP32-S3 5323 has none.
- **The TLV320DAC3100:** the ESP32-S3 has no built-in DAC. This board adds a real headphone driver plus hardware volume control over I²C.
- **The no-detent PEC11R-4015F:** detents multiplied through a gear ratio would feel like a buzzy ratchet. Any detents will be added to the ring itself instead.

### Architecture

```
                     ┌───────────────────────────────┐
   USB-C ──────────► │      ESP32-S3 Feather         │ ◄──── 18650 Li-ion
   (charge/flash)    │  (on the spine board)         │       (MAX17048 gauge)
                     └──┬──────────┬──────────┬──────┘
                        │ GPIO     │ I2S+I²C  │ SPI
                        ▼          │          │
               3× PEC11R encoders  │          │
               (gear-driven rings) │          │    2× 10-pin ribbon
                                   ▼          ▼  ───────────────────────┐
                        TLV320DAC3100    microSD card                   ▼
                        → headphones                          76×284 IPS TFT
                                                              (secondary board)
```

**Board split (planned):**

- **Spine board:** the ESP32-S3 Feather and the encoders. It runs the length of the body.
- **Secondary board:** the display end. It connects to the spine board with two 10-pin ribbon cables on 2×5 SMD headers.

---

## CAD

Mechanical design is done in SolidWorks.

```
CAD/
├── ScreenPCB.SLDPRT                 # screen-end PCB outline
└── mp3_cad/
    ├── MockAssembly.SLDASM          # full mock-up of the player
    ├── Screen.SLDASM / Screen.STEP  # display sub-assembly
    ├── adafruit/                    # Feather, 18650, DAC, microSD models + tube mock
    ├── er-tft2-25-1-1.snapshot.1/   # 2.25" bar TFT (ER-TFT2.25-1) in 3 FPC variants
    ├── headers/                     # 2×5 SMD headers at 1.27 / 2.00 / 2.54 mm pitch
    └── README.md                    # where every model came from
ECAD/
└── mp3_cad/                         # STEP models of the off-the-shelf parts for PCB layout
```

Most of the off-the-shelf models come from [Adafruit_CAD_Parts](https://github.com/adafruit/Adafruit_CAD_Parts) and the [KiCad 3D library](https://gitlab.com/kicad/libraries/kicad-packages3D). The Feather 5477 uses Adafruit's 5323 model, since both boards have the same PCB. [`CAD/mp3_cad/README.md`](CAD/mp3_cad/README.md) has the full source list, including where to get models for the PEC11R encoders and the ribbon cable.

---

## Design notes and open questions

These come from a feasibility review of the design. They're still being worked out.

- **Encoder mounting:** the PEC11R has rear-facing pins. Soldered flat to the spine, its shaft would point outward instead of along the tube. The leading fix is to mount each encoder on a small round "bulkhead" PCB set at right angles to the spine, so the shaft runs parallel to the tube axis.
- **Gear ratio:** a low ratio is better. The encoders are rated for 60 RPM and 30,000 cycles, and a high ratio burns through both. A good starting point is about 2.5:1 (module 0.8, a 36-tooth internal ring driving a 14-tooth pinion), which still gives about 62 pulses per knob turn.
- **Push input:** the ring knobs can't press their encoders' switches through the gears, so "press" needs another route. Options include making the last ring slide axially onto a tact switch, adding a small end-cap button, or using the DAC's headset-detect pins for inline-remote play/pause.
- **Power:** add a hard power switch, such as a slide switch from EN to GND. The Feather's onboard charger is slow (likely about 200 mA), so a full 18650 charge could take 10+ hours.
- **Pin budget:** the design fits the Feather's roughly 20 GPIOs if the microSD runs in SPI or 1-bit SDIO mode rather than 4-bit SDIO.
- **Audio noise:** keep Wi-Fi and Bluetooth off during playback, run a clean analog ground to the jack, and add bulk capacitance near the SD card and DAC.
- **Printed gears:** use a 0.8 module or larger, a 20° pressure angle, and PETG or nylon. Each ring rides on a stationary sleeve and is held between the bulkhead disks.

---

## Firmware

Not started yet. Planned approach (Arduino / ESP32-S3):

- **Decoding:** [ESP32-audioI2S](https://github.com/schreibfaul1/ESP32-audioI2S) handles MP3 decoding from the SD card, plus other formats, and provides ID3 tags for the track display.
- **DAC setup:** the TLV320DAC3100 is configured over I²C with Adafruit's TLV320 library, with volume set in DAC registers instead of by scaling samples.
- **Encoders:** read by the ESP32-S3's PCNT (pulse counter) hardware, so knob turns aren't missed while audio is playing.
- **Battery:** charge level read from the Feather's MAX17048 fuel gauge.

Planned features:

- [ ] Display driver and UI layout for the 76×284 bar screen
- [ ] Encoder input with PCNT plus push-switch handling
- [ ] microSD library scan and track browsing
- [ ] MP3 playback over I2S to the TLV320DAC3100
- [ ] Volume, play/pause, and next/previous track
- [ ] Scrolling artist and title text from ID3 tags
- [ ] Battery level display and deep sleep

---

## Roadmap

- [x] Concept sketch and form factor (~40 mm × 130–160 mm)
- [x] Feasibility review
- [x] Final parts selection
- [x] Collect CAD models for the off-the-shelf parts
- [ ] SolidWorks mock assembly *(in progress)*
- [ ] Knob, ring gear, and bulkhead design
- [ ] Spine board and secondary board PCB design
- [ ] Breadboard prototype (Feather + DAC + SD + display + encoders)
- [ ] Firmware
- [ ] PCB order and assembly
- [ ] Printed housing and final assembly

---

## Inspiration

The Sony NW-E507 is a stick-shaped digital music player from the mid-2000s. This project borrows its cylindrical body, inline controls, and translucent display window.

## Author

**Ryan Griffith**: [GitHub](https://github.com/ryangriffith121) · [LinkedIn](https://linkedin.com/in/ryan-l-griffith-g012108)
