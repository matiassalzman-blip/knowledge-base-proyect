# Principios de diseño

## 1. Reducir la fricción, no reclamar atención

Glumi existe para hacer que consultar la glucosa requiera prácticamente cero esfuerzo. La información debe estar disponible de un vistazo, sin sacar el celular, abrir una aplicación ni navegar una interfaz.

Pero estar siempre disponible no significa demandar atención. Glumi debe permanecer en segundo plano hasta que el usuario decida mirarlo.

Esto significa: adaptarse al ambiente, mantener una presencia visual calma y evitar interrupciones innecesarias. De noche, por ejemplo, debería reducir su luminosidad sin dejar de ser consultable.

Esto evita: parpadeos, animaciones constantes, sonidos innecesarios, gamificación, mensajes celebratorios o cualquier recurso que transforme cada cambio de glucosa en un evento.

## 2. El dato es el protagonista

La razón de existir de Glumi es comunicar la glucosa actual y hacia dónde se dirige. Todo lo demás es secundario.

La jerarquía visual debe hacerlo evidente: primero el valor, después su tendencia y, solo cuando aporte contexto suficiente, información secundaria.

```
    112 →
    mg/dL
```

Glumi no busca condensar toda la experiencia de una aplicación de CGM en una pantalla más pequeña.

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

## 6. Lo cotidiano debe ser inmediato; lo excepcional puede requerir interacción

Glumi no es una pequeña computadora ni una aplicación trasladada a un objeto físico.

Las acciones frecuentes deben ocurrir con poca o ninguna interacción. Las configuraciones infrecuentes pueden vivir fuera del dispositivo cuando hacerlo mantenga al objeto más simple.

Por ejemplo, consultar la glucosa debería requerir cero pasos. Configurar Wi-Fi, vincular una cuenta o modificar preferencias avanzadas puede ocurrir desde otro dispositivo.

Esto significa: favorecer la autonomía del objeto y reservar la interacción física para aquellas acciones que realmente se benefician de ella.

Esto evita: menús, navegación profunda, configuraciones permanentes en pantalla y controles físicos agregados únicamente para acceder a funciones excepcionales.

## 7. Diseñado para perdurar

Glumi debe aspirar a envejecer bien tanto física como conceptualmente.

Su forma debe surgir de su función, proporciones, materiales y construcción, no de tendencias estéticas pasajeras. La identidad debe provenir principalmente del propio objeto, no de logos o elementos gráficos aplicados.

Incluso condicionado inicialmente por fabricación mediante impresión 3D, el objeto debe sentirse deliberado y terminado, no como un prototipo tecnológico.

Esto significa: formas comprensibles, materiales honestos, detalles funcionales, construcción mantenible y una estética sobria y atemporal.

Esto evita: branding prominente, gestos gratuitamente futuristas, RGB decorativo, formas de moda y decisiones que hagan que Glumi se sienta obsoleto antes de que realmente lo esté.
