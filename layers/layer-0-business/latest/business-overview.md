---
title: "Business Overview"
layer: business
owner: "Matias Salzman"
status: needs_review
last_updated: 2026-09-15
relates_to:
  - layers/layer-0-business/latest/competitive-landscape.md
  - layers/layer-0-business/latest/stakeholder-map.md
  - layers/layer-0-business/latest/strategic-goals-and-constraints.md
---

# Business Overview

Quién está detrás del proyecto, cómo opera hoy y cuál es su realidad actual. Es el primer documento que debería leer cualquiera que se suma.

---

## Company Identity and History

No hay empresa. El proyecto es una iniciativa personal de Matias Salzman: autofinanciada, sin entidad legal, sin inversión externa y sin deadline impuesto. Una sola persona cubre producto, diseño, firmware, enclosure y todas las decisiones. La marca todavía no tiene nombre ni identidad definida ⚠️.

El fundador vive con diabetes tipo 1 y usa CGM. El producto nace de una fricción propia y diaria —consultar la glucosa decenas de veces al día a través del teléfono— y no de una oportunidad detectada desde afuera. Esa condición le da al proyecto autoridad de dominio real y un usuario cero permanentemente disponible.

La ambición declarada para los próximos 12-18 meses excede al primer producto: construir una marca de objetos cotidianos para personas que viven con T1D, con Argentina como primer mercado y la región como horizonte. El display de glucosa es el producto de entrada, no el destino. Esa ambición convive con una capacidad de ejecución de side project unipersonal, y la distancia entre ambas es la tensión estructural del proyecto.

---

## Business Model

Hoy no hay modelo de negocio y no hay ingresos. El único costo es el tiempo del fundador más los componentes del prototipo.

La intención es vender hardware: un objeto terminado, no un kit ni un servicio. El modelo comercial y el precio están sin definir ⚠️ — la referencia de mercado disponible es que un SugarPixel importado a Argentina ronda los USD 200-300. Lo que el modelo no puede ser sin contradecir la tesis del producto es una suscripción obligatoria: el usuario con T1D ya paga sensores caros todos los meses y el producto se posiciona como alivio, no como otro cargo recurrente.

La cadena de valor prevista es corta y deliberadamente liviana: firmware propio sobre componentes off-the-shelf (ESP32 con display integrado), enclosure impreso en 3D, ensamblado manual y venta directa. Sin PCB propia, sin tooling, sin certificaciones y sin inventario. Esa liviandad es lo que hace posible el MVP con los recursos actuales, y también el techo que habrá que romper para producir a escala.

---

## Customer Segments

El segmento raíz es angosto y concreto: personas con diabetes tipo 1 que usan CGM, consultan su glucosa con alta frecuencia y pasan períodos significativos en espacios relativamente fijos —casa u oficina—. Sobre esa base, la validación arranca en un solo usuario y se expande hacia la comunidad T1D local.

| Segment | Description | Relative Importance |
| ------- | ----------- | ------------------- |
| Usuario cero (el fundador) | Persona con T1D que usa FreeStyle Libre, trabaja en escritorio y quiere el dato accesible de noche y mientras trabaja. Única fuente de validación diaria hoy. | Crítica — define el MVP |
| Personas con T1D + CGM en Argentina | Adultos con cierta familiaridad tecnológica, que valoran no recurrir al teléfono. Mayoritariamente usuarios de FreeStyle Libre. | Alta — primer mercado |
| Convivientes y familiares | Pareja, padres, hijos que quieren ver el dato en casa sin tener que preguntar. Consumidores secundarios del mismo dispositivo. | Media — sin validar ⚠️ |
| Padres de niños con T1D | Segmento donde el dolor nocturno es más agudo y la disposición a pagar es más alta en otros mercados. Implica requisitos de alerta que el MVP no cubre. | Media — a explorar |
| Comunidad DIY (Nightscout / xDrip) | Usuarios técnicos que ya resolvieron el problema por su cuenta. Early adopters naturales y, a la vez, los menos dispuestos a pagar. | Baja — fuera del MVP |

---

## Organizational Structure and Culture

La estructura entera es una persona rotando entre roles. No hay equipos, comités, handoffs ni aprobaciones: el costo de coordinación es cero y la velocidad de decisión es máxima. Es la mayor ventaja operativa del proyecto.

También es su debilidad estructural. No existe contrapeso: nadie revisa las decisiones de diseño industrial ni de hardware, que son justamente las áreas de menor experiencia del decisor. La función de revisión tiene que importarse desde afuera —usuarios T1D reales probando el objeto, consulta puntual a especialistas— porque dentro del proyecto no hay quien la ejerza.

El ritmo es discontinuo: noches y fines de semana, sin deadline y sin nadie a quien reportar. Nada externo detecta si el proyecto se detiene.

---

## Existing Systems and Technology Landscape

El proyecto no construye infraestructura: se apoya sobre el CGM que el usuario ya tiene y consume su dato desde el dispositivo. El MVP no contempla backend propio, aplicación móvil ni cuenta de usuario.

| System | Purpose | Notes |
| ------ | ------- | ----- |
| FreeStyle Libre (Abbott) | CGM del usuario cero. Fuente del dato y referencia terapéutica oficial. | El producto nunca la reemplaza ni la contradice. |
| LibreLinkUp (API no oficial) | Única fuente de datos del MVP. | Sin contrato, sin SLA y sin permiso. Abbott puede modificarla o cortarla sin aviso. Riesgo técnico y legal sin evaluar ⚠️ |
| ESP32 con display integrado | Plataforma de hardware del MVP. | Componente off-the-shelf. Sin PCB propia y sin batería: alimentación continua por USB-C. |
| Impresión 3D | Enclosure. | Permite iterar rápido sobre proporciones, orientación, materiales y presencia física. |
| Wi-Fi doméstico | Conectividad. | Sin conectividad celular ni BLE directo al sensor: el producto solo funciona en un espacio con red conocida. |
| Nightscout / Dexcom | Fuentes de datos alternativas. | Fuera del alcance del MVP. Nightscout ampliaría el alcance a la comunidad DIY; Dexcom es minoritario en Argentina. |
