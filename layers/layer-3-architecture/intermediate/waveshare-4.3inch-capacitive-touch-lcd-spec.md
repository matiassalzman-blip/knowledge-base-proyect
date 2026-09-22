# Waveshare 4.3inch Capacitive Touch LCD --- Product Specification

**Product:** 4.3inch Capacitive Touch LCD, 800×480\
**Brand:** Waveshare\
**Part No.:** 4.3inch Capacitive Touch LCD\
**SKU:** 16249\
**Prepared:** 2026-09-21

> Based on Waveshare's official product page, user manual, and Wiki.
> Missing values are marked as unconfirmed rather than inferred from
> similar products.

## 1. Core specifications

  Specification            Value
  ------------------------ ----------------------------
  Display diagonal         4.3 inch
  LCD type                 IPS / TFT
  Resolution               800 × 480 px
  Total pixels             384,000
  Display interface        24-bit parallel RGB
  Display colors           16,777,216
  Active display area      95.04 × 53.86 mm
  Approx. pixel density    \~214 PPI (calculated)
  Dot pitch                0.1188 × 0.1122 mm
  Module dimensions        approx. 106 × 68 mm
  Backlight                LED
  Supply                   5 V
  Manual working voltage   3.3 V / 5 V
  Logic level              3.3 V
  Touch                    Capacitive, up to 5 points
  Touch controller         GT911
  Touch interface          I²C
  Product weight           0.105 kg
  Fixing-hole size         4.3 mm

## 2. Display geometry

``` text
               95.04 mm
       ◄────────────────────►
       ┌────────────────────┐ ▲
       │                    │ │
       │      800×480       │ │ 53.86 mm
       │                    │ │
       └────────────────────┘ ▼

Module envelope: approximately 106 × 68 mm
```

The 4.3-inch measurement refers to the display diagonal.

## 3. RGB display interface

The LCD exposes a 24-bit parallel RGB bus:

``` text
R0–R7   8 red bits
G0–G7   8 green bits
B0–B7   8 blue bits
──────────────────
        24 data bits
```

Display timing/control signals:

-   `CLK` --- LCD pixel clock
-   `HSYNC` --- horizontal synchronization
-   `VSYNC` --- vertical synchronization
-   `DE` --- Data Enable / control mode
-   `DISP` --- display/backlight enable
-   `PWM` --- backlight brightness control

This is fundamentally different from Glumi's current SPI ILI9341. It is
a parallel display intended for a controller with an LCD peripheral.

## 4. RGB565 support in Waveshare examples

Although the panel supports 24-bit RGB / 16.7M colors, Waveshare's
official manual explains that its example implementations use **RGB565**
to reduce RAM, data movement and CPU overhead.

RGB565:

``` text
Red      5 bits
Green    6 bits
Blue     5 bits
────────────────
Total   16 bits
```

Waveshare notes that the LCD controller converts the data for display.

For Glumi this is relevant because a future ESP32-S3 implementation can
be engineered around a lower-width color representation rather than
assuming every application must maintain RGB888 framebuffers.

## 5. Touch subsystem

The touch subsystem is separate from the RGB image interface.

  Specification         Value
  --------------------- --------------------
  Type                  Capacitive
  Controller            GT911
  Interface             I²C
  Simultaneous points   Up to 5
  Signals               SDA, SCL, RST, IRQ

Conceptually:

``` text
               MODULE
          ┌──────┴──────┐
          ▼             ▼
         LCD          TOUCH
          │             │
         RGB          GT911
          │             │
          ▼            I²C
         MCU ◄──────────┘
```

Glumi currently has no justified touch interaction, so this subsystem
would not need to be used.

## 6. Published interface definition

  Signal   Function
  -------- ---------------------------------
  5V       Power
  3.3V     Power
  GND      Ground
  R0--R7   Red pixel data
  G0--G7   Green pixel data
  B0--B7   Blue pixel data
  CLK      LCD clock
  DISP     Backlight/display enable
  HSYNC    Horizontal synchronization
  VSYNC    Vertical synchronization
  DE       Data Enable / mode selection
  PWM      Backlight brightness adjustment
  SDA      Touch I²C data
  SCL      Touch I²C clock
  RST      Touch reset
  IRQ      Touch interrupt
  NC       Not connected

## 7. Power and backlight

Waveshare's product page specifies **5 V supply** and **3.3 V logic**.
The older user manual lists working voltage as `3.3 V / 5 V`.

Backlight technology is LED.

The module exposes `PWM` for brightness adjustment and `DISP` as an
enable/control signal.

> **This matters more than it looks.** On Waveshare's integrated
> `ESP32-S3-Touch-LCD-4.3` / `4.3B` boards the backlight enable sits
> behind the **CH422G** IO expander (`EXIO2 → LCD_BL`) and is **on/off,
> with no PWM** --- so those boards cannot dim. This bare module *does*
> expose a real `PWM` brightness pin. For Glumi, whose night mode is in
> product scope, that makes the bare module's backlight interface the
> correct reference and the dev board's the one **not** to copy.
> See `glumi-electronics-architecture-research.md`, Riesgo 5.

For Glumi, physical validation should include:

-   maximum brightness
-   minimum stable brightness
-   nighttime readability
-   PWM flicker
-   low-brightness uniformity
-   thermal behavior
-   power consumption

The reviewed high-level specifications do **not** provide a complete
current/power budget.

## 8. Package contents

Waveshare lists:

-   1 × 4.3inch Capacitive Touch LCD
-   1 × 40-pin FFC, opposite-side contact
-   1 × screw pack (4 pcs)

## 9. Development resources

Waveshare's Wiki provides links to:

-   User Manual
-   Schematic
-   Demo code
-   GT911 datasheet
-   GT911 register documentation
-   SH430HI50 documentation
-   4.3inch IPS panel documentation
-   3D drawing

Official example platforms include:

-   STM32F746 / Open746I-C
-   STM32F429 / Open429I-C

The demos cover image display, text rendering, basic drawing primitives,
and up to five touch points.

## 10. Compatibility stated by Waveshare

Waveshare describes the module as intended for an **MCU with an LCD
controller**.

Its pin header is listed as compatible with certain Waveshare 4.3-inch
modules and directly pluggable into `Open746I-C` and `Open429I-C`.

This does **not** mean it directly plugs into an arbitrary ESP32-S3
development board.

## 11. What it is not

This product is not:

-   an SPI LCD
-   an HDMI display
-   a MIPI DSI display
-   an ESP32 development board
-   a drop-in wiring replacement for the current ILI9341

It is an RGB LCD/touch module requiring appropriate controller
electronics.

## 12. Relevance to Glumi

### Improvements over P0

Current display:

``` text
320 × 240 = 76,800 px
```

Candidate:

``` text
800 × 480 = 384,000 px
```

The candidate therefore has **5× as many pixels**.

Its 95 × 54 mm active area also gives Glumi significantly more physical
canvas while remaining substantially smaller than a 7-inch
dashboard-style product.

The IPS panel directly targets one of the current prototype's biggest
shortcomings: poor off-axis visibility.

### Potential drawbacks

-   Touch hardware would currently go unused.
-   Parallel RGB is substantially more complex than SPI.
-   It is not suitable for a long-Dupont/breadboard architecture as a
    final engineering solution.
-   A Waveshare retail module should not automatically be assumed to be
    the best volume-production sourcing strategy.

## 13. Specifications still to confirm

The high-level Waveshare product page/manual reviewed here do not
establish every production-critical parameter. Before design freeze,
confirm:

-   exact LCD panel manufacturer and MPN
-   exact viewing angle
-   brightness (cd/m²)
-   contrast ratio
-   refresh/timing limits
-   backlight electrical characteristics
-   current consumption
-   operating/storage temperature
-   exact thickness and dimensional tolerances
-   touch-glass construction
-   optical bonding / air gap
-   surface hardness
-   display/backlight lifetime
-   environmental compliance
-   lifecycle / long-term availability
-   MOQ and volume pricing
-   mating connector
-   second-source strategy

Do not substitute values from Waveshare's visually similar DSI/HDMI
4.3-inch products without confirming they use the same panel/module.

## 14. Glumi assessment

  Question                                 Status
  ---------------------------------------- ---------------------------
  4.3-inch target                          Candidate
  800×480                                  Yes
  IPS                                      Yes
  Resolution improvement                   Strong
  Touch required                           No
  Touch can remain unused                  Yes
  ESP32-S3 architecture plausible          Yes, requires engineering
  Direct ILI9341 replacement               No
  Good P1 prototype candidate              **No --- see below**
  Good P2 candidate (with own PCB)         Yes
  Confirmed production display             No
  Physical validation required             Yes
  Volume sourcing investigation required   Yes

### Correction: this module is not the P1 part

An earlier reading of this document treated the module as the P1
prototype display. It is not, because there is nothing to plug it into:
driving it from an ESP32-S3 dev board means carrying 16 data lines +
PCLK + HSYNC + VSYNC + DE at ~25--30 MHz, which requires a length-matched
adapter PCB --- i.e. P2's work.

For P1, the correct part is the integrated **Waveshare ESP32-S3-LCD-4.3**
board (non-touch, ~USD 28): same IPS panel, same ESP32-S3-WROOM-1-N16R8,
same RGB565 bus, no interfacing work.

This module becomes relevant again at **P2**, once a Glumi PCB exists.
See `glumi-electronics-architecture-research.md`, section 20 Paso A, and
`glumi-plan-prototipo-a-produccion.md` 4.5.

## 15. Short specification card

``` text
WAVESHARE 4.3INCH CAPACITIVE TOUCH LCD

SKU                 16249
Size                4.3"
Panel               IPS / TFT
Resolution          800 × 480
Pixels              384,000
Active area         95.04 × 53.86 mm
Module              ~106 × 68 mm
Display interface   24-bit RGB
Colors              16,777,216
Logic               3.3 V
Supply              5 V
Backlight           LED
Brightness control  PWM
Touch               Capacitive, 5-point
Touch controller    GT911
Touch interface     I²C
FFC                  40-pin included
Weight              105 g
```

## 16. Official sources

-   Product page:
    https://www.waveshare.com/4.3inch-capacitive-touch-lcd.htm
-   Wiki: https://www.waveshare.com/wiki/4.3inch_Capacitive_Touch_LCD
-   User manual:
    https://files.waveshare.com/upload/0/00/4.3inch_Capacitive_Touch_LCD_user_manual_en.pdf

------------------------------------------------------------------------

### Documentation note

This document intentionally distinguishes between Waveshare-published
specifications, calculated values, Glumi-specific implications, and
still-unconfirmed production parameters. Similar Waveshare 4.3-inch
DSI/HDMI product specifications have not been silently substituted where
this exact RGB module's documentation is incomplete.
