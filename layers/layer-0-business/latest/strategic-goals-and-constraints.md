---
title: "Strategic Goals and Constraints"
layer: business
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-15
relates_to:
  - layers/layer-0-business/latest/business-overview.md
  - layers/layer-0-business/latest/competitive-landscape.md
  - layers/layer-0-business/latest/stakeholder-map.md
---

# Strategic Goals and Constraints

Qué busca lograr el proyecto, por qué existe y qué limita lo que se puede hacer. Captura las fuerzas que condicionan toda decisión aguas abajo, desde la definición de producto hasta la arquitectura y la entrega.

---

## Strategic Goals

Los objetivos están ordenados por prioridad. Los dos primeros son lo que el MVP tiene que demostrar; el tercero es la puerta a los dos últimos.

- **Validar que el objeto físico cambia el comportamiento.** El criterio observable es que el fundador reduzca de forma medible sus consultas de glucosa en el teléfono mientras está en casa, durante un período de uso continuo. Es el objetivo de mayor prioridad: si el objeto no baja el chequeo, ningún otro resultado importa.

- **Que el dato sea confiable sin ambigüedad.** El usuario siempre debe saber si el número que ve está fresco, desactualizado o caído, sin tener que deducirlo. El criterio es que no se tome ninguna decisión sobre un dato viejo creyéndolo actual. Esto no es una funcionalidad sino una condición de existencia: un display ambiental en el que no se puede confiar es peor que no tener display.

- **Demostrar demanda más allá del usuario cero.** El criterio es que otras personas con T1D que ven el objeto pidan uno. Es la señal que separa un proyecto personal de un producto, y el único mecanismo que rompe el sesgo de diseñar para uno mismo.

- **Sentar la base de una marca de objetos cotidianos para T1D.** El display es el producto de entrada. La identidad, el lenguaje visual y el posicionamiento tienen que poder extenderse a otros problemas físicos de vivir con T1D —transporte de insulina, organización de suministros, tratamiento de hipoglucemias, accesorios de CGM, viaje, deporte— sin quedar atados a la glucosa.

- **Llegar a un producto comercializable en Argentina.** Horizonte de 12 a 18 meses. Depende de resolver tres cosas que hoy no están resueltas: modelo de negocio, postura regulatoria y manufactura.

---

## Engagement Drivers

El proyecto existe porque el fundador vive con diabetes tipo 1 y consulta su glucosa decenas de veces por día. El CGM eliminó la fricción de medir pero no la de mirar: el dato sigue viviendo dentro del teléfono y exige una interacción activa cada vez. Lo que se busca es sacarlo de la pantalla personal y ponerlo en el espacio físico.

No hay evento disparador externo, cliente que lo pida ni fecha que lo empuje. Lo que lo vuelve posible ahora es que la tecnología para construirlo está al alcance —ESP32 con display, impresión 3D, APIs de CGM accesibles— y que la categoría ya está validada internacionalmente sin ninguna oferta local.

Si no se hace nada, no pasa nada: el fundador sigue mirando el celular y la ventana local eventualmente la ocupa un importador o un competidor internacional. La urgencia es de oportunidad, no de crisis. Esa ausencia de presión es en sí misma un riesgo, porque un proyecto sin deadline compite por el tiempo del único integrante contra todo lo demás.

---

## Constraints

| Type | Description | Rigidity |
| ---- | ----------- | -------- |
| Regulatory | ANMAT regula productos médicos en Argentina. El producto se plantea como visualización secundaria no médica, con el CGM del fabricante como referencia terapéutica, pero su clasificación no fue evaluada. No aplica al uso personal; es bloqueante para comercializar. ⚠️ | Hard al comercializar |
| Regulatory | El acceso al dato depende de LibreLinkUp, una API no oficial de Abbott: sin contrato, sin SLA y sin permiso. El riesgo legal de usarla con fines comerciales no fue evaluado. ⚠️ | Hard |
| Timeline | No hay deadline ni fecha objetivo. El proyecto avanza al ritmo del tiempo disponible. | Soft |
| Organizational | Una sola persona cubre todos los roles, en tiempo discontinuo (noches y fines de semana). No hay forma de paralelizar trabajo ni de cubrir una ausencia. | Hard |
| Organizational | Sin experiencia previa en hardware, diseño industrial, materiales ni manufactura. Es, además, el área donde reside la diferenciación defendible que el proyecto necesita construir. | Hard |
| Organizational | Sin proceso de revisión ni contrapeso interno. Toda validación debe venir de fuera del proyecto. | Soft |
| Technical | Hardware off-the-shelf: ESP32 con display integrado. Sin PCB propia y sin batería; alimentación continua por USB-C. Enclosure por impresión 3D. | Hard en el MVP |
| Technical | Una única fuente de datos: LibreLinkUp. Dexcom y Nightscout quedan fuera del alcance del MVP. Sin plan alternativo si el acceso se corta. | Hard en el MVP |
| Technical | Requiere Wi-Fi doméstico. Sin conectividad celular ni vínculo directo con el sensor: el producto solo funciona en espacios con una red conocida. | Hard |
| Technical | El producto no mide glucosa, no administra insulina y no reemplaza las alarmas oficiales del CGM. Fuera de alcance: app móvil compleja, análisis avanzado de glucosa y recomendaciones terapéuticas. | Hard |
| Budget | Autofinanciado, sin inversión externa. El capital no limita el MVP, construido con componentes existentes e impresión 3D. Sí será limitante para tooling, certificación e inventario de un lote de producción, y ese costo todavía no fue dimensionado. ⚠️ | Soft en el MVP / Hard en producción |

---

## Open Questions

| Question | Why it matters | Owner |
| -------- | -------------- | ----- |
| ¿Cuál es el modelo de negocio y el precio objetivo? | Condiciona decisiones de BOM y manufactura desde ahora, no después. Referencia de mercado: un SugarPixel importado ronda los USD 200-300. | Matias Salzman |
| ¿Cómo clasifica ANMAT a este producto? | Bloquea la comercialización. No afecta al MVP de uso personal. | Matias Salzman |
| ¿Cuál es el riesgo real de depender comercialmente de LibreLinkUp, y cuál es el plan si se corta? | Es la única fuente de datos del producto y no hay alternativa definida. | Matias Salzman |
| ¿Alcanza la presencia local como diferenciación? | Es replicable por cualquier importador, y SugarHalo ya cubre Libre y el lenguaje ambiental. Si la respuesta es no, la ventaja debe construirse en diseño industrial y marca — el área de menor experiencia del proyecto. | Matias Salzman |
| ¿Cuál es el nombre y la identidad de la marca? | Tiene que poder sostener más de un producto sin quedar atada a la glucosa. | Matias Salzman |
| ¿Cómo y cuándo se valida con otras personas con T1D? | Es criterio de éxito del MVP y el único mecanismo que rompe el sesgo de diseñar para uno mismo. Hoy no está planificado. | Matias Salzman |
