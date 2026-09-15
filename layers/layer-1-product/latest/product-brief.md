---
title: "Product Brief"
layer: product
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-15
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

Está mal atendido, no desatendido. La categoría de display secundario existe y está validada internacionalmente, pero se ordena en dos polos que dejan un hueco en el medio: los productos que muestran el número lo hacen como sistema de alarmas con pantalla incorporada, y los que priorizan la calma renuncian al número y solo dan color. Ninguno se vende, garantiza ni soporta en Argentina. Si el problema sigue sin resolverse localmente, el usuario sigue mirando el teléfono y la ventana la ocupa un importador sin capacidad de sostener el producto después de la venta.

---

## Target Users

El usuario primario es el adulto con diabetes tipo 1 que usa FreeStyle Libre, consulta su glucosa con alta frecuencia y pasa períodos largos en espacios relativamente fijos: el escritorio durante el día y la mesa de luz durante la noche. Es alguien con suficiente familiaridad tecnológica para configurar un dispositivo en su red de casa, que ya tiene resuelto el acceso al dato y cuyo problema no es la información sino la forma de recibirla. El usuario cero del producto —el fundador— pertenece a este grupo, y es hoy la única fuente de validación diaria disponible.

Los convivientes son usuarios secundarios del mismo objeto: pareja, familia o compañeros de casa que quieren ver el dato sin tener que preguntar ni interrumpir. No configuran el dispositivo ni deciden nada sobre él, solo lo leen. Su valor es real pero todavía no está validado ⚠️.

Dos grupos quedan deliberadamente fuera del usuario primario en esta fase. Los padres y madres de niños con T1D son el segmento más activo y expresivo de la categoría, y también el que más valora exactamente lo que este producto no hace: alarmas fuertes y vibración que despierten de noche. Entran junto con las alertas, no antes. La comunidad DIY, por su parte, ya resolvió el problema por su cuenta y es simultáneamente el early adopter natural y el grupo menos dispuesto a pagar por un objeto terminado.

---

## Product Vision

Un objeto conectado que vive sobre el escritorio o la mesa de luz y muestra de forma permanente la glucosa actual, la flecha de tendencia y el delta, legibles desde el otro lado de la habitación y sin pedir nada a cambio: no suena, no notifica, no exige ser descartado. Se enchufa por USB-C, se configura una vez desde el navegador y después se comporta como un reloj de pared. El estado final que persigue es concreto y observable: que mirar el teléfono deje de ser el gesto por defecto para saber cómo se está. La diferencia frente a lo que hoy existe está en la combinación, no en los ingredientes — da el número y la tendencia, que las lámparas de color no dan, sin comportarse como un sistema de alarmas, que es lo que son los displays que sí dan el número; y es el único de la categoría pensado, configurado, garantizado y soportado en Argentina, sobre la fuente de datos que aquí es mayoritaria.

---

## Scope

El producto que describe este brief es una unidad para el usuario cero, construida para validar la tesis central antes de comprometer cualquier decisión irreversible. En alcance entra: la pantalla siempre presente con número, flecha de tendencia, delta y hora; un modo nocturno de brillo reducido que mantenga la lectura posible sin iluminar el cuarto; la señalización explícita e inequívoca del estado del dato —fresco, desactualizado o sin conexión— de modo que nunca haya que deducirlo; y la experiencia completa de configuración desde el propio dispositivo, sin app y sin backend.

Esa configuración es parte del producto, no un paso previo a él. Al arrancar sin configurar, el dispositivo levanta su propio access point y muestra en pantalla el nombre de la red y un código QR. El usuario se conecta, un portal web servido por el propio dispositivo se abre solo, elige su red de casa y carga la contraseña. El dispositivo se reinicia ya en la red del hogar y muestra un segundo QR con un PIN de cuatro dígitos que da acceso al portal en su dirección local, donde se cargan las credenciales de la cuenta seguidora de LibreLinkUp y los umbrales de glucosa. La red Wi-Fi y la cuenta de CGM se pueden cambiar más tarde desde ese mismo portal sin rehacer el resto de la configuración: es la falla más costosa documentada en los competidores —el cambio de teléfono que deja el dispositivo muerto y sin camino de vuelta— y el producto la trata como requisito, no como mejora.

Queda explícitamente fuera de alcance:

- **Alertas sonoras, vibración y accesorios de despertar.** No es una omisión técnica: el silencio es la propuesta. El CGM del fabricante sigue siendo el sistema de alarmas oficial y la referencia terapéutica.
- **App móvil, backend propio y cuenta de usuario.** Toda la configuración vive en el dispositivo.
- **Dexcom y Nightscout.** La fuente es LibreLinkUp, única y exclusiva.
- **Batería y portabilidad.** Alimentación continua por USB-C; el producto funciona donde hay enchufe y una red Wi-Fi conocida.
- **Múltiples perfiles o seguimiento de varias personas.** Un dispositivo, una persona.
- **Análisis de glucosa, historial extendido y cualquier recomendación terapéutica.** El producto muestra, no interpreta.
- **Todo lo comercial:** precio, clasificación ANMAT, manufactura, garantía y canal de venta. Son decisiones abiertas de Layer 0 ⚠️ y no condicionan este alcance más allá de lo dicho abajo.

Se difieren, con criterio de entrada explícito:

- **Alertas nocturnas y el segmento de cuidadores.** Entran cuando la propuesta calma esté validada con personas con T1D distintas del usuario cero. Aunque no se usen en el MVP, condicionan la elección de plataforma desde ahora: el hardware del MVP debe dejar lugar a salida de audio y vibración en lugar de cerrar esa puerta.
- **El lote de validación externo.** Poner el objeto en manos de otras personas con T1D es objetivo estratégico y es lo único que rompe el sesgo de diseñar para uno mismo. No está en este alcance, pero la decisión de construir la configuración real desde el MVP —en vez de dejar credenciales fijas en el firmware— existe justamente para que la unidad número dos no dependa de que el fundador la flashee a mano.

---

## Key Assumptions

- **Que ver el dato sin pedirlo cambia el comportamiento.** Es la tesis central: la hipótesis de que un objeto presente reduce de forma medible las consultas en el teléfono. **Sin validar**, y solo validable con uso continuo y sostenido. Si es falsa, ningún otro acierto del producto importa.

- **Que la propuesta calma tiene demanda más allá del fundador.** **Sin validar, y la evidencia pública disponible apunta en contra:** el atributo más celebrado de la categoría es la alarma fuerte que despierta de noche, y la voz dominante en las reviews es la de cuidadores, no la del adulto con T1D en su escritorio. Sobre los dos competidores más parecidos a esta propuesta no existe una sola review independiente, y no hay ninguna pieza de feedback de usuarios argentinos o latinoamericanos. Es el riesgo más alto del brief y la razón por la que la validación externa es prioridad y no un paso posterior.

- **Que un objeto calmo se sigue mirando a los seis meses.** No hay datos de retención ni de abandono en ningún producto de la categoría. Para una tesis construida sobre la no intrusión, que el objeto se vuelva invisible es un modo de falla plausible y completamente inexplorado.

- **Que LibreLinkUp sigue siendo accesible.** Es la única fuente de datos y no hay plan alternativo. El acceso no tiene contrato, SLA ni permiso, y toda la categoría comparte la misma dependencia. Un cambio de política de Abbott deja al producto sin dato.

- **Que la confiabilidad es alcanzable sobre Wi-Fi doméstico y una API no oficial.** El producto promete no mentir nunca sobre la frescura del dato. Cumplir esa promesa no depende de que la red no falle —va a fallar— sino de que el comportamiento en estado degradado esté diseñado: reconectar sin intervención, señalar la caída sin ambigüedad y recuperarse solo.

---

## Relationship to Business Goals

Este producto es el vehículo de los tres objetivos estratégicos que el MVP tiene que demostrar. La pantalla siempre presente existe para validar que el objeto físico cambia el comportamiento; la señalización del estado del dato es la traducción directa del objetivo de confiabilidad sin ambigüedad, que no es una funcionalidad sino una condición de existencia —un display ambiental en el que no se puede confiar es peor que no tener display—; y la decisión de construir la configuración real desde el dispositivo, en lugar de credenciales fijas, es lo que hace posible el tercer objetivo: que otra persona con T1D pueda tener uno y pedirlo.

La apuesta de diferenciación del producto tiene dos niveles. La confiabilidad y la configuración son el piso: no son costo operativo ni problema de implementación, son features diseñadas, y son el terreno donde los competidores efectivamente pierden. Prácticamente toda la crítica sustantiva de la categoría cae en cuatro cubetas —Wi-Fi que se cae, app compañera pobre, acoplamiento frágil a la cuenta del CGM y soporte que no contesta— y ninguna es estética. Es, además, el único de los terrenos disputados donde el proyecto ya tiene la competencia necesaria. El diseño industrial y la presencia física del objeto son el techo: la ventaja defendible de largo plazo, la que sostiene la ambición de una marca de objetos cotidianos para personas con T1D, y la que todavía está por construirse en el área de menor experiencia del proyecto. Un piso sin techo da un producto confiable y genérico; un techo sin piso da un objeto lindo que la gente devuelve.

Queda una tensión abierta que condiciona el paso de este MVP a un producto comercializable ⚠️. El ancla de precio internacional de la categoría está entre USD 60 y USD 85 —no en los USD 200-300 que cuesta importar el modelo grande a Argentina—, lo que deja un margen considerablemente más angosto para un objeto ensamblado a mano de lo que asume hoy Layer 0. La definición del modelo de negocio y del precio objetivo sigue siendo decisión abierta del fundador, y condiciona decisiones de BOM y manufactura desde ahora, no después.
