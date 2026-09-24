---
title: "Product Brief"
layer: product
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-22
relates_to:
  - layers/layer-0-business/latest/business-overview.md
  - layers/layer-0-business/latest/competitive-landscape.md
  - layers/layer-0-business/latest/strategic-goals-and-constraints.md
  - layers/layer-0-business/latest/stakeholder-map.md
  - layers/layer-0-business/intermediate/competitor-user-feedback-synthesis.md
---

# Product Brief

Qué estamos construyendo, para quién y por qué. Es el documento de alineación del producto y el contexto que heredan todos los feature specs.

---

## Problem Space

El CGM eliminó la fricción de medir la glucosa, pero no la de mirarla. El dato existe en tiempo real y sin pinchazos, y sin embargo sigue viviendo dentro del teléfono: para saber cuánto tiene, una persona con diabetes tipo 1 desbloquea, abre una app y lee. Decenas de veces por día, todos los días, sin fecha de terminación. Cada consulta es corta y ninguna es gratis: interrumpe lo que estaba haciendo, arrastra consigo todo lo demás que hay en esa pantalla y convierte un dato ambiental en un acto de atención.

La otra mitad del problema es lo que el teléfono hace sin que se lo pidan. Un mismo evento de glucosa dispara alertas simultáneas en parlante, teléfono, receptor y reloj, cada una exigiendo ser descartada. La consecuencia está medida: en un estudio transversal de 838 usuarios de CGM, entre el 44% y el 50% reporta a veces sobrecorregir hipoglucemias e hiperglucemias, y la fatiga de alarma desemboca en desresponsabilización, desactivación de alertas y, en el extremo, abandono del dispositivo. El problema no es nuevo ni exclusivo: lo tiene toda persona con T1D que usa CGM y pasa horas en espacios fijos. Lo que sí es nuevo es que resolverlo esté al alcance.

**Está resuelto afuera y sin resolver acá, y está mal ejecutado en todas partes.** La categoría de display secundario existe, funciona y está validada internacionalmente: no hay que probar que la gente quiere un objeto que muestre el número sin que haya que pedirlo. Pero ningún producto de la categoría se vende, garantiza ni soporta en Argentina, y prácticamente toda la crítica sustantiva que reciben cae en cuatro cubetas, ninguna estética: Wi-Fi que se cae, acoplamiento frágil a la cuenta del CGM, configuración que muere cuando el usuario cambia de teléfono, y soporte que no contesta. Hay una quinta que las reviews todavía no nombran porque tarda más en aparecer: el producto que queda obsoleto el día que la fuente de datos cambia el protocolo y nadie puede actualizarlo. Ese es el hueco — no de concepto, de ejecución.

---

## Target Users

El usuario primario es el adulto con diabetes tipo 1 que usa FreeStyle Libre, consulta su glucosa con alta frecuencia y pasa períodos largos en espacios relativamente fijos: el escritorio durante el día y la mesa de luz durante la noche. Es alguien con suficiente familiaridad tecnológica para poner un dispositivo en su red de casa, que ya tiene resuelto el acceso al dato y cuyo problema no es la información sino la forma de recibirla. El usuario cero del producto —el fundador— pertenece a este grupo, y es hoy la única fuente de validación diaria disponible.

Los convivientes son usuarios secundarios del mismo objeto: pareja, familia o compañeros de casa que quieren ver el dato sin tener que preguntar ni interrumpir. No configuran el dispositivo ni deciden nada sobre él, solo lo leen. Su valor es real pero todavía no está validado ⚠️.

El producto soporta mostrar a varias personas a la vez, pero **eso es una capacidad, no un segmento**. Quien sigue a más de una persona desde su cuenta de LibreLinkUp ve a todas; nadie carga credenciales ajenas en el dispositivo. La distinción importa porque define a quién le habla el producto y a quién no.

Dos grupos quedan deliberadamente fuera del usuario primario en esta fase. Los padres y madres de niños con T1D son el segmento más activo y expresivo de la categoría, y también el que más valora exactamente lo que este producto no hace: alarmas fuertes y vibración que despierten de noche. El seguimiento de varias personas les sirve, pero no alcanza: entran de verdad junto con las alertas, no antes. La comunidad DIY, por su parte, ya resolvió el problema por su cuenta y es simultáneamente el early adopter natural y el grupo menos dispuesto a pagar por un objeto terminado — aunque el soporte de Nightscout, diferido a post-lanzamiento, es la puerta por la que entran.

---

## Product Vision

Un objeto conectado que vive sobre el escritorio o la mesa de luz y muestra de forma permanente la glucosa actual y la flecha de tendencia —propia o de las personas que uno sigue—, legibles desde el otro lado de la habitación y sin pedir nada a cambio: no suena, no notifica, no exige ser descartado. Se enchufa por USB-C, se configura entero desde su propia pantalla sin necesidad de otro dispositivo, y después se comporta como un reloj de pared. El estado final que persigue es concreto y observable: que mirar el teléfono deje de ser el gesto por defecto para saber cómo se está.

La diferencia frente a lo que hoy existe no está en el concepto, que ya está validado, sino en **qué tan bien se ejecuta y por cuánto tiempo sigue funcionando**. Es el único de la categoría pensado, configurado, garantizado y soportado en Argentina, sobre la fuente de datos que aquí es mayoritaria; es el que trata la confiabilidad en red doméstica y la recuperación sin intervención como features diseñadas en vez de como problemas de implementación; es el que se configura solo, sin depender de un teléfono que después puede cambiar; y es el que puede actualizarse a distancia, que es lo que separa un producto de un objeto con fecha de vencimiento.

---

## Scope

Este brief describe el producto hasta la primera unidad vendible. El próximo hito construido es **P1** — el Product Architecture Prototype del plan de prototipo a producción: el primer Glumi que permite evaluar la experiencia física real, todavía sobre electrónica de desarrollo. Las primeras unidades que salen de la casa del fundador **se entregan, no se venden**.

En alcance entra:

- **La pantalla siempre presente** con número, flecha de tendencia y hora, sobre un panel de 7" con touch capacitivo.
- **Hasta seis personas a la vez**, tomadas de las conexiones que la cuenta de LibreLinkUp configurada ya sigue. El usuario elige cuáles se muestran y en qué orden; el layout se adapta a la cantidad. No se agregan personas cargando credenciales distintas.
- **Modo nocturno** de brillo reducido que mantenga la lectura posible sin iluminar el cuarto. El requisito se expresa como resultado medido a distancia de lectura, no como porcentaje de duty.
- **La señalización explícita e inequívoca del estado del dato** —fresco, desactualizado o sin conexión— de modo que nunca haya que deducirlo.
- **La configuración completa desde la propia pantalla del dispositivo**: red Wi-Fi, credenciales de LibreLinkUp, selección de personas, preferencias y reset de fábrica. Sin app, sin portal web y sin backend de usuario.
- **La capacidad de actualizarse a distancia**, o al menos sus cimientos irreversibles: tabla de particiones con doble slot de aplicación, esquema de configuración versionado y firma de imágenes. Son decisiones que vencen al flashear la primera unidad y no se pueden tomar después.

La configuración es parte del producto, no un paso previo a él, y vive entera en el dispositivo. Al arrancar sin configurar, la pantalla muestra el flujo de puesta en marcha y el usuario elige su red, carga la contraseña y después las credenciales de la cuenta seguidora de LibreLinkUp, todo sobre el mismo objeto. Cualquiera de esas cosas se cambia más tarde desde la misma pantalla sin rehacer el resto: es la falla más costosa documentada en los competidores —el cambio de teléfono que deja el dispositivo muerto y sin camino de vuelta— y un dispositivo que no necesita un teléfono para configurarse simplemente no la tiene.

El touch agrega capacidades pero no puede quitar la que ya existe: todo lo que el objeto hacía sin que lo tocaran tiene que seguir pasando mientras lo tocan. Un toque cualquiera no navega, la navegación vuelve sola al número, y de noche el dispositivo despierta al nivel de brillo nocturno y nunca a brillo pleno.

Queda explícitamente fuera de alcance:

- **Alertas sonoras, vibración y accesorios de despertar.** No es una omisión técnica: el CGM del fabricante sigue siendo el sistema de alarmas oficial y la referencia terapéutica.
- **App móvil, cuenta de usuario y backend de datos.** Toda la configuración vive en el dispositivo y ningún dato de glucosa pasa por infraestructura propia. La única infraestructura que el producto opera es la distribución de firmware, y existe solo para eso.
- **Dexcom.** Su API oficial entrega datos diferidos, inservibles para un display en tiempo real, y la alternativa no oficial duplicaría la exposición a una dependencia sin contrato antes de haber resuelto la primera.
- **Delta y countdown de próxima lectura.** El delta no viene en el payload y calcularlo obliga a retener historia, elegir ventana y darle su propia regla de frescura. El countdown no aporta información: con una cadencia de lectura de alrededor de un minuto la espera es más corta que el tiempo que toma mirar, y sería el único elemento del producto que exige redibujo continuo.
- **Batería y portabilidad.** Alimentación continua por USB-C.
- **Análisis de glucosa, historial extendido y cualquier recomendación terapéutica.** El producto muestra, no interpreta.
- **Todo lo comercial:** precio, clasificación ANMAT, manufactura, garantía y canal de venta. Son decisiones abiertas de Layer 0 ⚠️.

Se difieren, con criterio de entrada explícito:

- **Nightscout como fuente.** Entra después del lanzamiento, entregado por actualización remota a las unidades que ya estén en la calle. Es la fuente más barata de integrar y cubre transitivamente todos los ecosistemas de sensor. Obliga a un modelo de credenciales por persona, porque un sitio Nightscout es una persona — y por eso el modelo de datos se diseña desde ahora para admitirlo.
- **Alertas nocturnas y el segmento de cuidadores.** Entran cuando la propuesta silenciosa esté validada con personas con T1D distintas del usuario cero. El hardware debe dejar lugar a salida de audio y vibración en lugar de cerrar esa puerta.
- **El tratamiento visual de valores fuera de rango.** Decisión abierta ⚠️. Diferenciar un valor alto o bajo no es una alarma y el dato ya viene marcado por la fuente, pero un tratamiento visual fuerte es la puerta de entrada a convertir el objeto en un sistema de alertas — y a 7" de superficie emisora, en una fuente de luz nocturna.

---

## Key Assumptions

- **Que ver el dato sin pedirlo cambia el comportamiento. ✅ Validado sobre el usuario cero.** Era la tesis central del proyecto y dejó de ser hipótesis: con uso continuo y sostenido, el dato presente efectivamente desplazó la consulta al teléfono. La validación es n=1 y eso es exactamente lo que alcanza a cubrir: establece que el mecanismo funciona, no que funcione para otra persona ni que siga funcionando a los seis meses.

- **Que el objeto sigue siendo ambiental teniendo pantalla táctil y hasta seis tarjetas. Sin validar ⚠️.** Un objeto que se puede tocar invita a ser tocado, y una pantalla con seis números tiene seis veces más superficie que cambia sola. El diseño mitiga las dos cosas —el toque no navega, nada parpadea— pero la mitigación no está probada en uso real. Es lo que P1 tiene que medir además de lo óptico.

- **Que 7" es un objeto que se quiere tener en la mesa de luz.** El salto de 95 mm a 154 mm de ancho es lo que hace posible mostrar varias personas a distancia de habitación, y es también lo que acerca la silueta al producto tipo dashboard. Se resuelve antes de comprar nada, con una maqueta 1:1 en papel en los dos tamaños.

- **Que la propuesta silenciosa tiene demanda más allá del fundador. Sin validar, y la evidencia pública apunta en contra:** el atributo más celebrado de la categoría es la alarma fuerte que despierta de noche. No es la apuesta central del proyecto —esa es la ejecución— pero sostiene la decisión de no hacer alertas, y esa decisión no tiene respaldo externo.

- **Que un objeto calmo se sigue mirando a los seis meses.** No hay datos de retención ni de abandono en ningún producto de la categoría. Que el objeto se vuelva invisible es un modo de falla plausible y completamente inexplorado.

- **Que LibreLinkUp sigue siendo accesible.** Es la única fuente de datos hasta el lanzamiento. El acceso no tiene contrato, SLA ni permiso, y toda la categoría comparte la misma dependencia. La actualización remota mitiga el caso "cambió el protocolo" —que ya ocurrió dos veces en silencio— pero no mitiga el caso "Abbott cierra el acceso", para el que no hay plan.

- **Que la confiabilidad es alcanzable sobre Wi-Fi doméstico y una API no oficial.** El producto promete no mentir nunca sobre la frescura del dato. Cumplir esa promesa no depende de que la red no falle —va a fallar— sino de que el comportamiento en estado degradado esté diseñado: reconectar sin intervención, señalar la caída sin ambigüedad y recuperarse solo.

---

## Relationship to Business Goals

Este producto es el vehículo directo de la apuesta declarada en Layer 0: **ejecutar bien una categoría que ya existe y que acá no se vende.** Eso reordena qué es central y qué es accesorio.

La señalización del estado del dato es la traducción del objetivo de confiabilidad sin ambigüedad, que no es una funcionalidad entre otras sino el producto: si la apuesta es ejecución, el terreno donde los competidores pierden es el terreno donde se gana. La configuración completa en el dispositivo ataca de frente la falla más documentada de la categoría y, de paso, hace posible que otra persona tenga una unidad sin que el fundador la flashee a mano. Y la capacidad de actualizarse a distancia es la que convierte la venta en algo sostenible: la fuente de datos va a cambiar el protocolo otra vez, y ese día no falla una unidad sino todas al mismo tiempo.

La pantalla siempre presente sigue siendo la materialización del hallazgo validado —que el objeto físico cambia el comportamiento— y lo que ahora hay que sostener fuera de las manos del fundador.

Queda una tensión abierta que condiciona el paso a un producto comercializable ⚠️. El ancla de precio internacional de la categoría está entre USD 60 y USD 85 —no en los USD 200-300 que cuesta importar el modelo grande a Argentina—, y el salto a un panel de 7" con touch mueve el BOM hacia arriba justo donde el margen ya era angosto para un objeto ensamblado a mano. La definición del modelo de negocio y del precio objetivo sigue siendo decisión abierta del fundador, y condiciona decisiones de BOM y manufactura desde ahora, no después.
