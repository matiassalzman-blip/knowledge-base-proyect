---
title: "Stakeholder Map"
layer: business
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-15
relates_to:
  - layers/layer-0-business/latest/business-overview.md
  - layers/layer-0-business/latest/strategic-goals-and-constraints.md
---

# Stakeholder Map

Las personas y entidades que influyen, deciden o se ven afectadas por el proyecto. Documento de consulta recurrente: a quién involucrar, con quién alinearse y quién puede bloquear o impulsar el avance.

---

## Stakeholder Register

El registro tiene una particularidad que conviene leer antes de la tabla: los actores con mayor poder sobre el destino del producto son aquellos con los que el proyecto no tiene ninguna relación. Abbott y ANMAT pueden detenerlo, y ninguno de los dos sabe que existe.

| Name | Role / Title | Influence | Engagement Level | Primary Concerns |
| ---- | ------------ | --------- | ---------------- | ---------------- |
| Matias Salzman | Fundador y usuario cero. Producto, diseño, firmware, enclosure y decisiones. | High | Decision-maker | Que el objeto funcione en su propia mesa de luz; que el dato sea confiable; que el proyecto avance con tiempo discontinuo. |
| Convivientes y familia del usuario cero | Usuarios secundarios del mismo dispositivo dentro del hogar. | Medium | Informed — validación informal | Poder ver el dato sin preguntar; que el objeto no moleste de noche ni desentone en el ambiente. |
| Comunidad T1D en Argentina | Early adopters y única fuente de validación externa disponible. | Medium | Contributor — todavía no involucrada ⚠️ | Que resuelva un problema real y no sea otro gadget; que el precio sea accesible en pesos. |
| Abbott (FreeStyle Libre / LibreLinkUp) | Proveedor de facto del dato. Sin relación establecida ni contacto. | High | Ninguno — no consultado | Control sobre su API y sus términos de servicio. Puede dejar el producto sin datos de forma unilateral. |
| ANMAT | Autoridad regulatoria de productos médicos en Argentina. | High al comercializar | Ninguno — no consultado ⚠️ | Clasificación del producto y claims asociados. No aplica al uso personal. |
| Profesionales de salud (endocrinología, educación en diabetes) | Influenciadores de adopción y de confianza del usuario. | Low hoy — Medium al comercializar | Ninguno | Que el producto no induzca decisiones terapéuticas sobre un dato secundario o desactualizado. |
| Proveedores de componentes y manufactura | Componentes electrónicos, impresión 3D, ensamblado. | Low en el MVP — High en producción | Ninguno ⚠️ | Costos, plazos y escala mínima viable. |

---

## Decision-Making Structure

Todas las decisiones —alcance, diseño, técnica y comercial— las toma una sola persona, sin proceso formal, sin comité y sin plazos de aprobación. No hay sponsor, no hay presupuesto que aprobar y no hay a quién reportar.

Esto hace que la velocidad de decisión sea máxima y el costo de coordinación, cero. La contrapartida es que no existe ningún contrapeso: nadie cuestiona una decisión de producto, nadie revisa una elección de diseño industrial y nadie detecta desde afuera si el proyecto se frenó.

La consecuencia práctica es que la función de revisión debe importarse deliberadamente. Para este proyecto eso significa tres cosas concretas: poner el objeto en manos de otras personas con T1D antes de comprometerse con una dirección de producto, consultar a un especialista en diseño industrial antes de congelar una forma física, y consultar asesoría regulatoria antes de vender la primera unidad. Ninguna de las tres está en marcha.

---

## Key Relationships and Dynamics

La tensión central del proyecto es de escala: la ambición declarada —una marca regional de objetos para personas con T1D— y la capacidad instalada —una persona, tiempo discontinuo, sin experiencia previa en hardware ni manufactura— están a mucha distancia. Documentarla no la resuelve, pero evita que las decisiones de Layer 1 y Layer 3 asuman recursos que no existen.

El fundador es simultáneamente decisor, ejecutor y usuario. Eso da velocidad y autoridad de dominio genuina, y al mismo tiempo hace estructuralmente imposible distinguir "esto me sirve a mí" de "esto le sirve al mercado". La validación con otros usuarios con T1D es el único mecanismo capaz de romper ese sesgo, y es también uno de los criterios de éxito del MVP — lo que lo convierte en prioridad y no en un paso opcional más adelante.

La relación con Abbott es asimétrica y no recíproca: el producto depende por completo de LibreLinkUp, y Abbott no sabe que el producto existe ni tiene incentivo alguno para sostener ese acceso. Es la dependencia más fuerte del proyecto y la que menos control admite.
