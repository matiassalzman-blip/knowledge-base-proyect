---
title: "Principios de diseño"
layer: design
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-22
relates_to:
  - layers/layer-1-product/latest/product-brief.md
---

# Principios de diseño

## 1. Reducir la fricción, no reclamar atención

Glumi existe para hacer que consultar la glucosa requiera prácticamente cero esfuerzo. La información debe estar disponible de un vistazo, sin sacar el celular, abrir una aplicación ni navegar una interfaz.

Pero estar siempre disponible no significa demandar atención. Glumi debe permanecer en segundo plano hasta que el usuario decida mirarlo.

Esto significa: adaptarse al ambiente, mantener una presencia visual calma y evitar interrupciones innecesarias. De noche, por ejemplo, debería reducir su luminosidad sin dejar de ser consultable — y despertar siempre en ese nivel, nunca a brillo pleno, aunque lo hayan tocado.

Esto evita: parpadeos, animaciones constantes, sonidos innecesarios, gamificación, mensajes celebratorios o cualquier recurso que transforme cada cambio de glucosa en un evento. Un countdown hacia la próxima lectura cae de este lado: con una cadencia de alrededor de un minuto la espera es más corta que el tiempo que toma mirar, así que no agrega información y sí agrega movimiento permanente.

## 2. El dato es el protagonista

La razón de existir de Glumi es comunicar la glucosa actual y hacia dónde se dirige. Todo lo demás es secundario.

La jerarquía visual debe hacerlo evidente: primero el valor, después su tendencia y, solo cuando aporte contexto suficiente, información secundaria.

```
    112 →
    mg/dL
```

Cuando Glumi muestra a varias personas, el principio no se reparte: se repite. Cada persona en pantalla es un valor protagonista con su tendencia, y lo que compite no es una glucosa contra otra sino **todo lo demás contra todas ellas**. Nombres, marcas de estado, barra superior y cualquier elemento de interfaz ocupan el lugar que les sobra a los números, nunca al revés.

De ahí sale el límite duro: la cantidad de personas en pantalla no puede crecer hasta el punto en que los números dejen de leerse a distancia de habitación. Si no entra, no entra — un número que hay que acercarse a leer es peor que un número ausente, porque reconstruye exactamente la fricción que el producto vino a eliminar.

Glumi no busca condensar toda la experiencia de una aplicación de CGM en una pantalla más grande.

Esto significa: diseñar alrededor del número y preservar una jerarquía extremadamente clara.

Esto evita: curvas históricas, dashboards, métricas secundarias, múltiples widgets o información que compita visualmente con la glucosa.

## 3. Cada elemento debe justificar su presencia

En Glumi, "podemos agregarlo" nunca es razón suficiente para agregarlo.

Cada elemento de interfaz, componente físico y funcionalidad debe resolver una necesidad concreta y aportar suficiente valor como para justificar la complejidad que introduce.

Esto aplica tanto al software como al hardware. Una batería, un botón, un sensor, una animación o incluso el logo están sujetos a la misma pregunta:

¿Qué problema resuelve y qué perdemos si lo quitamos?

Si la respuesta no es suficientemente buena, no pertenece a Glumi.

Esto evita: feature creep, especificaciones construidas para marketing, sensores sin propósito, información redundante y decoración gratuita.

## 4. Simple, pero nunca críptico

La reducción no debe ocurrir a costa de la comprensión.

Glumi debe poder entenderse sin instrucciones: qué información está mostrando, qué estado tiene y qué puede hacer el usuario deberían resultar evidentes a partir del propio diseño.

Si existe un botón, debe reconocerse como tal. Si el dato está desactualizado, no debe parecer actual. Si Glumi perdió conexión, no debe presentar información vieja como si nada hubiera ocurrido.

Esto significa: utilizar jerarquía, affordances, signifiers, feedback y constraints para hacer que el funcionamiento del objeto sea evidente.

Esto evita: controles escondidos por pureza estética, gestos indescifrables, estados ambiguos y minimalismo que obligue a consultar un manual.

## 5. Ambiental por defecto, explícito cuando importa

Glumi debe comunicar mediante su presencia, no mediante interrupciones.

Cambios sutiles de color, luminosidad y jerarquía pueden comunicar estados sin exigir que el usuario interactúe con el dispositivo. Una glucosa fuera de rango puede adquirir otra presencia visual sin convertir inmediatamente al objeto en una alarma.

Pero la calma nunca debe producir ambigüedad. Cuando algo requiere ser entendido —un dato desactualizado, pérdida de conexión o un estado excepcional— Glumi debe comunicarlo inequívocamente.

La calma no debe confundirse con silencio informativo.

Esto evita: alarmismo visual por un lado y, por el otro, una estética tan discreta que oculte información relevante.

## 6. Lo cotidiano debe ser inmediato; lo excepcional vive en el objeto, detrás de un gesto deliberado

Glumi no es una pequeña computadora ni una aplicación trasladada a un objeto físico.

Las acciones frecuentes deben ocurrir con poca o ninguna interacción: consultar la glucosa requiere cero pasos, y esa es la acción que define al producto.

Las configuraciones infrecuentes —red Wi-Fi, credenciales, qué personas se muestran, preferencias— **viven en el dispositivo y no en otro lado**. Un dispositivo que depende de un teléfono para configurarse hereda el modo de falla más caro de la categoría —el cambio de teléfono que lo deja muerto y sin camino de vuelta—; un objeto que se configura solo no lo tiene.

Que la configuración viva en el objeto no la convierte en algo cotidiano. De ahí el invariante que ordena toda la interacción táctil:

> **El touch puede agregar capacidades, pero nunca puede quitar la que ya existe.** Todo lo que el objeto hacía sin que lo tocaran tiene que seguir pasando mientras lo tocan.

En la práctica: un toque cualquiera no navega —entrar a configuración pide un gesto deliberado sobre un target chico, nunca sobre el área del número—, la navegación vuelve sola al estado por defecto sin que nadie la cancele, y la única acción destructiva del objeto vive detrás de un gesto largo y confirmado.

Esto significa: favorecer la autonomía del objeto y reservar la interacción física para aquellas acciones que realmente se benefician de ella.

Esto evita: menús permanentes en pantalla, navegación profunda, configuración como estado en el que el objeto puede quedarse, y gestos que haya que aprender.

## 7. Diseñado para perdurar

Glumi debe aspirar a envejecer bien tanto física como conceptualmente.

Su forma debe surgir de su función, proporciones, materiales y construcción, no de tendencias estéticas pasajeras. La identidad debe provenir principalmente del propio objeto, no de logos o elementos gráficos aplicados.

Incluso condicionado inicialmente por fabricación mediante impresión 3D, el objeto debe sentirse deliberado y terminado, no como un prototipo tecnológico.

Perdurar también es una propiedad del software: el producto depende de una fuente de datos que ya cambió su protocolo en silencio y va a volver a hacerlo. Un objeto que no puede actualizarse no envejece, vence.

Esto significa: formas comprensibles, materiales honestos, detalles funcionales, construcción mantenible y una estética sobria y atemporal.

Esto evita: branding prominente, gestos gratuitamente futuristas, RGB decorativo, formas de moda y decisiones que hagan que Glumi se sienta obsoleto antes de que realmente lo esté.
