# MP3 Player CAD Models: Sources

The STEP files in this zip come from the sources listed below. Parts marked "download yourself" need a login or are on sites this bundle couldn't reach.

| Part (final list) | Model | Source |
|---|---|---|
| ESP32-S3 Feather 4MB/2MB PSRAM (5477) | `adafruit/5477 Feather ESP32-S3 (use 5323 model - same PCB).step` | Adafruit_CAD_Parts, folder `5323 Feather ESP32-S3`. There's no folder for 5477, but Adafruit uses the same schematic and layout for PIDs 5323, 5477 and 5885. Only the module variant changes. |
| 18650 2200 mAh (1781) | `adafruit/1781 2200mAh Battery.step` | Adafruit_CAD_Parts, `1781 2200mAh Battery` |
| TLV320DAC3100 DAC + headphone jack (6309) | `adafruit/6309 TLV320DAC3100 I2S DAC.step` | Adafruit_CAD_Parts, `6309 TLV320DAC3100 I2S DAC`. The model includes the onboard TRS jack. |
| Micro SD breakout (4682) | `adafruit/4682 Micro SD Breakout.step` | Adafruit_CAD_Parts, `4682 Micro SD Breakout` |
| 2x5 SMD male header | `headers/PinHeader_2x05_P{1.27,2.00,2.54}mm_Vertical_SMD.step` | KiCad 3D library (GitHub mirror). All three pitches are included; keep the one you ordered. |
| PEC11R-4015F-S0024 encoder ×3 | **download yourself** | See notes below |
| 2.25" 76×284 bar TFT | **download yourself or model it** | See notes below |
| 10-pin IDC ribbon cable | **download yourself or model it** | See notes below |
| Spine board, secondary board, housing | n/a | Your own design. Export these from KiCad/Fusion once they exist. |

Adafruit repo: https://github.com/adafruit/Adafruit_CAD_Parts (each folder also has .f3d files for Fusion 360)
KiCad 3D models: https://gitlab.com/kicad/libraries/kicad-packages3D

## PEC11R-4015F-S0024 encoder
- Options, best first:
  1. The **"EDA / CAD Models"** section on the DigiKey page in your parts list (SnapMagic/Ultra Librarian, free login): https://www.digikey.com/en/products/detail/bourns-inc/PEC11R-4015F-S0024/4499668
  2. SnapMagic: https://www.snapeda.com/parts/PEC11R-4015F-S0024/Bourns/view-part/
  3. The Bourns PEC11R product page (bourns.com > Encoders > PEC11R > 3D models)
- Any **PEC11R-4x15F-Sxxxx** model has the same geometry: rear-facing pins, 15 mm flatted shaft, push switch. Detent count and pulse count don't change the outside shape. So if your exact part number has no model, a model for PEC11R-4215F-S0012 or -S0024 will work.

## 2.25" 76×284 IPS bar TFT
- GrabCAD: "Bar Type 2.25 inch 76x284 IPS TFT LCD Display SPI" by Phil Gardiner (free GrabCAD login): https://grabcad.com/phil.gardiner-1/models
  This model is BuyDisplay's ER-TFT2.25-1 bare panel.
- Check which version you ordered before using a model:
  - **Bare FPC panel** (ER-TFT2.25-1 class): 17.90 × 62.50 × 2.3 mm outline. Viewing area about 15.2 × 55.7 mm.
  - **Panel on a breakout PCB with a pin header** (ER-TFTM2.25-1 class): about 30 × 63 mm board.
  - AliExpress listings use both formats. Measure yours with calipers.
- If you model it yourself, a box with an inset viewing-area rectangle and an FPC tail is enough for a housing mockup.

## 10-pin IDC ribbon cable
- In a mockup this is just a 2x5 female IDC plug block plus a flat cable. Measure the plug you receive. A 2.54 mm-pitch plug uses 1.27 mm ribbon, so a 10-wire cable is about 12.7 mm wide and about 1 mm thick.
- Manufacturer models: CONNFLY DS1016-10 series (TME product pages include 3D models), or search GrabCAD for "IDC 10 pin female".
- Your listing offers 1.27, 2.0 and 2.54 mm pitch. The plug and header must use the same pitch.
