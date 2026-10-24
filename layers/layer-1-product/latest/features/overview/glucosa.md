---
title: "Glucosa"
layer: product
owner: ""
last_updated: "2026-09-30"
relates_to: []
status: in_progress
---

# Feature: Glucosa

---

## Problem and Context

Las personas que usan un sensor de glucosa continua (CGM) necesitan saber su nivel actual de un vistazo, sin abrir la app oficial del sensor. Glumi expone ese valor de forma clara y legible, con contexto de tendencia y de rango, directamente en la pantalla principal.

---

## Users

Usuarios de Glumi que tienen un sensor LibreLink activo y lo han vinculado mediante LibreLinkUp. Acceden a esta información en cualquier momento del día, frecuentemente con una sola mirada rápida a la pantalla.

---

## Feature Description

La pantalla principal muestra el valor de glucosa más reciente obtenido de LibreLinkUp. El valor se presenta como número entero en la unidad que el sensor reporta (heredada de LibreLink, típicamente mg/dL), acompañado de una flecha de tendencia que indica la dirección del cambio.

La flecha adopta un color según el rango en que se encuentra el valor actual:

- **Verde (#91E4B3):** el valor está dentro del rango configurado como "en rango".
- **Amarillo (#FFE16F):** el valor está en rango alto (entre el límite superior de "en rango" y 240 mg/dL).
- **Rojo (#FF7B87):** el valor es muy alto (mayor a 240 mg/dL) o bajo (menor al límite inferior de "en rango").

El número de glucosa siempre se muestra en blanco; solo la flecha cambia de color.

La flecha de tendencia refleja 1:1 los cinco estados que entrega LibreLinkUp: bajando rápido, bajando, estable, subiendo, subiendo rápido.

El valor se actualiza en vivo, en sincronía con las lecturas que entrega el sensor LibreLink.

Cuando LibreLinkUp reporta un estado de error (por ejemplo, sensor desconfigurado, sin señal, o cualquier condición que impida entregar un valor válido), la pantalla reemplaza el número y la flecha por `---` y el mensaje "Revisar conexión". Glumi no conserva ni muestra el último valor conocido en estos casos.

---

## Business Rules

| Rule | Description | Confidence | Source |
| ---- | ----------- | ---------- | ------ |
| BR-1 | El valor de glucosa mostrado es siempre un número entero. LibreLink no entrega decimales en mg/dL. | Confirmed | LibreLink behavior |
| BR-2 | La unidad de medida se hereda de LibreLink. Glumi no la configura de forma independiente. | Confirmed | Product decision |
| BR-3 | La dirección de tendencia se hereda 1:1 de LibreLinkUp: cinco estados (bajando rápido, bajando, estable, subiendo, subiendo rápido). | Confirmed | Product decision |
| BR-4 | Solo la flecha de tendencia adopta el color del rango. El número de glucosa se muestra siempre en blanco. | Confirmed | Diseño (Figma) |
| BR-5 | El umbral de "Muy alto" es fijo en 240 mg/dL. No es configurable por el usuario. | Confirmed | Product decision |
| BR-6 | Los límites del rango "en rango" son configurables por el usuario en Settings. El límite inferior puede estar entre 70 y 179 mg/dL; el superior, entre 71 y 180 mg/dL. El inferior debe ser menor que el superior. | Confirmed | Product decision |
| BR-7 | Los valores por defecto del rango son: En rango 70–180, Alto 181–240, Muy alto >240, Bajo <70. | Confirmed | LibreLink defaults |
| BR-8 | Cualquier error reportado por LibreLinkUp activa el estado de error genérico (`---` + "Revisar conexión"). Glumi no distingue la causa ni muestra el último valor conocido. | Confirmed | Product decision |
| BR-9 | El estado de error se hereda 1:1 de LibreLinkUp. Glumi no define su propio umbral de antigüedad de datos. | Confirmed | Product decision |

---

## Acceptance Criteria

- Dado un valor de glucosa en rango, la flecha se muestra en verde (#91E4B3) y el número en blanco.
- Dado un valor en rango alto (entre límite superior de "en rango" y 240), la flecha se muestra en amarillo (#FFE16F).
- Dado un valor muy alto (>240) o bajo (por debajo del límite inferior de "en rango"), la flecha se muestra en rojo (#FF7B87).
- El número de glucosa nunca cambia de color, independientemente del rango.
- La flecha refleja la dirección de tendencia entregada por LibreLinkUp, sin transformaciones.
- Cuando LibreLinkUp reporta error, la pantalla muestra `---` y "Revisar conexión", sin mostrar ningún valor numérico previo.
- Si el usuario ha cambiado sus límites de rango en Settings, la clasificación de color usa esos límites personalizados.

---

## Edge Cases

| Scenario | Expected Behavior | Notes |
| -------- | ----------------- | ----- |
| LibreLinkUp entrega error inmediatamente al abrir la app | Se muestra el estado de error desde el primer render, sin mostrar un valor temporal | No hay valor previo disponible |
| El usuario cambia sus límites de rango en Settings | El color de la flecha en la pantalla principal se recalcula con los nuevos límites | El recálculo ocurre con la próxima lectura |
| El valor está exactamente en el límite (ej. 180 mg/dL) | Se clasifica como "en rango" si el límite superior configurado es 180 | Los límites son inclusivos |
| El valor es exactamente 240 mg/dL | Se clasifica como "alto", no "muy alto" | Muy alto es >240 |
| El valor es exactamente 241 mg/dL | Se clasifica como "muy alto" y la flecha es roja | |

---

## Out of Scope

- Configuración de los límites de rango — eso pertenece a la feature de Settings.
- La visualización del horario (hora de última medición y hace cuánto) — pertenece a la feature Horario.
- Historial de lecturas o gráficos de tendencia.
- Alertas o notificaciones por rango fuera de límites.

---

## Dependencies

| Dependency | Type | Notes |
| ---------- | ---- | ----- |
| LibreLinkUp API | Depends on | Fuente de datos del valor, tendencia, unidad y estado de error |
| Settings / Configuración de rangos | Depends on | Define los límites de "en rango" que usa esta feature para clasificar el color |

---

## Open Questions

| Question | Context | Owner | Target Date | Status | Resolution |
| -------- | ------- | ----- | ----------- | ------ | ---------- |
|          |         |       |             |        |            |
