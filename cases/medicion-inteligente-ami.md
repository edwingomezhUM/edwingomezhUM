# Plataforma Web para Medición Inteligente (AMI) y Analítica Energética

Plataforma empresarial para el monitoreo, auditoría de telemedida y balance energético en redes de distribución eléctrica, orientada a procesar lecturas continuas de medidores inteligentes y detectar pérdidas de energía. Desarrollada en el marco de proyectos de transferencia tecnológica del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una empresa regional de distribución y comercialización de energía eléctrica.

---

## 1. El reto

La transición de la medición manual periódica a la infraestructura de medición avanzada (**AMI** - *Advanced Metering Infrastructure*) introduce desafíos operacionales de escala:

1. **Volumen de series temporales continuas:** Cada medidor inteligente transmite perfiles de consumo en intervalos horarios o cuartohorarios (hasta 2.880 lecturas mensuales por usuario), requiriendo interfaces capaces de graficar perfiles temporales densos sin degradar el rendimiento del navegador.
2. **Cálculo de balances y detección de pérdidas:** Necesidad de cotejar la energía total inyectada en transformadores de distribución contra la suma de lecturas registradas aguas abajo, detectando discrepancias por pérdidas técnicas o posibles anomalías en las conexiones.
3. **Conciliación de medidores no asociados:** Equipos de medida activos transmitiendo datos sin un Número de Identificación de Usuario (**NIU**) asignado en el sistema comercial, lo que generaba vacíos operativos y retrasos de facturación.

---

## 2. Mi contribución

En este proyecto trabajé en la arquitectura frontend, la visualización de datos y la integración con la capa de persistencia relacional:

- **Diseñé la arquitectura general del frontend en Angular (v17/v18):** Estructuré la navegación principal mediante componentes autónomos (*Standalone Components*), separando de forma modular la matriz de clientes, el cálculo de balances y la reportería.
- **Desarrollé el módulo de matriz de clientes telemedidos:** Construí la interfaz de exploración masiva de medidores, incorporando filtros reactivos multicriterio (por subestación, circuito, transformador y estado de comunicación), paginación optimizada y búsqueda rápida por identificadores.
- **Integré visualizadores de series temporales con ApexCharts y Chart.js:** Configuré gráficos interactivos de series de tiempo para renderizar curvas de carga horaria y cuartohoraria con soporte de zoom dinámico, paneo y tooltips informativos.
- **Implementé un servicio multitema persistente:** Creé `ThemeService` para conmutar dinámicamente entre modo claro y oscuro, sincronizando las variables cromáticas de Tailwind CSS y la paleta de los lienzos gráficos para puestos de trabajo en centros de control.
- **Participé en la integración con el backend y Microsoft SQL Server:** Desarrollé endpoints en Node.js y Express consumiendo **consultas SQL y procedimientos almacenados (Stored Procedures)** en SQL Server para ejecutar agregaciones analíticas de consumos, conciliación de medidores sin NIU y cálculo de balances energéticos directamente en el motor de base de datos.
- **Colaboración en equipo:** Trabajé conjuntamente con ingenieros especialistas en distribución eléctrica en la definición de las fórmulas de balance y la interpretación de los estados de telemedida.

---

## 3. La solución

La arquitectura desacopla el procesamiento intensivo de series temporales en el motor relacional de la visualización reactiva en el cliente:

```
[ Navegador del Operador ]
           │
           ▼
┌────────────────────────────────────────┐
│  Frontend Angular (Standalone)         │
│  ├── Matriz de Clientes & Filtros      │
│  ├── Curvas de Carga (ApexCharts)      │
│  └── ThemeService (Modo Oscuro/Claro)  │
└────────────────────────────────────────┘
           │ HTTP / REST APIs
           ▼
┌────────────────────────────────────────┐
│  Backend Node.js / Express             │
│  (Controladores y validación de datos) │
└────────────────────────────────────────┘
           │ Conexión mssql
           ▼
┌────────────────────────────────────────┐
│  Microsoft SQL Server                  │
│  ├── Procedimientos Almacenados (SPs)  │
│  │   ├── Cálculo de balances           │
│  │   ├── Agregación de curvas horarias │
│  │   └── Conciliación de medidores     │
│  └── Repositorio relacional de lecturas│
└────────────────────────────────────────┘
```

### Decisiones técnicas destacadas

- **Procesamiento de agregaciones mediante Stored Procedures:** En lugar de transferir millones de registros sin procesar a la capa de aplicación, las consultas complejas de agregación horaria y balance de transformadores se ejecutan mediante procedimientos almacenados en SQL Server, optimizando el uso de red y reduciendo los tiempos de respuesta.
- **Gestión reactiva de filtros:** El estado de los filtros de búsqueda se encapsuló en servicios dedicados de Angular (`MatrizClienteService`), permitiendo a los operadores navegar entre vistas analíticas sin perder sus selecciones ni disparar peticiones repetitivas.
- **Adaptabilidad visual para centros de control:** La integración del modo oscuro garantiza confort visual en salas de monitoreo donde los operadores trabajan turnos prolongados frente a múltiples pantallas.

---

## 4. Funcionalidades destacadas

- **Matriz interactiva de medidores:** Búsqueda rápida y filtrado granular por subestación, circuito, transformador y estado de telemedida.
- **Inspección de curvas de carga:** Visualización comparativa de perfiles de potencia activa y reactiva para identificar patrones anómalos de consumo.
- **Balance energético de circuitos:** Comparación agregada entre la energía inyectada en macro-medidores y la suma de lecturas de usuarios individuales para auditar pérdidas.
- **Bandeja de medidores sin NIU:** Módulo administrativo para listar equipos que transmiten sin registro comercial asociado y facilitar su asignación.
- **Generación y exportación de reportes:** Extracción de resúmenes operativos para equipos de ingeniería y cuadrillas de campo.

---

## 5. Tecnologías aplicadas

- **Frontend:** Angular (v17/v18), TypeScript, Tailwind CSS, ApexCharts, Chart.js, RxJS.
- **Backend y APIs:** Node.js, Express, TypeScript, RESTful APIs.
- **Bases de datos:** Microsoft SQL Server, consultas SQL avanzadas, procedimientos almacenados (Stored Procedures), modelado relacional de datos.

---

## 6. Resultados y validación técnica

La solución fue verificada mediante pruebas funcionales y suites automatizadas en entorno de desarrollo:

- **Validación unitaria:** 25 suites de pruebas unitarias (`.spec.ts`) en Angular que comprobaron la reactividad de los componentes de filtrado, los servicios de estado y la lógica de conmutación de temas.
- **Eficiencia en consultas complejas:** La delegación de cálculos de balance a procedimientos almacenados en SQL Server permitió retornar resúmenes de miles de lecturas en tiempos compatibles con una navegación web fluida.
- **Renderizado de series temporales:** La configuración optimizada de ApexCharts aseguró una interacción continua al explorar curvas de carga con múltiples puntos de muestreo diario.

---

## 7. Aprendizajes y valor técnico

Este proyecto demuestra competencias en el desarrollo de software empresarial para el sector energético (*utilities* e IoT industrial):

- Capacidad para articular interfaces analíticas en Angular con bases de datos relacionales empresariales como Microsoft SQL Server.
- Criterio para decidir dónde ejecutar la computación intensiva de datos (procedimientos almacenados vs. capa de aplicación) para maximizar la eficiencia del sistema.
- Diseño de interfaces orientadas a la toma de decisiones operativas en tiempo real.
