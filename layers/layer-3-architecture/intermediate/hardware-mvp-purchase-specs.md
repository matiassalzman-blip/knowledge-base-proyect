---
title: "Hardware — Especificaciones de Componentes Comprados (MVP #1)"
layer: architecture
owner: "Matias Salzman"
status: draft
last_updated: 2026-09-16
relates_to:
  - layers/layer-3-architecture/intermediate/hardware-component-selection-research.md
  - layers/layer-3-architecture/intermediate/cgm-data-pipeline-integration-plan.md
---

# Hardware — Especificaciones de Componentes Comprados (MVP #1)

Registro de los 5 componentes ya comprados en MercadoLibre para el armado en protoboard de la unidad #1. Es un artefacto intermedio: documenta lo que se compró, no evalúa alternativas (eso ya lo cubre [Hardware — Desglose de Componentes](hardware-component-selection-research.md)).

## ⚠️ Limitación de origen de los datos

MercadoLibre bloquea el acceso automatizado a sus páginas de producto (HTTP 403 en los 5 links, tanto con la URL completa como con la URL corta `/p/{id}`; tampoco aparecen indexadas por buscador con el detalle de la ficha técnica — solo listados genéricos de la categoría). Como resultado:

- **ESP32-S3 N16R8, display ILI9341 y protoboard 400 puntos**: confirmadas con la descripción que pegaste manualmente del listado. Se marcan como "confirmado (fuente: descripción del listado, pegada por el usuario)".
- **Los 2 packs de cables jumper** (macho-macho y macho-hembra 30cm): todavía no tengo la descripción del listado. Lo que aparece en esas secciones son **especificaciones típicas de esa categoría de producto** (son commodities muy estandarizados), marcadas explícitamente como no verificadas. Si pegás también esas dos descripciones, cierro la ficha completa.

---

## 1 · Placa de desarrollo ESP32-S3 N16R8 — Wifi/Bluetooth, 2x USB-C

**Link:** https://www.mercadolibre.com.ar/placa-de-desarrollo-esp32-s3-n16r8-wifi-bluetooth-2-usb-c/p/MLA2069410538

**Fuente de los datos:** confirmado (descripción del listado, pegada por el usuario).

| Campo | Valor |
| --- | --- |
| Marca | Genérica |
| Modelo | ESP32-S3 (módulo ESP32-S3-WROOM-1) |
| Microcontrolador | ESP32-S3, dual-core Xtensa LX7 |
| Frecuencia de reloj | 240 MHz |
| Conectividad | Wi-Fi + Bluetooth LE |
| Memoria Flash | 16 MB (16.000 KB) |
| PSRAM | 8 MB — la ficha de ML lo etiqueta como "Capacidad SRAM: 8.000 KB", pero para la config N16R8 ese bloque es PSRAM, no SRAM interna |
| Pines digitales GPIO | 45 |
| Entradas analógicas | 2 según ficha — dato sospechoso: el ESP32-S3 expone hasta 20 canales ADC en el chip; verificar con el vendedor si son solo 2 pines etiquetados como analógicos en el silkscreen, o error de plantilla del listado |
| Voltaje de funcionamiento | 3.3V |
| Voltaje de entrada recomendado | 5V (vía USB-C) |
| Voltaje de entrada límite | 5.5V |
| Dimensiones | 5.7 cm x 2.8 cm |
| Puertos USB | 2x USB-C — uno típicamente UART (chip USB-serial, para flashear con Arduino IDE/ESP-IDF) y otro USB nativo/OTG del propio chip; **confirmar con el vendedor cuál es cuál** |
| Cable USB incluido | No |
| Compatibilidad de firmware | Arduino IDE, ESP-IDF, MicroPython |

**Falta confirmar del listado real:** precio pagado, vendedor/publicación específica, si el chip USB-UART es CP2102 o CH340.

---

## 2 · Módulo LCD TFT 3.2" SPI ILI9341 240x320 Touch 65K colores

**Link:** https://www.mercadolibre.com.ar/modulo-lcd-tft-32-spi-ili9341-240x320-touch-65k-colores/p/MLA2092239224

**Fuente de los datos:** confirmado (descripción del listado, pegada por el usuario).

| Campo | Valor |
| --- | --- |
| Controlador de display | ILI9341 |
| Tamaño de panel | 8.13 cm / 3.2" diagonal |
| Resolución | 240 x 320 px (QVGA) |
| Profundidad de color | 65K colores RGB |
| Interfaz | SPI serial |
| Voltaje de operación | 3.3V |
| Touch | Resistivo, "modelo MSP3218" según el listado |
| Slot microSD | Sí, integrado |
| Pines IO mínimos (según listado) | 4 |

⚠️ **Dos cosas para chequear antes de armar, porque la descripción del listado tiene datos que no cierran del todo:**

1. **"4 IOs" es para el display solo, no para el módulo completo.** El mínimo real para manejar únicamente el panel ILI9341 por SPI es CS, DC, SDI(MOSI) y SCK (4 señales, con RESET opcional y LED atado a VCC) — ahí sí cierran los "4 IO". Pero el touch resistivo agrega T_CS y T_IRQ, y el slot SD agrega SD_CS (comparten MOSI/SCK con el display) — sumando touch y SD, son ~7 GPIO del ESP32-S3, no 4. Si el plan es usar las tres funciones (display + touch + SD), reservá 7 pines, no 4.
2. **"MSP3218" no es un chip de touch conocido** — los controladores resistivos estándar en estos módulos ILI9341 son XPT2046 o similares; "MSP3218" parece ser el nombre/código del módulo completo del vendedor, no el chip táctil. No cambia el cableado (sigue siendo SPI resistivo), pero si buscás datasheet o librería para el touch, buscá por "resistive touch SPI ILI9341" o XPT2046, no por "MSP3218".

**Falta confirmar del listado real:** precio, marca/vendedor.

⚠️ **Nota de coherencia con investigación previa:** [Hardware — Desglose de Componentes](hardware-component-selection-research.md) señala que un panel LCD retroiluminado (como este ILI9341) no logra negro real en un cuarto oscuro — muestra un gris tenue encendido en vez de apagarse, que es justo lo que el brief de Glumi busca evitar. Esa decisión ya está tomada con esta compra; queda como antecedente para cuando se revise el enclosure/filtro óptico (subsistema G del desglose).

---

## 3 · Cables jumper macho-macho para protoboard y Arduino — pack de 40

**Link:** https://www.mercadolibre.com.ar/cables-jumper-macho-a-macho-para-protoboard-y-arduino-pack-de-40/p/MLA57345527

**Fuente de los datos:** ⚠️ no verificado contra el listado — specs típicas de este accesorio estandarizado.

| Campo | Valor típico |
| --- | --- |
| Tipo de conector | Macho-macho (Dupont) en ambos extremos |
| Cantidad | 40 unidades |
| Paso de pin | 2.54 mm (estándar protoboard/Arduino) |
| Longitud | No confirmada — el título no especifica largo; los packs de 40 M-M más comunes en este rubro vienen en 20 cm |
| Calibre | Típicamente 24-28 AWG, cable multifilar con terminal macho rígido |
| Colores | Habitualmente surtido de colores variados |

**Falta confirmar del listado real:** longitud exacta, calibre, colores incluidos, precio.

---

## 4 · Protoboard de 400 puntos, experimentador Arduino/breadboard

**Link:** https://www.mercadolibre.com.ar/protoboard-de-400-puntos-experimentador-arduino-breadboard/p/MLA2089243318

**Fuente de los datos:** confirmado (descripción del listado, pegada por el usuario).

| Campo | Valor |
| --- | --- |
| Puntos de contacto | 400 |
| Buses de alimentación | 2 |
| Espaciado de perforaciones | 0.1" (2.54 mm) — estándar para componentes DIP |
| Calibre de cable aceptado | 20 – 29 AWG |
| Código interno del vendedor | 1343 (identificador propio del vendedor, no un código de fabricante) |

⚠️ **La descripción del listado se contradice en el color/acabado:** dice "protoboard **blanco** pequeño estándar" al principio y "acabado en plástico **transparente**" al final del mismo texto. Es plantilla de vendedor reutilizada entre variantes — no lo asumas como dato confiable; confirmá el color real por la foto de la publicación o al recibir el pedido, aunque no afecta el uso funcional.

**Falta confirmar del listado real:** color real, dimensiones físicas exactas, si es de una pieza o empalmable con otras, precio.

---

## 5 · Cables macho-hembra 30 cm Dupont para Arduino y protoboard — pack de 40

**Link:** https://www.mercadolibre.com.ar/pack-40-cables-macho-hembra-30cm-dupont-arduino-y-protoboard/p/MLA2042000594

**Fuente de los datos:** ⚠️ no verificado contra el listado — specs típicas de este accesorio estandarizado (el largo de 30 cm sí está en el título del producto).

| Campo | Valor |
| --- | --- |
| Tipo de conector | Macho en un extremo, hembra en el otro (Dupont) |
| Cantidad | 40 unidades |
| Longitud | 30 cm (confirmado por el título del listado) |
| Paso de pin | 2.54 mm |
| Calibre | Típicamente 24-28 AWG |
| Colores | Habitualmente surtido de colores variados |

**Falta confirmar del listado real:** calibre exacto, colores incluidos, precio.

---

## Resumen de lo pendiente

| # | Componente | Specs confirmadas | Falta |
| --- | --- | --- | --- |
| 1 | ESP32-S3 N16R8 | Sí (descripción del listado) | Precio, chip USB-UART exacto, aclarar entradas analógicas reales |
| 2 | Display ILI9341 3.2" SPI touch | Sí (descripción del listado) | Precio, marca/vendedor |
| 3 | Jumpers M-M pack 40 | No (genérico) | Descripción del listado, longitud, calibre, precio |
| 4 | Protoboard 400 puntos | Sí (descripción del listado) | Color real, dimensiones, precio |
| 5 | Jumpers M-H 30cm pack 40 | Parcial (longitud) | Descripción del listado, calibre, precio |
