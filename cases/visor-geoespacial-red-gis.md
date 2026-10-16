# Visores Geoespaciales (GIS) para Red Eléctrica y Control de Infraestructura

Conjunto de aplicaciones web geoespaciales interactivas para el monitoreo en tiempo real de disponibilidad en redes de distribución eléctrica y el censo georreferenciado de ocupación de infraestructura física por operadores de telecomunicaciones. Desarrolladas en el marco de proyectos de transferencia tecnológica del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una empresa regional de distribución y comercialización de energía eléctrica.

---

## 1. El reto

La supervisión de activos eléctricos distribuidos sobre el territorio y la gestión de infraestructura compartida demandaban soluciones cartográficas ágiles frente a dos necesidades operativas:

1. **Visibilidad cartográfica unificada de afectaciones de red:** Ante tormentas o fallas en circuitos y transformadores, el personal de atención y despacho dependía de listados tabulares en lugar de una representación geográfica en vivo que mostrara qué circuitos estaban desenergizados y cuál era su área de cobertura.
2. **Control e inventario de infraestructura compartida:** Los postes de la red eléctrica son utilizados por diversas empresas de telecomunicaciones (cableoperadores) para tender cableado de fibra óptica y coaxial. La carencia de un visor georreferenciado dificultaba fiscalizar postes sobrecargados, identificar redes no autorizadas y gestionar el cobro de arriendos de infraestructura.

---

## 2. Mi contribución

En estas soluciones trabajé en la arquitectura frontend, la integración de mapas interactivos y la recepción de eventos en tiempo real:

- **Desarrollé la aplicación web del censo de cableoperadores en Angular y Leaflet:** Construí la solución completa para visualizar apoyos eléctricos en el mapa, integrando capas vectoriales GeoJSON, simbología dinámica por nivel de ocupación y consultas por operador.
- **Implementé los componentes del mapa de disponibilidad de red:** Diseñé los módulos `mapa.component.ts` y `mapa.service.ts` para renderizar circuitos de distribución en Angular, diferenciando su estado de servicio mediante estilos cromáticos condicionales.
- **Integré comunicación en tiempo real con WebSockets:** Configuré canales bidireccionales mediante `ngx-socket-io` para recibir eventos de apertura y cierre de circuitos emitidos por los sistemas de despacho, actualizando el mapa automáticamente sin requerir recargas de página.
- **Diseñé menús de filtrado multicriterio y resúmenes territoriales:** Desarrollé componentes interactivos para alternar capas cartográficas, filtrar apoyos por municipio y consultar métricas agregadas de infraestructura.
- **Colaboración en equipo:** Trabajé conjuntamente con especialistas en geomática en la estructuración de las fuentes de datos geográficos y con ingenieros de despacho en la interpretación de los eventos de red.

---

## 3. La solución

La arquitectura articula la cartografía web con servicios de eventos en vivo para ofrecer una experiencia reactiva:

```
[ Navegador / Cuadrilla de Campo ]
                │
                ▼
┌────────────────────────────────────────┐
│  Frontend Angular + Leaflet            │
│  ├── Capa de Circuitos (GeoJSON)       │
│  ├── Capa de Postes & Cableoperadores  │
│  └── Cliente WebSocket (Socket.IO)     │
└────────────────────────────────────────┘
        │                       ▲
        │ Peticiones GeoJSON    │ Eventos de red push
        ▼                       │
┌────────────────────┐  ┌───────────────────────┐
│ API Geoespacial    │  │ Servidor de Eventos   │
│ (Express / Node.js)│  │ (Socket.IO / Express) │
└────────────────────┘  └───────────────────────┘
```

### Decisiones técnicas destacadas

- **Uso de Leaflet frente a visores SIG pesados:** Se priorizó Leaflet por encima de plataformas GIS comerciales cerradas por su ligereza y rápida carga en navegadores de equipos portátiles de cuadrillas y computadores de oficina.
- **Capas vectoriales GeoJSON dinámicas:** Los apoyos físicos y las líneas de distribución se modelaron como colecciones vectoriales GeoJSON con renderizado condicional en el cliente (verde para circuitos energizados, amarillo/rojo para sectores con evento reportado).
- **WebSockets para propagación inmediata:** La integración de `Socket.IO` permitió que las variaciones en el estado de los circuitos se reflejen en pantalla en el momento exacto en que son registradas por el centro de control.

---

## 4. Funcionalidades destacadas

- **Monitoreo de interrupciones en vivo:** Detección visual en mapa de líneas desenergizadas y trazado del área territorial de influencia.
- **Censo georreferenciado de apoyos:** Ficha descriptiva por poste con detalle de cuántas empresas de telecomunicaciones comparten la infraestructura.
- **Filtro selectivo por operador:** Capacidad de aislar en el mapa únicamente los postes con presencia de una empresa de telecomunicaciones específica para auditorías en terreno.
- **Resumen territorial por municipio:** Tableros agregados de densidad de postes y nivel de compartición de infraestructura.

---

## 5. Tecnologías aplicadas

- **Frontend y GIS:** Angular, TypeScript, Leaflet, GeoJSON, Socket.IO Client, Chart.js.
- **Backend y APIs:** Node.js, Express, Socket.IO, APIs RESTful.
- **Cartografía Base:** OpenStreetMap y servidores de mosaicos de mapa (*tile servers*).

---

## 6. Resultados y validación técnica

Las herramientas fueron verificadas mediante pruebas unitarias y pruebas de carga cartográfica en entorno de desarrollo:

- **Validación con pruebas unitarias:** 18 archivos `.spec.ts` que validaron la carga de capas cartográficas, la lógica de filtrado por operador y la suscripción a eventos de socket.
- **Rendimiento con alta densidad de puntos:** La implementación de técnicas de agrupación (*clustering*) y umbrales de escala permitió interactuar de forma fluida con miles de apoyos georreferenciados sin degradar la memoria del navegador.
- **Estabilidad de canal WebSocket:** Se comprobó la capacidad del cliente para reconectarse automáticamente ante pérdidas momentáneas de conexión de red, recuperando el estado actualizado del mapa.

---

## 7. Aprendizajes y valor técnico

Este caso evidencia competencias en el desarrollo de soluciones geoespaciales para el sector de infraestructura y telecomunicaciones:

- Capacidad para transformar datos geoespaciales complejos (GeoJSON) en herramientas de consulta ágiles orientadas a la operación diaria.
- Dominio en la integración de mapas interactivos con arquitecturas web reactivas y eventos en tiempo real.
- Criterio técnico para diseñar interfaces cartográficas accesibles para personal administrativo y cuadrillas operativas en campo.
