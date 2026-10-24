---
title: "Sleep Mode"
layer: product
owner: ""
last_updated: "2026-09-30"
relates_to: []
status: in_progress
---

# Feature: Sleep Mode

---

## Problem and Context

Los usuarios que dejan el dispositivo en el velador durante la noche necesitan que la pantalla no los moleste mientras duermen, pero tampoco que se apague — quieren poder ver la glucosa con una mirada si se despiertan. Sleep Mode resuelve eso bajando el brillo al mínimo y manteniendo la pantalla encendida.

---

## Users

Usuarios de Glumi que utilizan el dispositivo como monitor nocturno, dejándolo encendido en la mesita de luz mientras duermen.

---

## Feature Description

Sleep Mode es un estado de la app que baja el brillo real del dispositivo al mínimo y mantiene la pantalla encendida (keep-awake), permitiendo al usuario dormir sin ser molestado por la luz mientras conserva acceso visual a la glucosa.

El usuario activa Sleep Mode presionando el ícono de luna en el top-bar. Al activarse, el ícono de luna se llena visualmente para indicar que el modo está activo, y el brillo del dispositivo baja al mínimo.

Para salir de Sleep Mode el usuario puede tocar cualquier parte de la pantalla. Además, si se dispara una alarma propia de Glumi, Sleep Mode se desactiva automáticamente. En ambos casos, el brillo del dispositivo vuelve al nivel estándar del sistema.

Sleep Mode no persiste entre sesiones: al cerrar y volver a abrir la app, la pantalla siempre arranca en modo normal.

---

## Business Rules

| Rule | Description | Confidence | Source |
| ---- | ----------- | ---------- | ------ |
| BR-1 | Sleep Mode baja el brillo real del dispositivo al mínimo. No usa un overlay de software. | Confirmed | Product decision |
| BR-2 | Sleep Mode mantiene la pantalla encendida (keep-awake) mientras está activo. | Confirmed | Product decision |
| BR-3 | Se activa presionando el ícono de luna en el top-bar. | Confirmed | Diseño (Figma) |
| BR-4 | Se desactiva tocando cualquier parte de la pantalla. | Confirmed | Product decision |
| BR-5 | Se desactiva automáticamente cuando se dispara una alarma propia de Glumi. | Confirmed | Product decision |
| BR-6 | Al desactivarse, el brillo vuelve al nivel estándar del dispositivo. | Provisional | A definir |
| BR-7 | El ícono de luna se muestra relleno cuando Sleep Mode está activo, y vacío cuando está inactivo. | Confirmed | Diseño (Figma) |
| BR-8 | Sleep Mode no persiste entre sesiones. Al reabrir la app, siempre arranca en modo normal. | Confirmed | Product decision |

---

## Acceptance Criteria

- Al presionar el ícono de luna, el brillo del dispositivo baja al mínimo y la pantalla se mantiene encendida.
- El ícono de luna aparece relleno mientras Sleep Mode está activo.
- Tocar cualquier parte de la pantalla desactiva Sleep Mode y restaura el brillo.
- Cuando se dispara una alarma de Glumi, Sleep Mode se desactiva automáticamente.
- Al desactivarse, el brillo vuelve al nivel estándar del dispositivo.
- Al cerrar y reabrir la app, Sleep Mode no está activo.

---

## Edge Cases

| Scenario | Expected Behavior | Notes |
| -------- | ----------------- | ----- |
| El usuario activa Sleep Mode y el dispositivo recibe una alarma de Glumi | Sleep Mode se desactiva y el brillo se restaura | Las alarmas propias de Glumi tienen prioridad sobre Sleep Mode |
| El usuario cierra la app con Sleep Mode activo | Al reabrir, la app arranca en modo normal con brillo estándar | Sleep Mode no persiste |
| El usuario activa Sleep Mode con el brillo del dispositivo ya al mínimo | No hay cambio perceptible en el brillo; el ícono igual se muestra relleno | Comportamiento normal |

---

## Out of Scope

- La configuración y comportamiento de las alarmas de Glumi — pertenece a una feature de Settings.
- Control de notificaciones externas del sistema operativo — Sleep Mode no las afecta.
- Configuración del nivel de brillo objetivo — siempre baja al mínimo del sistema.

---

## Dependencies

| Dependency | Type | Notes |
| ---------- | ---- | ----- |
| Alarmas de Glumi | Interacts with | Una alarma activa desactiva Sleep Mode automáticamente |
| API de brillo del dispositivo | Depends on | Necesaria para bajar y restaurar el brillo del hardware |

---

## Open Questions

| Question | Context | Owner | Target Date | Status | Resolution |
| -------- | ------- | ----- | ----------- | ------ | ---------- |
| ¿A qué nivel exacto se restaura el brillo al salir de Sleep Mode? | ¿Nivel previo al activar Sleep Mode, o nivel estándar del sistema? | | | Open | |
