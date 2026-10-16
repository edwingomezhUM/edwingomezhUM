# SGCB — Sistema de Gestión de Colas y Atención en Sala

Plataforma web en tiempo real para la administración de turnos, llamado audiovisual en salas de espera y gestión de módulos de servicio presencial en centros de atención al ciudadano. Desarrollada en el marco de proyectos de transferencia tecnológica del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una empresa regional de servicios públicos domiciliarios.

---

## 1. El reto

La operación presencial de oficinas con alta afluencia ciudadana presenta desafíos operativos y de servicio que impactan directamente la percepción del usuario:

1. **Congestión y priorización normativa:** Dificultad para ordenar la afluencia en horarios de alta demanda y asegurar el cumplimiento de la atención prioritaria para adultos mayores, personas en situación de discapacidad y mujeres gestantes.
2. **Carencia de visibilidad en tiempo real para supervisión:** Ausencia de tableros operativos inmediatos que informen cuántos usuarios esperan por tipología de trámite (reclamos, facturación, acuerdos de pago, conexiones) y qué ventanillas se encuentran activas, en receso o disponibles.
3. **Sincronización en sala de espera:** Necesidad de que la acción de llamado realizada por un asesor desde su módulo se refleje de manera instantánea y coordinada en los monitores públicos de la sala, emitiendo la alerta visual y sonora sin retrasos ni duplicidades.

---

## 2. Mi contribución

En este proyecto trabajé en el desarrollo de las interfaces de usuario, la lógica de estados de los turnos y la adaptabilidad de las pantallas de sala:

- **Desarrollé la consola interactiva del asesor en Angular:** Construí la interfaz de atención que permite a los funcionarios llamar el siguiente turno, rellamar usuarios ausentes, transferir trámites entre ventanillas especializadas y registrar la causa de cierre de cada atención.
- **Implementé los componentes de la pantalla pública de sala:** Diseñé la vista de visualización en pantalla completa (*display mode*) para proyectar el llamado del turno con alertas visuales destacadas y refresco automático, optimizada para televisores y monitores comerciales de sala.
- **Estructuré el sistema de enrutamiento modular por roles:** Diseñé la navegación en Angular para desacoplar el acceso de las terminales de sala (modo pasivo), las estaciones de trabajo de los asesores y los tableros de supervisión operativa.
- **Maqueté interfaces responsivas con Bootstrap:** Adapté los componentes visuales para garantizar legibilidad a distancia en pantallas de televisión de gran formato y ergonomía operativa en monitores de escritorio de asesores.
- **Implementé mecanismos de resiliencia y reconexión:** Añadí lógica en el cliente para gestionar caídas transitorias en la red local de las oficinas sin desincronizar el orden de la fila ni perder el estado del turno actual.
- **Colaboración en equipo:** Participé activamente con el equipo de desarrollo en la integración con las APIs de backend en Node.js y la base de datos relacional de turnos.

---

## 3. La solución

El sistema articula la emisión, asignación y notificación de turnos a través de un flujo coordinado entre dispositivos:

```
[ Kiosco de Emisión ]
        │ Emite tiquete
        ▼
┌────────────────────────────────────────┐
│  Servidor Central SGCB                 │
│  ├── Máquina de estados del turno      │
│  │   (Espera -> Llamado -> Atendido)   │
│  └── Colas por categoría y prioridad   │
└────────────────────────────────────────┘
        │                     │
        │ Siguiente turno     │ Notificación audiovisual
        ▼                     ▼
┌──────────────────┐   ┌──────────────────────────┐
│ Consola Asesor   │   │ Monitores de Sala        │
│ (Angular UI)     │   │ (Display Pantalla Completa)│
└──────────────────┘   └──────────────────────────┘
```

### Decisiones técnicas destacadas

- **Desacoplamiento de vistas por tipo de dispositivo:** Se aisló la pantalla pública de sala como un componente ligero con consumo mínimo de memoria, capaz de ejecutarse de forma continua en navegadores embebidos de Smart TVs o terminales ligeras sin requerir recargas manuales.
- **Máquina de estados determinística:** La transición de estados del turno se centralizó en la lógica de negocio para evitar condiciones de carrera cuando múltiples asesores solicitaban usuarios en la misma fracción de segundo.
- **Diseño visual orientado a legibilidad:** El uso de Bootstrap permitió un ajuste rápido de contrastes y escalas tipográficas, garantizando que los ciudadanos puedan leer el número de turno y la ventanilla asignada desde cualquier ángulo de la sala.

---

## 4. Funcionalidades destacadas

- **Emisión clasificada por prioridad:** Diferenciación automática de turnos entre atención general, preferencial/prioritaria, cajas y trámites específicos de cartera.
- **Pantalla pública con llamado audiovisual:** Presentación visual del turno con alerta sonora para orientar al ciudadano hacia el módulo correspondiente.
- **Consola integral del asesor:** Herramientas para llamar, rellamar por inasistencia, pausar atención por justificación operativa y transferir a otra área.
- **Monitoreo de tiempos de espera:** Indicadores en tiempo real de usuarios en fila y tiempos medios de atención para supervisar el cumplimiento de acuerdos de nivel de servicio (SLA).

---

## 5. Tecnologías aplicadas

- **Frontend:** Angular, TypeScript, Bootstrap, Chart.js, HTML5 Audio y Canvas.
- **Backend y APIs:** Node.js, Express, APIs RESTful.

---

## 6. Resultados y validación técnica

La solución fue evaluada y estabilizada mediante pruebas de componentes y validación de flujos en entorno de desarrollo:

- **Cobertura de pruebas unitarias:** 17 archivos de pruebas unitarias (`.spec.ts`) en Angular que verificaron el ciclo de vida de los turnos, la reactividad de los botones de acción del asesor y la navegación entre rutas protegidas.
- **Sincronización concurrente:** Se verificó el comportamiento del sistema ante peticiones simultáneas de múltiples asesores, confirmando que la cola de espera despachaba los turnos en orden estricto de llegada y prioridad sin duplicidades.
- **Compatibilidad de hardware:** Se comprobó la estabilidad del monitor de sala en navegadores comerciales con resolución estándar y pantallas de alta definición.

---

## 7. Aprendizajes y valor técnico

Este proyecto evidencia experiencia en el desarrollo de software para operaciones de misión crítica y atención al público:

- Criterio para diseñar interfaces de alta eficiencia operativa donde la agilidad del usuario final reduce tiempos de espera acumulados en la jornada.
- Capacidad para estructurar aplicaciones modulares en Angular con múltiples roles de visualización y restricciones de hardware diversas.
- Comprensión de las dinámicas operativas y regulatorias de la atención al ciudadano en el sector de servicios esenciales.
