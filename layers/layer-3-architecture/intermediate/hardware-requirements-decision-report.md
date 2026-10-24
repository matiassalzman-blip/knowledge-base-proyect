---
title: "Glumi Hardware Requirements Decision Report"
status: "in_progress"
last_updated: "2026-09-25"
relates_to:
  - ../../layer-1-product/latest/product-brief.md
  - ../../layer-2-design/latest/Principios de diseño.md
  - hardware-architecture-exploration.md
---

# Glumi Hardware Requirements Decision Report

## Purpose and decision status

This report translates the September 2026 hardware interview into a production-oriented architecture for an early-adopter batch of approximately 100 units in Argentina. It selects component classes and product behavior, identifies the few remaining decisions, and separates the first-batch architecture from its higher-volume cost-down path.

This report supersedes the recommendations—but not the research—in [hardware-architecture-exploration.md](hardware-architecture-exploration.md). That earlier exploration assumed a 4.3-inch display, automatic brightness, no audio, and optional touch. The interview changed all four premises.

The current [Product Brief](../../layer-1-product/latest/product-brief.md) also conflicts with these decisions: it excludes touch-led interaction, sound, multiple profiles, and commercial constraints. Those product decisions should be reconciled before this architecture is promoted to `latest` or converted into ADRs.

## Executive recommendation

Build the first commercial Glumi around:

- a **5.0-inch, 800×480, wide-view IPS LCD with integrated projected-capacitive touch**;
- an **ESP32-S3-class certified Wi-Fi module with 8 MB PSRAM and 16 MB flash**;
- continuous **5 V USB-C power**, without USB Power Delivery and without an internal battery;
- an approximately square, warm-neutral, matte 3D-printed enclosure with a **single-position fold-out kickstand**;
- a small dynamic speaker and class-D amplifier for configurable supplementary notifications;
- 2.4 GHz Wi-Fi, SoftAP provisioning, a short-lived QR-authorized local management portal, and no BLE product requirement;
- direct LibreLinkUp integration for the first batch, subject to a final device-versus-backend decision;
- no ambient-light, proximity, orientation, haptic, microphone, or decorative-LED hardware.

This is a connected appliance, not a small general-purpose computer. Its default screen shows one person's current glucose as a very large number against a state-colored background. It supports up to six profiles, monitors all of them in the background, and switches to the affected person when an enabled notification fires. It does not show glucose history or graphs.

### Commercial conclusion

The planned **USD 100 retail price is a scale target, not a realistic first-100-unit anchor**. A lean first batch is provisionally estimated at **USD 97–163 assembled COGS before Argentina import costs, taxes, fulfillment, support, and compliance**. Selling at USD 129–149 is possible only near the very low end of that range, with limited margin or deliberate subsidy as a learning batch. A healthier first-batch price may need to be higher.

At roughly 1,000 units, design-for-manufacture and molded parts could plausibly reduce recurring COGS toward **USD 55–85**, excluding tooling amortization and Argentina landed-cost effects. Supplier quotations are required before treating either range as a budget.

### Battery conclusion

Do **not** put an internal battery in the first product. Keeping a 5-inch LCD, Wi-Fi, processor, and audio system active for 8–12 hours likely requires approximately **25–45 Wh** after conversion loss and reserve—roughly a 7–12 Ah single-cell-equivalent pack. The likely finished-product delta is **USD 35–65 per unit**, plus enclosure volume, weight, thermal work, battery aging, transport constraints, and added testing.

Because Glumi already accepts ordinary 5 V USB-C power, a standard external USB power bank provides occasional room-to-room portability without imposing those costs on every unit. An internal battery should only return after measured power consumption and observed portability demand justify it.

---

## Decision summary by hardware category

| Category | First-batch decision | Avoid / defer |
|---|---|---|
| **01 — Compute** | ESP32-S3-class certified module; dual core; 8 MB PSRAM; 16 MB flash; secure boot, flash encryption, signed dual-image OTA | Linux MPU/SBC; larger storage; compute selected for speculative graphs or apps |
| **02 — Display** | 5.0-inch primary target; 800×480 IPS; full color; wide view; 350–450 nit class; very-low-end manual dimming; light anti-glare; integrated PCAP touch | OLED; TN; 7-inch default; square custom LCD; ambient-light sensor |
| **03 — Power** | 5 V USB-C, approximately 2 A design input; ordinary charger/power-bank compatible; detachable ~1.8 m cable | USB-PD dependency; proprietary adapter; internal battery/charger in V1 |
| **04 — Connectivity** | 2.4 GHz Wi-Fi; combined-SSID/mesh compatibility; SoftAP + QR local setup; USB data for service | Captive-portal networks; BLE requirement; 5 GHz requirement; user-facing USB workflow |
| **05 — Interaction** | PCAP touch; one momentary button; screen sleep/wake; touchscreen/button snooze; phone portal for administrative work | Physical keyboard; multiple visible buttons; deep on-device configuration; rotation |
| **06 — Sensors** | None | Ambient light, proximity, orientation, environmental sensors |
| **07 — Feedback** | Small speaker, built-in tones, Quiet/Loud/Ramp/Mute, per-alert mute override; display wakes for enabled alerts | Haptics, microphone, voice control, decorative/status LEDs, uploaded audio in V1 |
| **08 — Electronics** | Custom four-layer carrier PCB around certified module; direct RGB display; I²C touch; USB data; protected power input; internal PCB antenna | Development board inside production unit; unprotected USB input; custom RF section |
| **09 — Mechanical** | Approximately square printed enclosure; warm neutral matte CMF; low-reflection front; fold-out one-angle kickstand; non-slip feet; sealed appearance | Wall mount; waterproofing; continuous hinge; ballast; visible screws; metal enclosure |

---

## 01 — Compute

### Selected class

Use an ESP32-S3 module equivalent to **ESP32-S3-WROOM-1-N16R8**:

- 2.4 GHz Wi-Fi in a pre-engineered RF module;
- 16 MB flash;
- 8 MB PSRAM;
- native USB for factory programming, diagnostics, and recovery;
- RGB LCD, I²C touch, I²S audio, PWM backlight, and one physical button;
- enough performance for a simple responsive UI, up to six profiles, TLS, LibreLinkUp polling, audio, and OTA.

As a price anchor, DigiKey lists the N16R8 module at about **USD 5.11 each at 100 units**. The listing also confirms 16 MB flash, 8 MB memory-class capacity, native USB interfaces, and 2.4 GHz Wi-Fi ([DigiKey ESP32-S3-WROOM-1-N16R8](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-S3-WROOM-1-N16R8/16162642)).

### RAM and display buffering

An 800×480 RGB565 framebuffer occupies about 750 KiB. Eight megabytes of PSRAM provides room for two framebuffers or a framebuffer plus bounce buffers, decoded assets, network traffic, and the UI heap. Display/Wi-Fi contention must still be stress-tested; memory quantity does not remove bus-bandwidth constraints.

### Flash allocation

Use the 16 MB class for:

- signed A/B application partitions with rollback;
- fonts for Spanish, symbols, and units;
- a small set of built-in tones;
- encrypted configuration and multiple LibreLinkUp profiles;
- bounded logs and factory diagnostics;
- recovery and filesystem headroom.

### Why not Linux

Glumi does not need graphs, video, browser rendering, third-party applications, or complex local media. A Linux SoC would add boot/storage/update risk, power, PCB complexity, and cost without user value. The accepted 20–30 second recovery time does not require using the whole allowance; the MCU product should normally become useful faster.

### Required behavior

- Automatically boot, reconnect, and restore the prior screen after power loss.
- Fail closed: do not show a glucose value until time and fresh data are credible.
- Use watchdogs and brownout detection.
- Install updates only after explicit action in Settings.
- Support signed OTA, rollback, and USB recovery.
- Preserve configuration across normal firmware updates.

---

## 02 — Display

### Technology, size, and geometry

Select a **5.0-inch IPS TFT** as the baseline. Test 5.5-inch panels only if their interface, price, availability, and enclosure consequences remain close to the 5-inch option. Keep ~7 inches as a physical comparison prototype, not the default.

A genuinely square 5–5.5-inch LCD would unnecessarily constrain sourcing. Use a common rectangular active panel inside an **approximately square product silhouette**. A standard 5-inch module has an active area near 108 × 65 mm; the enclosure can become calmer and more square through the bezel, speaker volume, and internal layout without paying for a custom display.

Representative production panels combine 5-inch 800×480 color TFT, I²C PCAP, roughly 400-nit brightness, and a 6H cover surface in a 123 × 80 mm module envelope ([DISEA DS-T050QWH-01CA](https://www.diseaelec.com/standard-tft-lcd/ds-t050qwh-01ca)). Indicative 100-unit listings place standard 5-inch IPS+PCAP modules around **USD 24–27 before freight and qualification**, although a real supplier quote and samples are mandatory ([representative 100-unit listing](https://starteklcd.en.made-in-china.com/product/WFqxKzBGbmhg/China-5-0-Inch-800-480-Full-Viewing-Angle-and-Wide-Operating-Temperature-LCD-IPS-with-Capacitive-Touch-Panel.html)).

### Resolution

Use **800×480**. At 5 inches this is about 187 ppi: substantially above visibly crude appliance displays, while remaining economical and straightforward for the selected MCU. Phone-level density is unnecessary because the main value is read from 1–5 meters.

Do not pay for higher resolution unless 1:1 prototypes show unacceptable typography at arm's length. More pixels would increase interface and memory pressure without improving the across-room glucose value.

### Color and composition

- Full-color display.
- Background color communicates glucose state for recognition at up to 5 meters.
- Only the glucose value is required to be reliably legible at that maximum distance.
- Secondary content is optimized for closer viewing.
- No history graphs.
- Spanish only in V1, supporting both mg/dL and mmol/L.

### Brightness and night behavior

- Indoor use only; rare placement near a very bright window.
- Target a 350–450 nit panel class rather than paying for sunlight readability.
- Manual brightness only; no automatic sensor.
- The driver must dim smoothly to a genuinely low bedroom level without obvious flicker or large steps.
- A faint LCD glow is acceptable, but minimum brightness must not noticeably illuminate the room.
- Brightness at zero means display sleep, not processor shutdown.

The critical display specification is therefore not maximum nits. It is the measured luminance floor, PWM behavior, black uniformity, viewing cone, and the quality of the anti-glare stack.

### Viewing angle and surface

- Wide-view IPS behavior from the side, above, and below.
- Lightly anti-glare / low-reflection front surface.
- Preserve contrast and type sharpness; avoid both mirror gloss and coarse, hazy matte treatment.
- Prefer integrated PCAP cover glass with the required finish so a second front window does not add cost, reflections, and dust interfaces.
- Optical bonding is not required for the first batch unless samples demonstrate a decisive benefit at acceptable yield and MOQ.

### Touch

Touch is a core input, not a tier upgrade. Use projected-capacitive touch over I²C. Multi-touch may come with the controller, but the interface should not depend on multi-finger gestures.

At 100 units, the expected net finished-unit difference between otherwise comparable touch and non-touch builds remains approximately **USD 8–20** after the sensor/controller, cover integration, ESD/connector parts, added assembly/test, and any credit for eliminating a separate front window. Glumi should accept that cost: touch now carries onboarding-adjacent recovery, immediate settings, profile switching, and notification acknowledgement. Maintaining a second non-touch product would save too little while fragmenting the experience and production flow.

Touch supports immediate controls on Glumi: brightness, volume mode, mute, snooze, profile switching, screen sleep, and basic notification settings. Credentials and complex management remain in the phone portal.

---

## 03 — Power

### Wired baseline

- USB-C receptacle accepting ordinary **5 V** chargers and power banks.
- No USB Power Delivery negotiation requirement.
- Design for roughly **5 V / 2 A** input headroom; confirm the final rating after display and speaker measurement.
- Provide correct USB-C sink resistors, ESD protection, input current limiting/fusing, reverse/backfeed protection, brownout handling, and efficient 3.3 V regulation.
- Use a dedicated dimmable constant-current LED backlight driver rather than relying on a panel board's crude on/off control.
- Include a detachable ~1.8 m USB-C cable.

### Wall adapter decision

For the production product, compare:

| Option | Advantages | Costs / risks |
|---|---|---|
| Certified regional adapter included | Complete out-of-box experience; known power quality; fewer support variables | Approximately +USD 5–12 before local landed cost; additional SKU, packaging, certification, and logistics work |
| Cable only | Lower cash requirement and simpler inventory; users can reuse chargers | Less controlled power quality; setup friction; packaging and instructions must clearly state the required 5 V rating |

Argentina's current electrical-safety framework explicitly covers external power supplies and chargers, and its 2025 update permits some recognition of qualifying foreign certificates. The actual compliance route and labeling should be confirmed with a local specialist before procurement ([Argentina electrical-safety requirements](https://www.argentina.gob.ar/node/56435), [Resolution 16/2025](https://www.argentina.gob.ar/normativa/nacional/norma-410052)).

### Battery comparison

The battery decision is not close enough to remain ambiguous:

| Cost / consequence | Plugged-in baseline | 8–12 h internal battery |
|---|---:|---:|
| Energy storage | None | ~25–45 Wh planning class, pending measurement |
| Battery pack | — | +USD 18–32 |
| Charger, power path, gauge, protection, temperature sensing | — | +USD 7–13 |
| Larger enclosure/internal structure | — | +USD 5–10 |
| Added assembly, test, yield, shipping handling | — | +USD 5–10 |
| **Estimated COGS delta** | **USD 0** | **+USD 35–65/unit** |
| Product impact | Fixed but simple | Thicker, heavier, aging consumable, thermal and transport burden |

This delta is significant relative to a USD 129–149 product. Do not include internal charging or a battery in V1. A power bank can be used externally when occasional portability matters.

---

## 04 — Connectivity

### Wi-Fi

- 2.4 GHz 802.11 b/g/n is sufficient.
- Glumi must join modern dual-band and mesh networks that advertise a shared SSID without asking the user to split bands or understand Wi-Fi frequencies.
- 5 GHz is beneficial but not a requirement.
- Captive-portal guest/hotel networks are out of scope.
- Use the certified module's internal PCB antenna at the board edge with the required copper, display metal, cable, and enclosure keep-out.

Wireless consumer IoT devices are among the equipment types subject to Argentina's ENACOM commercialization/homologation process. The procedure changed in 2026, so the exact route should be confirmed against the product configuration rather than assumed from older projects ([ENACOM 2026 overview](https://www.enacom.gob.ar/institucional/enacom-agiliza-los-permisos-para-comercializar-equipos-de-telecomunicaciones-en-el-pais_n4823), [Resolution 57/2026](https://www.argentina.gob.ar/normativa/nacional/resoluci%C3%B3n-57-2026-423392/texto)). A pre-engineered radio module reduces RF design risk but does not automatically settle finished-product obligations.

### Provisioning

Primary flow:

1. Unconfigured Glumi creates a temporary SoftAP.
2. The screen displays its network and a QR entry point.
3. The phone joins the temporary network and opens a locally served setup page.
4. The user selects home Wi-Fi, enters credentials, and configures LibreLinkUp.
5. Glumi joins home Wi-Fi, validates fresh data, and displays the normal screen.

BLE is not a product requirement, even if the selected module physically contains it.

### Management portal

- The user chooses **Manage with phone** on the physical Glumi.
- Glumi displays a QR code containing an unguessable short-lived authorization token.
- The phone and Glumi are on the same home network.
- The portal closes after roughly 10–15 minutes of inactivity, while active multi-step operations may finish.
- No extra PIN and no Glumi user account are required.
- The portal handles Wi-Fi/CGM accounts, profiles, thresholds, OTA, diagnostics, and factory reset.

### USB data

Expose native USB data on the same USB-C connector for factory programming, logs, diagnostics, and firmware recovery. It is an undocumented service/development path. Ordinary users should never require a computer.

### Direct integration versus Glumi backend

| Dimension | Direct from device | Glumi backend |
|---|---|---|
| Setup | No Glumi account; local credentials | Usually requires account/device enrollment or another token flow |
| Privacy | Credentials and data remain on device/provider path | Glumi processes credentials or tokens and glucose data centrally |
| Reliability | Each device depends directly on LibreLinkUp and home internet | Adds Glumi service dependency but centralizes provider handling |
| Provider changes | Requires OTA to every device | Adapter can be fixed centrally |
| Support/diagnostics | Harder to inspect remotely | Better centralized observability |
| Operating cost | Near-zero backend cost | Ongoing hosting, security, monitoring, incident response, and support |
| Multi-profile polling | Performed by each device | Centralized and normalized |

**V1 recommendation:** direct on-device LibreLinkUp integration, because the first batch supports only one provider, has no Glumi account, and prioritizes low launch cash and recurring cost. Preserve the ability to switch to a backend later through a versioned internal data-provider interface. Do not let the display or profile model depend directly on LibreLinkUp response formats.

---

## 05 — Interaction

### Touchscreen

Glumi's touchscreen handles things users may want to change immediately while looking at it:

- brightness;
- Quiet / Loud / Ramp / Mute;
- snooze and notification acknowledgement;
- manual profile switching;
- screen sleep;
- basic notification enable/disable controls;
- opening **Manage with phone**.

### Phone portal

The phone handles tasks users configure, manage, or troubleshoot:

- Wi-Fi and LibreLinkUp credentials;
- adding/removing profiles;
- detailed thresholds and per-alert settings;
- no-data delay;
- firmware update;
- diagnostics and factory reset.

### One physical button

Use one clearly discoverable momentary button:

- normal short press: screen sleep/wake without rebooting;
- active-notification short press: acknowledge/snooze;
- long press: restart/power menu or protected recovery action;
- factory reset: initiated in the portal or Settings and confirmed through a long physical hold/on-screen countdown.

The button is not a mains power switch. Screen-off mode keeps compute, Wi-Fi, profile monitoring, and enabled notifications active. When an enabled notification fires, Glumi wakes the display and shows the affected state.

---

## 06 — Sensors

No user-valued behavior currently justifies a sensor.

- **Ambient light:** omit; brightness remains manual.
- **Proximity:** omit; the display remains visible until manually slept.
- **Orientation:** omit; Glumi has one fixed interface orientation.
- **Accelerometer/IMU:** omit; no gesture or rotation requirement.
- **Environmental sensors:** omit; they do not support the product's job.

This removes components, calibration, apertures, firmware states, and false-behavior support cases.

---

## 07 — Feedback

### Audio hardware

Use a small dynamic speaker, likely 8 ohm / approximately 1 W class, driven by an efficient class-D amplifier from an MCU digital-audio interface. This is preferable to a simple active buzzer because Glumi needs repeatable Quiet/Loud/Ramp behavior, pleasant built-in tones, preview playback, and smoother level control.

The acoustic design target is audible across the same quiet bedroom or living room—not throughout a house or through closed doors. Validate the actual enclosure, port direction, distortion, and ramp curve rather than selecting from speaker wattage alone.

### Notification behavior

- Supported V1 glucose notifications: urgent low and urgent high, configurable per profile.
- Glumi monitors all configured profiles even when they are not displayed.
- An enabled event wakes the screen and switches to the affected person.
- Modes: Quiet, Loud, Ramp, and Mute.
- Ramp begins quietly and rises to Loud.
- Per-alert **Mute Override** may force Loud playback even in Mute.
- Preview each tone in Quiet, Loud, or Ramp; previews can be acknowledged like real events.
- Touchscreen or physical button can acknowledge/snooze.
- Sound loops until the configured duration expires or it is snoozed.
- After snooze, an unresolved condition may notify again according to its configured policy.
- No-data notification is included, configurable by elapsed time, and disabled by default.
- Only built-in tones; no custom uploads in V1.

### Product boundary

Treat these as **supplementary audible notifications**, not a replacement for the official CGM's alerts. Glumi calculates states from mirrored values and user thresholds, but depends on mains power, Wi-Fi, the internet, LibreLinkUp, and its own software. Marketing and onboarding must not promise alarm continuity during those failures. Competitor behavior is a commercial precedent, not a regulatory determination.

### Omitted feedback hardware

- no haptic motor;
- no microphone or voice control;
- no decorative or status LEDs—the display already communicates state;
- no separate beeper if the speaker can produce all required system tones.

---

## 08 — Electronics

### PCB architecture

Use a custom **four-layer carrier PCB** around a certified ESP32-S3 module. Four layers are justified by the wide RGB bus, USB, backlight switching, audio, signal return paths, and RF/EMI control. The board should be production-intent, not a development board installed in a printed shell.

Core blocks:

```text
USB-C 5 V power + USB 2.0 data
  ├─ ESD / input protection / current limiting
  ├─ 3.3 V regulation
  ├─ dimmable constant-current backlight driver
  └─ ESP32-S3 module, 16 MB flash + 8 MB PSRAM
       ├─ RGB565/timing → 5-inch IPS LCD
       ├─ I²C + interrupt/reset → PCAP controller
       ├─ I²S/PWM → class-D amplifier → speaker
       ├─ GPIO → physical button
       ├─ native USB → factory/service recovery
       └─ pogo/test pads → programming and production test
```

### Display interface

Prefer a standard parallel RGB interface with a supplier-defined FPC and documented timing. Select the panel before freezing PCB outline and connector pinout. Run Wi-Fi, HTTPS, OTA, touch, audio, and full-frame color transitions simultaneously during validation to expose PSRAM/bus contention.

### Protection and recovery

- USB VBUS and D+/D− ESD protection.
- Input overcurrent/short protection and reverse/backfeed prevention.
- Brownout-safe state handling.
- Touch/display connector ESD strategy.
- Speaker pop/click control and amplifier shutdown state.
- Signed firmware, secure boot, flash encryption, protected credential storage, A/B OTA rollback.
- Unique device identity, serial/QR label, and bounded diagnostic log.
- Factory test mode covering pixels/backlight, touch grid, speaker sweep, button, Wi-Fi RSSI, USB, and LibreLinkUp-independent network checks.

### Antenna

Use the module's internal PCB antenna at the product perimeter. Keep copper, display metal, speaker magnet, cable, fasteners, and conductive coatings out of the antenna volume. Validate inside the finished printed enclosure and in weak-signal mesh-network conditions.

---

## 09 — Mechanical and CMF

### Enclosure

- Approximately square or softly rectangular exterior; exact proportions follow the selected 5-inch display.
- Quiet home-object character, warm neutral colors, soft matte texture, minimal visible technology.
- Polished production-grade 3D printing with honest, controlled manufacturing character.
- MJF/SLS PA12 or another repeatable low-volume process should be compared with carefully finished FDM; choose on quote, finish, dimensional control, and rework yield.
- Subtle embossed/debossed Glumi mark rather than prominent front printing.
- No waterproof/splash rating; dry indoor use. Normal dust and careful cleaning remain basic quality requirements.
- No wall mount.

### Front integration

Optimize for assembly quality and cost rather than forcing edge-to-edge glass. Prefer the PCAP cover lens as the exposed front when it meets anti-glare, hardness, border-mask, and cosmetic requirements. A slightly recessed screen is acceptable if it produces more consistent first-batch assembly.

### Stand

Primary concept: a **single fold-out kickstand**.

- Closed: Glumi can lie flat for storage/transport.
- Open: one stable, tested viewing angle.
- No continuously adjustable friction hinge.
- Prototype a simple pin hinge or removable lever; do not rely on an untested printed living hinge for the production promise.
- If hinge cost, play, or durability is unacceptable, fall back to one fixed-angle stand.

### Structure, assembly, and service policy

- The device may be light enough that users steady it while changing settings; do not add ballast solely for touch.
- Use non-slip elastomer feet and route the cable so it does not tip or rotate the unit.
- Hide seams and closure features; no visible user-service screws.
- The production enclosure may be permanently closed after full functional test.
- One-year replace-the-unit warranty is acceptable; customer repair is not a requirement.
- During engineering and pilot builds, use reversible assembly or sacrificial closures until yield is understood. A permanently closed early unit should not prevent root-cause analysis of failures.

---

## First-batch cost model

Planning basis: 100 units, direct-to-consumer, printed enclosure, direct-source display, custom production PCB, manual/low-volume assembly. Values are USD per finished unit before Argentina freight, duties, taxes, fulfillment, support labor, compliance fees, and R&D.

| Cost bucket | Planning range/unit |
|---|---:|
| 5-inch IPS + integrated PCAP + suitable front finish | USD 24–38 |
| ESP32-S3 module, PCB electronics, power, audio, protection, connectors | USD 22–34 |
| Bare PCB + PCBA setup/assembly amortization | USD 10–18 |
| Printed enclosure, fold-out stand, feet, finish | USD 20–35 |
| Final assembly, programming, cosmetic and functional test | USD 10–18 |
| 1.8 m USB-C cable + labels/basic packaging | USD 6–11 |
| Yield/scrap/warranty provision | USD 5–9 |
| **Estimated assembled COGS, cable only** | **USD 97–163** |
| Optional certified wall adapter | **+USD 5–12** |
| Internal 8–12 h battery system | **+USD 35–65** |

The top of the range is commercially incompatible with USD 129–149 retail. The first sourcing target should therefore be **≤USD 90 assembled before Argentina landing**, with a stretch target near USD 75. If supplier quotes cannot reach that, choose explicitly among a higher retail price, lower first-batch margin, a smaller/cheaper display, a fixed stand, or a more integrated electronics assembly. Do not remove touch, low-end dimming, fresh-data safeguards, or recovery paths to save the product into incoherence.

### Likely scale-down path

At higher volume:

1. Keep the same ESP32-S3 software architecture but integrate/optimize the carrier PCB.
2. Negotiate panel/PCAP and custom cover-lens pricing with a committed annual forecast.
3. Replace the printed multi-part shell with a DFM-optimized molded enclosure.
4. Reduce manual assembly through locating features, fewer connectors, and automated factory test.
5. Keep one display, one PCB, and one commercial hardware variant.

Avoid a cheap non-touch SKU. Splitting 100 units across touch/no-touch versions weakens purchasing, QA, firmware clarity, and the experience Glumi is now designed around.

---

## Required validation before parts are frozen

1. **Size/readability mockups:** compare 5.0, 5.5, and ~7-inch physical masks at 1, 3, and 5 meters; only the glucose number must pass at 5 meters.
2. **Display samples:** obtain at least two 5-inch IPS+PCAP candidates and one 5.5-inch challenger. Test viewing angle, anti-glare, fingerprints, text sharpness, color background recognition, minimum brightness, flicker, and dark-room glow.
3. **Power measurement:** measure the complete 5-inch system at minimum, typical, and maximum brightness with Wi-Fi polling and audio. Use that evidence to close the battery model.
4. **Compute stress test:** simultaneous RGB refresh, Wi-Fi/TLS, Libre polling, touch, audio, portal traffic, and OTA on the selected ESP32-S3 memory configuration.
5. **Network compatibility:** iPhone and Android setup across common Argentine routers/mesh systems using a shared 2.4/5 GHz SSID; recovery after wrong passwords, router restart, and provider outage.
6. **Audio mule:** evaluate speaker type, enclosure port, Quiet/Loud/Ramp curves, distortion, and same-room audibility. Verify Mute Override and screen-wake behavior.
7. **Stand/enclosure mule:** cycle the fold-out mechanism, test cable pull, feet, touch interaction, flat mode, print tolerances, seams, and speaker acoustics. Keep a fixed stand ready as the fallback.
8. **Five-unit production-intent pilot:** use the real PCB, display, enclosure process, cable, programming fixture, final test, and packaging before ordering 100.
9. **Argentina compliance review:** obtain written guidance/quotes for ENACOM, electrical safety, labeling, and included-versus-not-included power adapter before commercial sale.
10. **Regulatory/claims review:** review the glucose-threshold sound behavior and all companion-display claims with specialized counsel. Competitor disclaimers do not determine Glumi's classification.

## Decisions still open

- Exact 5.0-inch panel/PCAP supplier and second source.
- Whether a 5.5-inch candidate provides enough added readability to justify its interface/cost.
- Direct LibreLinkUp integration versus a Glumi backend; V1 recommendation is direct.
- Included certified regional adapter versus cable-only packaging.
- Fold-out kickstand versus fixed-angle fallback after prototype testing.
- Final acoustic target and speaker/port geometry.
- First-batch selling price after landed COGS and compliance quotations.

Everything else in the nine hardware categories is sufficiently defined to begin component sampling and a production-intent engineering prototype.
