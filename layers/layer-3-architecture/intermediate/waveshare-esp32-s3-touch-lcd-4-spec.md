---
title: "Waveshare ESP32-S3-Touch-LCD-4 — Especificación de la Placa Comprada (P1)"
layer: architecture
owner: "Matias Salzman"
status: draft
last_updated: 2026-09-22
relates_to:
  - layers/layer-3-architecture/intermediate/hardware-mvp-purchase-specs.md
  - layers/layer-3-architecture/intermediate/glumi-electronics-architecture-research.md
  - layers/layer-3-architecture/intermediate/waveshare-4.3inch-capacitive-touch-lcd-spec.md
  - layers/layer-1-product/latest/glumi-plan-prototipo-a-produccion.md
---

# Waveshare ESP32-S3-Touch-LCD-4 — Especificación de la Placa Comprada (P1)

Registro técnico de la placa integrada que sostiene **P1, el Prototipo de Arquitectura de Producto**. Es un artefacto intermedio: documenta lo que se compró y lo que hay que verificar al recibirlo, no evalúa alternativas.

La placa reemplaza el armado en protoboard de P0 y trae en una sola pieza el SoC, el panel, el táctil, el expansor de IO y la gestión de batería. Es un camino **Track B** —módulo comprado dentro del producto— en los términos de la sección 5.2 del plan.

---

## 1. Qué se compró

| | |
|---|---|
| **Producto** | ESP32-S3-Touch-LCD-4 |
| **Variante** | **Con táctil capacitivo** (la variante sin táctil es ESP32-S3-LCD-4) |
| **SKU Waveshare** | 28154 (la variante sin táctil es 30871) |
| **Fase** | P1 — Prototipo de Arquitectura de Producto |
| **Fecha de compra** | 2026-09-22 |

### Contenido de la caja

- Placa base ESP32-S3-Touch-LCD-4 × 1
- Bloque de terminales 10P, paso 3,5 mm, enchufe KA × 1
- Bloque de terminales 10P, paso 3,5 mm, enchufe × 1

**No incluye cable USB-C.** El paquete que Waveshare describe en su wiki lista además cable Type-C, tarjeta TF, conversor RS485 y adaptador CAN; el paquete comprado no los trae. Hay que conseguir un cable USB-C de datos (no solo de carga) antes de poder programar la placa.

---

## 2. Procesamiento y memoria

| Parámetro | Valor |
|---|---|
| Módulo | **ESP32-S3N16R8** |
| Procesador | Xtensa LX7 de 32 bits, doble núcleo |
| Frecuencia | hasta 240 MHz |
| Flash | **16 MB** |
| PSRAM | **8 MB** |
| SRAM interna | 512 KB |
| ROM | 384 KB |
| Wi-Fi | 2,4 GHz 802.11 b/g/n |
| Bluetooth | 5 (LE) |
| Antena | integrada en el módulo |

**Los 16 MB de flash son la razón por la que esta placa sirve.** La actualización remota necesita dos particiones de aplicación (`ota_0` / `ota_1`) más `otadata`, más NVS versionado. Con 16 MB eso entra con holgura; con 8 MB quedaría apretado y con 4 MB sería inviable sostener una app con LVGL y assets.

---

## 3. Pantalla

| Parámetro | Valor |
|---|---|
| Diagonal | 4" |
| Resolución | 480 × 480 |
| Tipo de panel | IPS |
| Interfaz | **RGB paralelo** |
| Color | 65K (RGB565) |
| Ángulo de visión | 160° |
| Controlador | **ST7701** |

La interfaz RGB paralela consume una cantidad grande de GPIO y comparte ancho de banda con la PSRAM. Es el mismo bus que documenta la investigación de electrónica para la familia de 4.3"/7", y la contención PSRAM ↔ Wi-Fi descripta ahí aplica igual acá.

---

## 4. Táctil

| Parámetro | Valor |
|---|---|
| Tecnología | Capacitivo |
| Controlador | **GT911** |
| Interfaz | I2C |
| Puntos | 5, con soporte de interrupción |
| Superficie | Vidrio templado |

El táctil es lo que reemplaza al portal web: las credenciales de LibreLinkUp se cargan en el dispositivo, durante el onboarding o en `settings → usuarios → agregar usuario`. Sin él, esa decisión de producto no se puede validar en P1.

---

## 5. Componentes a bordo

| # | Componente | Función |
|---|---|---|
| 1 | ESP32-S3N16R8 | Módulo SoC Wi-Fi + BLE, 240 MHz, 8 MB PSRAM / 16 MB Flash |
| 2 | **SW6106** | Chip de carga y descarga de batería |
| 3 | TJA1051T/3/1J | Transceptor CAN |
| 4 | SP3485H | Transceptor RS485 |
| 5 | **PCF85063** | Reloj de tiempo real (RTC) |
| 6 | **TCA9554PWR** | Expansor de IO |
| 7 | XL1509-ADJE1 | Fuente conmutada, entrada 7–36 V DC |
| 8 | Bornera 10P | Paso 3,5 mm |
| 9 | Conector de batería RTC | Solo baterías recargables |
| 10 | USB Type-C | Alimentación, grabación de firmware y log |
| 11 | Ranura micro SD | Almacenamiento externo |
| 12 | **Conector PH2.0** | Batería de litio 3,7 V, con carga y descarga |
| 13 | Buzzer | Periférico sonoro |
| 14 | Switches de terminación | CAN y RS485, apagados por defecto |
| 15 | LED indicador | Encendido |
| 16 | Botón BOOT | Modo de programación |
| 17 | Botón RESET | Reinicio |
| 18 | Botón BAT_PWR | Enciende/apaga cuando corre a batería |

### El expansor de IO no es el mismo que el de la familia 4.3"

Esta placa usa **TCA9554PWR**. La placa de 4.3" investigada anteriormente usa **CH422G**. Son integrados distintos de fabricantes distintos.

Todo lo que se concluyó sobre el control de brillo a partir del CH422G —que compuerta el backlight en encendido/apagado sin PWM— **no transfiere a esta placa** y hay que reverificarlo contra el TCA9554PWR. Es la incógnita abierta más importante de la lista de la sección 9.

### El RTC habilita comportamiento por horario sin red

El PCF85063 mantiene la hora aunque se corte el Wi-Fi o la alimentación. Eso permite un modo nocturno por franja horaria que no dependa de tener conectividad, y un registro de "hace cuánto que no hay dato" que sobreviva a un reinicio.

---

## 6. Alimentación

| Vía | Rango |
|---|---|
| USB Type-C | 5 V |
| Entrada DC (bornera) | 7–36 V |
| Batería de litio | 3,7 V vía conector PH2.0, con carga y descarga por SW6106 |

**La placa trae gestión de batería de fábrica.** El chip SW6106, el conector PH2.0 y el botón BAT_PWR están a bordo. No cambia la conclusión de que un LCD RGB siempre encendido no se sostiene con batería —el panel consume mientras muestra algo— pero sí permite **medir** el consumo real sin comprar nada más, y permite probar respaldo ante corte de luz.

Rango de temperatura de operación: **0 °C a 65 °C**.

---

## 7. Dimensiones

| Medida | Valor |
|---|---|
| Módulo completo | **84,2 × 84,2 mm** |
| Área activa | **71,86 × 70,18 mm** |
| Marco horizontal (total) | 12,34 mm |
| Marco vertical (total) | 14,02 mm |
| Peso del paquete | ~50 g |

### El área activa no es cuadrada

El módulo es cuadrado (84,2 × 84,2 mm) pero el área activa mide **71,86 mm de ancho por 70,18 mm de alto** — 1,68 mm de diferencia sobre 480 × 480 píxeles.

Consecuencia: el píxel no es cuadrado. Paso horizontal 0,1497 mm, paso vertical 0,1462 mm, una anisotropía de **~2,4%**. Un círculo dibujado con 480 × 480 píxeles se ve 2,4% más ancho que alto. Es poco, pero es visible en una forma circular grande y hay que compensarlo en el diseño, no descubrirlo después.

El marco vertical también es asimétrico: en el plano, el borde negro superior es visiblemente más grueso que los laterales. El área activa no queda centrada en el módulo, y el gabinete tiene que tenerlo en cuenta para que el número no quede ópticamente corrido hacia abajo.

---

## 8. Legibilidad

Densidad: 480 px sobre 70,18 mm = **6,84 px/mm ≈ 174 DPI**.

Aplicando la regla de altura de carácter ≈ distancia / 150 (vistazo cómodo) y / 200 (mínimo legible):

| Altura del número | % del alto útil | Píxeles | Cómodo | Mínimo |
|---|---|---|---|---|
| 28 mm | 40% | 192 px | 4,2 m | 5,6 m |
| 35 mm | 50% | 240 px | 5,3 m | 7,0 m |
| **42 mm** | **60%** | **287 px** | **6,3 m** | **8,4 m** |
| 49 mm | 70% | 335 px | 7,4 m | 9,8 m |

A 60% del alto útil el número se lee cómodo desde 6,3 m. Un dormitorio típico es de 3 a 4 m: sobra margen, incluso para una persona con visión reducida o sin anteojos al despertarse.

---

## 9. ⚠️ Preguntas abiertas — verificar al recibir

| # | Pregunta | Por qué importa | Cómo se responde |
|---|---|---|---|
| 1 | **¿El backlight admite atenuación, o solo encendido/apagado?** | Es la incógnita crítica. Un objeto de mesa de luz que solo puede estar a brillo pleno o apagado no sirve de noche. | Leer el esquemático de la placa y el mapeo del TCA9554PWR. Si el brillo cuelga del expansor, no hay PWM; si cuelga de un GPIO del S3, sí. |
| 2 | **¿A qué brillo deja de iluminar el cuarto?** | Define si el producto se puede dejar encendido toda la noche. | Medición subjetiva a oscuras, con la placa a distintos niveles. |
| 3 | **¿Cuánto consume, por nivel de brillo?** | Alimenta la decisión de batería/respaldo y el dimensionado de la fuente. | Medidor USB en línea, registrando W a brillo 0/25/50/100%. |
| 4 | **¿Hay contención PSRAM ↔ Wi-Fi visible?** | Riesgo documentado en esta familia de placas: el bus RGB y la PSRAM compiten, y una transferencia Wi-Fi puede producir tearing. | Redibujar la pantalla mientras corre una descarga HTTPS y observar. |
| 5 | **¿Aparece retención de imagen con un número estático 24/7?** | El caso de uso muestra los mismos dígitos durante horas. | Prueba de permanencia larga, comparando contra una pantalla limpia. |
| 6 | **¿La anisotropía de píxel es real o redondeo del plano?** | Define si hay que compensar en el diseño de UI. | Dibujar un círculo de 400 px y medirlo con calibre. |
| 7 | **¿Qué layout de particiones conviene para OTA en 16 MB?** | La actualización remota es un compromiso operativo permanente, no una feature. | Definir `ota_0` / `ota_1` / `otadata` + NVS versionado antes de escribir la primera app. |

---

## 10. Checklist de verificación al recibir

1. Confirmar que el módulo serigrafiado es **ESP32-S3N16R8** y no otra variante. Leer la serigrafía del módulo y corroborar por firmware con `esp_flash_get_size()` y la lectura de PSRAM.
2. Confirmar que el táctil responde — es la variante con GT911, no la placa sin táctil.
3. Conseguir cable **USB-C de datos**; no viene en el paquete.
4. Correr el demo de fábrica antes de tocar nada, para tener una referencia de "la placa funciona".
5. Medir el área activa real con calibre y contrastarla contra los 71,86 × 70,18 mm del plano.
6. Resolver la pregunta 1 de la sección 9 antes de avanzar con cualquier trabajo de UI nocturna.

---

## 11. Limitación de origen de los datos

`waveshare.com` bloquea el acceso automatizado a sus páginas de producto y wiki (HTTP 403 intermitente; la página de producto responde 403 de forma consistente, y la wiki respondió una vez y luego bloqueó los reintentos). En consecuencia:

- Las especificaciones de las secciones 2 a 8 provienen de la ficha del vendedor, del diagrama anotado de 18 componentes del fabricante, del plano dimensional, y de una lectura exitosa de la wiki de Waveshare.
- **El mapeo de pines GPIO no está documentado acá.** Hay que extraerlo de `waveshare.com/wiki/ESP32-S3-Touch-LCD-4` desde un navegador, o del esquemático que Waveshare publica en esa misma página, y agregarlo a este documento.
- Nada de lo que figura acá está confirmado contra hardware físico. Todo pasa a estado verificado recién con la placa en la mano.

---

## 12. Fuentes

- Ficha del listado de compra (vendedor) — parámetros, contenido del paquete, dimensiones de producto
- Diagrama anotado de componentes del fabricante — los 18 ítems de la sección 5
- Plano dimensional del fabricante — medidas de módulo y área activa
- `waveshare.com/wiki/ESP32-S3-Touch-LCD-4` — ST7701, GT911, contenido de paquete Waveshare
- `waveshare.com/esp32-s3-touch-lcd-4.htm` — SKU 28154 (con táctil) / 30871 (sin táctil)
