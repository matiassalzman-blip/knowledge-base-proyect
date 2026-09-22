# Glumi --- plan detallado de prototipo a producción

**Versión:** 0.2\
**Fecha:** 2026-09-21\
**Objetivo:** definir el camino desde el prototipo funcional actual de
Glumi hasta un producto reproducible y listo para fabricar.

## Cambios en 0.2

Revisión técnica cruzada contra
`layer-3-architecture/intermediate/waveshare-4.3inch-capacitive-touch-lcd-spec.md`,
`layer-3-architecture/intermediate/glumi-electronics-architecture-research.md`
y `layer-0-business/latest/strategic-goals-and-constraints.md`:

-   **P1 cambia de hardware** (4.5): la placa integrada
    `ESP32-S3-LCD-4.3`, no el display suelto, que no es cableable a una
    dev board.
-   **Nueva 4.6:** el backlight del reference design es on/off vía
    CH422G --- el modo nocturno no se podía validar. Sub-tarea explícita.
-   **Nueva 4.7:** térmica e image persistence adelantadas a P1.
-   **4.8 exit criteria** ahora son numéricos.
-   **Nueva sección 5:** hipótesis de volumen y costo objetivo, con
    decisión explícita Track A (PCB propia) / Track B (módulo comprado).
-   **Contradicción backend / on-device** marcada, no resuelta.
-   Restricciones de pin allocation, requisito de dimming en la PCB,
    caminos regulatorios de Argentina, gates con umbrales y gasto duro
    condicionado a LibreLinkUp/ANMAT.

------------------------------------------------------------------------

# 1. Punto de partida

Hoy Glumi ya superó la etapa de idea.

El prototipo actual demuestra el flujo esencial:

``` text
FreeStyle Libre
      ↓
LibreLinkUp / fuente de datos
      ↓
Backend Glumi
      ↓ Wi-Fi
ESP32-S3 N16R8
      ↓
ILI9341 3.2" 320×240
      ↓
Glucosa real en un objeto físico
```

## Lo que P0 ya validó

-   Es posible obtener la lectura de glucosa.
-   Podemos abstraerla y entregarla al dispositivo.
-   El ESP32-S3 puede conectarse por Wi-Fi.
-   Podemos renderizar valor, tendencia, unidad y hora.
-   La propuesta funciona conceptualmente como objeto ambient glucose
    display.

## Lo que P0 NO pretende validar

-   Display final.
-   Calidad visual final.
-   Dimensiones finales.
-   PCB final.
-   Carcasa final.
-   Proceso de onboarding.
-   Costos de producción.
-   Ensamblaje industrial.
-   Certificaciones.
-   Supply chain.

El prototipo actual debe conservarse como **P0 --- Proof of Concept**.

> ⚠️ **Contradicción abierta --- ¿backend sí o backend no?**
>
> Este plan describe, en las secciones 16 y 17, un backend propio con
> cuentas de usuario, asociación `user ↔ device` y OTA. El
> `product-brief.md` vigente declara explícitamente fuera de alcance
> *"app móvil, backend propio y cuenta de usuario"* y especifica una
> configuración íntegramente on-device: AP + portal cautivo + QR + PIN.
>
> Son dos productos distintos, con modelos de seguridad, modos de falla y
> exposición regulatoria distintos. **Hay que decidir cuál documento manda
> antes de entrar a P3.** Hasta entonces, las secciones 16 y 17 quedan
> marcadas como no resueltas y no deben usarse como requisito de
> firmware.

------------------------------------------------------------------------

# 2. Principio general del proceso

No necesitamos fabricar artesanalmente una unidad idéntica a la que
luego producirá una fábrica.

Sí necesitamos llegar progresivamente a un punto donde **cada decisión
del producto esté especificada, validada y documentada**.

El proceso será:

``` text
P0
Proof of Concept
      ↓
P1
Product Architecture Prototype
      ↓
P2
Engineering Prototype
      ↓
P3
EVT / Production-Intent Prototype
      ↓
P4
DVT / Design Validation
      ↓
P5
Pilot Production / PVT
      ↓
PRODUCCIÓN
```

Cada etapa debe responder preguntas distintas.

No avanzamos de etapa simplemente porque "el prototipo funciona".

Avanzamos cuando las preguntas de esa etapa están suficientemente
resueltas.

------------------------------------------------------------------------

# 3. P0 --- Proof of Concept

**Estado: completado**

## Objetivo

Demostrar que el concepto central de Glumi es técnicamente posible.

## Hardware

-   ESP32-S3 N16R8 development board.
-   ILI9341 3.2" 320×240.
-   Breadboard.
-   Dupont.
-   USB-C.

## Software

-   Conexión a la fuente de glucosa.
-   Backend funcional.
-   ESP32 conectado por Wi-Fi.
-   Render de glucosa real.

## Resultado

``` text
✓ DATA
✓ BACKEND
✓ WI-FI
✓ ESP32
✓ DISPLAY
```

## Decisión

No seguir optimizando este hardware.

El ILI9341 cumplió su función: probar la arquitectura.

Sus limitaciones conocidas ---resolución, tamaño y viewing angle---
justifican avanzar.

------------------------------------------------------------------------

# 4. P1 --- Product Architecture Prototype

**Próxima etapa**

Esta es probablemente la etapa más importante para diseño de producto.

## Objetivo

Construir el primer Glumi que permita evaluar **la experiencia física
real**, aunque internamente todavía utilice electrónica de desarrollo.

Queremos responder:

> ¿Este objeto, con aproximadamente este display, este tamaño y esta UI,
> funciona realmente en una mesa de luz, escritorio o living?

------------------------------------------------------------------------

## 4.1 Display candidato

Candidato actual:

**4.3" IPS --- 800×480**

Target aproximado:

``` text
Área activa
≈ 95 × 54 mm

Diagonal
4.3" / ≈109 mm
```

### Debemos evaluar físicamente

-   viewing angle;
-   resolución;
-   contraste;
-   calidad de negro;
-   brillo;
-   brillo mínimo;
-   legibilidad nocturna;
-   uniformidad;
-   reflejos;
-   color;
-   proporción;
-   distancia de lectura;
-   temperatura.

### Regla

El display debe probarse físicamente antes de diseñar el producto
alrededor suyo.

------------------------------------------------------------------------

## 4.2 UI P1

La UI debe mantenerse deliberadamente mínima.

Default:

``` text
        10:24

        112 →
        mg/dL
```

No incluir por defecto:

-   curva;
-   gráfica histórica;
-   Wi-Fi icon;
-   rango;
-   mensajes de tranquilidad;
-   botones;
-   menú;
-   navegación.

### Estados mínimos a validar

1.  steady;
2.  rising;
3.  falling;
4.  high;
5.  low;
6.  stale;
7.  unavailable;
8.  no connection.

------------------------------------------------------------------------

## 4.3 Prototipo industrial

Diseñar una primera carcasa 3D alrededor del display candidato.

No buscar todavía una superficie perfecta.

Buscar validar:

-   ancho;
-   alto;
-   profundidad;
-   inclinación;
-   tamaño del display respecto del cuerpo;
-   presencia visual;
-   estabilidad;
-   lectura sentado/acostado;
-   posición del USB-C;
-   espacio interno.

### Construcción

Idealmente:

``` text
front bezel
+
main body
+
rear cover
+
internal supports
+
feet
```

Evitar una pieza monolítica pegada permanentemente.

------------------------------------------------------------------------

## 4.4 Material

### Iteraciones dimensionales

PLA.

### Prototipo funcional

PETG o material equivalente apropiado.

### Frente

Explorar:

-   display expuesto;
-   bezel negro;
-   PMMA/policarbonato frontal;
-   ventana ahumada.

Objetivo:

> que el frente se perciba como un producto terminado y no como un
> módulo electrónico metido dentro de una caja.

------------------------------------------------------------------------

## 4.5 Electrónica P1

**Decisión: P1 usa la placa integrada, no el display suelto.**

El módulo retail `4.3inch Capacitive Touch LCD` (SKU 16249) es un panel
**RGB paralelo** con header pensado para placas STM32 `Open746I-C` /
`Open429I-C`. Conectarlo a una dev board ESP32-S3 exige llevar 16 líneas
de datos + PCLK + HSYNC + VSYNC + DE a ~25--30 MHz. Eso no se cablea con
Dupont "aunque quede poco elegante": requiere una PCB adaptadora con
longitudes controladas.

Dicho de otro modo: **"P1 con electrónica de desarrollo" escondía todo el
trabajo de P2**. Corrección:

``` text
P1  →  Waveshare ESP32-S3-LCD-4.3   (versión sin touch)
       USD ~28   (versión con touch, ~33)

       mismo panel IPS 4.3" 800×480
       mismo ESP32-S3-WROOM-1-N16R8
       mismo bus RGB565
       cero trabajo de interfaz
```

El display suelto recién tiene sentido en **P2**, cuando exista una PCB
Glumi donde enchufarlo.

### Qué valida y qué no valida P1

Valida:

-   experiencia visual: tamaño, resolución, viewing angle, color;
-   escala y presencia del objeto;
-   legibilidad de la UI a distancia real;
-   comportamiento nocturno (ver 4.6);
-   rendimiento de ESP32-S3 + RGB565 + PSRAM + Wi-Fi;
-   térmica de un objeto always-on (ver 4.7).

**No** valida:

-   el envolvente mecánico final. La placa integrada trae el panel
    montado sobre su propio PCB (106,1 × 67,8 mm); el módulo de
    producción tendrá otro espesor, otro FFC y otros puntos de montaje.
    El CAD de P1 sirve para validar **escala**, no para congelar
    tolerancias.

### Trabajo de firmware que P1 hace visible

Pasar de ILI9341 SPI 320×240 a 800×480 RGB **no es un cambio de driver**:
es reescribir la capa de render. Hay que decidir y presupuestar:

-   LVGL vs. dibujo directo sobre framebuffer;
-   render tipográfico a tamaños grandes;
-   estrategia de doble buffer / tearing;
-   `bounce buffer` y bajada de PCLK por contención PSRAM ↔ Wi-Fi (ver
    `glumi-electronics-architecture-research.md`, Riesgo 3).

Hoy este trabajo no está estimado en ninguna parte del plan.

------------------------------------------------------------------------

## 4.6 Backlight y brillo nocturno

⚠️ **Hallazgo bloqueante para un exit criterion de P1.**

En las placas de referencia de Waveshare (`ESP32-S3-Touch-LCD-4.3` y
`4.3B`) el backlight **no** cuelga de un GPIO del ESP32-S3: está detrás
del expansor de IO **CH422G**, en `EXIO2 → LCD_BL`, y funciona como
**on/off, sin PWM**.

O sea: **tal como viene, la placa integrada no permite validar el modo
nocturno**, que es a la vez:

-   alcance explícito del `product-brief.md`;
-   exit criterion de P1;
-   Riesgo 5 de la investigación de electrónica;
-   y, de noche, prácticamente todo el producto.

Detalle que importa: el módulo suelto (SKU 16249) **sí** expone un pin
`PWM` de brillo. La limitación es de la dev board, no del panel ---
justamente la parte del reference design que **no** hay que copiar.

### Sub-tarea de P1: habilitar dimming

Elegir una:

``` text
(a) levantar/cortar el enable de backlight del CH422G
    y manejarlo desde un GPIO libre con LEDC PWM

(b) alimentar VLED desde un driver LED de corriente
    constante externo, con entrada de dimming PWM
```

### Medición requerida

-   brillo mínimo estable en cuarto totalmente a oscuras;
-   flicker percibido a distancia de mesa de luz;
-   uniformidad a bajo duty;
-   a qué duty deja de ser legible.

### Requisito que esto fija para la PCB

El driver de backlight debe permitir dimming hasta **≤1--2 % de duty**,
con frecuencia PWM suficientemente alta --- o dimming analógico --- para
no producir flicker visible a distancia de lectura. Esto entra en la
sección 8 como requisito, no como deseo.

------------------------------------------------------------------------

## 4.7 Riesgos de "siempre encendido" a verificar en P1

Glumi es un objeto permanentemente encendido que muestra un layout casi
estático. Dos consecuencias que el plan trataba como pruebas tardías y
conviene adelantar a P1:

### Térmica

``` text
5 V × 450 mA ≈ 2,25 W continuos
dentro de una carcasa impresa cerrada
```

Medir temperatura de superficie a las 24 h ya en P1: condiciona
ventilación, espesor de pared y material. PLA es mala elección incluso
como prototipo funcional de un objeto always-on.

### Image persistence

El mismo número, en los mismos píxeles, durante meses. La mitigación es
de producto, no un test de EVT:

-   deriva lenta de todo el layout, de pocos píxeles, imperceptible;
-   fondo oscuro por defecto;
-   evitar elementos de alto contraste clavados al píxel.

------------------------------------------------------------------------

## 4.8 Exit criteria P1

No pasar a P2 hasta tener estos valores **medidos y registrados**:

  Criterio               Umbral
  ---------------------- --------------------------------------------------
  Legibilidad            número + flecha legibles a **3 m**
  Legibilidad nocturna   legible en cuarto a oscuras sin iluminarlo
  Brillo mínimo          dimming estable, sin flicker visible
  Estabilidad            **72 h** continuas, 0 reboots, 0 freezes
  Reconexión             < **60 s** tras caída de Wi-Fi o router
  Render                 sin tearing ni drift con Wi-Fi activo
  Térmica                superficie < **45 °C** a las 24 h
  Escala                 dimensiones decididas y documentadas
  Orientación            decidida y validada en uso real
  Dirección industrial   existe y está documentada

Si alguno no se puede medir porque el hardware de P1 no lo permite --- el
caso del backlight, 4.6 --- **eso es trabajo de P1, no una excusa para
avanzar**.

------------------------------------------------------------------------

# 5. Hipótesis de volumen y costo objetivo

**Esta sección va antes de P2 a propósito: casi todas las decisiones
caras del resto del plan dependen de un número que todavía no está
escrito en ningún lado.**

La versión 0.1 especificaba PCB propia (P2), DFM de inyección y CMF (P4)
y un lote piloto de 20--100 unidades (P5) sin declarar nunca cuántos
Glumis se esperan fabricar ni a qué precio. Eso vuelve indecidibles
varias cosas:

-   **Tooling de inyección** para una carcasa de 4 piezas ronda los
    USD 5k--20k+ y sólo amortiza sobre miles de unidades. En los
    volúmenes que implica "comercializable en Argentina" a 12--18 meses,
    lo más probable es **impresión MJF/SLS o vacuum casting, sin
    tooling** --- lo que elimina buena parte del DFM mecánico de la
    sección 19.
-   **PCB propia** tiene NRE real: diseño, 2--3 spins, stencils, setup de
    assembly, review pago. Contra una placa off-the-shelf de USD 28 que
    ya funciona, **a 100 unidades la PCB propia no se paga en costo
    unitario**. Se paga en libertad mecánica, en control de backlight y
    en no entregarle al cliente una dev board con un transceiver CAN
    adentro. Son razones legítimas, pero son *otras* razones, y cambian
    qué tiene que hacer la PCB.
-   **BOM y aranceles:** el costo de componentes importados en Argentina
    cambia materialmente la matemática y, por lo tanto, la elección de
    display.

## 5.1 Completar antes de entrar a P2

``` text
Unidades esperadas año 1        ___
Precio de venta objetivo        ___
Costo BOM objetivo              ___
Costo landed (con aranceles)    ___
Presupuesto NRE disponible      ___
```

Ancla de mercado ya documentada en layer-0: un SugarPixel importado ronda
**USD 200--300**.

## 5.2 Dos caminos, decisión explícita

La versión 0.1 asumía que producir requiere PCB propia. Eso no es un
hecho, es una decisión --- y debería tomarse con el número de arriba a la
vista.

``` text
TRACK A --- PCB propia

P1  placa integrada
P2  Glumi PCB V0.1
P3  EVT con PCB propia

+ libertad mecánica total
+ dimming de backlight bien resuelto
+ costo unitario bajo a volumen alto
− NRE alto
− requiere un skill de hardware que hoy no está
− 2–3 spins antes de tener algo confiable
```

``` text
TRACK B --- módulo comprado adentro del producto

P1  placa integrada
P2  carrier mínima: dimming + conectores + montaje
P3  EVT con el módulo adentro de la carcasa

+ arranca ya, sin NRE de PCB
+ riesgo técnico bajísimo
+ razonable hasta ~cientos de unidades
− costo unitario fijo (~USD 28 de piso)
− depende de un proveedor único
− periféricos que no usás viajan igual en la placa
```

Dado el constraint organizacional documentado en layer-0 --- **una sola
persona, en tiempo discontinuo, sin experiencia previa en hardware** ---
Track B puede perfectamente ser el camino correcto hasta la primera
venta. Hasta ahora no figuraba como opción en ningún lado.

## 5.3 Gate

**"PCB propia: sí / no"** es una decisión con trigger explícito, no una
etapa asumida. Definir el trigger --- volumen, o un requisito mecánico o
de backlight que Track B no pueda cumplir --- y escribirlo acá.

------------------------------------------------------------------------

# 6. P2 --- Engineering Prototype

Aquí Glumi empieza a convertirse en hardware propio.

## Objetivo

Eliminar las development boards y construir la primera electrónica
diseñada específicamente para Glumi.

Resultado esperado:

``` text
Display
   │
   │ FFC
   ▼
┌───────────────────────┐
│     GLUMI PCB V0.1    │
│                       │
│ ESP32-S3              │
│ power                 │
│ USB-C                 │
│ backlight             │
│ programming/test      │
└───────────────────────┘
```

------------------------------------------------------------------------

# 7. Arquitectura electrónica P2

Basándonos en la investigación del reference design de Waveshare:

``` text
                 USB-C 5 V
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   3.3 V regulation       Backlight driver
          │                     │
          ▼                     │
 ESP32-S3-WROOM-1-N16R8         │
          │                     │
          │ RGB565              │
          │ PCLK                │
          │ HSYNC               │
          │ VSYNC               │
          │ DE                  │
          ▼                     ▼
       LCD FFC ───────────► 4.3" IPS
                              800×480
```

------------------------------------------------------------------------

# 8. Qué incluir en PCB V0.1

## Obligatorio

-   ESP32-S3-WROOM-1-N16R8.
-   Alimentación desde USB-C.
-   Regulación.
-   Conector de display.
-   Bus RGB565.
-   Backlight driver **con entrada de dimming PWM manejada directamente
    por un GPIO del ESP32-S3** (LEDC), capaz de bajar a ≤1--2 % de duty
    sin flicker visible. No repetir el esquema del reference design, que
    deja el backlight detrás del expansor CH422G en on/off.
-   Programación/debug.
-   Boot/reset accesible para ingeniería.
-   Test points.
-   Capacitores de desacople.
-   Protecciones necesarias.
-   Antenna keepout correcto.

## No incluir salvo evidencia

-   touch;
-   batería;
-   RTC;
-   microSD;
-   CAN;
-   RS485;
-   speaker;
-   temperatura;
-   haptics;
-   botones de producto;
-   sensores adicionales.

------------------------------------------------------------------------

# 9. Diseño de PCB

## Paso 1 --- Architecture diagram

Congelar:

-   power tree;
-   interfaces;
-   señales;
-   módulos.

## Paso 2 --- Pin allocation

Asignar GPIO para:

``` text
RGB565      16
PCLK         1
HSYNC        1
VSYNC        1
DE           1
backlight    1   (PWM real; ver 4.6 y sección 8)
USB          2
boot/reset
futuras señales reservadas si realmente corresponde
```

### Restricciones a aplicar ANTES de repartir pines

**1. GPIO 33--37 no existen para nosotros.**\
El N16R8 lleva PSRAM octal, que ocupa `GPIO 33--37` dentro del módulo
WROOM-1. Hay que descontarlos antes de empezar a asignar las ~22 señales
de arriba, no después de quedarse sin pines.

**2. Strapping pins.**\
La placa de Waveshare ruteó líneas de datos RGB sobre `GPIO0`, `GPIO45` y
`GPIO46`: los tres son strapping. Funciona porque el panel está pasivo en
boot, pero es una trampa. Regla para Glumi: **no poner líneas de datos en
strapping pins** (`0`, `3`, `45`, `46`) salvo que no haya alternativa, y
si no la hay, verificar que ningún pull externo altere el arranque.

## Paso 3 --- Schematic

Crear en KiCad.

Usar diseños de referencia de:

-   Espressif;
-   Waveshare;
-   fabricantes de componentes.

No copiar ciegamente una placa de desarrollo.

## Paso 4 --- BOM electrónica

Cada componente debe tener:

``` text
Reference
Manufacturer
MPN
Description
Quantity
Supplier
Unit cost
Alternative
Lifecycle
```

## Paso 5 --- PCB layout

Especial atención a:

-   antenna keepout;
-   display routing;
-   USB;
-   alimentación;
-   ground;
-   decoupling;
-   backlight;
-   connector placement;
-   mechanical holes.

## Paso 6 --- Engineering review

Antes de fabricar, hacer revisar:

-   schematic;
-   PCB;
-   BOM;
-   power;
-   ESP32 strapping;
-   USB;
-   RGB;
-   FFC;
-   backlight;
-   ESD;
-   manufacturabilidad.

------------------------------------------------------------------------

# 10. Fabricación PCB V0.1

No soldar manualmente toda la placa como requisito.

Enviar:

``` text
Gerbers
BOM
CPL / Pick-and-Place
```

a un fabricante/assembler.

### Cantidad inicial sugerida

``` text
3–5 unidades
```

Una puede fallar.

Una puede necesitar modificaciones.

Otra debe quedar disponible para comparación/test.

------------------------------------------------------------------------

# 11. Bring-up de PCB

Cuando llegan las primeras placas, no conectar todo inmediatamente.

Secuencia:

``` text
1. inspección visual
2. buscar shorts
3. verificar alimentación
4. verificar 3.3 V
5. conectar ESP32
6. flash firmware mínimo
7. verificar USB
8. verificar Wi-Fi
9. conectar display
10. probar patrón de colores
11. probar backlight
12. cargar UI Glumi
13. conectar backend
14. soak test
```

Cada etapa debe poder aislar fallos.

------------------------------------------------------------------------

# 12. P3 --- EVT / Production-Intent Prototype

**EVT: Engineering Validation Test**

## Objetivo

Construir unidades que representen la arquitectura real del producto.

Ahora Glumi debería tener:

``` text
display candidato real
+
PCB Glumi
+
carcasa representativa
+
frente/lente
+
USB-C
+
firmware real
```

Ya no debería depender de breadboard, Dupont ni dev board.

------------------------------------------------------------------------

# 13. Mecánica P3

Diseñar alrededor de componentes ya validados.

## Front assembly

Resolver:

-   display;
-   bezel;
-   optical window;
-   tolerancias;
-   adhesivo o retención mecánica;
-   polvo;
-   reflejos.

## Main body

Resolver:

-   PCB mounts;
-   display mounts;
-   airflow si corresponde;
-   cable routing;
-   antenna clearance.

## Rear cover

Debe permitir:

-   reparación;
-   inspección;
-   reemplazo de PCB;
-   reemplazo de display.

Preferir tornillos + inserts frente a pegado permanente.

## Base

Resolver:

-   estabilidad;
-   patas;
-   fricción;
-   inclinación.

------------------------------------------------------------------------

# 14. EVT testing

## Display

-   dead pixels;
-   uniformidad;
-   brillo;
-   minimum brightness;
-   viewing angle;
-   image persistence;
-   comportamiento nocturno.

## Connectivity

-   Wi-Fi débil;
-   pérdida de Wi-Fi;
-   cambio de router;
-   backend unavailable;
-   reconexión;
-   stale readings.

## Power

-   power cycle;
-   desconexión USB;
-   reconexión;
-   brownout;
-   arranque repetido.

## Thermal

Medir temperatura después de:

``` text
1 h
8 h
24 h
varios días
```

## Long-running

Dejar Glumi funcionando continuamente.

Objetivo inicial:

``` text
72 h
→
1 semana
→
varias semanas
```

Registrar:

-   crashes;
-   freezes;
-   Wi-Fi drops;
-   memory issues;
-   display artifacts.

------------------------------------------------------------------------

# 15. Firmware production-intent

El firmware deja de ser solamente un demo.

Debe contemplar:

-   boot;
-   Wi-Fi provisioning;
-   authentication;
-   backend;
-   glucose state;
-   freshness;
-   errors;
-   reconnect;
-   OTA;
-   logging;
-   firmware version;
-   factory reset;
-   safe failure.

## Principio crítico

Glumi sigue siendo un **display secundario**.

No debe convertirse en la única fuente de alertas o decisiones de
tratamiento.

------------------------------------------------------------------------

# 16. Onboarding

> ⚠️ **No resuelto --- ver la contradicción abierta en la sección 1.**
> Esta arquitectura asume backend propio y cuenta de usuario; el
> `product-brief.md` vigente los declara fuera de alcance y especifica
> configuración íntegramente on-device (AP + portal cautivo + QR + PIN).
> No implementar hasta decidir cuál manda.

En P3/P4 debe definirse el onboarding real.

Arquitectura candidata:

``` text
Glumi nuevo
   ↓
setup mode
   ↓
Glumi crea Wi-Fi/AP temporal
   ↓
teléfono se conecta
   ↓
usuario selecciona Wi-Fi
   ↓
Glumi guarda credenciales
   ↓
se conecta al backend
   ↓
muestra QR/pairing code
   ↓
usuario vincula cuenta/fuente
   ↓
backend asocia
user ↔ device ↔ glucose source
```

Las credenciales de Libre no deberían almacenarse directamente en el
ESP32.

------------------------------------------------------------------------

# 17. Backend production-intent

> ⚠️ **No resuelto --- ver la contradicción abierta en la sección 1.** El
> `product-brief.md` vigente declara "backend propio y cuenta de usuario"
> fuera de alcance. Si ese documento manda, esta sección se elimina, la
> sección 16 se reescribe sobre el portal on-device, y hay que resolver
> aparte de dónde sale el OTA que la sección 15 da por supuesto.

Separar:

``` text
DEVICE
↓
Glumi API
↓
Glucose provider integration
```

El dispositivo debería conocer solamente lo necesario para autenticarse
contra Glumi.

Backend debe manejar:

-   usuarios;
-   devices;
-   provider credentials/tokens;
-   association;
-   caching;
-   freshness;
-   rate limits;
-   errors;
-   security;
-   logging.

------------------------------------------------------------------------

# 18. P4 --- DVT / Design Validation Test

## Objetivo

Validar que el **diseño de producto**, no solamente la ingeniería, está
listo para ser fabricado repetidamente.

Aquí las decisiones deberían estar cerca de congelarse.

------------------------------------------------------------------------

# 19. DFM --- Design for Manufacturing

Trabajar con el fabricante para adaptar el diseño.

### Electrónica

-   panelization;
-   component availability;
-   assembly;
-   test points;
-   programming fixture;
-   tolerances;
-   alternatives.

### Mecánica

**Primero decidir el proceso, que depende del volumen (sección 5).** El
tooling de inyección sólo amortiza sobre miles de unidades; por debajo de
eso, MJF/SLS o vacuum casting dan piezas de calidad de producción sin
tooling y sin obligar a congelar el diseño tan temprano.

Si producción final utiliza injection molding:

-   draft angles;
-   wall thickness;
-   ribs;
-   bosses;
-   screw posts;
-   parting line;
-   ejector pins;
-   sink marks;
-   assembly order.

La carcasa impresa en 3D sirve para validar forma, pero **no debe
asumirse que el CAD se puede mandar directamente a inyección**.

------------------------------------------------------------------------

# 20. CMF

Definir:

**Color, Material, Finish**

Para cada parte:

``` text
front
body
rear cover
lens
feet
cable
```

Ejemplo:

``` text
Body
Material: TBD
Color: warm off-white
Finish: fine matte

Front lens
Material: PMMA / PC
Color: smoked black
Finish: gloss

Feet
Material: silicone
Color: dark neutral
```

Los valores exactos deben especificarse posteriormente mediante muestras
físicas y códigos apropiados.

------------------------------------------------------------------------

# 21. Supply chain

Para cada componente crítico:

``` text
Primary supplier
+
Alternative supplier
```

Prioridad especial:

-   display;
-   ESP32 module;
-   power components;
-   backlight driver;
-   USB-C connector;
-   optical lens.

## Para el display final

Obtener:

-   manufacturer;
-   MPN;
-   datasheet;
-   MOQ;
-   pricing tiers;
-   lead time;
-   lifecycle;
-   tolerances;
-   connector;
-   backlight specification;
-   second source.

**Pista de sourcing ya disponible:** la wiki del
`ESP32-S3-Touch-LCD-4.3B` identifica el driver del panel como
**ST7262**. Es el punto de partida concreto para buscar el mismo panel
directo con un ODM de displays, en vez de comprarlo por retail.

Waveshare puede ser proveedor/prototyping source sin necesariamente ser
el proveedor de producción.

------------------------------------------------------------------------

# 22. BOM maestra

Ejemplo:

  ID         Categoría   Componente              MPN           Qty Estado
  ---------- ----------- ----------------------- ----------- ----- -----------
  GL-E-001   MCU         ESP32-S3 module         TBD             1 Candidate
  GL-E-002   Display     4.3" IPS 800×480        TBD             1 Candidate
  GL-E-003   PCB         Glumi Main PCB          GL-PCB-01       1 Custom
  GL-E-004   USB         USB-C connector         TBD             1 TBD
  GL-M-001   Housing     Front housing           GL-F01          1 Custom
  GL-M-002   Housing     Rear housing            GL-R01          1 Custom
  GL-M-003   Lens        Smoked optical window   TBD             1 TBD
  GL-M-004   Feet        Silicone foot           TBD           TBD TBD

La BOM se vuelve una de las fuentes centrales de verdad del producto.

------------------------------------------------------------------------

# 23. Diseño para assembly

Antes de producción debemos poder describir inequívocamente:

``` text
1. instalar display
2. fijar display
3. instalar PCB
4. conectar FFC
5. asegurar FFC
6. instalar lens
7. cerrar housing
8. colocar tornillos
9. colocar feet
10. conectar USB
11. flash
12. test
13. package
```

Idealmente:

-   pocas piezas;
-   pocos tipos de tornillo;
-   conectores imposibles de invertir;
-   acceso simple;
-   sin adhesivos innecesarios;
-   bajo riesgo de dañar FFC.

------------------------------------------------------------------------

# 24. Fixture de programación y test

Para producir muchas unidades no queremos:

``` text
abrir Arduino IDE
↓
enchufar
↓
seleccionar puerto
↓
upload
```

Necesitamos un proceso repetible.

Ejemplo:

``` text
unidad entra al fixture
        ↓
contacta test points
        ↓
flash firmware
        ↓
device ID / provisioning
        ↓
display test
        ↓
Wi-Fi test
        ↓
backend test
        ↓
PASS / FAIL
```

Esto debe diseñarse junto con la PCB.

------------------------------------------------------------------------

# 25. Compliance y certificaciones

Antes de vender Glumi comercialmente se deberá investigar formalmente
qué certificaciones y requisitos aplican en los mercados objetivo.

Áreas a considerar:

-   radio/Wi-Fi;
-   EMC;
-   electrical safety;
-   USB/power supply;
-   materials;
-   environmental requirements;
-   labeling;
-   consumer electronics requirements;
-   tratamiento regulatorio derivado del posicionamiento y claims del
    producto.

## Argentina --- caminos concretos a investigar

"Investigar qué aplica" es demasiado genérico para el mercado objetivo
declarado. Verificar al menos:

-   **homologación ENACOM / RAMATEL** del equipo de radio Wi-Fi;
-   régimen de **seguridad eléctrica** aplicable a electrónica de consumo
    con fuente externa;
-   **clasificación ANMAT**, ya abierta como pregunta en layer-0 y
    bloqueante para comercializar;
-   condición de importador/fabricante y etiquetado correspondiente.

**No asumir que usar un ESP32 certificado hace automáticamente
certificado al producto completo.**

Esta etapa requiere asesoramiento especializado según país y estrategia
comercial.

------------------------------------------------------------------------

# 26. P5 --- PVT / Pilot Production

**PVT: Production Validation Test**

## Objetivo

Validar la fábrica y el proceso, no solamente Glumi.

Producir un lote pequeño utilizando el proceso que se pretende escalar.

Cantidad orientativa:

``` text
20–100 unidades
```

La cantidad real dependerá del fabricante, tooling, costos y estrategia.

------------------------------------------------------------------------

# 27. Qué validar en el piloto

## Yield

``` text
unidades fabricadas
vs.
unidades que pasan QA
```

## Assembly time

¿Cuánto tarda cada unidad?

## Failure modes

-   FFC dañado;
-   display defectuoso;
-   USB mal alineado;
-   PCB defectuosa;
-   carcasa no cierra;
-   rayones;
-   Wi-Fi falla;
-   firmware falla;
-   provisioning falla.

## Cosmetic QA

Definir qué significa:

``` text
PASS
FAIL
```

para:

-   gaps;
-   scratches;
-   color variation;
-   display alignment;
-   dust;
-   dead pixels;
-   wobble.

------------------------------------------------------------------------

# 28. Packaging

Diseñar cuando dimensiones y producto estén suficientemente congelados.

Debe contemplar:

``` text
Glumi
USB cable / power solution
quick start
regulatory information
protection
serial/device identification
```

Evitar diseñar packaging definitivo antes de congelar el producto.

------------------------------------------------------------------------

# 29. Golden Sample

Antes de producción masiva debemos tener una unidad aprobada que
represente:

> **Así debe verse, sentirse y funcionar Glumi.**

Esta unidad se convierte en referencia para:

-   color;
-   finish;
-   gaps;
-   alignment;
-   display;
-   brightness;
-   assembly;
-   comportamiento.

No reemplaza drawings/especificaciones, pero sirve como referencia
física complementaria.

------------------------------------------------------------------------

# 30. Production Release Package

Cuando Glumi esté listo, el fabricante debería recibir un paquete
completo.

## 30.1 Electronics

``` text
schematic
PCB source
Gerbers
drill files
stack-up
BOM
CPL / pick-and-place
assembly drawing
fabrication drawing
test-point map
programming instructions
```

## 30.2 Mechanical

``` text
STEP
2D drawings
tolerances
materials
finishes
colors
fasteners
lens drawings
assembly drawings
```

## 30.3 Firmware

``` text
production binary
version
flash procedure
provisioning procedure
OTA configuration
recovery procedure
```

## 30.4 QA

``` text
incoming inspection
PCB test
display test
Wi-Fi test
backend test
cosmetic criteria
final functional test
burn-in requirements
PASS/FAIL rules
```

## 30.5 Assembly

``` text
assembly sequence
tools
torque values where relevant
adhesives where relevant
fixtures
warnings
```

## 30.6 Packaging

``` text
box drawings
inserts
labels
serial number
manual
regulatory marks
shipping carton
```

------------------------------------------------------------------------

# 31. Qué significa finalmente "mandarlo a producir"

No sería:

> "Acá tenés mi prototipo. Copialo."

Sería:

``` text
                 GLUMI PRODUCT DEFINITION

                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
       ▼                 ▼                  ▼
  ELECTRONICS         MECHANICAL          SOFTWARE
       │                 │                  │
 schematic             CAD              firmware
 PCB                    drawings         backend
 BOM                    materials        provisioning
 CPL                    finishes         OTA
       │                 │                  │
       └─────────────────┼──────────────────┘
                         │
                         ▼
                   ASSEMBLY SPEC
                         │
                         ▼
                      QA SPEC
                         │
                         ▼
                    GOLDEN SAMPLE
                         │
                         ▼
                     FACTORY
```

Ese es el objetivo final del proceso.

------------------------------------------------------------------------

# 32. Qué deberíamos comprar nosotros mismos

La regla no es:

> comprar cada componente que tendrá Glumi.

La regla es:

> **prototipar físicamente cada decisión que tenga riesgo significativo
> para la experiencia, ingeniería o fabricación.**

## Comprar/probar físicamente

-   display candidato **integrado** (`ESP32-S3-LCD-4.3`) para P1;
-   módulo de display suelto, recién cuando haya PCB (P2);
-   dev electronics necesarias;
-   materiales de carcasa;
-   optical window;
-   fasteners;
-   inserts;
-   feet;
-   cables;
-   prototipos PCB;
-   muestras de acabado;
-   eventualmente componentes alternativos críticos.

## No es necesario comprar individualmente antes del PCB

-   cada resistor;
-   cada capacitor;
-   cada componente SMD estándar.

Estos se seleccionan en schematic/BOM y pueden ser ensamblados
directamente por el fabricante de PCB.

------------------------------------------------------------------------

# 33. Documentación viva durante todo el proyecto

Mantener:

``` text
/glumi
│
├── product/
│   ├── principles
│   ├── requirements
│   └── states
│
├── industrial-design/
│   ├── dimensions
│   ├── cad
│   ├── cmf
│   └── assembly
│
├── electronics/
│   ├── architecture
│   ├── schematic
│   ├── pcb
│   ├── bom
│   └── reference-designs
│
├── firmware/
│
├── backend/
│
├── testing/
│   ├── EVT
│   ├── DVT
│   └── PVT
│
├── manufacturing/
│   ├── suppliers
│   ├── quotes
│   ├── assembly
│   └── qa
│
└── releases/
```

Cada prototype debería tener versión.

Ejemplo:

``` text
Glumi P0
Glumi P1.0
Glumi PCB 0.1
Glumi EVT 0.1
Glumi EVT 0.2
Glumi DVT 1.0
Glumi PVT 1.0
```

------------------------------------------------------------------------

# 34. Gates de decisión

Regla: un gate hecho de adjetivos no es un gate. Sin deadline y sin
revisor externo, el número medido es lo único que impide avanzar por
entusiasmo.

## Gate P0 → P1

**Estado: PASS**

Concepto técnico validado.

## Gate P1 → P2

Requiere:

-   los exit criteria medibles de 4.8, con valores registrados;
-   dimming nocturno demostrado físicamente (4.6), no asumido;
-   decisión de form factor tomada y documentada;
-   **sección 5 completa**: volumen esperado, precio objetivo, costo BOM
    objetivo y presupuesto NRE;
-   **decisión Track A / Track B tomada y justificada**.

## Gate P2 → P3

Requiere:

-   PCB propia funcional, o carrier de Track B funcional;
-   power estable;
-   display estable, sin tearing ni drift con Wi-Fi activo;
-   dimming funcionando en todo el rango;
-   Wi-Fi estable;
-   firmware estable;
-   mechanical integration viable.

## Gate P3 → P4

Éste es el gate que autoriza gasto duro: tooling, fixtures e inventario.
Requiere, además de lo técnico:

-   EVT estable: **2 semanas continuas sin crash, freeze ni reboot**;
-   arquitectura congelada;
-   principales fallos resueltos;
-   supply chain plausible, con second source para display y módulo;
-   **contradicción backend / on-device resuelta** (sección 1);
-   **riesgo LibreLinkUp evaluado**: layer-0 lo documenta como
    dependencia de una API no oficial, sin contrato ni SLA, con riesgo
    legal comercial sin evaluar;
-   **clasificación ANMAT respondida**, o decisión explícita y escrita de
    avanzar sin esa respuesta.

No tiene sentido pagar tooling contra una fuente de datos que puede
cortarse y una clasificación regulatoria desconocida.

## Gate P4 → P5

Requiere:

-   DVT aprobado;
-   DFM aprobado **para el proceso realmente elegido**, no para inyección
    por defecto;
-   tooling/proceso listo;
-   QA definido con criterios PASS/FAIL numéricos;
-   compliance encaminado: ENACOM y seguridad eléctrica iniciados;
-   production files congelados.

## Gate P5 → Mass Production

Requiere:

-   pilot yield aceptable, con umbral declarado **antes** de fabricar;
-   QA aprobado;
-   assembly repetible;
-   provisioning repetible;
-   packaging aprobado;
-   problemas del piloto resueltos.

------------------------------------------------------------------------

# 35. Roadmap resumido

``` text
HOY
│
│ P0 ✓
│ Glucosa real en ILI9341
│
▼
MAQUETA 1:1 EN PAPEL
sin comprar nada
│
▼
P1
Waveshare ESP32-S3-LCD-4.3 (placa integrada)
+ hack de dimming de backlight
+ carcasa 3D
+ UI reescrita a 800×480
│
▼
EXIT CRITERIA MEDIDOS
│
▼
VOLUMEN + COSTO OBJETIVO
│
▼
DECISIÓN TRACK A / TRACK B
│
▼
P2
Glumi PCB V0.1 (A)  o  carrier mínima (B)
│
▼
BRING-UP
│
▼
P3 / EVT
Display + electrónica + enclosure
+ firmware + (backend u on-device: decidir)
│
▼
ENGINEERING VALIDATION
+ riesgo LibreLinkUp / ANMAT resueltos
│
▼
P4 / DVT
DFM del proceso realmente elegido
+ CMF + sourcing
│
▼
DESIGN FREEZE
│
▼
TOOLING / FIXTURES / QA
│
▼
P5 / PVT
20–100 unidades aprox.
│
▼
PILOT REVIEW
│
▼
PRODUCTION RELEASE
│
▼
MASS PRODUCTION
```

------------------------------------------------------------------------

# 36. Próximas acciones concretas

Partiendo del estado actual, el orden recomendado es:

### 0. Maqueta 1:1 en papel --- costo cero, antes de comprar nada

Imprimir la UI a escala real (**95,04 × 53,86 mm**), montarla sobre un
bloque y dejarla unos días en la mesa de luz y en el escritorio.

Valida tamaño tipográfico, distancia de lectura y escala del objeto ---
varios exit criteria de P1 --- sin gastar un peso. Es exactamente el
principio de "cada prototipo elimina una categoría de incertidumbre" con
el que cierra este documento.

### 1. Comprar la placa integrada, no el display suelto

`Waveshare ESP32-S3-LCD-4.3`, versión **sin touch** (~USD 28; la versión
con touch, ~USD 33).

El módulo retail SKU 16249 queda para P2, cuando exista una PCB donde
enchufarlo.

### 2. Resolver dimming de backlight

Sub-tarea explícita de P1 (ver 4.6). Sin esto el modo nocturno --- que de
noche es el producto entero --- no se puede evaluar.

### 3. Empezar BOM viva

Crear categorías:

``` text
Display
Electronics
Mechanical
Optical
Fasteners
Cable/Power
Packaging
```

Estados:

``` text
Reference only
Prototype
Candidate
Validated
Production
Rejected
```

### 4. Escribir volumen y costo objetivo

Completar la sección 5. Sin ese número no se puede decidir Track A vs.
Track B, ni proceso de carcasa, ni display de producción.

### 5. Resolver la contradicción backend / on-device

Decidir qué documento manda y actualizar el que pierda. Bloquea el diseño
de firmware, de onboarding y de OTA.

### 6. Medir el display real

No cerrar CAD final usando solamente dimensiones web.

Usar calibre y registrar:

-   módulo;
-   active area;
-   FFC;
-   espesor;
-   mounting points;
-   tolerancias relevantes.

### 7. Reescribir la UI a 800×480

Decidir LVGL vs. framebuffer directo, resolver render tipográfico a
tamaño grande y validar el render con Wi-Fi activo (`bounce buffer`,
bajada de PCLK).

### 8. Diseñar Glumi P1

CAD simplificado primero.

Validar escala antes de detalles.

### 9. Ejecutar P1 durante uso real

Mesa de luz.

Escritorio.

Living/cocina.

Día.

Noche.

Mínimo 72 h continuas registrando crashes, reconexiones y temperatura.

### 10. Documentar findings y medir los exit criteria

No avanzar a PCB sólo porque técnicamente enciende.

### 11. Congelar arquitectura electrónica V0.1

Basada en los resultados reales de P1 y en la decisión Track A / Track B.

### 12. Diseñar PCB / carrier V0.1

Con review externa antes de fabricación.

### 13. Fabricar 3--5 unidades

Comenzar P2.

------------------------------------------------------------------------

# 37. Norte del proyecto

Cada etapa debe acercarnos a un Glumi:

-   más simple;
-   más reproducible;
-   más confiable;
-   más fácil de ensamblar;
-   más fácil de testear;
-   con menos componentes innecesarios;
-   con una experiencia física más clara.

El objetivo no es que cada prototipo se vea terminado.

El objetivo es que cada prototipo **elimine una categoría de
incertidumbre**.

La secuencia correcta es:

> **probar → aprender → decidir → documentar → reducir incertidumbre →
> congelar → fabricar.**

Cuando lleguemos a producción, la fábrica no debería tener que
interpretar qué quisimos hacer.

Debería poder construir Glumi a partir de una definición inequívoca del
producto.
