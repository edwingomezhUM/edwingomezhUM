# Caso de Estudio 3: Sistema de Gestión de Colas y Atención en Sala (SGCB)

## 1. Contexto y Origen

La atención presencial a usuarios en oficinas y salas de servicio al cliente de empresas de servicios públicos requiere una coordinación precisa entre el flujo de llegada de ciudadanos, la asignación equitativa de ventanillas y el monitoreo de tiempos de espera. Este proyecto corresponde al **Sistema de Gestión de Colas y Billeteras / Atención en Salas (SGCB)**, desarrollado en [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una **empresa regional de servicios públicos domiciliarios (distribución de energía eléctrica)**.

---

## 2. Problema Técnico

La operación presencial en salas de atención al público presentaba tres desafíos operativos:
1. **Picos de congestión y priorización normativa:** Dificultad para ordenar la afluencia en horarios de alta demanda y garantizar la atención prioritaria requerida por la normativa para adultos mayores, personas en situación de discapacidad y mujeres gestantes.
2. **Carencia de visibilidad en tiempo real para coordinadores:** Ausencia de tableros inmediatos que informen cuántos usuarios esperan por cada tipo de trámite (pago, reclamo, acuerdo de pago, nueva conexión) y qué asesores están disponibles o en atención.
3. **Sincronización audiovisual en sala:** Necesidad de que el llamado emitido por un asesor desde su módulo se refleje de manera instantánea y sin desincronización en las pantallas públicas de la sala de espera.

---

## 3. Objetivo

Desarrollar y mantener una plataforma web cliente-servidor robusta y en tiempo real para la emisión de turnos, el despliegue audiovisual en pantallas de sala de espera y la gestión de la atención en módulos de asesores, facilitando el control de tiempos de espera y el cumplimiento de Acuerdos de Nivel de Servicio (SLA).

---

## 4. Contribución Técnica y Diferenciación de Alcances

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE RESPONSABILIDADES                       │
├───────────────────────────────────┬────────────────────────────────────┤
│ Contribución Individual           │ Trabajo Colaborativo e             │
│ Verificada en Código              │ Institucional                      │
├───────────────────────────────────┼────────────────────────────────────┤
│ • 48 commits individuales         │ • Diseño y mantenimiento de la     │
│   verificados en Git (el mayor    │   base de datos relacional de      │
│   volumen en los proyectos        │   turnos e histórico de atenciones.│
│   auditados de la organización).  │ • Configuración de infraestructura │
│ • Construcción de vistas de la    │   de red local y terminales en     │
│   consola del asesor de atención. │   las sedes físicas.               │
│ • Componentes de pantalla pública │ • Definición de directrices de     │
│   de llamado audiovisual en sala. │   SLA y tipologías de trámite      │
│ • Configuración de enrutamiento   │   con el equipo de atención al     │
│   modular por roles.              │   cliente de la empresa.           │
│ • Maquetación y estilos           │                                    │
│   responsivos con Bootstrap.      │                                    │
└───────────────────────────────────┴────────────────────────────────────┘
```

- **Pruebas y Validación Local:** 17 archivos de pruebas unitarias (`.spec.ts`) en Angular para verificar el flujo de estados de turnos y componentes de presentación.
- **Estado de Producción:** Sistema desplegado para la operación presencial en sedes y centros de servicio de la entidad prestadora de servicios públicos.

---

## 5. Tecnologías y Arquitectura

```
                       ARQUITECTURA OPERATIVA DEL SGCB
                                                               
    ┌──────────────────────┐                     ┌──────────────────────┐
    │  Kiosco de Emisión   │                     │  Monitores de Sala   │
    │  (Generación Turno)  │                     │ (Llamado en Pantalla)│
    └──────────┬───────────┘                     └──────────▲───────────┘
               │                                            │
               │ HTTP Post                                  │ Actualización
               ▼                                            │ Visual
    ┌───────────────────────────────────────────────────────┴───────────┐
    │                       Servidor Web SGCB                           │
    │                (Node.js / Express / REST APIs)                    │
    │                                                                   │
    │   - Gestor de Colas por Categoría (General / Prioritaria / Caja)  │
    │   - Máquina de Estados del Turno (Espera -> Llamado -> Atendido)  │
    └───────────────────────────────▲───────────────────────────────────┘
                                    │
                         Llamar / Atender / Cerrar
                                    │
                         ┌──────────┴──────────┐
                         │  Consola del Asesor │
                         │  (Frontend Angular) │
                         └─────────────────────┘
```

- **Frontend:** Angular, TypeScript, Bootstrap, Chart.js, HTML5 Audio / Canvas.
- **Backend:** Node.js, Express, RESTful APIs.
- **Arquitectura de Vistas:** Desacoplamiento funcional entre modo kiosco/display público (pantalla completa) y consola interactiva de funcionario.

---

## 6. Decisiones de Diseño Técnico

1. **Vistas Especializadas por Dispositivo:** Se estructuró un módulo específico para el monitor de sala, optimizado para visualización pasiva y bajo consumo de memoria en pantallas comerciales y clientes ligeros, separado de la estación interactiva del asesor.
2. **Máquina de Estados Unificada:** Se centralizó la lógica de transición del turno (En espera $\rightarrow$ Llamado $\rightarrow$ En atención $\rightarrow$ Finalizado $\rightarrow$ Ausente) para prevenir llamadas simultáneas del mismo turno ante solicitudes concurrentes.
3. **Resiliencia en Redes de Oficina:** Se implementaron mecanismos de reintento automático y recuperación de estado en el cliente para garantizar que caídas momentáneas en la red local no desincronicen el orden de la fila.

---

## 7. Funcionalidades Principales

- **Clasificación de Turnos:** Emisión diferenciada de tiquetes según prioridad (Atención General, Prioritaria, Cajas y Reclamos).
- **Pantalla Pública de Llamados:** Despliegue de turno con alerta visual en pantalla y módulo asignado.
- **Consola Operativa para Asesores:** Acciones de siguiente turno, rellamado por inasistencia, transferencia entre ventanillas y cierre con tipificación.
- **Métricas de Sala:** Visualización de tiempos medios de espera y número de usuarios pendientes.

---

## 8. Desafíos Afrontados

- **Concurrencia en Momentos de Mayor Afluencia:** Evitar colisiones de asignación de turnos cuando múltiples asesores solicitaban el siguiente usuario en la misma fracción de segundo.
- **Adaptabilidad a Múltiples Formatos de Pantalla:** Garantizar que los monitores de sala mantuvieran legibilidad y encuadre independientemente de la resolución y relación de aspecto de los televisores instalados.

---

## 9. Evidencias Técnicas Verificables

- **Módulos implementados en frontend:**
  - Componentes de llamado audiovisual y visualizador de sala.
  - Vistas de consola del asesor y navegación modular.
- **Pruebas Unitarias:** 17 archivos `.spec.ts` en el código fuente.
- **Historial de Commits:** 48 commits verificados de autoría individual en el control de versiones (mayor contribución unitaria de la auditoría).

---

## 10. Hechos Verificados vs. Aspectos a Confirmar

| Categoría | Detalle Técnico / Operativo | Estado |
| :--- | :--- | :--- |
| **Hecho Verificado** | Autoría de 48 commits individuales en el control de versiones. | Verificado en Git log. |
| **Hecho Verificado** | Implementación de vistas de sala, consola de asesor y maquetación Bootstrap. | Verificado en archivos locales. |
| **Hecho Verificado** | Existencia de 17 pruebas unitarias en la solución. | Verificado en estructura local. |
| **Aspecto a Confirmar** | Total de salas u oficinas donde opera la plataforma actualmente. | Requiere confirmación si se desea citar públicamente. |
| **Aspecto a Confirmar** | Promedio diario de turnos procesados en operación real. | Requiere confirmación si se desea citar públicamente. |

---

## 11. Estrategia de Confidencialidad y Demo Sintética

- **Información Protegida:** Se omiten nombres reales de asesores de atención, evaluaciones laborales individuales, direcciones de sedes físicas y documentos de identidad de usuarios en espera.
- **Propuesta de Demostración Pública:** Crear un prototipo interactivo en Angular que simule una sala de atención mediante tres vistas: un dispensador virtual de turnos, un monitor público con animación de llamado y una consola de asesor funcional que demuestre el flujo completo con datos sintéticos.
