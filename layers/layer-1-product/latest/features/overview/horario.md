---
title: "Horario"
layer: product
owner: ""
last_updated: "2026-09-30"
relates_to: []
status: in_progress
---

# Feature: Horario

---

## Problem and Context

El usuario necesita saber no solo cuál es su nivel de glucosa actual, sino cuándo fue tomada esa medición. Un valor sin contexto temporal puede llevar a decisiones incorrectas — un 112 de hace 1 minuto es muy distinto a uno de hace 45 minutos.

---

## Users

Todos los usuarios de Glumi con un sensor LibreLink activo. Consultan esta información de forma pasiva cada vez que miran la pantalla principal.

---

## Feature Description

El top-bar de la pantalla principal muestra dos piezas de información temporal, siempre juntas y separadas por un punto medio:

- **La hora de la última medición:** el momento en que LibreLinkUp registró el valor actual. El formato (12h o 24h) se hereda del sistema operativo del dispositivo, sin configuración propia en Glumi.
- **El tiempo transcurrido desde esa medición:** un contador en tiempo real que se actualiza minuto a minuto. El formato varía según la antigüedad:
  - Menos de 1 minuto: "hace menos de 1 min"
  - Entre 1 y 59 minutos: "hace X min"
  - 60 minutos o más: "hace más de 1 hora"

El texto aparece en blanco en condiciones normales. Cuando LibreLinkUp reporta un estado de error, el texto del tiempo transcurrido cambia a naranja, alineándose visualmente con el estado de error de la pantalla.

El horario se muestra en ambos estados de la pantalla: normal y error.

---

## Business Rules

| Rule | Description | Confidence | Source |
| ---- | ----------- | ---------- | ------ |
| BR-1 | La hora mostrada corresponde al momento de la última medición, no a la hora actual del dispositivo. | Confirmed | Product decision |
| BR-2 | El formato de hora (12h o 24h) se hereda del sistema operativo. Glumi no ofrece configuración propia. | Confirmed | Product decision |
| BR-3 | El contador de tiempo transcurrido se actualiza en tiempo real, minuto a minuto. | Confirmed | Product decision |
| BR-4 | Formato del contador: menos de 1 min → "hace menos de 1 min"; 1–59 min → "hace X min"; 60 min o más → "hace más de 1 hora". | Confirmed | Product decision |
| BR-5 | El texto del tiempo transcurrido cambia a naranja únicamente cuando LibreLinkUp reporta error. En condiciones normales es blanco. | Confirmed | Diseño (Figma) |
| BR-6 | Hora y tiempo transcurrido se muestran siempre juntos, separados por "•" (ej. "10:24 • hace 1 min"). | Confirmed | Diseño (Figma) |
| BR-7 | El horario se muestra en ambos estados de la pantalla: normal y error. | Confirmed | Diseño (Figma) |

---

## Acceptance Criteria

- La hora mostrada corresponde a la de la última medición recibida de LibreLinkUp, no a la hora actual.
- El formato de hora respeta la configuración de 12h/24h del dispositivo.
- El contador muestra "hace menos de 1 min" cuando la medición tiene menos de 60 segundos.
- El contador muestra "hace X min" para valores entre 1 y 59 minutos, actualizándose cada minuto.
- El contador muestra "hace más de 1 hora" a partir de los 60 minutos.
- El texto del tiempo transcurrido es naranja cuando LibreLinkUp está en estado de error, y blanco en condiciones normales.
- Hora y tiempo transcurrido siempre aparecen juntos con el separador "•".

---

## Edge Cases

| Scenario | Expected Behavior | Notes |
| -------- | ----------------- | ----- |
| La medición acaba de llegar (< 1 min) | Se muestra "hace menos de 1 min" | No se silencia el texto |
| El contador llega a los 60 minutos | Pasa a mostrar "hace más de 1 hora" sin seguir contando | Tope fijo |
| LibreLinkUp reporta error desde el inicio | El tiempo transcurrido aparece en naranja; la hora de la última medición se muestra igualmente | El horario no desaparece en estado de error |
| El dispositivo cambia entre 12h y 24h en runtime | El formato de hora se actualiza en la próxima lectura | Heredado del sistema |

---

## Out of Scope

- Configuración del formato de hora dentro de Glumi — se hereda del sistema operativo.
- Alertas o notificaciones basadas en la antigüedad de la lectura.
- Mostrar segundos en el contador.

---

## Dependencies

| Dependency | Type | Notes |
| ---------- | ---- | ----- |
| LibreLinkUp API | Depends on | Fuente del timestamp de la última medición y del estado de error |
| Sistema operativo (locale) | Depends on | Define el formato de hora (12h/24h) |

---

## Open Questions

| Question | Context | Owner | Target Date | Status | Resolution |
| -------- | ------- | ----- | ----------- | ------ | ---------- |
|          |         |       |             |        |            |
