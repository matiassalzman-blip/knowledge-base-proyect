---
title: "Glumi Display Sourcing Study"
status: "in_progress"
last_updated: "2026-09-25"
source_of_truth:
  - hardware-requirements-decision-report.md
---

# Glumi Display Sourcing Study

## Executive conclusion

Glumi can use a real, standard 5-inch display architecture without moving away from the planned ESP32-S3. The viable production class is a **5.0-inch, 800×480 IPS LCD with PCAP touch, parallel RGB display input, and I²C touch**. A single RGB565 framebuffer is 768,000 bytes (750 KiB), so the planned 8 MB PSRAM is sufficient; display/Wi-Fi/flash bandwidth, not capacity, is the integration risk.

The strongest paper match is **CDTech S050HWV18HS-DC80-AG**: 400 nits, IPS, 1000:1, compact 120.7 × 75.8 × 4.55 mm construction, GT911 I²C PCAP, and an explicitly anti-glare front. It is only a **provisional winner**, because the exact AG variant is quote-only, its display driver is still listed as “TBD,” no formal lifecycle commitment is published, and the public technical package is thinner than the best industrial alternatives. It should not be designed in until CDTech supplies a controlled datasheet, drawing revision, sample quote, 100/1,000-unit pricing, MOQ, PCN/EOL policy, and written no-substitution terms.

The fastest P1 platform is **Waveshare ESP32-S3-Touch-LCD-5 (SKU 28117)**. It already combines the intended ESP32-S3-WROOM-1-N16R8, 5-inch 800×480 IPS display, RGB bus, GT911 touch, backlight driver, 8 MB PSRAM, and 16 MB flash for roughly USD 32–40. It is a development board, not a production display solution: the exact underlying panel MPN is undisclosed and most of its electronics would disappear on Glumi's PCB.

The strongest low-cost path is the **EastRising ER-TFT050-6 family**. The raw panel is listed from USD 16.15 before the PCAP option, and a controller-board version exists for P1. EastRising publishes a ten-year continuity promise from 2023. Its compromises are a lower 500:1 contrast specification, unverified front-surface treatment, direct-vendor dependence, and a GSL1680F touch stack that needs firmware/driver qualification.

The safest documented fallback is **Displaytech DT050CTFT-IPS-SHB-PTS**. It is active, newly documented, stocked at DigiKey, compact, and has a clear exact MPN. It is also 850 nits and USD 47.95 at 100 from DigiKey—both materially above Glumi's target—and the front-surface treatment is not specified. It earns a shortlist position for sourcing confidence, not cost or optical fit.

Prices, stock, lifecycle labels, and lead times below were checked on **2026-09-25** and can change. Prices exclude freight, duties, taxes, Argentina landing costs, payment fees, and display qualification.

## Scope and method

The requirements below come only from the [Glumi Hardware Requirements Decision Report](hardware-requirements-decision-report.md). Product facts were checked against manufacturer pages, datasheets, mechanical drawings, schematics, and authorized-distributor listings. Search snippets and reseller text were used only to locate primary material or, where explicitly labeled, to establish a live commercial signal.

“Not verified” means the source package reviewed did not establish the value. It does not mean zero, absent, or unacceptable.

The candidate taxonomy is:

1. **Bare LCD panel:** LCD glass/backlight/FPC, no touch and no controller PCB.
2. **LCD + touch assembly:** LCD plus touch sensor/controller and cover, but no host/controller PCB.
3. **Display module with controller/interface PCB:** panel plus a board that converts SPI/8080/HDMI/etc. or supplies display memory/control.
4. **Development board:** an evaluation/prototyping PCB around a display and host electronics.
5. **Integrated MCU + display product:** a development/HMI product containing the application MCU as well as the display.

## 1. Sourcing specification extracted from the hardware report

### MUST HAVE

| Dimension | Sourcing requirement | Evidence / boundary |
|---|---|---|
| Technology | **IPS TFT LCD**; do not select TN or OLED | The report selects 5-inch IPS and explicitly rejects TN and OLED. Normally-black operation is common among the candidates, but the source report does not make it a requirement. |
| Nominal size | **5.0-inch baseline** | A 5.5-inch part may be tested only when interface, price, availability, and enclosure consequences remain close. It is not the baseline. |
| Resolution | **800 × 480** | The report says “Use 800×480.” |
| Geometry | Standard rectangular panel in an approximately square product; no custom square LCD | 800×480 implies 5:3 pixel aspect. |
| Color | Full color; sufficient to distinguish state-colored backgrounds | No gamut, calibration, Delta-E, white-point, or color-temperature requirement is stated. |
| Viewing behavior | Wide-view IPS behavior from left, right, above, and below | A numerical minimum angle is not stated. |
| Touch | Integrated **projected-capacitive touch**, with I²C host interface | Touch is a core input, not an upgrade. |
| Display interface | Supplier-documented direct parallel RGB timing/FPC suitable for the ESP32-S3 architecture | The architecture explicitly selects direct RGB display plus I²C touch. |
| Night behavior | Backlight must dim smoothly to a genuinely unobtrusive bedroom level, without obvious flicker or large steps; zero brightness means screen sleep | No minimum-nit value or PWM frequency is specified. Physical measurement is mandatory. |
| Production integration | Must be connectable to Glumi's custom PCB; a development board cannot remain inside the production unit | The selected architecture is a custom four-layer carrier PCB, not a dev board in production. |

### STRONGLY PREFERRED

| Dimension | Sourcing target | Evidence / boundary |
|---|---|---|
| Brightness | **350–450 nit class** | Indoor product; occasional bright-window placement, not sunlight-readable duty. |
| Surface | Light anti-glare / low-reflection front that preserves sharpness and contrast | Avoid mirror gloss and coarse hazy matte. |
| Touch/front stack | Integrated PCAP cover lens usable as the exposed front | Avoid a second window, extra reflections, dust interfaces, cost, and yield loss. |
| Active area | Approximately **108 × 65 mm** | This is the report's standard 5-inch planning geometry. |
| Brightness control | Raw LED access compatible with a dedicated constant-current driver and fine PWM/current control | The report explicitly rejects crude on/off backlight control. |
| Cost | At 100 units, keep the qualified IPS+PCAP assembly inside the report's **USD 24–38 planning band** where possible | This is a planning basis, not a quoted cap. A higher-cost part can be used for P1 or as a risk-reduction fallback. |
| Availability | Exact MPN, current production/support, proper documentation, and a credible OEM lifecycle | Exact supplier and second source remain open decisions in the source report. |
| Mechanical integration | Stable, documented FPC, connector, outline, touch cover, and bezel geometry before PCB/enclosure freeze | The report says to select the panel before connector and board outline are frozen. |

### NICE TO HAVE

| Dimension | Sourcing preference | Boundary |
|---|---|---|
| 5.5-inch challenger | A close-interface/cost option for a physical readability comparison | Not worth accepting MIPI, unusual aspect ratio, major cost, or enclosure change merely to gain 0.5 inch. No strong standard 5.5-inch 800×480 RGB+PCAP candidate was found in this study. |
| Multi-touch | May be supplied by the controller | Glumi must not depend on multi-finger gestures. |
| Hard cover glass | 6H/7H-class cover is helpful | No numerical hardness requirement is stated. |
| Wide operating temperature | Industrial temperature rating reduces risk | Glumi is an indoor product; the report gives no required temperature range. |
| EMI-shielded FPC / integrated ESD design | Helpful for Wi-Fi and compliance robustness | Final product still needs board-level ESD/EMC design and test. |
| Optical bonding | May improve contrast/reflections if affordable and manufacturable | Not required for the first batch. |
| Common 40-pin ecosystem | Eases adapter and second-source work | Pinout compatibility must still be verified; “40-pin” alone does not establish interchangeability. |

### NOT REQUIRED

| Dimension | Not required / explicitly excluded |
|---|---|
| Sunlight-readable 800–1,000+ nit backlight | Indoor use does not justify its cost and power by default. |
| OLED black level | A faint LCD glow is acceptable. |
| Ambient-light sensor or automatic brightness | Brightness is manual. |
| Phone-level pixel density or resolution above 800×480 | Higher resolution is rejected unless physical prototypes show a typography problem. |
| Custom square LCD | Use the enclosure and bezel to achieve the product silhouette. |
| 7-inch default | A 7-inch mask may be a comparison prototype only. |
| Multi-touch-dependent UI | Single-finger interaction is sufficient. |
| Rotation support | Not required. |
| Second front window | Prefer to avoid it when the PCAP cover can be exposed. |

### Unspecified and therefore not safe to invent

- Numerical minimum brightness in cd/m².
- Required PWM frequency, PWM depth, analog-dimming range, or flicker metric.
- Minimum contrast ratio, black luminance, uniformity, response time, gamut, color accuracy, or white point.
- Maximum module outline, cover-lens outline, bezel width, FPC tail envelope, weight, or thickness.
- Required cover-glass thickness, hardness, anti-fingerprint coating, optical-bonding method, or exact haze/reflectance.
- Required operating/storage temperature or humidity range.
- Required touch count, glove/wet-touch performance, touch latency, or cover-lens thickness beyond the supplied stack.
- Formal lifecycle duration, PCN notice period, approved second-source policy, MOQ, or maximum acceptable lead time.
- A hard display purchase-price ceiling. The report gives a USD 24–38 planning range, not an absolute reject threshold.

## 2. Candidate set and what is actually being sold

| Candidate | Exact MPN | Sold item | Why it survived screening |
|---|---|---|---|
| CDTech | **S050HWV18HS-DC80-AG** | **2 — LCD + touch assembly**; no host PCB and no backlight driver | Closest optical/mechanical match: IPS, 400 nits, 1000:1, GT911 I²C PCAP, compact stack, explicit anti-glare. |
| Displaytech / SEACOMP | **DT050CTFT-IPS-SHB-PTS** | **2 — LCD + touch assembly**; integrated display/touch FPC, no host PCB | Excellent current documentation, exact MPN, active distributor stock, compact outline, industrial/OEM positioning. |
| Winstar | **WF50FTWAGDNGB** | **2 — LCD + touch assembly**; no control board | Exact 400-nit IPS/RGB/I²C-PCAP class from an established display manufacturer. |
| Riverdi | **SM-RVT50HQTNWCA0** (display datasheet: RVT50HQTNWCA0) | **2 — LCD + touch assembly**; no EVE/host board | Strong documentation, OEM product family, direct and authorized-distributor purchasing. |
| Newhaven Display | **NHD-5.0-800480AF-ASXP-CTP** | **2 — LCD + touch assembly**; no display-controller PCB | Best documentation and authorized-channel availability; useful low-risk benchmark despite high brightness/cost. |
| EastRising / BuyDisplay | **ER-TFT050-6** with optional **ER-TPC050A2-1** PCAP | **1/2 — raw LCD, optionally LCD + touch assembly** | Lowest credible cost-down family, published docs, direct low-quantity ordering, stated ten-year continuity. |
| Waveshare | **ESP32-S3-Touch-LCD-5**, SKU **28117** | **5 — integrated MCU + display development product** | Fastest way to validate the intended MCU, memory, RGB bus, touch, LVGL, Wi-Fi, and 5-inch UX. |

No candidate is being mislabeled as a bare glass panel. The six production-oriented products above include the LCD driver-on-glass/FPC and, except the raw EastRising configuration, PCAP sensor/controller. They **do not** include the application/display framebuffer controller or a backlight power stage. The Waveshare product includes both the application MCU and supporting board electronics.

## 3. Detailed comparison

### Optical and mechanical comparison

All 5.0-inch 800×480 candidates are approximately **186.6 PPI (calculated)**:

`sqrt(800² + 480²) / 5.0 = 186.59 PPI`

| MPN | Technology | Diagonal / aspect | Resolution / PPI | Active or view area (mm) | Overall dimensions W×H×D (mm) | Viewing angle | Brightness | Contrast | Surface / front stack |
|---|---|---|---|---|---|---|---:|---:|---|
| **S050HWV18HS-DC80-AG** | IPS, normally-black status not verified | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.0×64.8 active | 120.7×75.8×4.55 | “IPS all viewing”; degrees not published | **400 nit typ** | **1000:1 typ** | **Anti-glare**, G+G PCAP, air bonded; haze/reflectance not published |
| **DT050CTFT-IPS-SHB-PTS** | IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.6×65.4 viewing; active size not separately stated | **114×78×5.0** | 80/80/80/80° typ | **850 nit typ**, 650 min | 1000:1 typ | PCAP, ≥7H; gloss/AG treatment **Not verified** |
| **WF50FTWAGDNGB** | Normally-black transmissive IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.0×64.8 active | **147×104×4.88** | 80/80/80/80° | **400 nit typ** | 1000:1 typ | **Glare**, 1.1 mm lens; very large decorative cover |
| **SM-RVT50HQTNWCA0** | Normally-black transmissive IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.0×64.8 active | Commercial page: **136×92.8×5.4**; datasheet summary says 120.5×75.6×5.4—resolve before CAD | 80° each direction | **800 nit typ**, 756 min | 1000:1 typ | Black decorative cover glass, air bonded; AG/gloss **Not verified** |
| **NHD-5.0-800480AF-ASXP-CTP** | Normally-black transmissive IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.0×64.8 active; 109×65.8 viewing | **120.7×75.8×4.55** | 80/80/80/80° typ | **900 nit typ**, 680 min | 1000:1 typ | Cover glass; AG/gloss treatment **Not verified** |
| **ER-TFT050-6 + PCAP** | IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 108.0×64.8 active | LCD: 120.7×75.8×2.8; PCAP assembly thickness **Not verified** | 80/80/80/80° | **450 nit typ** | **500:1 typ** | Optional PCAP; front treatment **Not verified** |
| **ESP32-S3-Touch-LCD-5 / 28117** | IPS | 5.0 in / 5:3 | 800×480 / **186.6 calculated** | 109×65.8 display area reported in drawing-derived documentation | 127.7×87.45 board/front outline; thickness **Not verified by primary page** | 160° total | **550 nit** | **Not verified** | Tempered-glass PCAP; AG/gloss treatment **Not verified** |

For enclosure-scale comparison, the symmetric-equivalent border from `overall outline − 108 × 64.8 mm active area` is approximately 6.35 mm left/right and 5.5 mm top/bottom for CDTech/Newhaven/EastRising; 3 mm left/right and 6.6 mm top/bottom for Displaytech; 19.5 mm left/right and 19.6 mm top/bottom for Winstar; 14 mm on all sides for Riverdi if its 136 × 92.8 mm commercial outline is correct; and 9.85 mm left/right plus 11.3 mm top/bottom for Waveshare. These are **calculated envelope comparisons, not verified visible-bezel dimensions**: real active-area offsets can be asymmetric and must come from the drawing.

Primary optical/mechanical sources: [CDTech product specification page](https://www.cdtech-display.com/products/5-0-inch-rgb-interface-anti-glare-capacitive-touch-lcd-screen-400nits/), [Displaytech Rev. 1.1 datasheet](https://www.seacomp.com/sites/default/files/datasheets/DT050CTFT-IPS-SHB-Rev1_1-Displaytech-Spec.pdf), [Winstar product page](https://www.winstar.com.tw/products/tft-lcd/ips-tft/wf50ftwagdngb.html) and [datasheet](https://www.winstar.com.tw/uploads/files/909fb557c753504a373dae7edfe29b07.pdf), [Riverdi product page](https://riverdi.com/product/high-brightness-ips-display-rvt50hqtnwca0-5-inch-projected-capacitive-touch-panel-air-bonding-atouch-rgb) and [datasheet](https://download.riverdi.com/RVT50HQTNWCA0/DS_RVT50HQTNWCA0_Rev.1.0.pdf), [Newhaven product page](https://newhavendisplay.com/5-0-inch-ips-capacitive-tft-display/) and [datasheet/mechanical drawing](https://newhavendisplay.com/content/specs/NHD-5.0-800480AF-ASXP-CTP.pdf), [EastRising product page](https://www.buydisplay.com/ips-5-tft-lcd-display-module-wvga-800x480-high-resolution-for-mp4-gps) and [datasheet](https://www.buydisplay.com/download/manual/ER-TFT050-6_Datasheet.pdf), and [Waveshare product documentation](https://docs.waveshare.com/ESP32-S3-Touch-LCD-5).

### Touch, electrical, interface, and power comparison

“Calculated full-power” values below multiply published typical voltage/current and add published logic/touch loads where available. They are **not measured Glumi system power** and exclude conversion loss.

| MPN | Touch / controller | Display / touch interface | Color depth | Backlight | Logic/touch supply | Typical or max power information |
|---|---|---|---|---|---|---|
| **S050HWV18HS-DC80-AG** | PCAP, G+G; **GT911**; point count **Not verified for exact suffix** | 24-bit RGB / I²C; 40-pin 0.5 mm LCD FPC + 6-pin 0.5 mm CTP FPC | **Not verified**; panel accepts 24-bit RGB | 2 strings × 6 LEDs; 16.2–20.4 V, 40 mA; no driver | Exact AG-variant logic/touch current **Not verified** | Backlight **0.65–0.82 W calculated**; total not verified |
| **DT050CTFT-IPS-SHB-PTS** | PCAP; **ST1633I** | 24-bit RGB / I²C; one 45-signal FPC carrying LCD and touch | Color count **Not verified** | 3×6 LEDs; 18.3 V typ, 60 mA; no driver | 3.3–3.45 V; logic current marked TBD | Backlight **1.10 W calculated**; total not verified |
| **WF50FTWAGDNGB** | 5-point PCAP; **GT911 or equivalent** | 24-bit RGB / I²C; separate 40-pin and 10-pin FPCs | 24-bit / 16.7M panel class | 18 LEDs; 19.2 V typ, 60 mA; no driver; 50k h | LCD 3.3 V, 65 mA typ; touch 2.8–3.3 V, 15 mA typ | Approximately **1.42 W calculated** at full backlight, excluding conversion loss |
| **SM-RVT50HQTNWCA0** | PCAP; **ILI2132A** | 24-bit RGB / I²C, USB, optional UART touch | 16.7M | 12 V typ, 140 mA; no driver; 50k h | LCD 3.3 V, 80 mA typ; touch active 90 mA | Approximately **2.24 W calculated** at full backlight if touch is on 3.3 V |
| **NHD-5.0-800480AF-ASXP-CTP** | 10-point PCAP; **FT5426G** | 24-bit RGB / I²C; 40-pin 0.5 mm LCD + 6-pin 1.0 mm CTP | 16.7M | 21.1 V typ, 60 mA; no driver; 30k h | LCD 3.3 V, 62 mA typ; touch 3.3 V, 15 mA typ | Approximately **1.52 W calculated** at full backlight, excluding conversion loss |
| **ER-TFT050-6 + PCAP** | Optional PCAP; **GSL1680F** | RGB / I²C touch; 40-pin 0.5 mm LCD + 6-pin PCAP | Up to 16.7M panel | White LED, 60 mA typ; recommended PT4110 driver; voltage/power **Not verified in reviewed summary** | 3.3 V panel; touch supply/current **Not verified** | Total **Not verified** |
| **ESP32-S3-Touch-LCD-5 / 28117** | 5-point PCAP; **GT911** | Internal 16-bit RGB565 wiring / I²C | System documented as **65K** | AP3032 boost/backlight stage on board; PWM behavior and LED electricals not published | Whole board: USB-C 5 V or 7–36 V input; includes regulators | **5 V, 450 mA / 2.25 W** published whole-board figure |

The S050HWV18HS-DC80-AG product page includes its 2D drawing and electrical summary. The Displaytech and Newhaven datasheets include the mechanical drawing, pin map, timing, touch information, and backlight circuit. Riverdi's datasheet includes all of those, but its top-level outside dimension conflicts with the sold-product page and must be resolved. Winstar's short datasheet includes its drawing and electrical pinout. EastRising publishes separate panel, touch-controller, PCAP outline, and controller-board documentation. Waveshare publishes the [board schematic](https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-5/ESP32-S3-Touch-LCD-5-Sch.pdf), [resources](https://docs.waveshare.com/ESP32-S3-Touch-LCD-5/Resources-And-Documents), and software examples.

### Commercial availability, price, and lifecycle

| MPN | Low-quantity price | ~100-unit price | MOQ | Supplier / current availability signal | Lifecycle signal | Sourcing risk |
|---|---:|---:|---:|---|---|---|
| **S050HWV18HS-DC80-AG** | **Quote required** | **Quote required** | Not published | Direct from CDTech; current official catalog page, no public stock/lead time | No formal longevity/PCN/EOL commitment found | **MEDIUM** |
| **DT050CTFT-IPS-SHB-PTS** | **USD 64.96** at DigiKey | **USD 47.9453** at 100 | 1 | [DigiKey](https://www.digikey.com/en/products/detail/displaytech/DT050CTFT-IPS-SHB-PTS/26772450): 237 in stock, active, 9-week manufacturer lead; RS also lists the exact MPN | New 2025 datasheet; active status; established manufacturer/distributors | **LOW** |
| **WF50FTWAGDNGB** | Quote required | Quote required | Not published | Direct inquiry; [Nijkerk](https://www.nijkerk-ne.com/p/wf50ftwagdngb/) lists exact part for quote | Current Winstar product page; no formal longevity statement | **MEDIUM** |
| **SM-RVT50HQTNWCA0** | **USD 61.97 direct**; **USD 75.72 DigiKey** | Not published | 1 | [Riverdi direct](https://riverdi.com/product/high-brightness-ips-display-rvt50hqtnwca0-5-inch-projected-capacitive-touch-panel-air-bonding-atouch-rgb); [DigiKey](https://www.digikey.com.mx/es/products/detail/riverdi/SM-RVT50HQTNWCA0/25855130) showed 2 in stock, active, 5-week standard lead | OEM family and active authorized-distributor status; no duration commitment found | **LOW** |
| **NHD-5.0-800480AF-ASXP-CTP** | **USD 90.02 direct**; DigiKey listing around **USD 82.36** | Quote / no useful public 100 break found | 1 | [Newhaven direct](https://newhavendisplay.com/5-0-inch-ips-capacitive-tft-display/); [DigiKey](https://www.digikey.com/en/products/detail/newhaven-display-intl/NHD-5-0-800480AF-ASXP-CTP/16592393) showed 110 in stock, active, 18-week standard lead | Active, multi-distributor, documented [2024 engineering change](https://support.newhavendisplay.com/hc/en-us/articles/27966279961239-11-21-24-NHD-5-0-800480AF-ASXP) rather than silent change | **LOW** |
| **ER-TFT050-6 + PCAP** | LCD base **USD 16.15**; PCAP configuration total not exposed in static page | Configured PCAP price **Not verified** | 1 | [BuyDisplay direct](https://www.buydisplay.com/ips-5-tft-lcd-display-module-wvga-800x480-high-resolution-for-mp4-gps), listed in stock; related controller module [ER-TFT050-6-5517](https://www.buydisplay.com/low-cost-ips-5-inch-arduino-lcd-display-project-800x480-capacitive-touch) starts at USD 24.17 and USD 21.65 at 100 before configuration verification | Vendor promises continuity for at least ten years from 2023 | **MEDIUM** |
| **ESP32-S3-Touch-LCD-5 / 28117** | Direct family range **USD 31.99–39.99**; exact option price requires cart selection | Quote / customization inquiry | 1 | [Waveshare direct](https://www.waveshare.com/product/iot-communication/esp32-s3-touch-lcd-5.htm), add-to-cart product; third-party distributor signals also exist | Current documented board, but no disclosed panel MPN or panel lifecycle guarantee | **HIGH for production; LOW for P1** |

#### Risk interpretation

- **LOW** means an exact MPN, strong documentation, current active status, and credible authorized distribution are present. It does not mean the part is cost-optimal or guaranteed forever.
- **MEDIUM** means the product is plausible for OEM production but requires a supplier quote, controlled documentation, substitution controls, or stronger channel/lifecycle evidence.
- **HIGH** means Glumi would depend on an integrated development product whose underlying display is not controlled as an exact production component.

### Decision-oriented comparison

| MPN | Main compromises relative to Glumi | Prototype suitability | Production suitability |
|---|---|---|---|
| **S050HWV18HS-DC80-AG** | Quote/lifecycle unknown; controller TBD; exact AG documentation incomplete | **Medium:** needs adapter and backlight driver, but tests the real candidate | **High, conditional:** best fit after documentation, quote, and sample qualification |
| **DT050CTFT-IPS-SHB-PTS** | 850 nits, no verified AG, USD 47.95/100, logic current TBD | **Medium:** bare assembly with excellent documentation | **High technically / medium commercially** |
| **WF50FTWAGDNGB** | Explicit glare; oversized 147×104 cover; controller substitution language; quote-only | **Medium** | **Medium as listed; potentially high with a controlled compact AG cover** |
| **SM-RVT50HQTNWCA0** | 800 nits, highest panel power, USD 62–76, surface unverified, conflicting outlines | **Medium** | **Medium-low for Glumi** |
| **NHD-5.0-800480AF-ASXP-CTP** | 900 nits, USD 82–90, surface unverified | **Medium; strong quality benchmark** | **Low at current price** |
| **ER-TFT050-6 + PCAP** | 500:1, surface unverified, GSL1680F qualification, configured price unknown | **High via controller-board sibling** | **High potential after supplier/front-stack qualification** |
| **ESP32-S3-Touch-LCD-5 / 28117** | Anonymous panel, unverified surface/minimum brightness, irrelevant electronics | **Very high; fastest architecture mule** | **None as a production assembly** |

## 4. Candidate-by-candidate assessment

### CDTech S050HWV18HS-DC80-AG — closest match to the sourcing specification

Why it matters: it is the only reviewed exact MPN combining the target 400-nit class with IPS, 1000:1 contrast, compact standard-panel dimensions, integrated GT911 PCAP, and an explicitly anti-glare front.

Main compromises:

- The public page identifies the LCD controller only as **TBD**.
- A complete controlled datasheet for the exact `-AG` suffix was not available in the reviewed package; the page provides a 2D drawing and specifications.
- Price, MOQ, stock, lead time, lifecycle, PCN policy, and 100/1,000-unit breaks are quote-only.
- “Anti-glare” is not quantified by haze, gloss units, or reflectance. It still needs a sample.

Prototype suitability: **medium**. It is a raw production assembly and needs an FPC adapter/custom PCB plus a constant-current boost driver. That inconvenience is useful: optical and mechanical testing occurs on the actual candidate, not an anonymous dev-board panel.

Production suitability: **high, conditional on commercial and documentation closure**.

### Displaytech DT050CTFT-IPS-SHB-PTS — strongest documented production fallback

Why it matters: exact 2025 MPN, thorough datasheet, unusually compact 114 × 78 mm outline, integrated touch signals on the same FPC, active status, 237 pieces of DigiKey stock, and published 100-unit pricing.

Main compromises:

- 850 nits is more than Glumi needs and makes low-end bedroom dimming harder, not easier.
- USD 47.95 at 100 is roughly USD 10 above the top of the report's planning range before Argentina landing.
- Surface finish is not specified. Hardness ≥7H does not establish anti-glare.
- Logic current is still “TBD” in the datasheet.

Prototype suitability: **medium**; it still needs an adapter/backlight circuit.

Production suitability: **high technically, medium commercially**.

### Winstar WF50FTWAGDNGB — credible 400-nit OEM option with the wrong standard cover

Why it matters: established display supplier, exact current MPN, 400 nits, 1000:1, IPS, RGB, GT911-class I²C PCAP, and a full -30 to +80°C rating.

Main compromises:

- The supplied 147 × 104 mm glare cover is much larger than the standard 120.7 × 75.8 mm LCD and fixes a large reflective front geometry.
- The datasheet allows `GT911 or equivalent` and `ST7262 or equivalent`; Glumi needs written change/substitution control.
- No public price or stock.

Prototype suitability: **medium**. It can validate the large-cover direction, but not Glumi's preferred low-reflection front.

Production suitability: **medium** as listed; potentially high if Winstar quotes a compact custom anti-glare cover and controlled controller BOM at acceptable MOQ.

### Riverdi SM-RVT50HQTNWCA0 — premium OEM benchmark

Why it matters: strong technical documentation, 800-nit IPS, 1000:1 contrast, ILI2132A PCAP, direct purchasing, authorized distribution, and an exact active MPN.

Main compromises:

- High brightness raises backlight power to 1.68 W alone and provides no documented advantage for Glumi's indoor use.
- Direct price is USD 61.97; authorized distribution is higher.
- Front finish is not verified as anti-glare.
- Manufacturer product-page and datasheet outline dimensions disagree. This must be resolved with a signed drawing before CAD.

Prototype suitability: **medium**.

Production suitability: **medium-low for Glumi** because of cost/power, despite low sourcing risk.

### Newhaven NHD-5.0-800480AF-ASXP-CTP — best documentation/channel benchmark

Why it matters: exact component, full datasheet and drawing, named touch controller, multiple authorized distributors, active status, current stock, EMI-shielded FPC, and transparent change notification.

Main compromises:

- 900 nits and roughly USD 82–90 at low quantity are both far above Glumi's target.
- Surface treatment is not stated in the current datasheet.
- Backlight requires about 1.27 W at full output and a 21 V-class constant-current boost stage.

Prototype suitability: **medium**. It is a good quality/reference sample, but it does not remove the adapter requirement.

Production suitability: **low at the current price**, unless direct volume pricing is dramatically better.

### EastRising ER-TFT050-6 + ER-TPC050A2-1 — best cost-down architecture

Why it matters: the same 5-inch IPS panel family is offered as a raw RGB panel and in several controller-board forms. That creates a credible bridge: use a module now, then remove its controller PCB and connect the raw RGB panel on Glumi's board.

Main compromises:

- Contrast is only 500:1.
- Surface finish is not verified.
- The capacitive option uses GSL1680F; its firmware blob/driver, coordinate behavior, interrupt/reset timing, and long-term controller availability must be qualified.
- Static web pricing does not expose the final PCAP-configured price; do not treat USD 16.15 or USD 21.65 as the finished touch-assembly price.
- The sales channel is primarily direct, with no comparable broad authorized-distributor stock signal.

Prototype suitability: **high** when ordered as the [ER-TFT050-6-5517 controller module](https://www.buydisplay.com/low-cost-ips-5-inch-arduino-lcd-display-project-800x480-capacitive-touch), provided the selected option explicitly includes PCAP.

Production suitability: **high potential**, conditional on AG/front-stack, touch firmware, exact configured quote, and supplier controls.

### Waveshare ESP32-S3-Touch-LCD-5 / SKU 28117 — fastest P1, not a production display

Why it matters: it tests nearly the exact planned compute/display architecture immediately: ESP32-S3-WROOM-1-N16R8, 8 MB PSRAM, 16 MB flash, 800×480 RGB565, GT911 I²C PCAP, Wi-Fi, USB, and LVGL. The board schematic and examples are public.

Main compromises:

- The panel is not sold under a disclosed production MPN. Optical stack, minimum brightness, and substitution control are unknown.
- The board contains CAN, RS-485, isolated I/O, RTC, battery/charger circuitry, regulators, connectors, and other electronics Glumi does not need.
- The 550-nit tempered-glass front is not verified anti-glare.
- It does not include Glumi's intended audio implementation.
- Published operating temperature is only 0 to 65°C, adequate for P1 but not an industrial signal.

Prototype suitability: **very high**.

Production suitability: **none as an assembly**. It is evidence for the architecture, not the BOM.

## 5. ESP32-S3 integration with the finalists

### Common RGB architecture

The ESP32-S3 can directly drive these 800×480 RGB panels. Espressif's [RGB LCD driver documentation](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/lcd/rgb_lcd.html) supports 8-, 16-, or 24-bit data width, PSRAM framebuffers, double buffering, and bounce-buffer modes. Espressif also warns that PSRAM bandwidth, Wi-Fi, flash writes, and OTA can produce display drift/flicker; its [LCD FAQ](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html) recommends PSRAM XIP plus RGB bounce buffers for concurrent Wi-Fi and flash activity.

Recommended Glumi connection:

- Use **RGB565 over 16 data GPIOs**, connecting the most significant R/G/B inputs of the panel and fixing/omitting unused low bits according to the panel/controller guidance.
- Add PCLK, DE, HSYNC, VSYNC, and DISP/standby as required by the exact panel.
- Use I²C SDA/SCL plus touch INT and RESET.
- Use one PWM/enable signal for the backlight driver.
- Budget roughly **23 direct MCU GPIOs plus expander-controlled resets/enables**, or up to about **26 direct signals** if every RGB timing, touch, display-enable, and PWM line is driven directly. The exact count depends on DE-only mode and use of an I/O expander.
- The Waveshare design is a useful proof: it connects 16 RGB data bits plus PCLK/DE/HSYNC/VSYNC directly, uses direct I²C/IRQ, and moves reset/enable signals to a CH422G expander.

The production panels accept 24-bit RGB, but Glumi does not need a 24-bit framebuffer. Panel color capability and transport/framebuffer format are different: an RGB565 framebuffer provides 65,536 colors while the panel may accept 16.7 million.

### Memory and bandwidth

| Buffer format | One 800×480 buffer | Two buffers |
|---|---:|---:|
| RGB565, 2 bytes/pixel | **768,000 bytes = 750 KiB** | **1,536,000 bytes ≈ 1.46 MiB** |
| RGB888, 3 bytes/pixel | 1,152,000 bytes ≈ 1.10 MiB | 2,304,000 bytes ≈ 2.20 MiB |

The planned 8 MB PSRAM has ample capacity for RGB565 double buffering plus UI assets and network state. A 25 MHz pixel clock with 16-bit output represents up to roughly 50 MB/s of pin-side pixel traffic; actual PSRAM/CPU behavior depends on timing, blanking, cache, DMA, draw strategy, and concurrent work. Capacity therefore does not prove stability.

Required P1/P2 stress case:

1. Continuous RGB refresh and animated full-screen state changes.
2. Wi-Fi traffic and TLS/LibreLinkUp parsing.
3. Touch interrupts and I²C reads.
4. I²S audio playback.
5. OTA and NVS writes while keeping a stable progress/fallback screen.
6. Portal traffic and all configured profile polling.

Use ESP-IDF 5.x or newer, octal PSRAM configuration, PSRAM XIP, and a tuned bounce buffer. Start with RGB565 and a single PSRAM framebuffer plus internal bounce buffers; test double buffering only if tearing justifies it.

### PCB, connectors, and backlight

| Candidate | Production connector implications | Backlight / PCB implication | Can custom PCB connect directly? |
|---|---|---|---|
| CDTech | 40-pin 0.5 mm RGB FPC + 6-pin 0.5 mm touch FPC | Add 5 V-to-~18 V constant-current boost, PWM/enable, filtering, ESD | **Yes** |
| Displaytech | One 45-signal FPC combines RGB, touch, power, and controls; exact connector from drawing/BOM must be frozen | Add ~18.3 V / 60 mA boost driver and fine dimming | **Yes** |
| Winstar | 40-pin RGB + 10-pin CTP FPC | Add ~19.2 V / 60 mA boost; large cover dictates enclosure | **Yes** |
| Riverdi | 40-pin RGB plus touch FPC/USB options; resolve drawing revision and outline first | Add 12 V / 140 mA constant-current/voltage-appropriate driver; highest panel load | **Yes** |
| Newhaven | 40-pin 0.5 mm RGB + 6-pin 1.0 mm touch; recommended Molex parts are named | Add ~21.1 V / 60 mA boost; strong documentation | **Yes** |
| EastRising raw | 40-pin 0.5 mm RGB + 6-pin PCAP; verify exact option drawing | Add supplier-recommended/equivalent constant-current boost; qualify GSL1680F firmware | **Yes** |
| Waveshare 28117 | Already integrated; panel FPC terminates on Waveshare PCB | AP3032 backlight stage already present | **Not as a controlled production panel**; reverse reuse is not recommended without a disclosed panel MPN |

For every raw assembly, the production PCB should include:

- correctly selected FPC connectors with strain relief and keep-out;
- series damping footprints on PCLK and RGB lines, with final values set by signal-integrity test;
- ESD protection and grounding strategy for the exposed PCAP/front;
- a dedicated constant-current LED boost driver with both PWM and, if useful, analog/current dimming;
- touch reset/interrupt control, pull-ups, and a recovery path for a wedged touch controller;
- test points and a factory pattern/touch-grid mode;
- a display power sequence that matches the selected datasheet;
- mechanical support that does not point-load the LCD or FPC.

No external display RAM or separate display controller is required for the raw RGB candidates. The ESP32-S3 LCD peripheral continuously scans the PSRAM framebuffer. Controller-board products such as the EastRising ER-TFT050-6-5517 include electronics that should disappear into Glumi's custom PCB.

## 6. Physical UX assessment

### Across-room readability at approximately 5 m

The production candidates share essentially the same 108 × 64.8 mm active area. A deliberately sparse glucose screen can reserve roughly 50–58 mm of physical height for the primary digits after modest top/bottom margins. At 5 m, 50–58 mm subtends roughly 34–40 arc-minutes. That makes the design **plausible**, but not proven: font weight, numeral shapes, foreground/background pairing, glare, eyesight, and room lighting dominate the last step.

Because all serious 5-inch candidates have the same active area, switching among them will not materially improve 5 m digit size. A 5.5-inch screen at the same aspect ratio would increase linear size only about 10%. That comparison can be done first with printed masks; it does not justify accepting a weakly sourced panel.

### Arm's-length quality

At 186.6 PPI, pixel pitch is about 0.135 mm. This is sufficient for intentional appliance typography, icons, QR codes, and settings at arm's length when anti-aliasing, spacing, and font hinting are handled well. The main quality risks are not raw PPI but:

- RGB565 gradients/banding on large state-color fields;
- poor scaling or raster fonts;
- cover-glass air-gap reflections;
- coarse anti-glare haze;
- low contrast or uneven black in a dim room.

The EastRising panel's 500:1 contrast deserves direct comparison with the 1000:1 candidates. Datasheet ratios are measured under controlled conditions and do not capture the full front stack.

### Viewing angle

The serious raw candidates specify IPS/all-view behavior, usually 80° in each direction. That is appropriate for bedside, desk, shelf, and counter use. The DISEA reference part does not pass this test on current documentation. Vertical black-level/color-shift behavior still needs a real enclosure-angle test, because “80° at CR≥10” can look poor well before the formal limit.

### Nighttime

No reviewed raw-panel datasheet states a minimum luminance, dimming curve, PWM frequency, flicker metric, black-uniformity floor, or lowest stable LED current. Maximum brightness is therefore not evidence of nighttime suitability.

The 400-nit CDTech and Winstar candidates are more promising than the 800–900-nit alternatives because the driver needs less total range, but even 400 nits can fail if the boost driver has a high minimum current or PWM becomes coarse at low duty. The backlight electronics must be evaluated with the actual panel at the intended PWM frequency and at 1%, sub-1%, and mixed current/PWM settings.

### Reflections

- **CDTech `-AG`:** only candidate with an exact MPN explicitly marketed as anti-glare; strongest paper fit, still needs haze/sharpness inspection.
- **Winstar:** explicitly glare; fails the preferred surface as listed.
- **Riverdi, Newhaven, Displaytech, EastRising, Waveshare:** front treatment not verified. Cover hardness or tempered glass does not imply low reflection.

### Touch

Display quality and touch quality must be qualified separately. GT911, FT5426G, ILI2132A, ST1633I, and GSL1680F all meet the basic I²C PCAP architecture, but Glumi still needs to test:

- edge accuracy and dead zones;
- false touches from the charger, speaker amplifier, and Wi-Fi activity;
- operation with the final grounded/ungrounded enclosure stack;
- wake-from-sleep and reset recovery;
- latency and tap rejection;
- fingerprints, cleaning, and front hardness;
- firmware availability and whether controller substitution changes behavior.

## 7. Prototype → production paths

### CDTech S050HWV18HS-DC80-AG

**P1:** Order 2–3 exact `-AG` samples and a small FPC adapter/backlight board. Validate optics and mechanical stack with the planned ESP32-S3 firmware or an RGB LCD evaluation board.

**P2 custom PCB:** Direct 16-bit RGB565-to-24-bit-panel wiring, separate 6-pin GT911 connection, boost backlight driver, ESD, and factory test.

**First ~100:** Buy the exact assembly only against a signed specification/drawing revision and agreed substitution/PCN terms. Keep incoming inspection for luminance, front finish, dead pixels, and touch-grid response.

**1,000+:** Negotiate cover-mask customization, optical bonding only if test evidence supports it, lower direct pricing, reserved production lots, and a mechanically compatible second source. Do not change the active area or enclosure opening during cost-down.

### Displaytech DT050CTFT-IPS-SHB-PTS

**P1:** Buy one or two from DigiKey and build an adapter/backlight board. It is the quickest way to test a fully documented exact production assembly.

**P2 custom PCB:** Terminate the single combined FPC, add the 18 V-class LED driver, use ST1633I I²C touch, and validate 16-bit ESP32-S3 wiring to the 24-bit input.

**First ~100:** Distributor purchase is possible immediately but adds roughly USD 4,800 of display cash before freight; seek a direct SEACOMP quote before committing.

**1,000+:** Direct contract pricing and a lower-brightness/AG variant would be required to make this economically attractive. Preserve the compact mechanical envelope if a custom optical variant is created.

### EastRising ER-TFT050-6 family

**P1:** Buy an ER-TFT050-6-5517 controller-board configuration explicitly including PCAP. Use it to inspect the exact panel family and touch, while accepting that the controller board is temporary.

**P2 custom PCB:** Replace the controller board with the raw ER-TFT050-6 RGB panel/PCAP on Glumi's board. Port and qualify GSL1680F touch firmware.

**First ~100:** Buy the configured panel+touch assembly directly after sample acceptance and a written quote. Lock panel, touch controller, FPCs, bonding, front finish, and inspection standard.

**1,000+:** Negotiate AG/low-reflection cover treatment, custom black mask/FPC if needed, price breaks, safety stock, and a second-source mechanical pinout. The vendor's continuity promise is useful but should become a contract/PCN term.

### Waveshare ESP32-S3-Touch-LCD-5

**P1:** Use immediately for firmware, LVGL, Wi-Fi/TLS/display contention, touch interaction, QR codes, 5-inch layout, and backlight-control experiments.

**P2 custom PCB:** Replace the entire Waveshare PCB and anonymous panel with the chosen exact production assembly. Carry forward only the proven architecture and software settings.

**First ~100:** Do not build production Glumi around this board. Its unnecessary circuitry, unknown panel control, and physical layout work against cost, RF, audio, service, and enclosure goals.

**1,000+:** No direct path as an assembly. Waveshare's published design remains a useful implementation reference.

### Newhaven, Riverdi, and Winstar

- **Newhaven:** P1 sample → direct custom PCB → authorized-channel first 100 is technically straightforward, but cost-down would require a direct quote or migration to a compatible cheaper stack.
- **Riverdi:** same direct-RGB path, but resolve the mechanical drawing discrepancy and high backlight power before enclosure work.
- **Winstar:** the electronics transition cleanly, but the listed glare/large-cover assembly fixes the wrong front geometry. Ask Winstar for a compact AG cover variant before P2.

## 8. Screened-out and non-winning options

### DISEA DS-T050QWH-01CA — cited in the hardware report but not verified as IPS

The current [official DISEA page](https://www.diseaelec.com/standard-tft-lcd/ds-t050qwh-01ca) confirms 5 inches, 800×480, 400 nits, 123.26 × 80.06 × 4.80 mm, 24-bit RGB, I²C PCAP, ST16331 touch, ≥6H cover, “In Production,” and a displayed stock quantity of 100. It does **not** identify IPS or all-view behavior. It lists viewing direction at 12 o'clock and grayscale inversion at 6 o'clock, which is characteristic of a directional panel and contradicts Glumi's must-have wide-view IPS requirement. It is therefore screened out unless DISEA provides a revised exact-MPN datasheet proving IPS/all-view construction.

This is an important correction to the source report's representative description; it does not change the report's requirements.

### Display Visions EA TFT050-84AHITC

This exact 5-inch IPS+PCAP RGB/I²C assembly is well documented, but its [datasheet](https://www.displayvisions.us/fileadmin/eng/pdf/grafik/TFT050-84AHITC.pdf) specifies a glare surface, uses `ILI2130 or equivalent`, has no useful public price/stock signal found, and largely duplicates the Winstar class. It does not improve the shortlist.

### Controller and smart-display modules

Riverdi EVE, Winstar SSD1963/BT81x/UART smart displays, DWIN HMI products, HDMI modules, and similar products can simplify a bench prototype but add a second graphics/controller architecture Glumi does not need. They were not shortlisted because the source architecture already selects direct RGB from ESP32-S3, and their controller/MCU boards would be removed in production.

## 9. Recommendation shortlist

### Candidate A — strongest overall: CDTech S050HWV18HS-DC80-AG

Why it made the shortlist: it is the closest exact match to Glumi's optical, electrical, and mechanical intent.

- Does particularly well: 400 nits, 1000:1, IPS, explicit AG, compact 120.7 × 75.8 mm outline, GT911 I²C touch, low 40 mA backlight current.
- Compromise: quote-only, no formal lifecycle, no public stock, controller listed TBD, and incomplete controlled documentation for the exact AG suffix.
- Approximate cost: **unknown until quote**; it must land near the report's USD 24–38/100-unit planning band to keep first-batch economics coherent.
- Integration complexity: **medium**—direct RGB is right for production but needs FPCs and an LED boost driver.
- Sourcing confidence: **MEDIUM**.
- Buy for P1: **yes, 2–3 exact samples**, if CDTech supplies the requested documentation with the quote.
- Consider for production: **yes, provisional first choice**.

### Candidate B — best prototype candidate: Waveshare ESP32-S3-Touch-LCD-5, SKU 28117

Why it made the shortlist: it removes nearly all electrical bring-up friction while preserving the planned MCU, memory, RGB, touch, and software architecture.

- Does particularly well: immediate LVGL/ESP-IDF work, public schematic/examples, exact ESP32-S3 N16R8 configuration, GT911 touch, integrated backlight driver.
- Compromise: anonymous panel, unverified reflection/minimum-brightness behavior, lots of irrelevant board hardware, and no production sourcing path for the display.
- Approximate cost: **USD 31.99–39.99 family range**; verify exact 800×480 touch option in cart.
- Integration complexity: **low for P1; irrelevant/highly unsuitable for production**.
- Sourcing confidence: **LOW risk for P1, HIGH risk for production**.
- Buy for P1: **yes, now**.
- Consider for production: **no**.

### Candidate C — strongest production/cost alternative: EastRising ER-TFT050-6 family

Why it made the shortlist: it provides both a convenient module now and a raw RGB panel later, with direct low-quantity purchasing and a stated ten-year continuity commitment.

- Does particularly well: low base cost, standard mechanics, 450 nits, IPS, multiple bridge/controller options, explicit continuity statement.
- Compromise: 500:1 contrast, surface not verified, GSL1680F qualification, direct-vendor concentration, and PCAP-configured price not statically visible.
- Approximate cost: **USD 16.15 raw LCD base**; related controller module starts at **USD 24.17 / USD 21.65 at 100 before PCAP option verification**. Obtain the exact PCAP assembly quote.
- Integration complexity: **low-medium for P1 module; medium for production raw RGB**.
- Sourcing confidence: **MEDIUM**.
- Buy for P1: **yes, one controller module explicitly configured with PCAP plus one raw assembly if available**.
- Consider for production: **yes, as the cost-down contender**.

### Candidate D — strongest sourcing-confidence alternative: Displaytech DT050CTFT-IPS-SHB-PTS

Why it made the shortlist: it is the cleanest exact-MPN, documented, stocked, authorized-distributor production option.

- Does particularly well: compact mechanics, current full datasheet, exact touch controller, active status, 237 pieces in stock, published 100-unit price.
- Compromise: 850 nits, unspecified front finish, TBD logic current, and price above target.
- Approximate cost: **USD 64.96 at 1; USD 47.95 at 100** from DigiKey.
- Integration complexity: **medium**, helped by a single combined FPC.
- Sourcing confidence: **LOW risk / high confidence**.
- Buy for P1: **only as the documented production benchmark or if CDTech cannot supply promptly**.
- Consider for production: **yes as fallback, not at current distributor cost**.

## 10. Procurement and qualification actions before selection

Send the same RFQ/technical questionnaire to CDTech, Displaytech/SEACOMP, EastRising, and Winstar:

1. Confirm exact MPN, revision, panel maker, LCD driver, touch controller, bonding method, cover material/thickness, surface treatment, and FPC drawings.
2. State whether any controller, panel glass, polarizer, backlight, or touch IC may be substituted under the same MPN; request written PCN notice terms.
3. Quote samples, 25, 100, 250, 1,000, and 3,000 units; include MOQ, tooling/NRE, payment terms, Incoterm, sample lead, production lead, and validity.
4. Provide stock position, annual capacity, expected lifecycle, last-time-buy policy, warranty, defect allowance, and RMA process.
5. Provide full datasheet, mechanical/3D model, display timing, touch-controller programming guide/firmware, reliability results, RoHS/REACH, and inspection criteria.
6. Provide measured luminance-versus-current and luminance-versus-PWM curves down to the lowest stable output, supported PWM frequency range, and backlight-driver recommendations.
7. Quantify anti-glare haze, gloss/reflectance, hardness, fingerprint treatment, and whether AG is on the cover outer surface.
8. Quote an exposed-front black-mask cover with Glumi artwork and AG treatment, both air-bonded and optically bonded, without changing the active area or FPC.
9. Ask for three same-lot samples and, if possible, one sample from a different production lot.
10. Ask which mechanically/electrically compatible second-source panel they recommend.

# What we still need to test physically

- **Minimum brightness in a dark bedroom:** measured cd/m² at each UI step, lowest stable current/PWM, step size, and whether zero cleanly sleeps the panel.
- **Flicker:** visible and camera-detectable flicker at the lowest brightness settings; interaction between PWM and touch/audio/Wi-Fi loads.
- **Perceived black level and uniformity:** center, edges, corners, clouding, leakage, and glow at the intended viewing angle.
- **Glare/reflections:** window, ceiling lamp, bedside lamp, dark-room specular reflection, and whether AG haze softens type.
- **Touch feel:** friction, fingerprints, edge accuracy, latency, false touches, cleaning, and behavior with the final enclosure grounding.
- **Viewing angle in real environments:** side, above, and below at desk, bedside, shelf, and counter heights—not just the datasheet CR threshold.
- **Typography quality:** Spanish UI, mg/dL and mmol/L, small settings text, icons, QR code scan reliability, antialiasing, and RGB565 banding.
- **Color quality:** state-background separation, saturation, gradient/banding behavior, white point, and consistency between units/lots.
- **Across-room readability:** 1, 3, and 5 m with representative users, lighting, eyewear, and all glucose-state colors.
- **Digit size:** determine the maximum robust numeral height and stroke width within the 108 × 64.8 mm active area.
- **Nighttime room illumination:** whether minimum backlight and black areas remain unobtrusive after dark adaptation.
- **Thermal behavior:** panel, boost driver, ESP32-S3, Wi-Fi, and enclosure temperature at minimum/typical/maximum brightness plus audio.
- **Power:** complete-system input power across brightness, Wi-Fi/TLS, touch, audio, OTA, and display sleep.
- **EMI/ESD robustness:** touch stability, RGB integrity, Wi-Fi RSSI, speaker-amplifier noise, charger variation, and front-surface discharge.
- **Mechanical quality:** cover flatness, FPC bend, adhesive/bond line, dust, bezel fit, pressure sensitivity, and drop/transport behavior.
- **Perceived product quality:** reflections, black border, cover edge, touch sound/feel, pixel defects, and whether the front looks like an intentional appliance rather than a dev kit.
- **Lot consistency and substitution:** repeat key tests across at least two lots before the first 100-unit PO.

# Displays I would order now

The smallest sensible order set is **three directions**, not three interchangeable production winners:

1. **One Waveshare ESP32-S3-Touch-LCD-5, SKU 28117, specifically the 800×480 touch version.** Use it immediately for firmware, RGB/PSRAM/Wi-Fi stress, touch flows, typography, QR codes, digit sizing, and the 5-inch physical UX. It does not qualify the production optical stack.
2. **Three CDTech S050HWV18HS-DC80-AG samples from the same exact revision, ideally with one from another lot.** These answer the most important optical questions: anti-glare quality, 400-nit range, black level, minimum brightness, touch feel, and compact mechanical integration. Do not accept the non-AG `DC80` as an unnoticed substitute.
3. **One EastRising ER-TFT050-6-5517 configured explicitly with capacitive touch, plus one matching raw ER-TFT050-6 + ER-TPC050A2-1 assembly if available.** This validates whether the low-cost module-to-raw-panel path is real and whether 500:1 contrast/GSL1680F touch are acceptable.

If CDTech cannot provide a controlled exact-AG datasheet, sample lead time, and credible 100/1,000-unit quote, replace item 2 with **two Displaytech DT050CTFT-IPS-SHB-PTS units from DigiKey** as the documented production benchmark. Do not order Riverdi or Newhaven first unless a premium high-brightness reference is specifically needed; their known cost/power compromises do not resolve a unique Glumi uncertainty.
