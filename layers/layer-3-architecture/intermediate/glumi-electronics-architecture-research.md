# Glumi --- investigación de arquitectura electrónica para display 4.3" 800×480

**Fecha:** 2026-09-21\
**Revisado:** 2026-09-22 --- el panel objetivo pasa a 7"; se actualiza el
Riesgo 5 con la consecuencia del cambio de área y se agregan los
requisitos de flash que impone la actualización remota.\

> **Alcance.** El título dice 4.3" porque ese era el panel investigado. El
> objetivo pasó a **7", 800×480 preferido**, y casi todo lo de acá
> transfiere sin cambios: es la misma familia de placas, el mismo SoC, el
> mismo bus RGB565 y el mismo expansor CH422G. Lo que **no** transfiere son
> los números atados al área del panel --- el piso de brillo (Riesgo 5) y
> la térmica --- y el ancho de banda, que a 1024×600 pasa a estar en el
> borde del S3 y por eso se prefiere 800×480 también en 7".\
**Objetivo:** entender cómo Waveshare implementa un ESP32-S3 con un LCD
IPS RGB de 4.3" / 800×480 y traducir ese reference design a una
arquitectura de producción razonable para Glumi.

------------------------------------------------------------------------

## 1. Conclusión ejecutiva

La arquitectura que Glumi necesita **ya está técnicamente validada** por
un producto de referencia muy cercano: el **Waveshare ESP32-S3-LCD-4.3 /
ESP32-S3-Touch-LCD-4.3**.

Ese producto combina:

-   ESP32-S3-WROOM-1-N16R8
-   16 MB Flash
-   8 MB PSRAM
-   LCD IPS 4.3"
-   800×480
-   interfaz RGB
-   USB-C / 5 V
-   circuito dedicado de backlight
-   Wi-Fi/BLE

Esto es muy cercano al núcleo que necesitamos para Glumi. Waveshare
además publica el schematic, por lo que podemos usarlo como **reference
design**, no como una caja negra.

La conclusión más importante del schematic es que Waveshare **no usa
realmente las 24 líneas RGB del panel**. El panel expone RGB888 (24
bits), pero la placa utiliza **RGB565 (16 bits)**:

-   5 bits de rojo
-   6 bits de verde
-   5 bits de azul

Los bits menos significativos restantes del panel quedan sin utilizar.
Esto reduce significativamente la cantidad de GPIO necesaria y coincide
con el soporte RGB565 del periférico LCD del ESP32-S3.

Para Glumi, la arquitectura de producción más razonable sería:

``` text
USB-C 5 V
   │
   ├──────────────► Backlight driver ─────► LEDs LCD
   │
   ▼
Regulador 3.3 V
   │
   ▼
ESP32-S3-WROOM-1-N16R8
   │
   │ RGB565 + PCLK + HSYNC + VSYNC + DE
   ▼
Conector FFC
   │
   ▼
LCD IPS 4.3" 800×480
```

El touch **no forma parte de esta arquitectura mínima**.

> ⚠️ **Salvedad importante, encontrada después de escribir esta
> conclusión.** El reference design resuelve bien la *potencia* del
> backlight y mal su *control*: el enable cuelga del expansor CH422G y es
> on/off, sin PWM. Para Glumi, cuyo modo nocturno es alcance de producto,
> eso no sirve --- y significa que la placa integrada, tal como viene, no
> permite validar el brillo nocturno en P1. Ver sección 8 y Riesgo 5.

------------------------------------------------------------------------

## 2. El producto de referencia de Waveshare

Waveshare comercializa dos versiones de su placa de 4.3":

-   `ESP32-S3-LCD-4.3` --- sin touch
-   `ESP32-S3-Touch-LCD-4.3` --- con touch

Según su documentación:

  Propiedad           Valor
  ------------------- ------------------------------
  MCU                 ESP32-S3-WROOM-1-N16R8
  CPU                 Dual-core LX7, hasta 240 MHz
  Flash               16 MB
  PSRAM               8 MB
  Display             4.3" IPS
  Resolución          800×480
  Colores             65K
  Interfaz            RGB
  Ángulo de visión    160°
  Brillo              270 cd/m²
  Alimentación        USB-C, 5 V
  Consumo publicado   5 V / 450 mA
  Tamaño sin touch    105.4 × 67.1 mm
  Tamaño con touch    106.1 × 67.8 mm

Que Waveshare publique **65K colors** es consistente con una
implementación RGB565:

`2^16 = 65,536 colores`

Esto coincide también con el schematic.

------------------------------------------------------------------------

## 3. Qué descubrimos en el schematic

### 3.1 El LCD expone 40 pines

El conector del LCD del reference design contiene:

``` text
1   VLED-
2   VLED+
3   GND
4   VCC

5–12    R0–R7
13–20   G0–G7
21–28   B0–B7

29  GND
30  DCLK / PCLK
31  DISP
32  HSYNC
33  VSYNC
34  DE
35  NC
36  GND

37–40   señales asociadas al panel táctil/resistivo del conector
```

Esto explica por qué el módulo tiene un FFC grande: no es SPI como
nuestro ILI9341 actual. Los píxeles viajan en paralelo.

------------------------------------------------------------------------

## 4. Hallazgo clave: Waveshare usa RGB565, no RGB888

Aunque el LCD expone:

``` text
R0 R1 R2 R3 R4 R5 R6 R7
G0 G1 G2 G3 G4 G5 G6 G7
B0 B1 B2 B3 B4 B5 B6 B7
```

el schematic muestra varios bits inferiores como **no conectados**.

La placa utiliza:

``` text
ROJO
R3 R4 R5 R6 R7  → 5 bits

VERDE
G2 G3 G4 G5 G6 G7 → 6 bits

AZUL
B3 B4 B5 B6 B7 → 5 bits
```

Por lo tanto:

``` text
5 + 6 + 5 = 16 bits

RGB565
```

Esto es muy relevante para Glumi.

En vez de necesitar 24 GPIO únicamente para color, necesitamos 16.

A ellos se suman aproximadamente:

``` text
16  RGB data
 1  PCLK
 1  HSYNC
 1  VSYNC
 1  DE
────────────
20 señales principales
```

más las señales que decidamos usar para display enable/backlight.

Sigue siendo un bus considerable, pero es mucho más razonable para
ESP32-S3.

------------------------------------------------------------------------

## 5. El ESP32-S3 está diseñado para esto

Espressif documenta soporte nativo para paneles RGB mediante el
periférico LCD del ESP32-S3.

Su propia documentación utiliza explícitamente RGB565 como ejemplo:

``` text
data_width = 16
```

y permite configurar:

-   16 GPIO de datos
-   PCLK
-   VSYNC
-   HSYNC
-   DE
-   DISP/enable opcional

Esto significa que no estamos implementando un protocolo de display "a
mano": el ESP32-S3 tiene hardware dedicado para transmitir el
framebuffer al LCD mediante DMA.

------------------------------------------------------------------------

## 6. Por qué los 8 MB de PSRAM importan

Una imagen completa de Glumi en RGB565 necesita:

``` text
800 × 480 × 2 bytes
= 768,000 bytes
≈ 750 KiB
```

Un double framebuffer necesita aproximadamente:

``` text
1.5 MiB
```

Nuestro target N16R8 tiene:

``` text
8 MB PSRAM
```

Espressif soporta explícitamente colocar uno o dos framebuffers del LCD
en PSRAM.

Por lo tanto, **800×480 RGB565 entra cómodamente dentro de la capacidad
de memoria del N16R8**.

Esto no significa que cualquier firmware vaya a funcionar
automáticamente: pixel clock, DMA, acceso concurrente a PSRAM y
configuración del driver deben validarse. Pero la arquitectura base está
respaldada tanto por Espressif como por el producto funcional de
Waveshare.

------------------------------------------------------------------------

## 7. Alimentación

El reference design de Waveshare separa conceptualmente dos necesidades:

``` text
5 V USB
 │
 ├── alimentación general
 │
 └── regulación
       │
       ▼
      3.3 V
       │
       ▼
    ESP32-S3
```

Waveshare utiliza un **SGM2212-3.3**, un LDO de 3.3 V / 800 mA, en su
placa.

Para Glumi no deberíamos copiar automáticamente ese regulador. El
regulador final deberá elegirse según:

-   consumo real del ESP32-S3 con Wi-Fi
-   consumo de periféricos
-   eficiencia
-   temperatura
-   disponibilidad
-   costo
-   margen de corriente

Pero confirma que nuestra arquitectura puede partir de **USB-C 5 V →
rail 3.3 V**.

------------------------------------------------------------------------

## 8. El backlight necesita su propio circuito

Este es uno de los descubrimientos más importantes para pasar del
prototipo a una PCB real.

Los LEDs del backlight no se alimentan simplemente desde un GPIO.

Waveshare utiliza:

**MP3302DJ-LF-Z**

como boost converter dedicado para el backlight.

El circuito incluye además:

-   inductor
-   diodo Schottky
-   resistencias
-   capacitores
-   señal de enable/control

Conceptualmente:

``` text
3.3 V / alimentación
       │
       ▼
┌────────────────┐
│ Backlight      │
│ boost driver   │
│ MP3302         │
└───────┬────────┘
        │
        ▼
      VLED+
        │
   LEDs del LCD
        │
      VLED-
```

### Implicación para Glumi

La PCB propia necesitará resolver el backlight.

No alcanza con:

``` text
ESP32 → LCD
```

La arquitectura real es más parecida a:

``` text
              ┌──────── ESP32-S3
USB-C ────────┤
              │
              └──────── Backlight driver ── LCD
```

### ⚠️ Lo que Waveshare resuelve mal para nuestro caso

El reference design sirve para la **potencia** del backlight, no para su
**control**.

En las placas `ESP32-S3-Touch-LCD-4.3` y `4.3B`, el enable del backlight
(`LCD_BL`) cuelga del expansor de IO **CH422G**, en `EXIO2`: es
**on/off, sin PWM**. Para una dev board genérica es una decisión
razonable --- el panel se come casi todos los GPIO. Para Glumi es
inaceptable: el brillo nocturno es alcance de producto.

Requisito para la PCB Glumi:

``` text
backlight driver
    con entrada de dimming
    manejada por un GPIO del ESP32-S3 (LEDC PWM)
    capaz de bajar a ≤1–2 % de duty
    sin flicker visible a distancia de lectura
```

Ver Riesgo 5.

------------------------------------------------------------------------

## 9. Touch: podemos eliminarlo

En el producto Waveshare con touch, el touch capacitivo se comunica
mediante I²C.

La propia documentación lo describe como **opcional**, y Waveshare vende
una variante sin touch.

Para Glumi:

``` text
Touch controller
Touch FPC
Touch interrupt
Touch reset
Touch I²C
```

no son requisitos.

Esto tiene varias ventajas:

-   menor BOM
-   menor complejidad
-   menos GPIO/periféricos
-   menor superficie de fallo
-   mejor alineación con los principios de producto
-   potencialmente menor costo

**Recomendación:** la PCB Glumi V1 no debería incluir soporte de touch
salvo que aparezca un caso de uso concreto que lo justifique.

------------------------------------------------------------------------

## 10. Qué NO necesitamos copiar de Waveshare

La placa de Waveshare es una development board genérica. Incluye muchas
funciones que Glumi no necesita.

Entre ellas:

``` text
CAN
RS485
microSD
battery charger
battery connector
USB-to-UART dedicado
IO expander
sensor header
external I²C header
múltiples terminales
```

Glumi debería eliminar todo lo que no tenga una función de producto.

### Arquitectura Waveshare

``` text
ESP32-S3
├── LCD
├── Touch
├── USB
├── USB-UART
├── SD
├── CAN
├── RS485
├── I²C expansion
├── Battery
├── Charger
└── IO expansion
```

### Arquitectura Glumi candidata

``` text
ESP32-S3
├── LCD RGB565
├── Wi-Fi
├── USB-C power/programming
├── Backlight driver
└── [futuro: ambient light sensor, solo si se valida]
```

Esto es considerablemente más simple.

------------------------------------------------------------------------

## 11. Arquitectura electrónica Glumi V0

Propuesta inicial:

``` text
                    GLUMI V0

                USB-C 5 V
                    │
           ┌────────┴────────┐
           │                 │
           ▼                 ▼
      3.3 V regulator   Backlight driver
           │                 │
           ▼                 ▼
   ESP32-S3-WROOM       LCD backlight
   N16R8
           │
           │ RGB565
           │ PCLK
           │ HSYNC
           │ VSYNC
           │ DE
           │ DISP
           ▼
      FFC connector
           │
           ▼
   4.3" IPS 800×480
```

No battery.

No touch.

No microSD.

No CAN.

No RS485.

No RTC en V0.

No audio.

No botones de interacción de producto.

------------------------------------------------------------------------

## 12. USB-C

Para Glumi tiene sentido que USB-C cumpla dos funciones durante
desarrollo y potencialmente producción:

``` text
USB-C
├── alimentación
└── programación / diagnóstico
```

El ESP32-S3 soporta USB nativo.

Por lo tanto, una PCB Glumi bien diseñada podría evitar parte de la
circuitería USB-to-UART que Waveshare incluye por conveniencia de
desarrollo.

Esto debe validarse durante el schematic V1, especialmente pensando en:

-   flashing inicial en fábrica
-   recuperación de firmware
-   debugging
-   test de producción
-   protección ESD
-   resistencias CC de USB-C
-   acceso al modo boot/reset

------------------------------------------------------------------------

## 13. Qué debería existir físicamente en nuestro siguiente prototipo

No necesitamos comprar cada resistencia antes de diseñar la placa.

Sí necesitamos validar físicamente los componentes que condicionan el
producto.

### Componentes críticos que sí conviene tener

#### 1. Display para el siguiente prototipo

Acá hay una tensión a resolver de forma explícita: la sección 17 concluye
que el panel de producción probablemente venga de un ODM, así que **el
"mismo módulo exacto que vamos a especificar" todavía no existe**. No se
puede tener en P1.

Por lo tanto, lo correcto para el siguiente prototipo es la **placa
integrada** `ESP32-S3-LCD-4.3` (ver sección 20, Paso A): mismo panel,
mismo MCU, cero trabajo de interfaz. P1 valida experiencia visual y
escala, **no** el envolvente mecánico final --- ese se congela recién
cuando haya panel de producción elegido.

Debemos validar:

Debemos validar:

-   tamaño
-   viewing angle
-   negros
-   brillo mínimo
-   brillo máximo
-   legibilidad nocturna
-   color
-   uniformidad
-   temperatura
-   montaje
-   FFC
-   tolerancias mecánicas

#### 2. ESP32-S3 N16R8

Ya tenemos una dev board N16R8.

Sirve para validar:

-   firmware
-   Wi-Fi
-   memoria
-   backend
-   rendering
-   performance

La PCB final usaría probablemente el módulo:

`ESP32-S3-WROOM-1-N16R8`

y no toda la development board.

#### 3. Sistema óptico / frente

Hay que prototipar:

-   LCD desnudo
-   bezel
-   posible PMMA/policarbonato frontal
-   negro visual alrededor del active area
-   reflejos
-   pérdida de brillo
-   fingerprints

#### 4. Carcasa

Varias iteraciones 3D antes de congelar el PCB.

------------------------------------------------------------------------

## 14. Qué NO comprar todavía

Hasta cerrar el schematic inicial, evitar comprar en cantidad:

-   reguladores
-   resistencias
-   capacitores
-   inductores
-   diodos
-   conectores FFC
-   chips de backlight
-   botones
-   sensores
-   RTC
-   speakers
-   batería

Primero se define el circuito.

Después se genera la BOM.

Después se compran/producen las partes.

------------------------------------------------------------------------

## 15. Proceso recomendado hacia producción

### P0 --- Proof of concept --- COMPLETADO

``` text
Libre
 ↓
backend
 ↓
ESP32-S3 dev board
 ↓
ILI9341
```

**Objetivo validado:** podemos obtener glucosa real y mostrarla
físicamente.

------------------------------------------------------------------------

### P1 --- Display / industrial design prototype

``` text
Waveshare ESP32-S3-LCD-4.3   (placa integrada, sin touch)
+
hack de dimming de backlight   (obligatorio, ver Riesgo 5)
+
carcasa 3D
```

No el display suelto: no es cableable a una dev board (ver sección 20,
Paso A).

Validar:

-   escala
-   legibilidad
-   viewing angle
-   UI
-   brillo, incluido el **mínimo** a oscuras
-   proporciones
-   orientación
-   ubicación USB-C
-   ángulo del producto
-   térmica a 24 h dentro de la carcasa

En esta etapa no importa todavía que la electrónica interna sea
elegante. Sí importa que el brillo se pueda bajar: sin eso, medio
producto queda sin validar.

------------------------------------------------------------------------

### P2 --- Engineering prototype

Diseñar **Glumi PCB V0.1**.

La placa debería incluir únicamente lo necesario para:

``` text
USB-C
  ↓
power
  ↓
ESP32-S3 N16R8
  ↓
RGB565
  ↓
LCD

+ backlight driver
```

Fabricar pocas unidades ensambladas.

Ejemplo:

``` text
5 × PCBs
```

No tiene sentido soldar manualmente cientos de componentes SMD para
demostrar manufacturabilidad. El mismo proceso de assembly que usaría un
fabricante puede utilizarse para las primeras placas.

------------------------------------------------------------------------

### P3 --- EVT / production-intent

Integrar:

-   PCB Glumi
-   display exacto
-   carcasa
-   lente/frente
-   USB-C
-   fasteners
-   patas
-   firmware

Ejecutar pruebas de:

-   uso continuo
-   Wi-Fi
-   temperatura
-   power cycling
-   pérdida de conexión
-   reconexión
-   actualización
-   display burn / image persistence según panel
-   brillo nocturno
-   montaje/desmontaje

------------------------------------------------------------------------

### P4 --- DVT / diseño cercano a producción

Optimizar:

-   PCB
-   DFM
-   tolerancias
-   assembly
-   test points
-   flashing
-   enclosure
-   materiales
-   acabados
-   cable
-   packaging

------------------------------------------------------------------------

### P5 --- Producción piloto

Pequeño batch:

``` text
20–100 unidades
```

Objetivo: validar el proceso, no maximizar volumen.

------------------------------------------------------------------------

## 16. Qué le entregaríamos eventualmente a un fabricante

No es simplemente "un Glumi armado".

El paquete debería incluir:

### Electrónica

``` text
schematic
PCB layout
Gerbers
drill files
BOM
CPL / pick-and-place
fabrication notes
assembly notes
test points
programming procedure
```

### Mecánica

``` text
STEP files
STL de prototipos
2D drawings
material specification
surface finish
tolerances
fastener specification
lens/window drawing
```

### Firmware

``` text
production firmware
factory flashing image
firmware version
OTA strategy
device provisioning process
```

### QA / test

``` text
power-on test
display test
Wi-Fi test
backend connectivity test
pixel/display inspection
burn-in criteria
final functional test
```

### BOM maestra

Cada componente debería terminar identificado por:

  Campo                      Ejemplo
  -------------------------- ----------------------
  Internal ID                GL-E-001
  Categoría                  Display
  Fabricante                 TBD
  Manufacturer Part Number   TBD
  Descripción                4.3" IPS 800×480 RGB
  Cantidad                   1
  Supplier                   TBD
  Unit cost                  TBD
  Alternative                TBD
  Lifecycle                  Active
  Prototype validated        Yes/No

------------------------------------------------------------------------

## 17. Una advertencia importante sobre el display actual de Waveshare

El producto integrado `ESP32-S3-LCD-4.3` es una excelente **prueba de
arquitectura**.

Eso no significa automáticamente que el display retail
`4.3inch Capacitive Touch LCD` de Waveshare sea el componente que
deberíamos especificar para fabricar cientos o miles de Glumis.

Para producción debemos obtener:

1.  manufacturer part number exacto del panel;
2.  disponibilidad en volumen;
3.  MOQ;
4.  precio por 100 / 1.000 / 10.000 unidades;
5.  lifecycle / garantía de suministro;
6.  datasheet oficial completo;
7.  tolerancias mecánicas;
8.  mating connector exacto;
9.  especificación de backlight;
10. alternativa second-source.

**Conclusión:** Waveshare es excelente para P1/P2 y como reference
design. El panel final de producción podría terminar viniendo
directamente de un fabricante/ODM de displays.

------------------------------------------------------------------------

## 18. Riesgos técnicos que todavía debemos resolver

### Riesgo 1 --- GPIO

RGB565 utiliza una cantidad importante de GPIO.

La PCB final debe reservar cuidadosamente:

-   16 data
-   PCLK
-   HSYNC
-   VSYNC
-   DE
-   display enable/backlight control
-   USB
-   cualquier sensor futuro

Dos restricciones concretas a aplicar **antes** de repartir pines:

**GPIO 33--37 no están disponibles.** El N16R8 lleva PSRAM octal, que los
ocupa dentro del propio módulo WROOM-1. Hay que descontarlos del
presupuesto de pines desde el arranque, no descubrirlo al final.

**Strapping pins.** Waveshare ruteó líneas de datos RGB sobre `GPIO0`,
`GPIO45` y `GPIO46`, los tres strapping. Le funciona porque el panel está
pasivo en boot, pero para Glumi la regla debería ser **no poner datos en
`0`, `3`, `45`, `46`** salvo que no haya alternativa, y en ese caso
verificar que ningún pull externo altere el arranque.

**Estado:** viable, pero el margen es más chico de lo que parecía.

### Riesgo 2 --- Backlight

Necesitamos diseñar y validar el driver según la especificación exacta
del panel final.

Requisito que fija el producto: dimming hasta **≤1--2 % de duty** sin
flicker visible a distancia de mesa de luz, manejado por un GPIO del
ESP32-S3 con LEDC (o dimming analógico). Ver Riesgo 5.

**Estado:** reference design disponible, pero **no reutilizable tal
cual** en la parte de control; componente final pendiente.

### Riesgo 3 --- PSRAM / bandwidth

La memoria alcanza, pero el rendimiento real debe probarse con:

-   Wi-Fi activo
-   UI real
-   framebuffer en PSRAM
-   actualizaciones de pantalla
-   backend polling

Esto no es un riesgo genérico: Espressif documenta el modo de falla y su
mitigación. Con el framebuffer en PSRAM, EDMA y CPU comparten ancho de
banda de PSRAM, y como la PSRAM comparte el bus SPI con la flash
principal, el acceso concurrente produce **corrupción visible / drift de
imagen** y obliga a bajar el pixel clock.

Mitigaciones, con nombre propio:

``` text
bounce_buffer_size_px
    dos buffers chicos en RAM interna; el LCD lee por DMA de uno
    mientras la CPU copia desde PSRAM al otro.
    Más robusto ante picos de ancho de banda, a costa de CPU.

esp_lcd_rgb_panel_set_pclk()
    bajar el pixel clock en runtime cuando hay contención.
```

Glumi hace polling por Wi-Fi contra una pantalla permanentemente
encendida: esto es un *cuándo*, no un *si*.

**Estado:** manejable y con solución conocida, pero hay que
implementarlo, no descubrirlo.

### Riesgo 4 --- Supply chain del display

El display es probablemente el componente de mayor riesgo de suministro
y uno de los que más condiciona la mecánica.

Pista concreta ya disponible: la wiki del `ESP32-S3-Touch-LCD-4.3B`
identifica el driver del panel como **ST7262**. Es el punto de partida
para buscar el mismo panel directo con un ODM en vez de por retail.

**Estado:** pendiente de sourcing de producción.

### Riesgo 5 --- Night experience

⚠️ **No es sólo "requiere prueba física": es un bloqueo de hardware en el
reference design, y el salto a 7" lo agranda.**

En las placas de Waveshare (`ESP32-S3-Touch-LCD-4.3` y `4.3B`) el
backlight **no** cuelga de un GPIO del ESP32-S3: está detrás del expansor
de IO **CH422G**, en `EXIO2 → LCD_BL`, y es **on/off, sin PWM**.

Consecuencia: la placa integrada, tal como viene, **no permite validar el
modo nocturno** --- que es alcance explícito del producto y exit
criterion de P1.

El módulo suelto (SKU 16249) **sí** expone un pin `PWM` de brillo. La
limitación es de la dev board, no del panel: es justamente la parte del
reference design que no hay que copiar.

Para habilitar dimming en P1, elegir una:

``` text
(a) levantar/cortar el enable de backlight del CH422G
    y manejarlo desde un GPIO libre con LEDC PWM

(b) alimentar VLED desde un driver LED de corriente
    constante externo, con entrada de dimming PWM
```

Y medir: brillo mínimo estable a oscuras, flicker a distancia de mesa de
luz, uniformidad a bajo duty, a qué duty deja de ser legible y **a qué duty
deja de iluminar el cuarto**.

**El salto a 7" cambia la magnitud del problema.** La superficie emisora
pasa de 95 × 54 = 5.130 mm² a 154 × 86 = 13.244 mm²: **~2,6× más luz al
mismo duty**. El requisito de "≤1--2 % de duty" se calibró contra el panel
chico y por lo tanto **ya no significa nada**: un porcentaje de duty
depende del backlight, del número de LEDs y del área, y no transfiere entre
paneles de distinto tamaño.

El requisito se re-expresa como resultado medido --- *legible a distancia de
lectura en un cuarto a oscuras, sin iluminar el cuarto, sin flicker
perceptible* --- y el duty mínimo que lo cumple es lo que P1 mide. Ese
número medido es el que entra como requisito de la PCB.

Riesgo asociado: con 2,6× de área, el umbral "deja de ser legible" y el
umbral "deja de iluminar el cuarto" pueden cruzarse. Si se cruzan, el modo
nocturno no cierra en 7", y es un hallazgo de P1, no de P3.

Verificar además **dónde va `LCD_BL` en el esquemático de la placa de 7"
concreta antes de comprarla**. Es de la misma familia y presumiblemente
comparte la arquitectura, pero comprar sin mirarlo es comprar una placa que
no puede validar el exit criterion por el que P1 existe.

270 cd/m² de máximo no dice nada sobre el piso, y el piso es el producto
entero de noche.

**Estado:** bloqueante para P1; mitigación conocida, trabajo pendiente.

------------------------------------------------------------------------

## 19. Decisiones que esta investigación permite tomar

### Podemos considerar validadas

-   ESP32-S3 es una arquitectura razonable para 800×480.
-   N16R8 ofrece memoria suficiente para esta clase de UI.
-   RGB565 es suficiente para Glumi.
-   No necesitamos RGB888.
-   No necesitamos touch.
-   El display necesita un circuito de backlight dedicado.
-   Una PCB custom para Glumi es una evolución razonable, no una
    extravagancia.
-   No necesitamos mantener la enorme cantidad de periféricos del
    reference design de Waveshare.

### Todavía NO deberíamos congelar

-   modelo final de panel para producción;
-   regulador 3.3 V;
-   backlight driver final;
-   pin mapping final;
-   conector FFC final;
-   sensor de luz;
-   dimensiones PCB;
-   posición USB-C;
-   material final de carcasa.

------------------------------------------------------------------------

## 20. Recomendación concreta de siguiente paso

### Paso A --- comprar la placa integrada, no el display suelto

`Waveshare ESP32-S3-LCD-4.3`, versión **sin touch** (~USD 28; con touch,
~USD 33).

El módulo retail suelto (SKU 16249) es RGB paralelo con header para
placas STM32 `Open746I-C`/`Open429I-C`: llevar 16 líneas de datos + PCLK
+ HSYNC + VSYNC + DE a ~25--30 MHz hasta una dev board ESP32-S3 exige una
PCB adaptadora con longitudes controladas. No es cableable con Dupont.

Usar la placa integrada para validar la experiencia visual y dimensional,
y dejar el módulo suelto para cuando exista PCB propia.

**Primero**, resolver el dimming de backlight (Riesgo 5): tal como viene,
la placa no permite bajar brillo.

### Paso B --- crear `Glumi Electronics Architecture v0.1`

Antes de hacer PCB:

``` text
power tree
pin allocation
display interface
USB
backlight
boot/reset
programming
test points
```

### Paso C --- crear BOM V0

Separar claramente:

``` text
PROTOTYPE PART
PRODUCTION CANDIDATE
REFERENCE ONLY
```

### Paso D --- schematic en KiCad

Tomar el schematic de Waveshare como referencia, pero reconstruir
únicamente lo que Glumi necesita.

### Paso E --- revisión electrónica

Antes de mandar a fabricar la primera PCB, hacer revisar schematic +
layout por alguien con experiencia en hardware/PCB.

Especial atención a:

-   alimentación
-   USB
-   ESP32 strapping pins
-   antenna keepout
-   RGB routing
-   backlight
-   ESD
-   decoupling
-   FFC pinout

### Paso F --- fabricar 3--5 PCB V0.1 ensambladas

Recién ahí Glumi deja de ser:

``` text
development boards + cables
```

y pasa a ser:

``` text
un verdadero engineering prototype
```

------------------------------------------------------------------------

# Arquitectura objetivo resumida

``` text
                        GLUMI

                    USB-C 5 V
                        │
              ┌─────────┴──────────┐
              │                    │
              ▼                    ▼
       3.3 V regulation      Backlight driver
              │                    │
              ▼                    │
    ESP32-S3-WROOM-1-N16R8         │
              │                    │
       Wi-Fi  │                    │
       USB    │                    │
              │ RGB565             │
              │ PCLK               │
              │ HSYNC              │
              │ VSYNC              │
              │ DE                 │
              ▼                    ▼
         FFC connector ───────► 4.3" IPS
                                  800×480
```

La dirección de diseño debería ser:

> **Tomar una arquitectura técnicamente probada y quitar todo lo que
> Glumi no necesita.**

No diseñar electrónica por el placer de diseñarla; diseñar la mínima
electrónica que permita que Glumi cumpla su función de forma confiable,
mantenible y fabricable.

------------------------------------------------------------------------

# Fuentes

1.  Waveshare --- ESP32-S3-Touch-LCD-4.3 / ESP32-S3-LCD-4.3
    documentation\
    https://docs.waveshare.com/ESP32-S3-Touch-LCD-4.3

2.  Waveshare --- ESP32-S3-Touch-LCD-4.3 resources and schematic\
    https://docs.waveshare.com/ESP32-S3-Touch-LCD-4.3/Resources-And-Documents

3.  Waveshare --- ESP32-S3-LCD-4.3 schematic PDF\
    https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-4.3/manual/ESP32-S3-Touch-LCD-4.3-Sch.pdf

4.  Waveshare --- ESP32-S3 4.3" development board product page\
    https://www.waveshare.com/esp32-s3-touch-lcd-4.3.htm

5.  Espressif --- ESP32-S3 RGB LCD documentation\
    https://docs.espressif.com/projects/esp-idf/en/v6.0.2/esp32s3/api-reference/peripherals/lcd/rgb_lcd.html

6.  Waveshare --- ESP32-S3-Touch-LCD-4.3 wiki (backlight vía CH422G
    `EXIO2 → LCD_BL`)\
    https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-4.3

7.  Waveshare --- ESP32-S3-Touch-LCD-4.3B wiki (driver de panel
    **ST7262**; mismo esquema de backlight)\
    https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-4.3B

8.  Espressif --- RGB LCD: frame buffer en PSRAM, `bounce_buffer_size_px`
    y `esp_lcd_rgb_panel_set_pclk()`\
    https://docs.espressif.com/projects/esp-idf/en/release-v5.5/esp32s3/api-reference/peripherals/lcd/rgb_lcd.html

------------------------------------------------------------------------

## Nota sobre el nivel de certeza

Este documento distingue entre:

-   **confirmado por schematic/documentación:** RGB565, N16R8, 800×480,
    PSRAM, backlight boost, USB-C, periféricos de la placa;
-   **arquitectura propuesta para Glumi:** simplificación de la placa,
    eliminación de touch/periféricos, PCB propia;
-   **pendiente de ingeniería:** selección final de componentes, pin
    mapping Glumi, power budget, routing, DFM y sourcing del panel de
    producción.

El schematic de Waveshare debe tratarse como **reference design**, no
como un diseño listo para copiar directamente a producción.
