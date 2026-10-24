---
title: "Glumi Hardware Architecture Exploration — Essential, Balanced, Premium"
last_updated: "2026-09-25"
---

# Glumi Hardware Architecture Exploration

> **Superseded for current decisions:** the September 2026 hardware interview changed the product assumptions around display size, touch, brightness control, audio, profiles, and battery. Use [Glumi Hardware Requirements Decision Report](hardware-requirements-decision-report.md) for the current recommendation. This document remains the underlying option research.

Decision-oriented comparison of three production architectures for an initial run of 100 units. This document defines component *classes*, specifications, trade-offs, approximate costs, and validation work. It intentionally does not select manufacturers, suppliers, or final part numbers.

## Executive conclusion

The strongest architecture for Glumi is not the one with the most hardware. It is a quiet, wired 4.3-inch IPS object with excellent low-brightness control, a high-resolution display, automatic ambient-light adaptation, one recessed recovery control, and no alarm hardware. Projected-capacitive touch is a viable cross-tier interaction architecture when tied to a defined experience; it should not be treated as a cosmetic Premium feature.

The three coherent configurations are:

- **A — Essential:** 4.3-inch 480×272 IPS, controller-backed display interface, wired USB-C, ambient-light sensor, one recessed recovery button, and a production Wi-Fi MCU module without a large external framebuffer. Estimated COGS at 100 units: **USD 99–155 without touch; USD 109–175 with touch**.
- **B — Balanced:** 4.3-inch 800×480 IPS, direct RGB565 drive, external PSRAM, improved optical stack and enclosure finish, wired USB-C, the same minimal controls, and the same ambient-light behavior. Estimated COGS: **USD 135–208 without touch; USD 145–228 with touch**.
- **C — Premium:** the Balanced compute/display foundation with a selected higher-grade IPS panel, bonded or near-bonded anti-glare cover glass, rechargeable battery for temporary portability and power-cut continuity, a service-replaceable battery architecture, and a robust fold-flat stand. Estimated COGS: **USD 205–314 without touch; USD 213–330 with touch**.

**Recommendation:** build toward **Balanced**, but do not freeze it until the display/night tests pass. Essential preserves the product thesis and may be the commercially necessary architecture. Premium is justified only if a portability experiment demonstrates repeated movement between desk, kitchen, and bedside; otherwise its battery and hinge are expensive liabilities.

The most important spending decisions are, in order:

1. reliable freshness and fail-safe degraded states;
2. minimum-brightness performance and automatic adaptation;
3. 800×480 rendering quality and viewing angle;
4. optical and mechanical finish;
5. battery/portability only after behavioral validation.

Audio, haptics, decorative LEDs, proximity sensing, a Linux-class processor, AMOLED, and a metal enclosure do not earn a place in any tier. Touch earns a place only through its interaction value, not through tier signaling.

---

## 1. Scope, evidence, and relationship to existing work

### 1.1 Upstream product constraints carried forward

This exploration inherits the following principles from the [Product Brief](../../layer-1-product/latest/product-brief.md) and [Design Principles](../../layer-2-design/latest/Principios%20de%20dise%C3%B1o.md):

- glucose value and trend are the protagonist;
- everyday use requires no interaction;
- stale or unavailable data must never resemble fresh data;
- night behavior is a product property, not a cosmetic mode;
- silence is intentional; Glumi is not the medical alarm system;
- the object should feel domestic, durable, and understandable;
- configuration and recovery must not strand the user.

The present P0 has already shown that a physical display can obtain and render real glucose data over Wi-Fi. It has **not** validated the production display, night experience, industrial design, PCB, manufacturing route, or cost.

### 1.2 Existing assumptions that should be kept

- **4.3-inch 800×480 IPS is a credible P1 reference architecture.** Existing research correctly identifies its physical fit, adequate active area, and need for roughly 750 KiB per RGB565 framebuffer.
- **ESP32-S3-class hardware is sufficient.** A Linux MPU would add boot time, storage corruption exposure, update surface, and power without improving the ambient UI.
- **Touch is not automatically Premium.** Earlier work treated touch as unnecessary because no interaction had been defined. That premise is now reopened: if touch creates a meaningful, validated experience, it should be considered across all tiers and costed as a system, not dismissed from the panel price alone.
- **Backlight control must be designed, not assumed.** The reference board's on/off backlight cannot validate Glumi's night behavior.
- **Wi-Fi/display memory contention is a real engineering risk.** Espressif documents RGB panel corruption when the CPU, flash, Wi-Fi, and LCD compete for PSRAM bandwidth; bounce buffers and conservative pixel clocks are required design considerations ([Espressif RGB LCD guide](https://docs.espressif.com/projects/esp-idf/en/latest/esp32s3/api-reference/peripherals/lcd/rgb_lcd.html), [Espressif LCD FAQ](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)).

### 1.3 Existing assumptions to reconsider

| Previous assumption | Reconsideration |
|---|---|
| “No backend; direct on-device integration” | The new brief mandates communication with the Glumi backend. This is a product/security decision, not a hardware differentiator: all three architectures can support HTTPS to either a Glumi backend or a direct provider. It must be reconciled before firmware architecture is frozen. |
| “No battery” | Keep this for Essential and Balanced. Reopen it only as a Premium hypothesis about temporary portability, not as an assumed upgrade. |
| “Dedicated temperature-compensated RTC plus coin cell is required” | A safer, simpler strategy is to fail closed after reboot until trusted time and fresh data are obtained, and to use monotonic elapsed time while powered. A low-cost 32.768 kHz reference is reasonable; a coin-cell RTC is not automatically justified. |
| “Future audio requires reserving display-bus GPIO now” | The current product explicitly rejects alarm behavior. Do not compromise today's display architecture for an unvalidated future caregiver product. A future alerting variant may require a different PCB. |
| “E-paper is disqualified because it is dark at night” | **Unlit** e-paper is disqualified for bedside use. Front-lit 4.2-inch modules exist, so monochrome e-paper deserves a prototype. It still carries ghosting, retained-stale-image, front-light, and integration risks that prevent selecting it now. |
| “HUB75 is a primary contender” | It remains an informative visual experiment, but its 160–192 mm width, pixel structure, depth, power, and signage character do not fit the current compact 4–4.5-inch domestic-object direction. |

### 1.4 Decision status

This is an intermediate architecture exploration, not an ADR. None of the three configurations is frozen. Costs are planning ranges, not quotations.

---

## 2. Assumptions behind the comparison

- Initial run: **100 finished units**.
- Currency: **USD**, September 2026 planning basis.
- Product is assembled in a low-volume process using a custom PCB and production-grade additive manufacturing, cast urethane/soft tooling, or a hybrid enclosure process.
- A certified external 5 V USB power adapter is **not** included; a USB-C cable and basic packaging are included.
- COGS includes component BOM, bare PCB, low-volume PCBA setup amortization, final assembly, programming, functional test, basic packaging, and a 5–8% yield/scrap allowance.
- COGS excludes R&D labor, industrial-design fees, compliance lab fees, hard injection tooling, VAT/import duties, financing, warranty reserve, fulfillment, and retail margin.
- An injection-molded enclosure at only 100 units is a separate capital decision. Even a representative low-volume example assigns roughly USD 30 per part to aluminum tooling at 100 pieces; a multi-part enclosure can therefore add **USD 60–150+ per unit** in amortized tooling before molded part cost ([Formlabs cost overview](https://formlabs.com/blog/injection-molding-cost/)).
- Published 100-unit panel pricing spans roughly USD 12 for a bare 4.3-inch 800×480 IPS panel from a specialist display supplier to USD 26–30 from broad-line/industrial channels. This is why the display ranges below include both cost-focused and supply-assured paths ([100-unit display benchmark](https://www.buydisplay.com/4-3-800x480-ips-tft-lcd-module-all-viewing-optl-touchscreen-display), [industrial-channel benchmark](https://www.digikey.com/en/products/detail/orient-display/AFK480800A0-4-3INTM/15775100)). These are market anchors, not selections.
- Low-volume PCBA has meaningful fixed setup/NRE. The effective cost at 100 units depends more on unique line items, board sides, inspection, and test than on bare-board area ([low-volume PCBA cost model](https://lowvolumepcbassembly.com/)).
- International freight and Argentina import exposure should receive a separate landed-cost model once actual sourcing regions and Incoterms are known.

---

## 3. Display decision space

### 3.1 Technology comparison

| Technology | What it does well | What works against Glumi | Decision |
|---|---|---|---|
| **TN TFT LCD** | Lowest cost, mature supply, fast refresh | Narrow/uneven viewing cone and gray inversion are especially harmful on a bedside object seen from above and below | Reject for all tiers |
| **IPS TFT LCD** | Mature 4.3-inch supply, wide viewing angle, full color, instant updates, long static-content life, moderate cost | Backlight makes black glow at night; continuous power; needs a proper LED driver and optical treatment | **Use for A/B/C** unless physical testing rejects it |
| **AMOLED/OLED** | True black, excellent low-light behavior, thin, high contrast | Affordable panels are generally too small; 4-inch-class supply often implies MIPI-class interfaces and a stronger processor; static UI creates uneven-aging risk. OLED manufacturers themselves warn against persistent bright static elements ([Samsung](https://www.samsung.com/us/support/troubleshoot/TSG10003240/), [Sony](https://www.sony.com/electronics/support/televisions-projectors-lcd-tvs-android-/kd-55x85j/articles/00173479)) | Reject for the first product |
| **Monochrome e-paper** | Paper-like daylight readability, near-180° view, image retention with zero hold power, strong battery potential. E Ink describes the technology as reflective and bistable ([E Ink](https://www.eink.com/tech/detail/Benefits)) | No emission without a front light; visible refresh/ghosting; retained old number after loss of power; front-light integration and optical stack are less standardized | Prototype as a divergent option; do not assign to a tier yet |
| **Multi-color e-paper** | Ambient color without continuous display power | Slower refresh, muted color, higher cost, more ghosting, and color adds little to the current UI | Reject |
| **Reflective memory LCD** | Low power, fast relative to e-paper, sunlight-readable | Limited 4-inch sourcing, no inherent night readability, weaker contrast than e-paper | Monitor, not a production candidate |
| **HUB75/direct-view LED matrix** | True black, exceptional distance readability, broad dimming range | Physically large, thick, power hungry, coarse pixels, and visually “signage” rather than restrained domestic object | Reject for current form factor |
| **Segmented LED/VFD** | Legible and characterful | Cannot explain stale, unavailable, setup, and recovery states without cryptic codes | Reject |

### 3.2 Why 4.3 inches remains the center

A typical 4.3-inch 16:9 active area is approximately 95 × 54 mm. That permits a 35–42 mm-high primary number while retaining room for the trend, unit, and exceptional-state language.

- **Below roughly 3.5 inches**, the product remains readable nearby but weakens the “across the room” promise and makes secondary state text cramped.
- **At 4.0–4.5 inches**, Glumi can preserve the simple composition without becoming an appliance-sized screen.
- **At 5 inches and above**, the enclosure, cable leverage, stand, packaging, and visual presence all grow. The UI does not need the extra area.

The size decision should still be tested with 1:1 masks at 3 m because readable glyph height, not diagonal inches, is the true requirement.

### 3.3 Resolution and pixel density

| Class | Approx. density at 4.3 in | Consequence |
|---|---:|---|
| 480×272 | ~128 ppi | Large numerals are adequate; curves and diagonals show more aliasing; small freshness and setup text need careful typography |
| 800×480 | ~217 ppi | Clearly smoother numerals/arrows, more robust secondary text, more freedom for exceptional states; requires ~750 KiB per RGB565 framebuffer |
| Above 800×480 | >217 ppi | Little visible value at normal viewing distance; increases interface, memory, sourcing, and power pressure |

The jump from 480×272 to 800×480 is meaningful. The jump beyond 800×480 is not.

### 3.4 Brightness, black level, and night behavior

Maximum brightness is not the key specification for an indoor product. The architecture must instead specify:

- stable dimming to an effective **≤1–2%** of maximum without visible flicker;
- no abrupt steps near the dark end;
- temporal hysteresis so room-light changes do not pump brightness;
- a user-calibratable minimum;
- a warm, low-luminance night palette;
- an optical stack that hides the LCD border without making daylight text muddy;
- no “black-screen but glowing rectangle” surprise.

An ambient-light sensor is therefore included in all three configurations. A scheduled night mode can supplement it, but should not replace it.

### 3.5 Optical stack by tier

- **Essential:** air-gapped hard-coated smoked PMMA/polycarbonate window, matte or fine-texture outer surface, black internal mask.
- **Balanced:** higher-transmission hard-coated PMMA or thin anti-glare glass, tighter air gap, controlled black mask, improved gasket and dust management.
- **Premium:** selected panel plus optically bonded or near-bonded anti-glare cover glass, validated for low reflection and no trapped dust/bubbles.

Optical bonding can improve perceived black, reflections, and solidity, but it adds supplier dependence, yield risk, rework difficulty, and MOQ. It must be demonstrated on coupons or samples before entering C.

---

## 4. Interaction architecture

### 4.1 Daily interaction

Looking remains the primary interaction. Glumi should not require a tap to wake, reveal the number, acknowledge a reading, or dismiss routine states. Touch may add valuable secondary interactions, but the current glucose state must remain available without touching the device.

### 4.2 Setup

Setup should occur through a browser-based local flow or the chosen backend onboarding flow. The display only needs to present clear instructions, QR/PIN information, progress, and failure recovery.

BLE provisioning could reduce captive-portal friction, but it also adds a second radio workflow, mobile browser/app dependencies, coexistence testing, privacy surface, and support cases. Because the hardware module may contain BLE anyway, it can be prototyped in firmware without becoming a launch requirement. ESP32-class Wi-Fi and BLE share RF time, so simultaneous operation is not free ([Espressif coexistence guide](https://docs.espressif.com/projects/esp-idf/en/v5.0.6/esp32s3/api-guides/coexist.html)).

### 4.3 Recovery

All tiers need one **recessed physical service control**, reachable with a fingernail or paperclip:

- short hold: show network/device diagnostics or initiate a safe retry;
- long hold with on-screen countdown: clear network credentials and return to setup;
- protected boot gesture: recovery firmware/factory test.

The destructive action must be visible and cancelable. A hidden button with unexplained timing would violate “simple, never cryptic.”

### 4.4 Controls rejected

| Control | Why it sounds attractive | Why it is rejected |
|---|---|---|
| Capacitive touch surface | Invisible control | Poor affordance and hard recovery semantics |
| Resistive touch display | Low-cost touch input | Worse optical clarity, pressure-based feel, softer surface, and weaker consumer-product perception than projected capacitive touch |
| Rotary encoder | Tactile brightness/settings | Creates a menu problem the product does not have |
| Multiple visible buttons | Direct settings | Adds clutter and invites unnecessary interaction |
| No physical control at all | Pure object | Makes recovery from router/account failure fragile |

### 4.5 Projected-capacitive touch architecture

If the touch experience earns its place, the appropriate technology is a **projected-capacitive touch panel (PCAP)** integrated over the LCD. A typical 4.3-inch PCAP assembly includes the sensor, a touch controller, and a cover layer; it communicates over I²C and normally adds interrupt and reset signals ([capacitive-touch interface overview](https://docs.waveshare.com/ESP32-Peripheral-Tutorials/Display/Touch)).

Conceptually:

```text
Finger
  ↓
Cover lens + capacitive sensor
  ↓ I²C + INT + RESET
Touch controller ──→ MCU

LCD pixel interface remains unchanged
```

Touch does **not** require a different processor class, more framebuffer memory, a different Wi-Fi architecture, or a battery. It usually requires:

- one additional FPC connector;
- shared I²C plus one or two GPIO, or an inexpensive I/O expander if RGB pin pressure is severe;
- ESD protection at the exposed touch assembly;
- power sequencing/reset handling;
- an air-bonded, tape-bonded, or optically bonded display stack;
- touch alignment, functional test, and additional cosmetic/dust inspection.

The optical penalty matters. Representative 4.3-inch PCAP panels specify **≥86% transmission**, so the same perceived screen brightness may require roughly 10–16% more backlight output, with corresponding heat and battery impact ([representative 4.3-inch PCAP specification and 100-unit pricing](https://www.ronboe.com/shop/rxc-pg043099a-01/)). Optical bonding can recover some perceived contrast by removing reflective air interfaces, but increases supplier/process dependence and rework cost.

### 4.6 Touch cost delta at 100 units

Public 100-unit pricing for a standard 4.3-inch PCAP sensor/controller assembly is roughly **USD 3–6** before bonding and final-product integration. By contrast, low-quantity industrial distribution can show a **USD 20–32** premium for an integrated LCD+PCAP assembly over the corresponding non-touch display, illustrating how channel, integration, validation, and supply assurance can dominate raw sensor cost ([paired industrial display listings](https://www.digikey.com/en/product-highlight/n/newhaven-display/4-3-inch-ips-tft-displays)).

For Glumi at 100 units, use this planning model:

| Incremental item | Delta/unit | Notes |
|---|---:|---|
| PCAP sensor + controller + FPC | +USD 3–7 | Direct/production-channel class, not single-unit retail |
| Bonding/tape/gasket and black mask changes | +USD 2–6 | Air/tape bonding near low end; optical bonding near high end |
| Connector, ESD, passives, possible I/O expansion | +USD 1–2 | RGB architecture may make the extra GPIO more expensive than the parts |
| Added assembly, touch test, cosmetic inspection, yield | +USD 2–5 | Includes alignment and false-touch/basic gesture test |
| Credit for front window that PCAP cover can replace | USD 0 to −5 | Only if the touch cover provides the required size, hardness, finish, and black border |
| **Net finished-unit COGS delta** | **+USD 8–20** | Budget **+USD 10–20** until the optical stack is quoted |

At 100 units, touch therefore adds approximately **USD 1,000–2,000 to the production run**, not including product-design and firmware development.

Physical impact is modest but not zero: expect roughly **1–2.5 mm** additional display-stack thickness and **15–30 g** before subtracting any front window that the PCAP cover replaces. The touch controller's power is negligible for wired A/B; on battery-powered C, the larger effect is the extra backlight needed to compensate for optical transmission loss rather than the controller itself.

One-time effort is more consequential than the parts:

- electrical/PCB integration: low, typically several engineering days;
- basic driver and coordinate handling: low;
- a polished interaction model, animation, accidental-touch rejection, exceptional states, and usability testing: medium to high, depending on the proposed experience;
- touch/display optical samples or custom cover-lens artwork/tooling: quote-dependent NRE;
- production fixture/software changes: modest but required.

If touch is valuable, the preferred strategy is **one touch architecture across A/B/C**, not separate touch and non-touch commercial variants. Two variants at 100 units split purchasing leverage, double optical/mechanical QA paths, and create firmware/support ambiguity. Tier differentiation should remain display resolution, optics, construction, and portability—not whether the product can perform its intended interaction.

---

## 5. Processing and memory architecture

### 5.1 Workload

The processor must support:

- Wi-Fi station plus temporary setup access point;
- TLS to the Glumi backend;
- JSON or compact payload parsing;
- secure credential storage;
- watchdogs and a fail-safe state machine;
- dual-image signed OTA with rollback;
- USB factory programming/recovery;
- font rendering and display updates;
- monotonic freshness tracking;
- local diagnostic page/log buffer.

This is a connected appliance workload, not a general-purpose-computer workload.

### 5.2 Architecture by display class

| Requirement | Essential | Balanced / Premium |
|---|---|---|
| CPU class | Production Wi-Fi MCU, hardware crypto, at least one capable application core; dual-core preferred | Dual-core Wi-Fi MCU with native LCD/DMA support |
| Internal RAM | ~400–512 KiB class | ~512 KiB class |
| External RAM | Not mandatory if display module contains GRAM and updates over serial/8080 | **8 MB PSRAM class** to comfortably hold one/two framebuffers and network/UI allocations |
| Flash | **8 MB minimum** for firmware, dual OTA slots, fonts/assets, config, rollback | **16 MB preferred** for dual OTA, recovery image, assets, diagnostics |
| Display interface | QSPI/SPI or 8-bit controller-backed interface; low pin count | 16-bit RGB565 plus timing signals, or a proven high-throughput serial controller |
| Boot target | Useful status in <5 s after power; do not show glucose until freshness is established | Same |

### 5.3 Why not a Linux MPU

A Linux-class processor provides more RAM and easy web rendering, but Glumi would inherit:

- longer boot;
- filesystem/update complexity;
- greater idle power and heat;
- larger PCB and power tree;
- more security maintenance;
- higher corruption and recovery burden;
- no user-visible benefit for a number and arrow.

It is appropriate for a disposable prototype, not the production architecture.

### 5.4 Long-term availability and production suitability

The eventual module must have:

- published lifecycle/last-time-buy policy;
- stable SDK and production flashing tools;
- hardware secure boot, flash encryption, unique identity support;
- RF certification evidence appropriate to target markets;
- module shielding and a documented antenna keep-out;
- at least one credible second-source strategy at the architecture level.

Using a certified radio module reduces RF design risk, but it does not eliminate final-host compliance responsibilities. Argentina treats Wi-Fi modules and IoT devices as homologable equipment under current ENACOM rules ([ENACOM 2026 overview](https://www.enacom.gob.ar/institucional/enacom-agiliza-los-permisos-para-comercializar-equipos-de-telecomunicaciones-en-el-pais_n4823), [technical category](https://www.enacom.gob.ar/multimedia/normativas/2026/res57.pdf)).

---

## 6. Connectivity

| Interface | Decision | User impact |
|---|---|---|
| 2.4 GHz Wi-Fi | **Core, all tiers** | Works with broad domestic-router installed base; connects to backend |
| 5 GHz Wi-Fi | Omit | No meaningful data-rate benefit; may reduce range and increase module constraints |
| Bluetooth/BLE | Hardware capability may exist; disabled by default | Only enable if a provisioning prototype is demonstrably better than browser setup |
| USB data | Factory/recovery only | Enables flashing, logs, and rescue without exposing a customer data workflow |
| Ethernet | Omit | Connector and cable conflict with domestic-object positioning; Wi-Fi is mandatory anyway |
| Cellular | Omit | Subscription, antenna, power, certification, and support burden vastly exceed value |
| Direct CGM radio link | Omit | Protocol, regulatory, compatibility, and reliability scope contradict the backend architecture |

The device should be designed so “backend vs direct LibreLinkUp” changes firmware and security, not the PCB. Both require TLS, adequate heap, secure credentials, and fail-safe stale handling.

---

## 7. Power, USB-C, and portability

### 7.1 Wired baseline

Essential and Balanced are continuously powered from USB-C at 5 V.

Conceptually:

```text
USB-C 5 V sink
  ├─ CC resistors / ESD / input protection
  ├─ 3.3 V efficient regulator → MCU + logic + sensors
  └─ constant-current LED driver with PWM/analog dimming → LCD backlight
```

USB Power Delivery negotiation is unnecessary. Standard 5 V Type-C current is adequate if input behavior and cable drop are designed correctly; the USB Type-C specification defines default/1.5 A/3 A current advertisement at 5 V ([USB-IF Type-C specification](https://www.usb.org/sites/default/files/USB%20Type-C%20Spec%20R2.0%20-%20August%202019.pdf)).

### 7.2 Battery value test

Portability creates value only if users repeatedly move Glumi or value continuity during short outages. It does **not** make an IPS Glumi a week-long cordless product.

For a Premium architecture:

- single-cell rechargeable lithium pack, **4–6 Ah class / roughly 15–22 Wh**;
- real-world autonomy target: **6–14 hours**, depending on brightness, Wi-Fi duty, panel, and conversion losses;
- charge while operating;
- protected power path, cell protection, temperature sensing, safety timers, state-of-charge measurement, undervoltage cutoff, and shipping mode;
- external 5 V USB-C input; no higher-voltage PD required;
- battery pack serviceable by trained service using screws and connector, not permanently bonded;
- firmware communicates low battery quietly on the display.

The correct product promise is “move it around the home for part of a day and survive short power cuts,” not “cordless for days.”

### 7.3 Battery consequences

| Consequence | Effect |
|---|---|
| Volume | Adds roughly 8–14 mm of local depth unless spread behind the display |
| Weight | Adds ~90–140 g for cell, protection, structure, and fastening |
| Thermal | Charging while the backlight and Wi-Fi operate creates the worst-case enclosure temperature |
| Aging | Capacity loss becomes a product-support and service issue after several years |
| Safety | Requires qualified cell/pack, protection, thermal validation, abuse review, and transport documentation |
| Certification | Lithium transport commonly invokes UN 38.3 evidence; portable sealed cells are commonly assessed against IEC 62133-class safety expectations ([UL overview](https://latam.ul.com/es/industrias/productos-y-componentes)) |
| Manufacturing | Adds cell handling, state-of-charge control, insulation, connector inspection, and end-of-line charge test |

Lithium charging is not “charger IC plus cell.” Safe architectures monitor voltage, current, time, and temperature and include protection against overcharge/overdischarge; thermal design is especially important for linear charging ([Microchip charging guidance](https://www.microchip.com/content/dam/mchp/documents/OTH/ApplicationNotes/ApplicationNotes/00947a.pdf)).

### 7.4 Why battery is not in Essential or Balanced

If removed, the core glanceable experience is unchanged in its intended fixed locations. The current product evidence does not show that carrying one unit is better than leaving it where users spend time or eventually owning two lower-cost units. Battery therefore remains an **experience enhancer with unvalidated demand**, not a core component.

---

## 8. Sensors and feedback hardware

### 8.1 Sensor evaluation

| Sensor | User problem | Software substitute | Cost / complexity | Decision |
|---|---|---|---|---|
| Ambient-light sensor | A day-bright screen is intrusive at night; a night-dim screen is unreadable by day | Schedule/manual setting, but both fail when routines or room lighting change | Low component cost; moderate calibration and placement work | **Include in all tiers** |
| Proximity sensor | Could wake/dim on approach | The number should already be available; waking adds friction and motion | Adds aperture, false triggers, behavior tuning | Omit |
| Accelerometer | Could rotate UI or detect being moved | Fixed landscape UI and stand geometry already define orientation | Adds no useful experience | Omit |
| General temperature sensor | Could report device/room temperature | Internal MCU estimate is enough for diagnostics | Displaying room temperature dilutes the product | Omit |
| Battery thermistor | Protects charging | No software substitute | Low cost, safety-critical in C | Include in Premium battery pack |
| RTC IC + coin cell | Maintains wall time through power loss | Sync trusted time before showing data; use monotonic time while running | Adds cell holder, service/lifetime issue | Omit initially; keep PCB option if testing proves need |
| 32.768 kHz reference | Improves low-power/elapsed-time stability | Internal RC plus fail-closed behavior | Very low cost | Recommended in B/C; evaluate in A |

The ambient-light sensor must see the room, not the backlight. Its window, gasket, angle, display leakage, and enclosure color are part of its system specification.

### 8.2 Feedback hardware

| Hardware | Decision | Reason |
|---|---|---|
| Speaker/buzzer | Omit all tiers | Glumi is not the official medical alarm; sound would change the product's category and anxiety profile |
| Haptic motor | Omit all tiers | A desk object's vibration is weak, noisy, and alarm-like |
| Visible status LED | Omit all tiers | Duplicates the display and can become a night nuisance |
| RGB accent LED | Omit all tiers | Decorative state competes with the data and dates the object |
| Hidden factory LED/test pads | Include as enabler | Useful for production and service; not visible in normal use |

---

## 9. Shared safety and reliability requirements

All tiers must meet the same behavioral floor:

- never present cached glucose as fresh after boot;
- freshness is derived from source timestamp plus trusted elapsed time, not display-update time;
- if time is not trusted, show an explicit unavailable state;
- brownout detection and watchdog recovery;
- signed OTA, rollback, and protected credentials;
- a recovery image or robust factory-flash path;
- display/backlight fails dark or explicit, never with an indefinitely “fresh-looking” number;
- 72-hour and later 30-day soak tests with Wi-Fi reconnect, backend loss, OTA, and power cycling;
- antenna keep-out maintained through PCB, battery, fasteners, and enclosure;
- ESD and input protection at USB-C and user-accessible metal;
- no required cloud or mobile action for ordinary viewing.

Because Glumi displays health-adjacent information, intended-use language and exceptional-state design may affect regulatory classification. Argentina now has explicit SaMD criteria tied to intended diagnostic/preventive/treatment/mitigation use; a formal classification consultation remains necessary rather than assuming “secondary display” is exempt ([ANMAT update](https://www.argentina.gob.ar/noticias/anmat-actualiza-los-criterios-regulatorios-para-software-como-dispositivo-medico-samd), [classification consultation](https://www.argentina.gob.ar/servicio/consulta-de-clasificacion-de-nuevas-tecnologias-para-la-salud)).

---

## 10. Architecture A — Essential

### 10.1 Product definition

The least expensive architecture that still feels intentional: a compact, fixed-location, automatically dimming consumer object. Money is not spent on high pixel density, battery, optical bonding, adjustable mechanisms, or redundant feedback. Touch may still belong here if it is part of the intended experience.

### 10.2 Electronics stack

```text
USB-C 5 V
  ├─ input/ESD protection
  ├─ efficient 3.3 V regulation
  │    └─ Wi-Fi MCU module
  │         ├─ secure flash / OTA
  │         ├─ controller-backed display bus
  │         ├─ [optional] PCAP controller over I²C
  │         ├─ ambient-light sensor
  │         ├─ recessed recovery button
  │         └─ factory test/program pads
  └─ dimmable constant-current backlight driver
       └─ 4.3 in 480×272 IPS LCD
```

### 10.3 Target specifications

- 4.3-inch, landscape, 480×272 IPS, ~128 ppi;
- full viewing-angle class; 250–350 nit class is enough indoors;
- 16-bit color is sufficient;
- controlled low-end backlight and automatic ambient adaptation;
- Wi-Fi MCU with hardware security, ~400–512 KiB RAM, 8 MB flash class;
- display module/controller with local GRAM so a full external framebuffer is not required;
- optional integrated PCAP over I²C; no resistive touch, BLE workflow, battery, audio, or visible LEDs;
- one recessed service button;
- fixed 12–18° tilt;
- USB-C power and factory recovery data.

### 10.4 Industrial design and CMF

- approximate envelope: **125 × 78 × 28 mm** excluding cable and fixed foot;
- target weight: **180–260 g**, including deliberate base mass if needed to resist cable pull;
- production-grade MJF/SLS nylon, high-quality FDM with finishing, or cast urethane shell;
- fine matte texture; warm neutral pigment rather than paint where viable;
- hard-coated smoked PMMA/polycarbonate front window;
- elastomer feet and captive/thread-forming fasteners;
- removable rear cover for service;
- fixed stand/base is preferable to a hinge at this tier.

### 10.5 Cost model — 100 units

| Cost bucket | Range/unit |
|---|---:|
| Display + backlight interface | USD 15–23 |
| MCU/radio module + flash | USD 5–9 |
| PCB + regulation + protection | USD 7–11 |
| Sensor + connector + button + misc. electronics | USD 6–8 |
| **Electronics BOM** | **USD 33–51** |
| Enclosure/body | USD 18–31 |
| Front optical window/mask | USD 6–10 |
| Stand/feet/fasteners/internal mounts | USD 4–7 |
| **Mechanical BOM** | **USD 28–48** |
| PCBA setup/assembly, amortized | USD 11–17 |
| Final assembly/program/test | USD 16–23 |
| Cable + basic packaging | USD 6–9 |
| Yield/scrap allowance | USD 5–7 |
| **Assembly / production** | **USD 38–56** |
| **Estimated assembled COGS — no touch** | **USD 99–155** |
| **Touch system delta** | **+USD 10–20** |
| **Estimated assembled COGS — touch** | **USD 109–175** |

### 10.6 Value engineering

| Component | Class | If removed |
|---|---|---|
| IPS display | CORE | Product ceases to exist |
| Wi-Fi MCU/module | CORE | Cannot obtain or render data |
| Secure flash/OTA capacity | ENABLER | Field failures become physical returns/reflashes |
| Dimmable backlight driver | CORE | Night behavior becomes unacceptable |
| Ambient-light sensor | EXPERIENCE ENHANCER | Device requires schedules/manual compromise; night failures increase |
| Recessed button | ENABLER | Router/account recovery can strand the user |
| Hard-coated front window | EXPERIENCE ENHANCER | More prototype-like appearance, dust exposure, visible panel border |
| Projected-capacitive touch | EXPERIENCE ENHANCER if UX is defined | Removes the proposed touch interactions; ambient glance experience remains |
| Fixed weighted base/feet | EXPERIENCE ENHANCER | Cable moves the device and perceived quality falls |
| USB data traces/test pads | ENABLER | Production and field recovery become harder |

### 10.7 Main compromises

- coarser typography and exceptional-state text;
- lower optical depth/black than B/C;
- no temporary portability;
- fixed viewing angle;
- controller-backed display sourcing must be verified for lifecycle and performance.

---

## 11. Architecture B — Balanced

### 11.1 Product definition

The best cost/experience balance: spend on the parts the user sees continuously—resolution, viewing behavior, dimming, optics, seams, and stability—without adding interaction or portability subsystems.

### 11.2 Electronics stack

```text
USB-C 5 V
  ├─ input/ESD protection
  ├─ efficient 3.3 V regulation
  │    └─ dual-core Wi-Fi MCU module + PSRAM
  │         ├─ secure flash / dual OTA
  │         ├─ RGB565 + timing → LCD
  │         ├─ [optional] PCAP controller over I²C
  │         ├─ ambient-light sensor
  │         ├─ low-frequency clock reference
  │         ├─ recessed recovery button
  │         └─ factory test/program pads
  └─ wide-range dimmable constant-current backlight driver
       └─ 4.3 in 800×480 IPS LCD
```

### 11.3 Target specifications

- 4.3-inch 800×480 IPS, ~217 ppi;
- 350–500 nit class, selected for minimum brightness and uniformity rather than peak output;
- RGB565; one or two framebuffers in 8 MB PSRAM class;
- 16 MB flash preferred;
- conservative pixel clock, bounce-buffer path, and Wi-Fi/OTA stress validation;
- ambient-light sensor with calibrated curve and hysteresis;
- optional integrated PCAP over I²C; no resistive touch, audio, haptics, or visible status LED;
- one recessed recovery control;
- continuous wired USB-C power.

### 11.4 Industrial design and CMF

- approximate envelope: **128 × 82 × 30 mm**;
- target weight: **230–330 g**;
- tighter bezel reveal, smaller front-to-panel air gap, controlled internal black mask;
- higher-quality pigmented nylon/cast urethane or stable low-volume resin with consistent texture;
- hard-coated premium PMMA or anti-glare mineral glass;
- threaded inserts where repeated service warrants them;
- single fixed or removable stand angle; do not add a hinge just to signal quality;
- rear cable exit positioned so the connector does not become the third leg.

### 11.5 Cost model — 100 units

| Cost bucket | Range/unit |
|---|---:|
| 800×480 IPS display + backlight interface | USD 20–32 |
| MCU/radio module + PSRAM + flash | USD 8–13 |
| PCB + regulation + protection | USD 10–15 |
| Sensor + clock reference + connector/button/misc. | USD 10–12 |
| **Electronics BOM** | **USD 48–72** |
| Finished enclosure/body | USD 25–42 |
| Improved front optic/mask/gasket | USD 11–20 |
| Stand/feet/fasteners/internal mounts | USD 7–10 |
| **Mechanical BOM** | **USD 43–72** |
| PCBA setup/assembly, amortized | USD 14–21 |
| Final assembly/program/test | USD 19–27 |
| Cable + packaging | USD 6–9 |
| Yield/scrap allowance | USD 5–7 |
| **Assembly / production** | **USD 44–64** |
| **Estimated assembled COGS — no touch** | **USD 135–208** |
| **Touch system delta** | **+USD 10–20** |
| **Estimated assembled COGS — touch** | **USD 145–228** |

### 11.6 Value engineering

| Component | Class | If removed |
|---|---|---|
| 800×480 IPS | CORE for this tier | Falls back to A's typography and state-rendering quality |
| PSRAM | ENABLER | Full RGB framebuffers and robust network/UI concurrency become constrained |
| 16 MB flash | ENABLER | OTA/recovery/assets need tighter partition compromises |
| Wide-range backlight driver | CORE | Night behavior fails |
| Ambient-light sensor | EXPERIENCE ENHANCER | Automatic calm behavior becomes unreliable |
| Improved optic/gasket | EXPERIENCE ENHANCER | User-visible black, reflections, dust, and panel reveal worsen |
| Better enclosure finish/tolerances | EXPERIENCE ENHANCER | Product looks like a prototype despite strong electronics |
| Projected-capacitive touch | EXPERIENCE ENHANCER if UX is defined | Loses the touch interaction; display resolution and ambient behavior remain |
| Battery | OPTIONAL / omitted | Fixed-location experience remains intact; cost and risk fall |

### 11.7 Main compromises

- still has an LCD glow floor at night;
- cable defines placement;
- direct RGB bus consumes PCB pins and raises firmware validation complexity;
- premium optical treatment is limited by the 100-unit process.

---

## 12. Architecture C — Premium

### 12.1 Product definition

Premium is not a feature-rich Glumi. It is B with better optical/mechanical integration and a single additional product capability: temporary cordless movement and short-outage continuity. The battery must earn its place through observed use.

### 12.2 Electronics stack

```text
USB-C 5 V
  └─ protected power-path charger
       ├─ protected 1-cell battery + thermistor + fuel gauge
       ├─ efficient 3.3 V regulation
       │    └─ dual-core Wi-Fi MCU module + PSRAM
       │         ├─ secure flash / dual OTA
       │         ├─ RGB565 + timing → selected LCD
       │         ├─ [optional] PCAP controller over I²C
       │         ├─ ambient-light sensor
       │         ├─ recessed recovery / shipping-mode control
       │         └─ factory test/program pads
       └─ efficient dimmable LED driver → LCD backlight
```

### 12.3 Target specifications

- selected 4.3-inch 800×480 IPS panel with tighter viewing/uniformity/contrast criteria;
- bonded or near-bonded anti-glare cover glass only after yield validation;
- same 8 MB PSRAM / 16 MB flash class as Balanced;
- 4–6 Ah protected single-cell battery class;
- 6–14 h autonomy target at realistic mixed brightness;
- charge-and-operate power path, temperature-qualified charging, shipping mode, and battery diagnostics;
- optional integrated PCAP; no resistive touch, audio, haptics, proximity sensor, or decorative LEDs;
- fold-flat, two-position support only if its stability and durability exceed a fixed stand.

### 12.4 Industrial design and CMF

- approximate envelope: **132 × 86 × 36 mm**;
- target weight: **380–520 g**;
- extra weight should feel planted, not merely heavy;
- battery separated from hot regulator/backlight zones and antenna keep-out;
- anti-glare cover glass, precision mask, and controlled edge reveal;
- internal glass-filled polymer or thin metal structural part is acceptable if it does not shadow the antenna; exterior metal is not automatically desirable;
- robust hinge with two intentional angles and no free-sliding friction joint;
- service screws/inserts for battery replacement;
- TPU feet positioned for both stand angles and flat use;
- cable connection remains usable while standing and should not load the hinge.

### 12.5 Cost model — 100 units

| Cost bucket | Range/unit |
|---|---:|
| Selected display + optical integration interface | USD 28–40 |
| MCU/radio module + PSRAM + flash | USD 8–12 |
| PCB + regulation + protection | USD 12–17 |
| Charger/power path/fuel gauge/protection | USD 10–16 |
| Qualified battery pack + thermistor | USD 12–20 |
| Sensor + connector/button/misc. | USD 5–7 |
| **Electronics BOM** | **USD 75–112** |
| Finished enclosure/body | USD 34–55 |
| Bonded/near-bonded anti-glare optic | USD 20–34 |
| Folding stand/hinge/feet/fasteners/internal frame | USD 18–29 |
| **Mechanical BOM** | **USD 72–118** |
| PCBA setup/assembly, amortized | USD 18–26 |
| Battery-safe final assembly/program/test | USD 25–36 |
| Cable + packaging/transport protection | USD 8–12 |
| Yield/scrap allowance | USD 7–10 |
| **Assembly / production** | **USD 58–84** |
| **Estimated assembled COGS — no touch** | **USD 205–314** |
| **Touch system delta** | **+USD 8–16** |
| **Estimated assembled COGS — touch** | **USD 213–330** |

### 12.6 Value engineering

| Component | Class | If removed |
|---|---|---|
| B compute/display foundation | CORE | Premium loses the proven Glumi experience |
| Selected panel/binning criteria | EXPERIENCE ENHANCER | More unit variation in backlight, viewing, and color |
| Bonded/near-bonded glass | EXPERIENCE ENHANCER | Reflections and visible layer separation increase; service/yield improve |
| Battery | EXPERIENCE ENHANCER, currently unvalidated | Loses room-to-room movement and outage continuity; major cost/risk disappears |
| Charger/power path/protection/NTC | ENABLER | Battery becomes unsafe or disrupts operation while charging |
| Fuel gauge | ENABLER | Low-battery behavior becomes unreliable and support-heavy |
| Fold-flat stand | OPTIONAL until tested | Portability/flat storage worsen; mechanics simplify materially |
| Internal structural frame | ENABLER if hinge retained | Hinge loads migrate into cosmetic shell |
| Projected-capacitive touch | EXPERIENCE ENHANCER if UX is defined | Loses the touch interaction; premium optical stack may simplify |
| Speaker/haptic | QUESTIONABLE / omitted | Product remains calm and clearly secondary |

### 12.7 Main compromises

- materially thicker, heavier, more expensive, and harder to certify;
- battery ages even if portability is rarely used;
- charging thermal conditions can degrade the very display/battery quality being purchased;
- hinge and optical bonding create two new yield/rework risks;
- may deliver less value than buying two Essential/Balanced units for two fixed rooms.

---

## 13. Master comparison matrix

| Dimension | A — Essential | B — Balanced | C — Premium |
|---|---|---|---|
| Display technology | IPS TFT, controller-backed | IPS TFT, direct RGB565 or proven high-throughput controller | Selected IPS TFT, higher optical/uniformity criteria |
| Size | 4.3 in | 4.3 in | 4.3 in |
| Resolution | 480×272 | 800×480 | 800×480 |
| Pixel density | ~128 ppi | ~217 ppi | ~217 ppi |
| Viewing angle | Wide IPS class | Wide IPS class | Selected wide IPS class with tighter unit criteria |
| Color | 16-bit sufficient | 16-bit sufficient | 16-bit sufficient; no extra value from wider gamut |
| Night behavior | Auto dim + smoked window; highest glow risk | Auto dim + better optic/backlight control | Best IPS result via selected panel + optical integration |
| Brightness management | ALS + calibrated PWM/analog dimming | ALS + wider low-end control/hysteresis | Same, plus battery-aware power behavior |
| Touch | PCAP option; +USD 10–20 COGS | PCAP option; +USD 10–20 COGS | PCAP option; +USD 8–16 COGS |
| Controls | One recessed recovery button | Same | Same; also shipping-mode/service behavior |
| MCU class | Secure Wi-Fi MCU, controller-backed display | Dual-core Wi-Fi MCU with LCD/DMA | Same as B |
| RAM | ~400–512 KiB internal | ~512 KiB internal + 8 MB PSRAM class | Same as B |
| Storage | 8 MB flash class | 16 MB preferred | 16 MB preferred |
| Wi-Fi | 2.4 GHz | 2.4 GHz | 2.4 GHz |
| Bluetooth | Capability may exist; disabled | Same | Same |
| Sensors | Ambient light | Ambient light + clock reference | Ambient light + clock reference + battery NTC |
| Audio | None | None | None |
| Haptics | None | None | None |
| Power source | USB-C 5 V continuous | USB-C 5 V continuous | USB-C 5 V + internal battery |
| Battery | None | None | 4–6 Ah single-cell class, serviceable |
| Autonomy | 0 unplugged | 0 unplugged | 6–14 h target |
| USB-C behavior | Power + factory recovery data | Same | Power/charge + factory recovery data |
| PCB complexity | Low–Medium | Medium–High | High |
| Enclosure architecture | 3-part serviceable shell, fixed base | Tighter shell/optic, fixed or removable base | Deeper shell, battery bay/internal frame, fold-flat stand candidate |
| Visible materials | Matte polymer + hard-coated PMMA/PC | Higher-finish polymer + premium PMMA or AG glass | Higher-finish polymer + AG cover glass; restrained internal reinforcement |
| Approx. dimensions | 125×78×28 mm | 128×82×30 mm | 132×86×36 mm |
| Approx. weight | 180–260 g | 230–330 g | 380–520 g |
| Electronics BOM | USD 33–51 | USD 48–72 | USD 75–112 |
| Mechanical BOM | USD 28–48 | USD 43–72 | USD 72–118 |
| Assembly/production | USD 38–56 | USD 44–64 | USD 58–84 |
| Total COGS @100 — no touch | **USD 99–155** | **USD 135–208** | **USD 205–314** |
| Total COGS @100 — touch | **USD 109–175** | **USD 145–228** | **USD 213–330** |
| Electrical engineering | Low–Medium | Medium–High | High |
| Firmware | Medium | High | High |
| Mechanical engineering | Medium | Medium–High | High |
| Manufacturing | Medium | Medium–High | High |
| Testing | Medium | High | High |
| Compliance | Medium | Medium | High |
| Sourcing risk | Medium | Medium–High (panel/interface) | High (panel + optic + cell + hinge) |
| Perceived quality | Deliberate, simple | High | Highest if executed well; worst if gaps/hinge/bonding are poor |
| Key UX advantages | Lowest cost; quiet; automatic; robust simplicity | Crisp UI, better states and optics, same calm behavior | Best optics plus temporary portability and outage continuity |
| Key UX compromises | Coarser text/arrow, simpler finish | Wired, LCD black floor | Thick/heavy; charging and battery maintenance; portability value unproven |

---

## 14. Engineering complexity comparison

| Area | A | B | C | Why |
|---|---|---|---|---|
| Electrical | Low–Medium | Medium–High | High | A delegates framebuffer to display controller; B adds high-pin-count RGB/PSRAM; C adds power path, cell protection, gauge, and thermals |
| Firmware | Medium | High | High | All need robust state/OTA; B/C add DMA/PSRAM contention; C adds charge/SOC/shipping modes |
| Mechanical | Medium | Medium–High | High | Optical gap and fixed base → tighter glass/gasket → battery bay and hinge |
| Manufacturing | Medium | Medium–High | High | Increasing cosmetic tolerances, dust control, bonding, and battery handling |
| Assembly | Medium | Medium | High | C adds pack installation, insulation, hinge torque, and safety checks |
| Test | Medium | High | High | B needs display stress/uniformity checks; C adds charge/discharge/thermal/SOC tests |
| Compliance | Medium | Medium | High | All have Wi-Fi and health-adjacent intended use; C adds lithium transport/safety scope |
| Development risk | Medium | Medium–High | High | A risks controller supply; B risks bus/bandwidth; C compounds optics, mechanics, power, and certification |

---

## 15. Delta and marginal-value analysis

Ranges overlap because quotes and process choices are unresolved. The following uses the no-touch midpoint planning model; adding touch to every tier increases absolute COGS but changes the A→B→C deltas very little because the same interaction architecture is carried across them.

```text
Essential   ≈ USD 127
   ↓ + USD 45
Balanced    ≈ USD 172
   ↓ + USD 88
Premium     ≈ USD 260
```

### Essential → Balanced: approximately +USD 45/unit

| Increment | Approx. midpoint delta |
|---|---:|
| 800×480 panel, PSRAM/flash, display power/interface | +USD 15 |
| Improved cover optic, mask, gasket, dust control | +USD 10 |
| Better enclosure finish, tighter assembly and test | +USD 20 |

**User gains:**

- visibly smoother value and arrow;
- secondary state text that remains clear without enlarging the object;
- better reflection/black-border handling;
- more consistent unit-to-unit finish;
- more headroom for reliable exceptional states and future localization.

**User does not gain:**

- new touch capabilities if A and B use the same touch architecture, sound, portability, medical alerts, or glucose history.

**Assessment:** this is the most defensible marginal spend if users can perceive the difference at actual distance and lighting.

### Balanced → Premium: approximately +USD 88/unit

| Increment | Approx. midpoint delta |
|---|---:|
| Battery, power-path charging, protection, gauge, thermal parts | +USD 28 |
| Selected panel and bonded/near-bonded cover optic | +USD 20 |
| Deeper enclosure, internal frame, hinge/stand, service hardware | +USD 25 |
| Extra assembly, battery/thermal testing, yield allowance | +USD 15 |

**User gains:**

- several hours of temporary movement without unplugging/rebooting;
- continuity through short power cuts;
- lower reflections and a more integrated front surface;
- fold-flat storage/angle choice if the mechanism proves robust.

**User does not gain:**

- more accurate glucose, faster source data, medical-grade alerting, a history dashboard, or meaningfully better typography than Balanced.

**Assessment:** only the optical portion is obviously valuable. The battery/hinge portion needs behavioral evidence. Without that evidence, a “Premium Wired” variant—Balanced plus better optics and construction—would be the stronger product at roughly **USD 175–240 COGS**.

---

## 16. Challenge to attractive assumptions

### 16.1 Features that sound better than they are

- **Touch added only to signal Premium:** a defined touch experience can earn its place, but tier labeling alone cannot. If the interaction is core, it belongs across tiers; if it is not core, adding it only to C creates avoidable UI and support fragmentation.
- **AMOLED as premium:** true black is compelling, but 24/7 static content, panel availability, interface requirements, and replacement risk overwhelm the gain at this size and volume.
- **More brightness:** beyond indoor/daylight readability it adds heat, leakage, and power; minimum brightness is more important.
- **Metal enclosure:** can compromise the antenna, feels cold/technical, complicates grounding and cosmetic finishing, and is not inherently more domestic.
- **Audio/haptic “just in case”:** changes Glumi's responsibility and emotional tone, and creates pressure to act like a reliable medical alarm.
- **A foldable stand by default:** adds tolerance stack, rattle, wear, assembly, and return risk. It belongs only with validated portability or a proven viewing-angle need.
- **Battery as expected premium feature:** most of the time it will age while the device remains beside an outlet.
- **Dedicated RTC with coin cell:** fail-closed boot behavior can be safer and simpler than maintaining another battery-backed state.
- **Reserve hardware for every future variant:** designing today's calm display around a hypothetical future alarm product is a form of feature bloat.

### 16.2 Hidden architecture locks

- The display's exact FPC, timing, backlight string, and active area lock the PCB and front tooling.
- Direct RGB can consume nearly all convenient GPIO; controller-backed serial displays trade pins for controller availability and throughput.
- Optical bonding locks the cover geometry and rework path to a supplier/process.
- A battery determines enclosure thickness, internal fastening, shipping, service policy, firmware states, and certification.
- A hinge moves loads into the enclosure and changes packaging, cable routing, sensor angle, and drop behavior.
- Backend vs direct CGM access changes credential ownership, provisioning, privacy, outage behavior, and support—even if the PCB is unchanged.

### 16.3 Where Essential may be better

- faster and more deterministic bring-up;
- fewer field failure modes;
- easier repair and panel replacement;
- lower heat;
- fixed stance always returns to the intended viewing angle;
- no battery swelling, degradation, shipping mode, or charge anxiety;
- its lower cost may let a user place two Glumis in fixed rooms instead of carrying one.

### 16.4 Where spending more materially helps

- display resolution from 480×272 to 800×480;
- a backlight circuit designed for stable near-dark dimming;
- sensor placement and brightness calibration;
- anti-reflection/front-window execution;
- mechanical stability, seam control, and cable management;
- secure OTA/recovery and sufficient flash/RAM headroom;
- production test coverage.

---

## 17. Recommendation

### 17.1 Strongest Glumi experience

**Balanced** currently provides the strongest *proven* Glumi experience. It improves what users continuously see without changing the product's calm interaction model or adding a consumable subsystem.

Premium could become strongest only if portability proves behaviorally valuable. Until then, it is the highest-spec architecture, not the best product.

### 17.2 Best cost/experience balance

**Balanced**, subject to one condition: the 800×480 and improved optical stack must win a blind side-by-side test at realistic distances and light levels. If users cannot reliably prefer it, Essential becomes the rational production choice.

### 17.3 Decisions worth paying more for

1. low-end backlight quality and ambient sensing;
2. 800×480 resolution if the perceptual test confirms it;
3. better front optics and dust/seam control;
4. memory/flash margin for robust OTA and display/network concurrency;
5. serviceable construction and production testability.
6. projected-capacitive touch, if a usability prototype shows that the defined interaction creates meaningful value without making the glucose glance interaction dependent on touch.

### 17.4 “Premium” decisions probably unnecessary

- AMOLED;
- external metal shell;
- high peak brightness;
- wide color gamut;
- multiple sensors;
- speakers/haptics/RGB light;
- a general-purpose processor;
- a battery before portability is validated;
- a hinge before fixed-angle testing shows a problem.

### 17.5 Uncertainties research alone cannot resolve

- whether the IPS black floor is acceptable at 3 a.m.;
- whether 480×272 actually looks cheap at 1–3 m;
- whether 800×480 is worth ~USD 45 in assembled cost at 100 units;
- how much a smoked/AG/bonded front improves black without reducing daytime legibility;
- whether automatic brightness feels invisible rather than unstable;
- which tilt works from bed, desk, kitchen counter, and standing distance;
- whether users carry the object enough to justify a battery;
- whether a folding support feels solid after repeated use;
- whether the object needs ballast or the cable/base geometry is enough;
- whether front-lit e-paper's calmness outweighs refresh and stale-image behavior;
- whether the proposed touch interaction is discoverable, faster or more valuable than its non-touch alternative, and resistant to accidental activation;
- whether touch-panel transmission and reflections remain acceptable at Glumi's minimum night brightness;
- actual supplier yield, optical variation, and availability at 100 units;
- ANMAT classification and the commercial permissibility of the data source.

---

## 18. Minimum hardware validation plan

Do not build three complete Glumis. Build reusable benches, coupons, and one architecture mule in this order.

### Experiment 0 — Physical size and viewing geometry

**HYPOTHESIS**  
A 4.3-inch active area with 35–42 mm numerals is legible at 3 m, while a 5-inch display adds presence without useful information.

**PROTOTYPE**  
1:1 printed masks for 3.5, 4.0, 4.3, and 5.0 inches; cardboard/foam bodies at 10°, 15°, 20°, and 25°; UI prints for fresh, stale, unavailable, and setup states.

**TEST**  
Place them on desk, bedside, kitchen counter, and living-room surfaces. View seated, standing, and lying down at 0.7, 1.5, and 3 m. Include at least five people, including people who wear corrective lenses.

**SUCCESS CRITERIA**  
At least 90% correct reading of value/trend at 3 m and 100% correct discrimination of stale/unavailable states at 1.5 m, with no consistent preference for 5 inches.

**DECISION ENABLED**  
Freeze display size class and first enclosure-angle range.

### Experiment 1 — Display technology and resolution bench

**HYPOTHESIS**  
4.3-inch IPS is acceptable day and night; 800×480 produces a meaningful improvement over 480×272; front-lit 4.2-inch e-paper is not clearly superior overall.

**PROTOTYPE**  
Bare or evaluation assemblies representing 4.3-inch 480×272 IPS, 4.3-inch 800×480 IPS, and 4.2-inch monochrome e-paper with front light. Use the same physical UI, typography, freshness language, and approximate front-window treatment.

**TEST**  
Side-by-side evaluation in daylight, indirect sunlight, typical indoor light, dim bedroom, and full darkness. Observe head-on and ±45° vertically/horizontally. Change readings and exceptional states at realistic intervals.

**SUCCESS CRITERIA**  
- IPS has no objectionable inversion at required angles.
- Minimum night setting is readable at bedside without lighting the room.
- 800×480 is consistently preferred or produces measurably faster/error-free state reading.
- E-paper shows no unacceptable ghosting and cannot leave an apparently current number after simulated failure.

**DECISION ENABLED**  
Select display technology and A-vs-B resolution class; decide whether e-paper deserves a separate architecture track.

### Experiment 1B — Touch value and integration

**HYPOTHESIS**  
The proposed touch experience creates a meaningful user benefit without making the primary glucose glance slower, less legible, or more error-prone.

**PROTOTYPE**  
One 4.3-inch PCAP display assembly running the proposed touch interactions and one otherwise equivalent non-touch flow. Use production-like cover thickness and an interrupt-driven I²C touch path.

**TEST**  
Compare task time, completion, discoverability, delight, and errors for the specific touch workflows. Also test accidental activation while moving/cleaning the object, wet fingers, edge touches, wake/recovery behavior, dim-room optical quality, PWM noise, and operation during Wi-Fi/display load.

**SUCCESS CRITERIA**  
- The defined touch task is materially faster, clearer, or more satisfying than its non-touch alternative.
- The current glucose value never requires touch to reveal.
- Accidental actions are rare and reversible; destructive actions require explicit confirmation.
- Touch works across the whole intended area with no phantom input during backlight dimming, charging, Wi-Fi transmission, or ESD pre-compliance testing.
- Night luminance and reflection remain within the display experiment's accepted limits.

**DECISION ENABLED**  
Choose touch or no touch as a product-wide architecture; freeze cover-stack, GPIO/I²C, ESD, and production-test requirements.

### Experiment 2 — Backlight and optical-stack dark-room test

**HYPOTHESIS**  
A custom dimming path plus the correct front optic can make IPS calm enough for bedside use.

**PROTOTYPE**  
One IPS panel with externally controllable constant-current backlight driver; interchangeable clear, smoked, matte, anti-glare, and near-bonded optical coupons; black masks with two air gaps.

**TEST**  
Sweep PWM/analog brightness logarithmically in a dark room. Measure input power, front luminance/lux at user position, flicker with camera and subjective observation, black-field glow, reflections, uniformity, and readability after dark adaptation.

**SUCCESS CRITERIA**  
- Stable effective output at ≤1–2% without visible flicker or stepping.
- No participant reports that the lowest readable state disturbs sleep.
- Chosen window preserves daytime contrast and hides inactive panel borders.

**DECISION ENABLED**  
Freeze LED-driver requirements, optic transmission range, night palette, and whether IPS remains viable.

### Experiment 3 — Compute/display/network stress mule

**HYPOTHESIS**  
The Balanced MCU/PSRAM/RGB architecture can update the display while Wi-Fi, TLS, flash writes, and OTA operate without corruption or unacceptable stalls.

**PROTOTYPE**  
One development assembly matching the intended MCU memory class and 800×480 interface; instrumented firmware that cycles Wi-Fi reconnect, backend polling, flash logging, asset reads, and OTA while rendering.

**TEST**  
72-hour automated stress run followed by a 30-day soak; record resets, watchdogs, frame corruption, maximum heap, task latency, connection recovery, and thermal state. Repeat at low input voltage/cable-drop corner.

**SUCCESS CRITERIA**  
Zero persistent display corruption, zero unrecovered disconnects, zero unsafe fresh-state events, successful OTA rollback, and adequate memory margin under worst observed load.

**DECISION ENABLED**  
Freeze display bus, RAM/flash class, pixel clock strategy, and MCU class.

### Experiment 4 — Ambient-light behavior

**HYPOTHESIS**  
Automatic brightness can be calmer and more reliable than schedule-only behavior.

**PROTOTYPE**  
ALS mounted behind three candidate enclosure windows/locations with adjustable firmware curve, filtering, hysteresis, and user minimum.

**TEST**  
Run through sunrise/day/clouds/evening/lights-off/phone-flashlight scenarios in bedroom, office, and kitchen. Log sensor values and output. Observe backlight leakage into sensor.

**SUCCESS CRITERIA**  
No visible hunting, no night flash on boot, correct adaptation within 2–5 seconds for large changes, and readable output across target rooms without daily manual adjustment.

**DECISION ENABLED**  
Freeze sensor range/placement, aperture, filtering, and calibration approach.

### Experiment 5 — Portability demand before battery engineering

**HYPOTHESIS**  
Users repeatedly move one Glumi between rooms enough that temporary cordless use is more valuable than a second fixed device.

**PROTOTYPE**  
Use a Balanced display mule powered by a small external USB power bank integrated into a temporary enclosure/strap. Do **not** design an internal battery yet.

**TEST**  
Two-week home diary with at least five target users: number of moves, destinations, unplug/replug friction, missed viewing time, preference for one movable unit vs two fixed units, and charging failures.

**SUCCESS CRITERIA**  
At least 60% of participants move it on most days, report meaningful benefit, and prefer one portable device over two lower-cost fixed devices; required cordless sessions fit within 6–14 hours.

**DECISION ENABLED**  
Accept/reject Premium battery architecture before cell, charger, enclosure, and compliance work.

### Experiment 6 — Enclosure, stand, cable, and ballast mule

**HYPOTHESIS**  
A fixed stand satisfies A/B; C only benefits from a fold-flat two-position stand if portability is accepted.

**PROTOTYPE**  
Unfinished 1:1 shells with real panel mass, PCB dummy, cable, optional battery dummy, interchangeable fixed wedges and hinge concepts.

**TEST**  
Cable pull, tip stability, button/recovery access, connector insertion, flat/standing use, bed/desk angles, 500 stand cycles, rattle, one-meter packed transport, and service disassembly.

**SUCCESS CRITERIA**  
No tip or slide under normal cable loads; no hinge drift/rattle after cycles; tool-based service without cosmetic damage; acceptable sensor and antenna placement in every position.

**DECISION ENABLED**  
Freeze envelope, depth, weight, stand architecture, cable location, and fastener strategy.

### Experiment 7 — CMF and optical quality coupons

**HYPOTHESIS**  
Perceived quality comes from texture, edge control, seam consistency, and optic integration—not exterior metal.

**PROTOTYPE**  
Small coupon set: MJF/SLS nylon, finished FDM, cast urethane, candidate molded resins/textures, PMMA/PC/glass fronts, two rubber hardnesses, several warm neutral colors.

**TEST**  
Blind handling/appearance ranking in daylight and home lighting; scratch, skin-oil, cleaning-agent, UV/heat, and dust tests; compare antenna attenuation behind representative structures.

**SUCCESS CRITERIA**  
A non-metal construction is consistently perceived as intentional and durable, survives cleaning, and meets RF/mechanical needs within the tier cost.

**DECISION ENABLED**  
Freeze low-volume manufacturing process and CMF direction; identify what can wait for injection molding.

### Experiment 8 — Five-unit production-intent pilot, not three tier builds

**HYPOTHESIS**  
The selected architecture can be assembled, flashed, calibrated, tested, and serviced repeatedly with acceptable variation.

**PROTOTYPE**  
Five units of the *winning* architecture using production-intent PCB, optics, enclosure, fasteners, cable, and end-of-line procedure.

**TEST**  
Build-time study, first-pass yield, display/ALS calibration spread, 30-day soak, power cycling, Wi-Fi recovery, OTA/rollback, cosmetic inspection, and service teardown/reassembly.

**SUCCESS CRITERIA**  
≥90% first-pass build yield before rework, no critical reliability failures, repeatable calibration, documented assembly under target labor time, and no stale-as-fresh event.

**DECISION ENABLED**  
Move from architecture exploration to EVT/DFM and specific component sourcing.

---

## 19. Decision gates before sourcing exact parts

1. Reconcile **backend vs direct on-device data integration**.
2. Complete Experiments 0–2 and freeze display technology, size, resolution, optical target, and touch/no-touch architecture.
3. Decide whether 800×480's user value justifies the B cost and complexity.
4. If touch is selected, carry it across A/B/C and freeze cover construction, active area, touch controller interface, GPIO budget, and ESD/test requirements before panel sourcing.
5. Complete the external-power-bank portability study before any internal battery design.
6. Obtain an ANMAT classification opinion and confirm ENACOM path before treating certification as a normal line item.
7. Define target COGS and acceptable one-time tooling/NRE at 100, 500, and 1,000 units.
8. Only then request panel lifecycle, MOQ, 100/1k pricing, FPC/backlight drawings, sample variation, and second-source evidence.

---

## 20. Source notes

Primary/authoritative technical references used for architecture claims:

- [Espressif RGB LCD programming guide](https://docs.espressif.com/projects/esp-idf/en/latest/esp32s3/api-reference/peripherals/lcd/rgb_lcd.html)
- [Espressif LCD troubleshooting and PSRAM/Wi-Fi guidance](https://docs.espressif.com/projects/esp-faq/en/latest/software-framework/peripherals/lcd.html)
- [Espressif Wi-Fi/BLE coexistence](https://docs.espressif.com/projects/esp-idf/en/v5.0.6/esp32s3/api-guides/coexist.html)
- [E Ink technology benefits and bistability](https://www.eink.com/tech/detail/Benefits)
- [Representative manufacturer evidence for a 4.2-inch front-lit monochrome e-paper class](https://www.winstar.com.tw/products/e-paper-display/4_2-inch-e-paper-module.html)
- [Samsung OLED static-image/burn-in guidance](https://www.samsung.com/us/support/troubleshoot/TSG10003240/)
- [Sony OLED image-retention guidance](https://www.sony.com/electronics/support/televisions-projectors-lcd-tvs-android-/kd-55x85j/articles/00173479)
- [USB-IF Type-C specification](https://www.usb.org/sites/default/files/USB%20Type-C%20Spec%20R2.0%20-%20August%202019.pdf)
- [Microchip lithium charging application guidance](https://www.microchip.com/content/dam/mchp/documents/OTH/ApplicationNotes/ApplicationNotes/00947a.pdf)
- [UL LATAM battery safety standards overview](https://latam.ul.com/es/industrias/productos-y-componentes)
- [ENACOM 2026 homologation update](https://www.enacom.gob.ar/institucional/enacom-agiliza-los-permisos-para-comercializar-equipos-de-telecomunicaciones-en-el-pais_n4823)
- [ANMAT 2026 SaMD criteria update](https://www.argentina.gob.ar/noticias/anmat-actualiza-los-criterios-regulatorios-para-software-como-dispositivo-medico-samd)
- [ANMAT new-technology classification consultation](https://www.argentina.gob.ar/servicio/consulta-de-clasificacion-de-nuevas-tecnologias-para-la-salud)
- [Projected-capacitive touch interface and interrupt architecture](https://docs.waveshare.com/ESP32-Peripheral-Tutorials/Display/Touch)
- [Microchip touch-system design guidance](https://www.microchip.com/en-us/application-notes/an2934)

Cost references are public market anchors only and must be replaced with formal quotes during sourcing:

- [Representative 4.3-inch 800×480 IPS 100-unit price](https://www.buydisplay.com/4-3-800x480-ips-tft-lcd-module-all-viewing-optl-touchscreen-display)
- [Representative industrial-channel 4.3-inch panel price](https://www.digikey.com/en/products/detail/orient-display/AFK480800A0-4-3INTM/15775100)
- [Representative 4.3-inch 480×272 100-unit price](https://www.crystalfontz.com/product/cfaf480272h0043sn-480x272-4-point-3-inch-tft-display)
- [Representative 4.2-inch monochrome e-paper 100-unit price](https://www.buydisplay.com/black-4-2-inch-e-paper-display-module-for-arduino-raspberry-pi)
- [Representative 4.3-inch PCAP sensor/controller 100-unit price](https://www.ronboe.com/shop/rxc-pg043099a-01/)
- [Paired industrial-channel 4.3-inch touch/non-touch display pricing](https://www.digikey.com/en/product-highlight/n/newhaven-display/4-3-inch-ips-tft-displays)
- [Low-volume PCBA cost structure](https://lowvolumepcbassembly.com/)
- [Low-volume injection-molding cost dynamics](https://formlabs.com/blog/injection-molding-cost/)
- [Cast urethane as a very-low-volume bridge process](https://www.xometry.com/capabilities/urethane-casting/)

Internal references:

- [Product Brief](../../layer-1-product/latest/product-brief.md)
- [Design Principles](../../layer-2-design/latest/Principios%20de%20dise%C3%B1o.md)
- [Hardware Component Selection Research](hardware-component-selection-research.md)
- [4.3-inch Electronics Architecture Research](glumi-electronics-architecture-research.md)
- [4.3-inch Reference Display Specification](waveshare-4.3inch-capacitive-touch-lcd-spec.md)
- [Prototype-to-Production Plan](../../layer-1-product/latest/glumi-plan-prototipo-a-produccion.md)
