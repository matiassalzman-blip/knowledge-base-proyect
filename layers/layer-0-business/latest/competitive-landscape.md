---
title: "Competitive Landscape"
layer: business
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-15
relates_to:
  - layers/layer-0-business/latest/business-overview.md
  - layers/layer-0-business/latest/strategic-goals-and-constraints.md
---

# Competitive Landscape

Dónde se para el proyecto en su mercado, contra quién compite y qué fuerzas moldean la categoría. Documento de referencia: se consulta al idear, al posicionar y al decidir estrategia, no todos los días.

---

## Market Position

Entrante nuevo: sin producto en mercado, sin ventas y sin marca. La categoría de display secundario de CGM no hay que crearla —existe y está poblada internacionalmente—, pero en Argentina no tiene oferta local.

La posición buscada no es inventar una categoría sino ser el primero en atenderla localmente con calidad de producto de consumo. El mercado primario es Argentina, donde FreeStyle Libre domina la penetración de CGM. El horizonte declarado es regional, y es lo que justifica que la identidad de marca deba poder trascender a este único dispositivo.

Conviene nombrar el tamaño real: personas con T1D que usan CGM, con familiaridad tecnológica y espacios de uso fijos, es un subconjunto pequeño y disperso de un mercado ya chico.

---

## Key Competitors

El competidor más fuerte no está en la tabla: es el teléfono que el usuario ya tiene en el bolsillo. Tiene costo marginal cero, está siempre presente y ya muestra el dato. Todo producto de esta categoría compite contra el hábito de desbloquearlo, no contra otro dispositivo.

Dentro de la categoría, la oferta se ordena en dos polos: los que muestran el número y priorizan la alerta, y los que muestran solo color y priorizan la calma. Este proyecto se propone en el medio —número y tendencia legibles, comportamiento silencioso— que es exactamente donde ya está SugarHalo.

| Competitor | What They Offer | Strengths | Weaknesses |
| ---------- | --------------- | --------- | ---------- |
| SugarPixel / SugarPixel+ / Mini (CustomTypeOne, EE.UU.) | Display LED dedicado de ~3×7", Wi-Fi. Número y tendencia, alertas sonoras con tonos variables, alertas por vibración, puck vibrador para poner bajo la almohada, modo reloj con color dinámico y seguimiento multiusuario (hasta 4 perfiles en el +). | Producto maduro y feature-complete. Distribución internacional vía retailers de diabetes. Muy fuerte en el caso de uso nocturno y en cuidadores. | Es alert-first, no calm: un sistema de alarmas con display incorporado. Estética de gadget técnico. Sin presencia, garantía ni soporte en Argentina; importado ronda los USD 200-300. |
| SugarHalo (EE.UU.) | Display de mesa de luz con pantalla ambiental suave. Se conecta por Wi-Fi a Dexcom, Libre o Nightscout. Configuración única, sin sensor nuevo y sin suscripción adicional. | El competidor más cercano: mismo usuario, mismo contexto de uso, mismo lenguaje de "ambiental" y "de un vistazo". Ya soporta Libre. | Ocupa la posición que este proyecto plantea como oportunidad. Sin presencia en Argentina. |
| Glowcose | Luz ambiental que cambia de color según el rango de glucosa. Silenciosa, sin números. Compatible con Dexcom G6/G7 y FreeStyle Libre 2/3. | La expresión más pura de calm technology: cero lectura, cero ansiedad, comprensible sin aprender nada. | No responde "cuánto", solo "en qué rango". Deja afuera número y tendencia, que son el núcleo de esta propuesta. |
| Glucolamp | Lámpara que traduce el dato del CGM en gradientes de color calmos en lugar de números. | Objeto doméstico legítimo, con estética de producto de consumo y no de equipamiento clínico. | Mismo límite que Glowcose: sin dato preciso ni dirección de tendencia. |
| DIY (Nightscout + Home Assistant, SugarClock, xDrip) | Displays, lámparas e integraciones armadas por la comunidad. | Gratis, infinitamente personalizable, comunidad activa y de rápida iteración. | Exige competencia técnica y mantenimiento continuo. No es un objeto terminado: excluye a la mayoría del segmento. |
| Sustitutos incumbentes (teléfono, smartwatch, receptor del CGM) | Widget, complication y notificaciones que ya muestran glucosa y tendencia. | Ya los tiene el usuario. Costo marginal cero y cero fricción de adopción. | Son exactamente el problema: requieren atención activa y mantienen el dato dentro de una pantalla personal. |

---

## Client Differentiators

La ventaja identificada hoy es de distribución, no de producto: ninguno de estos competidores vende, garantiza ni da soporte en Argentina. Importarlos significa pagar en dólares, esperar aduana y quedarse sin respaldo post-venta si el dispositivo falla. Un producto pensado, vendido y sostenido localmente resuelve un problema concreto que los incumbentes no están resolviendo.

Esa ventaja se apoya en una decisión de producto coherente con el mercado: el MVP se construye sobre LibreLinkUp. Los competidores nacen en mercados donde Dexcom pesa más; en Argentina la base instalada es Libre, y funcionar bien con Libre es lo que hace que la propuesta local sea relevante y no solo más cercana.

El fundador vive con diabetes tipo 1 y usa CGM. La autoridad de dominio es genuina: no hay que imaginar al usuario ni inferir el problema. Eso reduce el riesgo de diseñar para un usuario inventado y acorta drásticamente el ciclo de validación de la experiencia principal.

Conviene ser explícito sobre la fragilidad de todo esto ⚠️. La presencia local es replicable: cualquier importador puede traer SugarHalo o Glowcose y darles soporte, y SugarHalo ya cubre Libre y el mismo lenguaje ambiental. La distribución da una ventana temporal, no un foso. La diferenciación defendible tiene que construirse donde los competidores efectivamente son débiles —la calidad del diseño industrial, la presencia física del objeto en una casa y una marca capaz de sostener más de un producto— y ninguna de esas tres está probada todavía. Es, además, el área de menor experiencia del único integrante del proyecto.

---

## Relevant Industry Trends

- **La adopción de CGM sigue expandiéndose.** El monitoreo continuo pasó de nicho a estándar de cuidado en T1D y avanza sobre T2D y usuarios sin diabetes. Cada año hay más personas con un stream de glucosa en tiempo real y, con ellas, más demanda de formas de consumir ese dato sin mirar el teléfono.

- **La categoría ya está validada internacionalmente y vacante localmente.** Que SugarPixel, SugarHalo, Glowcose y Glucolamp tengan distribución real y retailers especializados prueba que existe demanda sostenida. La validación de categoría ya ocurrió afuera: en Argentina el trabajo pendiente es de acceso, no de evangelización.

- **La calm technology llegó al producto de consumo.** Displays de e-ink domésticos y dispositivos ambientales normalizaron la idea de información presente en el espacio en lugar de notificada en una pantalla. El lenguaje de "menos pantalla" dejó de ser de nicho, lo que facilita explicar el producto y, al mismo tiempo, vuelve el posicionamiento más disputado.

- **Toda la categoría depende de APIs no oficiales.** SugarPixel, SugarHalo, Glowcose y las soluciones DIY se apoyan en endpoints no documentados de Dexcom Share y LibreLinkUp. Un cambio de política de Abbott o Dexcom puede dejar sin funcionar a todos los productos a la vez. Es un riesgo sistémico de la categoría, no una debilidad particular de este proyecto — pero lo hereda entero.

- **La comunidad DIY es fuente de demanda y de competencia.** Nightscout y xDrip instalaron la expectativa de que el dato de glucosa le pertenece al usuario. Esa comunidad es el early adopter natural y, a la vez, el grupo más capaz de armarse una alternativa gratis.

- **El encuadre regulatorio es un límite de categoría.** La frontera entre "accesorio de visualización" y "producto médico" define qué puede prometer el producto. En Argentina la autoridad es ANMAT y la clasificación de este producto no fue evaluada ⚠️.
