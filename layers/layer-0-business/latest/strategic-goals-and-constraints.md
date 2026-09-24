---
title: "Strategic Goals and Constraints"
layer: business
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-22
relates_to:
  - layers/layer-0-business/latest/business-overview.md
  - layers/layer-0-business/latest/competitive-landscape.md
  - layers/layer-0-business/latest/stakeholder-map.md
---

# Strategic Goals and Constraints

Qué busca lograr el proyecto, por qué existe y qué limita lo que se puede hacer. Captura las fuerzas que condicionan toda decisión aguas abajo, desde la definición de producto hasta la arquitectura y la entrega.

---

## La apuesta

Glumi no apuesta a inventar una categoría. Apuesta a **ejecutar bien una categoría que ya existe, está validada internacionalmente y no se vende acá.**

El formato no requiere prueba: un display de escritorio que muestra número y tendencia, para una o varias personas seguidas desde una cuenta de CGM, es un producto que existe, que funciona y que la gente compra. Lo que ningún competidor resolvió es lo que rodea al formato, y es exactamente donde cae la crítica sustantiva de la categoría: Wi-Fi que se cae, acoplamiento frágil a la cuenta del CGM, configuración que muere cuando el usuario cambia de teléfono, producto que queda obsoleto cuando la fuente de datos cambia el protocolo, y soporte que no contesta. Ninguna de esas cuatro es estética, y las cuatro son terreno de ingeniería y de operación — el único de los terrenos disputados donde el proyecto ya tiene la competencia necesaria.

El diseño industrial y la presencia física del objeto son **condición de entrada, no diferenciación**: un objeto que nadie quiere tener sobre la mesa de luz no se compra, pero tampoco se defiende solo por ser lindo. La calma es una decisión de comportamiento del producto, no la ventaja competitiva.

La consecuencia incómoda de esto está en Open Questions y es la pregunta más importante del documento: si la apuesta es ejecución y distribución, la ventaja es **replicable por cualquiera dispuesto a ejecutar igual de bien**. El foso no es el producto, es la operación sostenida.

---

## Strategic Goals

Los objetivos están ordenados por prioridad.

- **Que el dato sea confiable sin ambigüedad.** El usuario siempre debe saber si el número que ve está fresco, desactualizado o caído, sin tener que deducirlo. El criterio es que no se tome ninguna decisión sobre un dato viejo creyéndolo actual. Esto no es una funcionalidad sino una condición de existencia: un display ambiental en el que no se puede confiar es peor que no tener display. Con la apuesta declarada arriba, este objetivo no es uno más: **es el producto**.

- **Que el producto siga funcionando después de vendido.** La fuente de datos es una API no oficial que ya cambió de protocolo en silencio al menos dos veces. Cuando vuelva a cambiar, no falla una unidad: fallan todas al mismo tiempo. La capacidad de actualizar el software de una unidad que está en la casa de otra persona no es una feature, es la condición que hace posible vender. Sin ella, cada unidad vendida es un ladrillo con fecha.

- **Validar que el objeto físico cambia el comportamiento.** ✅ **Validado sobre el usuario cero.** Con uso continuo, el dato presente desplazó la consulta al teléfono. La validación es n=1 y eso es exactamente lo que cubre: establece que el mecanismo funciona, no que funcione para otra persona ni que siga funcionando a los seis meses. El riesgo del proyecto se corrió de lugar: ya no está en si la idea sirve, sino en si generaliza y si dura.

- **Demostrar demanda más allá del usuario cero.** El criterio es que otras personas con T1D que ven el objeto pidan uno. Es la señal que separa un proyecto personal de un producto, y el único mecanismo que rompe el sesgo de diseñar para uno mismo. **Las primeras unidades se entregan, no se venden**: eso da la señal sin cruzar ninguna línea regulatoria ni comercial, y obliga igual a construir la configuración real y la actualización remota, que es lo que de verdad hace falta probar.

- **Llegar a un producto comercializable en Argentina.** Horizonte de 12 a 18 meses. Depende de resolver tres cosas que hoy no están resueltas: modelo de negocio, postura regulatoria y manufactura.

- **Sentar la base de una marca de objetos cotidianos para T1D.** Sigue siendo la ambición de largo plazo y sigue condicionando identidad y lenguaje visual. **No es la ventaja defendible** — esa función la ocupa la ejecución sostenida. Es aspiración, no foso.

---

## Engagement Drivers

El proyecto existe porque el fundador vive con diabetes tipo 1 y consulta su glucosa decenas de veces por día. El CGM eliminó la fricción de medir pero no la de mirar: el dato sigue viviendo dentro del teléfono y exige una interacción activa cada vez. Lo que se busca es sacarlo de la pantalla personal y ponerlo en el espacio físico.

No hay evento disparador externo, cliente que lo pida ni fecha que lo empuje. Lo que lo vuelve posible ahora es que la tecnología para construirlo está al alcance —ESP32 con display, impresión 3D, APIs de CGM accesibles— y que la categoría ya está validada internacionalmente sin ninguna oferta local.

Si no se hace nada, no pasa nada: el fundador sigue mirando el celular y la ventana local eventualmente la ocupa un importador o un competidor internacional. La urgencia es de oportunidad, no de crisis. Esa ausencia de presión es en sí misma un riesgo, porque un proyecto sin deadline compite por el tiempo del único integrante contra todo lo demás.

---

## Constraints

| Type | Description | Rigidity |
| ---- | ----------- | -------- |
| Regulatory | ANMAT regula productos médicos en Argentina. El producto se plantea como visualización secundaria no médica, con el CGM del fabricante como referencia terapéutica, pero su clasificación no fue evaluada. No aplica al uso personal ni a unidades entregadas sin cobrar; es bloqueante para vender. ⚠️ | Hard al comercializar |
| Regulatory | El acceso al dato depende de LibreLinkUp, una API no oficial de Abbott: sin contrato, sin SLA y sin permiso. El riesgo legal de usarla con fines comerciales no fue evaluado, y vender convierte el problema de personal en flota. ⚠️ | Hard |
| Timeline | No hay deadline ni fecha objetivo. El proyecto avanza al ritmo del tiempo disponible. | Soft |
| Organizational | Una sola persona cubre todos los roles, en tiempo discontinuo (noches y fines de semana). No hay forma de paralelizar trabajo ni de cubrir una ausencia. El cuello de botella real es hardware, industrial design y manufactura; el software no lo es. | Hard |
| Organizational | Sin experiencia previa en hardware, diseño industrial, materiales ni manufactura. | Hard |
| Organizational | Sin proceso de revisión ni contrapeso interno. Toda validación debe venir de fuera del proyecto. | Soft |
| Technical | Hardware off-the-shelf hasta P2: placa ESP32-S3 integrada con display de 7" y touch capacitivo. Sin PCB propia y sin batería; alimentación continua por USB-C. Enclosure por impresión 3D. | Hard hasta P2 |
| Technical | **El control de brillo no viene resuelto en la placa integrada.** En las placas de referencia de Waveshare el backlight cuelga del expansor CH422G y es on/off, sin PWM. El modo nocturno es alcance de producto, así que habilitar dimming es trabajo obligatorio de P1, no una mejora. El requisito se especifica como resultado medido —legible a distancia de lectura sin iluminar el cuarto— y no como porcentaje de duty, que no transfiere entre paneles de distinto tamaño. | Hard |
| Technical | Una única fuente de datos hasta el lanzamiento: LibreLinkUp. Nightscout se difiere a post-venta y se entrega por actualización remota. Dexcom queda descartado por ahora: su API oficial sirve datos diferidos e inservibles para un display en tiempo real, y la alternativa (Share) duplicaría la exposición a una dependencia no oficial sin haber resuelto la primera. | Hard hasta el lanzamiento |
| Technical | El seguimiento de varias personas está acotado a **las que una misma cuenta de LibreLinkUp ya sigue**. No se agregan personas cargando credenciales distintas en el dispositivo: eso es una arquitectura diferente y la fuerza únicamente el soporte de Nightscout, que es posterior. | Hard hasta el lanzamiento |
| Technical | **La actualización remota crea un compromiso operativo permanente:** un servidor con los binarios y un manifiesto, alcanzable por cada unidad vendida, que tiene que sobrevivir al interés del fundador en el proyecto. Fija además tres decisiones irreversibles al momento de flashear la primera unidad — tabla de particiones con doble slot, esquema de configuración versionado y par de claves de firma. | Hard |
| Technical | Requiere Wi-Fi doméstico. Sin conectividad celular ni vínculo directo con el sensor: el producto solo funciona en espacios con una red conocida. | Hard |
| Technical | El producto no mide glucosa, no administra insulina y no reemplaza las alarmas oficiales del CGM. Fuera de alcance: app móvil, backend de datos de usuario, análisis avanzado de glucosa y recomendaciones terapéuticas. | Hard |
| Budget | Autofinanciado, sin inversión externa. El capital no limita las fases de prototipo. Sí será limitante para tooling, certificación e inventario de un lote de producción, y ese costo todavía no fue dimensionado. ⚠️ | Soft en prototipo / Hard en producción |

---

## Open Questions

| Question | Why it matters | Owner |
| -------- | -------------- | ----- |
| Si la apuesta es ejecución y distribución, ¿qué la vuelve defendible? | Es la pregunta que reemplaza a "¿alcanza la presencia local como diferenciación?", y es más dura: ejecutar bien es replicable por cualquiera dispuesto a ejecutar igual de bien. El foso, si existe, es operación sostenida y soporte — no producto. Sin respuesta, el plan de 12-18 meses no tiene defensa. | Matias Salzman |
| ¿Cuál es el modelo de negocio y el precio objetivo? | Condiciona decisiones de BOM y manufactura desde ahora. El ancla internacional de la categoría está entre USD 60 y 85; un SugarPixel importado ronda los USD 200-300. El paso a 7" con touch mueve el BOM hacia arriba y el margen hacia abajo. | Matias Salzman |
| ¿Cómo clasifica ANMAT a este producto? | Bloquea la venta. No afecta a las unidades entregadas sin cobrar. | Matias Salzman |
| ¿Cuál es el riesgo real de depender comercialmente de LibreLinkUp, y cuál es el plan si se corta? | Es la única fuente de datos. La actualización remota mitiga el caso "cambió el protocolo" pero no el caso "Abbott cierra el acceso". | Matias Salzman |
| ¿Quién custodia la clave de firma, y qué pasa con las unidades vendidas si el proyecto se detiene? | Perder la clave deja a la flota sin poder actualizarse nunca más; filtrarla deja a la flota sin poder confiar en una actualización nunca más. Y un endpoint de actualización que muere es una unidad que eventualmente se rompe sin arreglo. | Matias Salzman |
| ¿Cuál es el nombre y la identidad de la marca? | Tiene que poder sostener más de un producto sin quedar atada a la glucosa. | Matias Salzman |
| ¿Cómo y cuándo se entregan las primeras unidades a otras personas con T1D? | Es criterio de éxito y el único mecanismo que rompe el sesgo de diseñar para uno mismo. Hoy no está planificado. | Matias Salzman |
