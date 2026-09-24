---
title: "Glumi — plan detallado de prototipo a producción"
layer: product
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-22
relates_to:
  - layers/layer-1-product/latest/product-brief.md
  - layers/layer-0-business/latest/strategic-goals-and-constraints.md
  - layers/layer-3-architecture/latest/architecture-overview.md
---

# Glumi --- plan detallado de prototipo a producción

**Versión:** 0.3\
**Fecha:** 2026-09-22\
**Objetivo:** definir el camino desde el prototipo funcional actual de
Glumi hasta un producto reproducible y listo para fabricar.

## Cambios en 0.3

El producto que P1 tiene que validar cambió de forma, y eso mueve la mitad
técnica del plan:

-   **P1 pasa a 7"** (4.1). El objeto comercial mide ~183 × 114 mm y muestra
    hasta seis personas; validar la presencia física sobre un panel de 4.3"
    respondería una pregunta sobre un objeto que no se va a vender.
-   **4.2 UI P1 reescrita.** P1 incluye touch, configuración on-device y
    seguimiento de varias personas. La disciplina de "nada compite con el
    número" se mantiene; la prohibición de botones y navegación no.
-   **4.6:** el requisito de dimming se expresa como resultado medido y no
    como porcentaje de duty, que no transfiere entre paneles de distinto
    tamaño. La superficie emisora del 7" es ~2,6× la del 4.3".
-   **4.8 y Gate P1 → P2** incorporan touch, multi-persona y los cimientos
    de actualización remota.
-   **Contradicción backend / on-device resuelta** a favor de on-device
    (16 y 17). No hay cuenta de usuario ni backend de datos; sí hay
    distribución de firmware.

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

> **Backend: no de usuario, sí de firmware.**
>
> No hay cuenta de usuario, no hay asociación `user ↔ device` y ningún dato
> de glucosa pasa por infraestructura propia. Las credenciales de la fuente
> viven en el dispositivo y la configuración entera ocurre sobre su propia
> pantalla, sin app, sin portal web y sin otro dispositivo.
>
> La única infraestructura que el producto opera es la **distribución de
> firmware**, y existe solo para eso: binarios firmados más un manifiesto.
> Eso hace que las secciones 16 y 17 describan dos cosas distintas y no dos
> versiones de la misma — puesta en marcha en el objeto, actualización desde
> un endpoint — y ninguna de las dos requiere conocer quién es el usuario.

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

**7" IPS --- 800×480 preferido**

Target aproximado:

``` text
Área activa
≈ 154 × 86 mm

Diagonal
7" / ≈178 mm

Gabinete objetivo
≈ 183 × 114 × 25 mm
```

### Por qué 7" y no 4.3"

La altura del área activa es el recurso escaso, porque de ella sale el alto
de dígito y de ahí la distancia de lectura (altura ≈ distancia / 150 para
lectura cómoda de un vistazo):

  Personas en pantalla   4.3" (95×54)          7" (154×86)
  ---------------------- --------------------- ---------------------
  1                      ~32 mm → 4,8 m        ~32 mm → 4,8 m
  3 apiladas             ~13 mm → 2,0 m        ~18 mm → **2,7 m**
  6 en grilla 3×2        ~13 mm → 2,0 m        ~18 mm → **2,7 m**

A 4.3", seis personas caen a distancia de brazo: el objeto deja de leerse
desde el otro lado de la habitación y reconstruye la fricción que vino a
eliminar. A 7" el mismo layout se sostiene.

### Resolución: 800×480 antes que 1024×600

La legibilidad se mide en milímetros, no en píxeles — a 2,7 m nadie percibe
la diferencia entre 110 y 170 PPI. El ESP32-S3 sí: 1024×600 en RGB565 son
1,2 MB de framebuffer, obligado a PSRAM, y ~100 MB/s sostenidos sobre el bus
RGB, que está en el borde conocido del S3 y se paga con tearing o con bajar
el refresh. 800×480 es la mitad de todo para la misma lectura.

### Acabado de superficie

Con touch, la superficie de lectura es también superficie de contacto. En un
objeto siempre encendido, leído a 2,7 m y tocado a diario, las huellas son
pérdida de contraste sobre el único elemento que el producto existe para
mostrar. El acabado (glossy vs. antirreflejo) se confirma **antes de
comprar**: es lo único de esta lista que no se corrige por firmware.

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

El estado por defecto es la lectura, y es el único estado sin salida
explícita. Con una sola persona seguida:

``` text
        10:24

        112 →
        mg/dL
```

Con varias, el layout se adapta a la cantidad y el usuario elige cuáles se
muestran y en qué orden. El principio no se reparte: se repite. Cada persona
es un valor protagonista con su tendencia, y lo que compite no es una
glucosa contra otra sino todo lo demás contra todas ellas.

No incluir:

-   curva;
-   gráfica histórica;
-   rango;
-   delta;
-   countdown de próxima lectura;
-   mensajes de tranquilidad;
-   menú permanente en pantalla.

### Interacción táctil

P1 incluye touch porque la configuración vive entera en el dispositivo. El
invariante que ordena toda la interacción:

> El touch puede agregar capacidades, pero nunca puede quitar la que ya
> existe. Todo lo que el objeto hacía sin que lo tocaran tiene que seguir
> pasando mientras lo tocan.

En la práctica:

-   un toque cualquiera no navega; entrar a configuración pide un gesto
    deliberado sobre un target chico, nunca sobre el área del número;
-   la navegación vuelve sola al estado por defecto (~30 s), y el timeout se
    suspende mientras hay entrada de texto activa;
-   de noche el dispositivo despierta al nivel de brillo nocturno, nunca a
    brillo pleno: tocarlo no es una razón para subir el brillo;
-   el reset de fábrica vive detrás de un gesto largo y confirmado.

### Estados mínimos a validar

1.  steady;
2.  rising;
3.  falling;
4.  high;
5.  low;
6.  stale;
7.  unavailable;
8.  no connection;
9.  unconfigured (puesta en marcha desde cero, sin otro dispositivo);
10. varias personas con frescura distinta al mismo tiempo.

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
P1  →  placa integrada 7" con touch capacitivo

       panel IPS 7", 800×480 preferido
       ESP32-S3-WROOM-1-N16R8
       bus RGB565
       cero trabajo de interfaz

       verificar antes de comprar: LCD_BL en el
       esquemático, acabado de superficie,
       resolución real  (ver 4.1 y 4.6)
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

La placa de 7" es de la misma familia y presumiblemente comparte la
arquitectura. **Verificar dónde va `LCD_BL` en el esquemático de la placa
concreta antes de comprarla:** comprar sin mirar eso es comprar una placa
que no puede validar el exit criterion por el que P1 existe.

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
-   a qué duty deja de ser legible;
-   **a qué duty deja de iluminar el cuarto** --- que no es lo mismo, y con
    2,6× de superficie emisora los dos umbrales pueden cruzarse. Si se
    cruzan, el modo nocturno no cierra en 7" y hay que saberlo en P1.

### Requisito que esto fija para la PCB

El requisito se expresa como **resultado medido**, no como porcentaje de
duty: un porcentaje depende del backlight, del número de LEDs y del área
del panel, y por lo tanto no transfiere entre paneles de distinto tamaño.

> Legible a distancia de lectura en un cuarto totalmente a oscuras, sin
> iluminar el cuarto ni proyectar luz sobre superficies cercanas, y sin
> flicker perceptible a distancia de mesa de luz.

El duty mínimo que cumple eso es lo que P1 **mide**, y ese número medido es
el que entra en la sección 8 como requisito de la PCB. El driver debe
permitir dimming por PWM a frecuencia suficientemente alta --- o dimming
analógico --- para alcanzarlo sin flicker.

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
  Legibilidad            número + flecha legibles a **3 m** con 1 persona
  Legibilidad multi      legibles a **2,7 m** con 6 personas en grilla
  Legibilidad nocturna   legible en cuarto a oscuras sin iluminarlo
  Brillo mínimo          dimming estable, sin flicker visible
  Touch ambiental        **0** navegaciones accidentales en 72 h de uso real
  Retorno                vuelve al estado por defecto sin intervención
  Despertar nocturno     nunca sube a brillo pleno por un toque
  Puesta en marcha       configuración completa sin usar otro dispositivo
  Estabilidad            **72 h** continuas, 0 reboots, 0 freezes
  Reconexión             < **60 s** tras caída de Wi-Fi o router
  Render                 sin tearing ni drift con Wi-Fi activo
  Térmica                superficie < **45 °C** a las 24 h
  Image persistence      sin retención visible tras 72 h de UI estática
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
       LCD FFC ───────────► 7" IPS
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

## Tres decisiones irreversibles

Vencen al flashear la primera unidad que sale de casa, y después no se
pueden tomar:

1.  **Tabla de particiones con doble slot de aplicación** (`ota_0`,
    `ota_1`, `otadata`). En los 16 MB del N16R8 sobra espacio. Una unidad
    flasheada con tabla de app única no se puede actualizar nunca: la tabla
    de particiones no se reescribe por OTA de forma segura.
2.  **Esquema de configuración versionado** en NVS. Una unidad que guarda
    una credencial como singleton no puede migrar a varias fuentes sin
    perder la configuración del usuario. El número de versión cuesta cero
    hoy.
3.  **Par de claves de firma.** La clave pública va dentro del binario que
    se flashea. Sin firma, quien controle el endpoint --- o quien se quede
    con el dominio cuando venza --- empuja firmware arbitrario a un
    dispositivo donde alguien mira su glucosa.

## Camino de recuperación

Sin portal web y sin app, una actualización fallida deja al usuario sin
ninguna vía. Requiere: rollback automático --- la imagen nueva se marca
válida o el bootloader vuelve solo a la anterior --- y reset de fábrica
alcanzable desde el touch.

## Principio crítico

Glumi sigue siendo un **display secundario**.

No debe convertirse en la única fuente de alertas o decisiones de
tratamiento.

------------------------------------------------------------------------

# 16. Onboarding

La puesta en marcha ocurre entera sobre la pantalla del dispositivo. No
interviene ningún otro aparato:

``` text
Glumi nuevo
   ↓
pantalla de puesta en marcha
   ↓
elige red Wi-Fi de la lista
   ↓
tipea la contraseña en pantalla
   ↓
tipea credenciales de la cuenta seguidora
   ↓
elige a quién mostrar y en qué orden
   ↓
lectura
```

Cualquiera de esos pasos se rehace más tarde desde la misma pantalla sin
tocar el resto. Eso ataca de frente la falla más costosa documentada en la
categoría --- el cambio de teléfono que deja el dispositivo muerto y sin
camino de vuelta ---: un objeto que no necesita un teléfono para
configurarse no la tiene.

### Por qué no un portal cautivo

La detección de captive portal se comporta distinto en iOS, Android, macOS
y Windows, y es fuente crónica de "se conectó al AP y no se abrió nada". Es
la pieza más frágil del flujo y se puede borrar entera.

### Ergonomía y la contraseña de Wi-Fi

Un teclado en pantalla sobre 154 mm de ancho tiene teclas más grandes que
las de un teléfono: tipear no es el problema. El problema es la contraseña
de Wi-Fi --- aleatoria, con símbolos, sensible a mayúsculas, leída del
sticker del router --- sin gestor de contraseñas ni autocompletado. Se
muestra **en claro mientras se tipea**, con opción de ocultarla: el riesgo
de mirón en la propia casa es menor que el de tipear a ciegas.

### Almacenamiento de credenciales

Las credenciales de la fuente se guardan en el dispositivo, cifradas en
reposo, bajo el esquema versionado de la sección 15. No hay a dónde más
mandarlas: no existe backend de usuario.

------------------------------------------------------------------------

# 17. Backend production-intent

La única infraestructura que el producto opera es la distribución de
firmware:

``` text
DEVICE
↓  consulta manifiesto (HTTPS)
Manifiesto de versiones + binarios firmados
```

Eso es todo. No hay usuarios, no hay devices registrados, no hay tokens de
proveedor del lado servidor, no hay caching de glucosa y no hay asociación
`user ↔ device`. El dispositivo habla directo con la fuente de datos con
las credenciales que el usuario le cargó.

### Lo que sí hay que resolver

-   **Permanencia.** El endpoint tiene que sobrevivir al interés del
    fundador en el proyecto. Una unidad que consulta un endpoint muerto es
    una unidad que eventualmente se rompe sin arreglo. Es el primer
    compromiso operativo permanente que toma Glumi.
-   **Custodia de la clave privada.** Perderla deja a la flota sin poder
    actualizarse nunca más; filtrarla deja a la flota sin poder confiar en
    una actualización nunca más.
-   **Quién dispara la actualización.** Silenciosa es más calma, pero una
    actualización silenciosa en un objeto ligado al manejo de diabetes es
    una postura de riesgo que hay que elegir a propósito --- y nunca de
    madrugada sin explicar por qué la pantalla se apagó un minuto.

### Por qué esto importa más de lo que parece

La fuente de datos ya cambió su protocolo en silencio al menos dos veces
(`account-id`, `user-agent`). Cuando vuelva a hacerlo no falla una unidad:
fallan todas al mismo tiempo. La actualización remota no es la vía para
agregar features --- es la que hace posible vender.

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
  GL-E-002   Display     7" IPS 800×480          TBD             1 Candidate
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
-   dimming nocturno demostrado físicamente (4.6), no asumido, con el duty
    mínimo medido y escrito como requisito de la sección 8;
-   decisión de form factor tomada y documentada;
-   **los tres cimientos de actualización remota resueltos** (sección 15):
    tabla de particiones con doble slot de aplicación, esquema de
    configuración versionado y par de claves de firma generado. Son
    irreversibles en cuanto una unidad sale de casa;
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
-   **endpoint de distribución de firmware operativo**, con rollback
    automático y reset de fábrica probados en hardware;
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

### 0. Maqueta 1:1 en papel, en los dos tamaños --- antes de comprar nada

Imprimir la UI a escala real en **106 × 68 mm** y en **183 × 114 mm**,
montarlas sobre bloques y dejarlas unos días en la mesa de luz y en el
escritorio.

Resuelve gratis la decisión más cara del plan: si 7" resulta demasiado
objeto para el cuarto, la grilla de varias personas se cae sola por
aritmética (4.1) y hay que volver a 4.3" antes de gastar un peso. Valida
además tamaño tipográfico, distancia de lectura y escala --- varios exit
criteria de P1. Es el principio de "cada prototipo elimina una categoría de
incertidumbre" aplicado al costo cero.

### 1. Comprar la placa integrada, no el display suelto

Placa integrada de **7" con touch capacitivo**, preferentemente 800×480
(ver 4.1). El módulo retail suelto queda para P2, cuando exista una PCB
donde enchufarlo.

Verificar **antes de pagar**:

-   dónde va `LCD_BL` en el esquemático --- si cuelga del CH422G, el hack de
    dimming es trabajo obligatorio de P1 (4.6);
-   el acabado de superficie, glossy vs. antirreflejo (4.1);
-   la resolución real del panel.

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

### 5. Fijar los tres cimientos de actualización remota

Tabla de particiones, esquema de configuración versionado y par de claves
de firma (sección 15). No hace falta construir el cliente OTA todavía; sí
hace falta que la primera unidad flasheada pueda recibirlo después.

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
