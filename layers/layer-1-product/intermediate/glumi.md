---
title: "Glumi"
layer: product
owner: ""
last_updated: 2026-10-03
relates_to: []
status: out_of_scope
---

# Feature: Glumi

> **Out of scope por ahora.** Esta feature (el personaje/mascota visual) quedó descoped del MVP. El documento se conserva en `intermediate/` como referencia para cuando se retome, en vez de perderse. Mientras tanto, el valor de glucosa, la flecha de tendencia (Glucosa) y el horario (Horario) cubren la comunicación de estado sin este elemento.

---

## Problem and Context

Leer un número y una flecha de tendencia requiere un momento de atención e interpretación: el usuario debe procesar el valor, ubicarlo mentalmente en un rango, y combinarlo con la dirección de la flecha para entender si su situación requiere acción. Glumi resuelve esto con una capa adicional, no verbal, que comunica el estado de glucosa de forma inmediata mediante color, expresión y nivel de presencia en pantalla — permitiendo reconocer el estado general de un vistazo, antes incluso de leer el número. Además de acelerar la interpretación, Glumi aporta personalidad al producto.

---

## Users

Usuarios de Glumi con un sensor LibreLink activo vinculado mediante LibreLinkUp, en cualquier momento del día en que miran la pantalla principal — desde una mirada rápida y distraída hasta una revisión más detenida. También interactúan con esta feature desde los ajustes de apariencia, donde pueden desactivar la presencia del personaje.

---

## Feature Description

Glumi es un elemento visual que acompaña al valor numérico de glucosa y a la flecha de tendencia (feature Glucosa), comunicando el mismo estado de glucosa mediante color, expresión facial y nivel de presencia en pantalla.

El nivel de presencia de Glumi es proporcional al nivel de atención que requiere el estado actual: cuanto más urgente es el estado, mayor es su protagonismo en pantalla. Por defecto Glumi permanece ambiental, y se vuelve explícito únicamente cuando el estado lo amerita — el objetivo no es mantener un personaje constantemente visible, sino usar su presencia como una capa adicional de información.

Glumi reutiliza los mismos umbrales de rango ya definidos en Glucosa para determinar su color y expresión:

- **En rango:** Glumi se muestra en verde y permanece casi oculto en el borde inferior de la pantalla, minimizando distracciones durante el uso cotidiano.
- **Alto** (entre el límite superior de "en rango" y 240 mg/dL): Glumi se muestra en amarillo, aumenta su presencia en pantalla y adopta una expresión de preocupación.
- **Urgente** (mayor a 240 mg/dL, o menor al límite inferior configurado de "en rango"): Glumi se muestra en rojo, ocupa mayor espacio en pantalla y adopta una expresión de mayor urgencia. Si existe una alarma propia de Glumi activa, su protagonismo puede aumentar temporalmente aún más.
- **Sin datos** (LibreLinkUp reporta un estado de error): Glumi se muestra en gris con una expresión neutral/confundida, comunicando que no puede determinar el estado actual.

Desde los ajustes de apariencia, el usuario puede desactivar por completo la presencia de Glumi. Al hacerlo, el valor de glucosa, la flecha de tendencia y las señales esenciales de estado (Glucosa, Horario) se siguen mostrando sin cambios.

---

## Business Rules

| Rule | Description | Confidence | Source |
| ---- | ----------- | ---------- | ------ |
| BR-1 | Glumi complementa el valor numérico de glucosa y la flecha de tendencia de la feature Glucosa; no los reemplaza. | Confirmed | Product decision |
| BR-2 | El color, la expresión y el nivel de presencia de Glumi varían según el estado de glucosa actual, en proporción al nivel de atención requerido: a mayor urgencia, mayor protagonismo. | Confirmed | Product decision |
| BR-3 | Estado "En rango" (mismo rango configurado que usa Glucosa): Glumi se muestra en verde y permanece casi oculto en el borde inferior de la pantalla. | Confirmed | Team decision |
| BR-4 | Estado "Alto" (entre el límite superior de "en rango" y 240 mg/dL — mismo umbral que Glucosa): Glumi se muestra en amarillo, aumenta su presencia en pantalla y adopta una expresión de preocupación. | Confirmed | Team decision |
| BR-5 | Estado "Urgente" (mayor a 240 mg/dL, o menor al límite inferior configurado de "en rango" — mismos umbrales que el estado rojo de la flecha en Glucosa): Glumi se muestra en rojo, ocupa mayor espacio en pantalla y adopta una expresión de mayor urgencia. | Confirmed | Team decision |
| BR-6 | No existe un estado amarillo intermedio para valores bajos: cualquier valor por debajo del límite inferior configurado de "en rango" activa directamente el estado "Urgente" (rojo), igual que en Glucosa. | Confirmed | Team decision |
| BR-7 | Si existe una alarma propia de Glumi activa, su protagonismo puede aumentar temporalmente más allá del nivel base del estado "Urgente". | Provisional | No explicit source |
| BR-8 | Estado "Sin datos": corresponde al mismo estado de error que ya reportan Glucosa y Horario cuando LibreLinkUp falla. Glumi se muestra en gris con una expresión neutral/confundida. | Confirmed | Team decision |
| BR-9 | Por defecto, Glumi permanece ambiental (presencia mínima) y solo se vuelve explícito (mayor presencia) cuando el estado de glucosa requiere atención. | Confirmed | Product decision |
| BR-10 | Desde los ajustes de apariencia, el usuario puede desactivar la presencia de Glumi mediante un control binario (encendido/apagado). No existe un nivel intermedio "reducido". | Confirmed | Team decision |
| BR-11 | Independientemente de si Glumi está activo o desactivado, el valor de glucosa, la flecha de tendencia y las señales esenciales de estado siempre se muestran. | Confirmed | Product decision |

---

## Edge Cases

| Scenario | Expected Behavior | Notes |
| -------- | ----------------- | ----- |
| El valor está exactamente en el límite inferior configurado de "en rango" | Glumi se clasifica como "En rango" (verde) | Límites inclusivos, igual que en Glucosa |
| El valor es exactamente 240 mg/dL | Glumi se clasifica como "Alto" (amarillo), no "Urgente" | Igual que en Glucosa |
| El valor es exactamente 241 mg/dL | Glumi se clasifica como "Urgente" (rojo) | Igual que en Glucosa |
| LibreLinkUp reporta error inmediatamente al abrir la app | Glumi se muestra en gris desde el primer render, sin pasar por ningún otro estado de color | No hay estado previo disponible |
| El usuario cambia sus límites de rango en Settings | La clasificación de estado y color de Glumi se recalcula con los nuevos límites | El recálculo ocurre con la próxima lectura, igual que en Glucosa |
| El usuario desactiva Glumi en ajustes de apariencia mientras el estado es "Urgente" (rojo) | Glumi deja de mostrarse por completo; el valor, la flecha y el horario siguen visibles sin cambios | |
| Se activa una alarma propia de Glumi mientras el estado es "Urgente" (rojo) | El protagonismo de Glumi aumenta temporalmente más allá de su nivel base | Comportamiento exacto depende de la feature de Alarmas — ver Open Questions |

---

## Out of Scope

- *Definición y comportamiento de las alarmas propias de Glumi — pertenece a una feature de Alarmas/Settings aún no especificada.*
- *Configuración de los límites de rango ("en rango", "alto", "urgente") — pertenece a la feature Glucosa / Settings.*
- *Animaciones, tamaños exactos o posicionamiento específico de Glumi en pantalla — detalle de diseño (Layer 2).*
- *Historial o analítica del estado de Glumi a lo largo del tiempo.*
- *Niveles intermedios de presencia (por ejemplo, un modo "reducido") — se definió un control binario de encendido/apagado.*
- *La feature completa — descoped del MVP por decisión de producto (2026-10-03). Ver nota al inicio del documento.*

---

## Dependencies

| Dependency | Type | Notes |
| ---------- | ---- | ----- |
| Glucosa (feature) | Depends on | Glumi reutiliza los mismos umbrales de rango y el mismo estado de error de LibreLinkUp para determinar su color y expresión |
| Horario (feature) | Interacts with | Comparte el mismo estado de error de LibreLinkUp para mostrar "Sin datos" |
| Alarmas de Glumi | Depends on | Una alarma activa puede aumentar temporalmente el protagonismo de Glumi; el comportamiento exacto depende de esta feature, aún no especificada |
| Ajustes de apariencia (Settings) | Depends on | Expone el control binario para activar/desactivar la presencia de Glumi |
| LibreLinkUp API | Depends on | Fuente del valor de glucosa y del estado de error que determinan el estado de Glumi |

---

## Open Questions

| Question | Context | Owner | Target Date | Status | Resolution |
| -------- | ------- | ----- | ----------- | ------ | ---------- |
| ¿Cómo exactamente aumenta el protagonismo de Glumi cuando hay una alarma activa (tamaño, animación, duración del aumento)? | El brief indica que una alarma activa puede aumentar temporalmente el protagonismo de Glumi, pero el comportamiento depende de la feature de Alarmas, aún no especificada. | | | Open | |
| ¿En qué condiciones se retoma esta feature para el roadmap? | Descoped del MVP el 2026-10-03; no hay fecha ni trigger definido para reconsiderarla. | | | Open | |
