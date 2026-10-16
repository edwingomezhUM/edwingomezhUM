# Caso de Estudio 2: Plataforma Web para Medición Inteligente (AMI) y Analítica Energética

## 1. Contexto y Origen

La modernización de la infraestructura eléctrica hacia redes inteligentes (*Smart Grids*) demanda la incorporación de sistemas de medición avanzada (**AMI** - *Advanced Metering Infrastructure*). Este proyecto corresponde a la plataforma web central de monitoreo, telemedida y balance energético desarrollada en el marco de proyectos de transferencia tecnológica del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una **empresa regional de distribución y comercialización de energía eléctrica**.

---

## 2. Problema Técnico

La transición de lecturas mensuales tradicionales a la telemetría continua genera un incremento exponencial en los volúmenes de datos: cada medidor inteligente emite registros de consumo en intervalos horarios o cuartohorarios (hasta 2.880 muestras mensuales por suscriptor). Los retos de ingeniería identificados fueron:
1. **Auditoría visual de grandes volúmenes temporales:** Carencia de una interfaz centralizada y fluida capaz de procesar y graficar curvas de carga de miles de usuarios sin degradar la memoria del navegador.
2. **Cálculo de balances y detección de pérdidas:** Necesidad de cotejar la energía inyectada en transformadores de distribución contra la suma de lecturas de medidores aguas abajo, identificando discrepancias por pérdidas técnicas o posibles anomalías comerciales.
3. **Control de inconsistencias de inventario:** Presencia de medidores transmitiendo activamente que carecían de asociación con un Número de Identificación de Usuario (**NIU**) en los sistemas comerciales, lo que requería conciliación operativa.

---

## 3. Objetivo

Construir una plataforma web empresarial, modular y responsiva para permitir al equipo técnico auditar la matriz de clientes telemedidos, visualizar perfiles de carga horaria, conciliar balances energéticos y gestionar contingencias de equipos de medida.

---

## 4. Contribución Técnica y Diferenciación de Alcances

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE RESPONSABILIDADES                       │
├───────────────────────────────────┬────────────────────────────────────┤
│ Contribución Individual           │ Trabajo Colaborativo e             │
│ Verificada en Código              │ Institucional                      │
├───────────────────────────────────┼────────────────────────────────────┤
│ • 17 de los 19 commits del        │ • Diseño de bases de datos y       │
│   repositorio principal (89.5% de │   almacenamiento de series         │
│   autoría verificada en Git).     │   temporales de telemedida.        │
│ • Arquitectura del layout general │ • Definición de fórmulas de        │
│   y enrutamiento modular.         │   balance energético con           │
│ • Desarrollo de la matriz de      │   especialistas en distribución.   │
│   clientes (`matriz-cliente`).    │ • Configuración de infraestructura │
│ • Integración de visualizadores   │   de red y concentradores AMI.     │
│   de series temporales con        │                                    │
│   ApexCharts y Chart.js.          │                                    │
│ • Implementación del servicio     │                                    │
│   reactivo multitema (Dark/Light).│                                    │
│ • Vistas para reportes dinámicos. │                                    │
└───────────────────────────────────┴────────────────────────────────────┘
```

- **Pruebas y Validación Local:** Cobertura de 25 suites de pruebas unitarias (`.spec.ts`) en Angular para validar la reactividad de los filtros, la gestión de estados y la conmutación de temas.
- **Estado de Producción:** Plataforma entregada para la operación y supervisión de cuadrillas y especialistas de ingeniería de medición de la empresa de energía.

---

## 5. Tecnologías y Arquitectura

```
                  ARQUITECTURA DE LA PLATAFORMA DE MEDICIÓN INTELIGENTE
                                                                      
    ┌─────────────────────────────────────────────────────────────┐
    │           Frontend Angular (Standalone Components)          │
    │                                                             │
    │  ┌────────────────────┐ ┌────────────────────────────────┐  │
    │  │    ThemeService    │ │    MatrizClienteService        │  │
    │  │ (Dark/Light State) │ │ (Filtros, Paginación, Estado)  │  │
    │  └─────────┬──────────┘ └──────────────┬─────────────────┘  │
    │            │                           │                    │
    │  ┌─────────┴──────────┐ ┌──────────────┴─────────────────┐  │
    │  │  Tailwind CSS UI   │ │   Visualizadores ApexCharts    │  │
    │  │ (Component Layout) │ │ (Curvas de Carga Horarias)     │  │
    │  └────────────────────┘ └────────────────────────────────┘  │
    └───────────────────────────────┬─────────────────────────────┘
                                    │ HTTP / REST APIs
                                    ▼
    ┌─────────────────────────────────────────────────────────────┐
    │          Backend de Datos & Servicios de Telemedida         │
    │                                                             │
    │  ┌───────────────────────┐     ┌─────────────────────────┐  │
    │  │ API de Balances & MDM │     │  Consolidador de Curvas │  │
    │  │   (Python / Flask)    │     │   (Lecturas Horarias)   │  │
    │  └───────────┬───────────┘     └────────────┬────────────┘  │
    └──────────────┼──────────────────────────────┼───────────────┘
                   │                              │
                   ▼                              ▼
    ┌─────────────────────────────────────────────────────────────┐
    │      Repositorios de Datos de Telemedida y Sistema AMI      │
    └─────────────────────────────────────────────────────────────┘
```

- **Frontend:** Angular (v17/v18), TypeScript, Tailwind CSS, ApexCharts, Chart.js, RxJS.
- **Backend de Integración:** Python (Flask), Node.js (Express), APIs RESTful.
- **Diseño de Interfaz:** Maquetación responsiva con Tailwind CSS adaptada para pantallas de centros de control y estaciones de escritorio.

---

## 6. Decisiones de Diseño Técnico

1. **Uso de ApexCharts para Curvas de Carga:** Se seleccionó esta librería por su eficiencia para renderizar series temporales densas, ofreciendo herramientas nativas de paneo, zoom por rangos horarios y tooltips informativos sin sobrecargar el hilo del navegador.
2. **Servicios Reactivos de Estado para Filtros:** Se encapsuló el estado de filtrado de la matriz en `MatrizClienteService`, permitiendo conservar los criterios de búsqueda (circuito, estado de enlace, tipo de tarifa) al alternar entre pantallas sin realizar peticiones redundantes.
3. **Servicio Centralizado Multitema:** Se diseñó un servicio desacoplado (`ThemeService`) que propaga cambios en el DOM y actualiza en caliente los colores de los lienzos gráficos, facilitando la visualización en entornos con poca luz típicos de salas de control.

---

## 7. Funcionalidades Principales

- **Exploración de Matriz de Medidores:** Filtros combinados por subestación, circuito, transformador y estado de transmisión.
- **Inspección de Perfiles Horarios:** Graficación de curvas de potencia activa y reactiva por cliente y transformador.
- **Balance Energético Agregado:** Comparación de balances entre macro-medición de entrada y suma de consumos individuales.
- **Bandeja de Medidores sin NIU:** Módulo administrativo para auditar equipos en operación que requieren asignación en la base comercial.
- **Exportación de Informes:** Generación de resúmenes consolidados para análisis de pérdidas.

---

## 8. Desafíos Afrontados

- **Fluidez Gráfica con Múltiples Puntos:** Mantener una tasa de refresco estable al graficar perfiles continuos de varios días mediante agregación previa de datos y optimización de opciones de animación.
- **Manejo de Estados de Conexión Incompletos:** Adaptar las tablas y gráficos para reflejar de forma clara lapsos donde medidores remotos experimentaron cortes temporales de comunicación.

---

## 9. Evidencias Técnicas Verificables

- **Módulos clave implementados en el frontend:**
  - `src/app/features/matriz-cliente/`
  - `src/app/layout/main-layout/`
  - `src/app/core/services/theme.service.ts`
  - `src/app/features/reportes/`
- **Pruebas Unitarias:** 25 archivos `.spec.ts` verificados en el frontend.
- **Historial de Commits:** 17 commits con autoría individual directa verificados en el historial de Git (89.5% del desarrollo del frontend).

---

## 10. Hechos Verificados vs. Aspectos a Confirmar

| Categoría | Detalle Técnico / Operativo | Estado |
| :--- | :--- | :--- |
| **Hecho Verificado** | Autoría comprobable del 89.5% de los commits del frontend. | Verificado en Git log. |
| **Hecho Verificado** | Implementación de matriz de clientes, integración de ApexCharts y ThemeService. | Verificado en archivos del código fuente. |
| **Hecho Verificado** | Presencia de 25 suites de pruebas unitarias en Angular. | Verificado en la estructura local. |
| **Aspecto a Confirmar** | Total de medidores inteligentes desplegados en campo por la empresa de energía. | Requiere confirmación si se desea citar públicamente. |
| **Aspecto a Confirmar** | Porcentaje oficial de reducción o detección de pérdidas energéticas. | Requiere autorización institucional previa. |

---

## 11. Estrategia de Confidencialidad y Demo Sintética

- **Información Protegida:** Se suprimen nombres de subestaciones reales, identificadores de contrato, números de cédula, códigos de medidor reales y cifras financieras de pérdidas de energía.
- **Propuesta de Demostración Pública:** Elaborar un prototipo demostrativo en Angular poblado con un conjunto sintético de 20 transformadores y 50 medidores inteligentes ficticios, mostrando la matriz de consulta, el cálculo de balance simulado y las curvas de carga interactivas sin comprometer datos confidenciales de la distribuidora.
