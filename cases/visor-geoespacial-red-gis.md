# Caso de Estudio 4: Visores Geoespaciales (GIS) para Monitoreo de Red y Control de Infraestructura

## 1. Contexto y Origen

La gestión de redes de distribución eléctrica y la supervisión de activos físicos sobre el territorio exigen herramientas cartográficas interactivas y actualizadas en tiempo real. Este caso de estudio comprende dos desarrollos complementarios elaborados en el [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una **empresa regional de distribución y comercialización de energía eléctrica**:
1. **Visor de Disponibilidad de Red Eléctrica en Tiempo Real** (con backend de eventos en tiempo real).
2. **Sistema Geoespacial de Censo de Cableoperadores**.

---

## 2. Problema Técnico

Las operaciones de distribución eléctrica y control de activos compartidos enfrentaban dos problemas principales:
1. **Falta de visibilidad cartográfica unificada de afectaciones:** Ante tormentas o fallas operativas en circuitos y transformadores, el personal de atención y supervisión dependía de tablas alfanuméricas en lugar de un mapa en vivo que reflejara qué circuitos se encontraban desenergizados y cuál era la afectación territorial estimada.
2. **Carencia de inventario georreferenciado de infraestructura compartida:** Los postes de la red eléctrica son utilizados por múltiples empresas de telecomunicaciones para tender cableado. La ausencia de un censo geográfico interactivo dificultaba la fiscalización de postes sobrecargados, la identificación de redes no autorizadas y el control administrativo del arriendo de apoyos.

---

## 3. Objetivo

Desarrollar plataformas web geoespaciales (GIS) livianas, interactivas y sincronizadas mediante canales en tiempo real para visualizar el estado operativo de los circuitos de distribución y auditar el censo de ocupación de postes por parte de operadores externos.

---

## 4. Contribución Técnica y Diferenciación de Alcances

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE RESPONSABILIDADES                       │
├───────────────────────────────────┬────────────────────────────────────┤
│ Contribución Individual           │ Trabajo Colaborativo e             │
│ Verificada en Código              │ Institucional                      │
├───────────────────────────────────┼────────────────────────────────────┤
│ • 100% de autoría verificada (7   │ • Modelado y custodia de datos     │
│   de 7 commits) en el visor de    │   cartográficos corporativos       │
│   cableoperadores.                │   (Oracle Spatial / PostGIS).      │
│ • 42.3% de autoría (11 commits)   │ • Integración con sistemas de      │
│   en el frontend del mapa de      │   despacho y SCADA de la empresa.  │
│   disponibilidad de red.          │ • Levantamiento de información     │
│ • Implementación de capas         │   geográfica en campo por          │
│   GeoJSON y marcadores Leaflet.   │   cuadrillas técnicas.             │
│ • Integración de WebSockets con   │                                    │
│   `ngx-socket-io` para eventos en │                                    │
│   tiempo real.                    │                                    │
│ • Layout y menús de filtrado      │                                    │
│   multicriterio por municipio y   │                                    │
│   operador.                       │                                    │
└───────────────────────────────────┴────────────────────────────────────┘
```

- **Pruebas y Validación Local:** 18 archivos de pruebas unitarias (`.spec.ts`) combinados para validar la carga de capas cartográficas, la reactividad de los filtros y la recepción de eventos vía sockets.
- **Estado de Producción:** Soluciones empleadas como visores de consulta y apoyo a la toma de decisiones por áreas operativas y de gestión de activos.

---

## 5. Tecnologías y Arquitectura

```
                        ARQUITECTURA DE LAS SOLUCIONES GIS
                                                               
   ┌─────────────────────────────────────────────────────────────┐
   │                  Frontend Angular + Leaflet                 │
   │                                                             │
   │  ┌─────────────────────────┐   ┌─────────────────────────┐  │
   │  │    Capa Cableoperadores │   │    Capa Disponibilidad  │  │
   │  │ (Postes, Operador, Usos)│   │  (Circuitos, Estados)   │  │
   │  └────────────┬────────────┘   └────────────┬────────────┘  │
   │               │                             │               │
   │  ┌────────────┴─────────────────────────────┴────────────┐  │
   │  │           Motor de Mapas Leaflet + GeoJSON            │  │
   │  │         (Marcadores interactivos, Tooltips)           │  │
   │  └──────────────────────────┬────────────────────────────┘  │
   └─────────────────────────────┼───────────────────────────────┘
                                 │
                 ┌───────────────┴───────────────┐
                 │ HTTP (GeoJSON)                │ WebSockets (Socket.IO)
                 ▼                               ▼
   ┌───────────────────────────┐   ┌───────────────────────────┐
   │      API Geoespacial      │   │    Servidor de Eventos    │
   │      (Express / REST)     │   │     (Node.js / Socket.IO) │
   └─────────────┬─────────────┘   └─────────────┬─────────────┘
                 │                               │
                 ▼                               ▼
   ┌───────────────────────────────────────────────────────────┐
   │             Bases de Datos GIS & Eventos OMS              │
   └───────────────────────────────────────────────────────────┘
```

- **Frontend:** Angular, TypeScript, Leaflet, GeoJSON, Socket.IO Client, Chart.js.
- **Backend:** Node.js, Express, Socket.IO, Axios.
- **Cartografía Base:** Mapas base mediante OpenStreetMap y mosaicos de mapa estándar.

---

## 6. Decisiones de Diseño Técnico

1. **Uso de Leaflet frente a Servidores SIG Pesados:** Se priorizó Leaflet por su ligereza y agilidad de carga en navegadores de equipos portátiles de cuadrillas y computadores de oficina, evitando la complejidad de visores GIS comerciales cerrados.
2. **Capas Vectoriales GeoJSON Dinámicas:** Los apoyos físicos y las líneas de distribución se representaron como capas vectoriales GeoJSON con simbología condicional según estado (verde para circuitos energizados, amarillo/rojo para sectores con evento reportado).
3. **WebSockets para Notificaciones Push:** Se configuró un canal persistente con `Socket.IO` para reflejar automáticamente aperturas o cierres de circuito reportadas por los centros de despacho sin requerir refresco manual de la página.

---

## 7. Funcionalidades Principales

- **Visualización de Circuitos y Afectaciones:** Identificación en mapa de líneas desenergizadas y demarcación del área de influencia.
- **Censo y Ocupación de Postes:** Ficha informativa por apoyo físico con indicación del número de operadores de telecomunicaciones conectados.
- **Filtros por Operador:** Aislamiento selectivo de capas para inspeccionar únicamente los apoyos donde tiene presencia un operador determinado.
- **Panel de Resumen Territorial:** Estadísticas de postes y ocupación discriminadas por municipio.

---

## 8. Desafíos Afrontados

- **Fluidez del Navegador con Alta Densidad de Puntos:** Controlar el uso de memoria ante la presencia de miles de marcadores simultáneos mediante agrupación por sectores (*clustering*) y umbrales de escala de visualización.
- **Estabilidad de la Conexión en Tiempo Real:** Configuración de políticas de reconexión y sincronización tras pérdidas momentáneas de conectividad en el cliente.

---

## 9. Evidencias Técnicas Verificables

- **Archivos fuente desarrollados:**
  - Componentes del módulo de censo de cableoperadores (100% autoría en Git).
  - Componentes del mapa interactivo de disponibilidad de red (`mapa.component.ts`, `mapa.service.ts`).
  - Lógica de cliente Socket.IO para eventos en tiempo real.
- **Pruebas Unitarias:** 18 archivos `.spec.ts` distribuidos entre los componentes cartográficos.
- **Historial de Commits:** 7 commits de autoría individual exclusiva en cableoperadores; 11 commits de desarrollo de mapa en disponibilidad; 1 commit en backend de eventos.

---

## 10. Hechos Verificados vs. Aspectos a Confirmar

| Categoría | Detalle Técnico / Operativo | Estado |
| :--- | :--- | :--- |
| **Hecho Verificado** | Autoría comprobable del 100% de commits en el módulo de cableoperadores. | Verificado en Git log. |
| **Hecho Verificado** | Autoría de 11 commits de componentes de mapa en el visor de disponibilidad. | Verificado en Git log. |
| **Hecho Verificado** | Integración de Leaflet, GeoJSON y Socket.IO en código fuente. | Verificado en archivos locales. |
| **Aspecto a Confirmar** | Total de postes o kilómetros de red censados en la versión productiva. | Requiere confirmación si se desea citar públicamente. |
| **Aspecto a Confirmar** | Motor de base de datos geográfica corporativo utilizado en el backend institucional. | Requiere confirmación si se desea citar públicamente. |

---

## 11. Estrategia de Confidencialidad y Demo Sintética

- **Información Protegida:** Se suprimen coordenadas exactas de subestaciones y transformadores de potencia, esquemas unifilares de red y razones sociales de empresas privadas de telecomunicaciones.
- **Propuesta de Demostración Pública:** Crear un visor interactivo en Leaflet sobre un mapa abierto de OpenStreetMap, con un dataset sintético de 30 postes ficticios y 2 circuitos simulados en un polígono urbano neutro, demostrando el filtrado por operadores ficticios y la conmutación de eventos de red mediante un emulador local de WebSockets.
