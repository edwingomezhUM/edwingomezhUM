# Pipeline de Analítica de Voz del Cliente (VOC) y Calidad en Contact Center

Pipeline analítico de procesamiento de lenguaje natural (NLP) y minería de texto desarrollado en Python para procesar transcripciones de interacciones de servicio al cliente, calcular polaridad emocional, generar tendencias de desempeño por asesor y activar alertas tempranas de insatisfacción. Desarrollado en el marco de proyectos de transferencia tecnológica del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una empresa regional de servicios públicos domiciliarios.

---

## 1. El reto

El seguimiento de calidad y experiencia del usuario en centros de contacto telefónicos y canales de chat de empresas de servicios públicos afrontaba tres limitaciones operacionales:

1. **Muestreo manual reducido:** La auditoría manual por parte de supervisores alcanzaba a revisar únicamente entre el 1% y el 2% del volumen total de grabaciones, dejando la gran mayoría de las conversaciones sin análisis objetivo.
2. **Detección reactiva de inconformidades:** Los picos de insatisfacción generados por fallas prolongadas de red o inconsistencias en facturación solo se identificaban días después, limitando la capacidad de respuesta operativa proactiva.
3. **Ausencia de indicadores objetivos del tono conversacional:** Dificultad para cuantificar cómo evoluciona la polaridad emocional del usuario durante la llamada y su correlación con el desempeño y la amabilidad del asesor.

---

## 2. Mi contribución

En este proyecto trabajé en el diseño e implementación de los módulos analíticos de NLP en Python y el microservicio de alertas:

- **Desarrollé el algoritmo de análisis de sentimientos y estados emocionales (`Feelings.py`):** Construí la lógica para calcular índices de polaridad (positivo, neutro, negativo) y extraer patrones afectivos sobre el texto transcrito de las conversaciones.
- **Implementé el módulo de tendencias cronológicas por asesor (`trendAgent.py`):** Diseñé el cálculo de evolución temporal del desempeño de los agentes de atención a partir de las calificaciones de sentimiento de los usuarios atendidos.
- **Construí el módulo de categorización temática (`trendCategory.py`):** Desarrollé la clasificación y seguimiento de volúmenes de llamadas según la tipología del servicio consultado (daños de red, dudas de facturación, duplicados, reconexiones).
- **Desarrollé el cruce territorial de sentimiento (`trendFeelingsTown.py`):** Relacioné los resultados analíticos con la procedencia geográfica de las interacciones para mapear focos de inconformidad por municipio.
- **Implementé la extracción y visualización de palabras clave (`WordCloud`):** Diseñé rutinas de limpieza lingüística con exclusión de fórmulas protocolarias de saludo para identificar términos emergentes en las conversaciones.
- **Empaqueté el microservicio de alertas en Docker:** Configuré el contenedor Docker para ejecutar de forma reproducible las rutinas de monitoreo y disparo de alertas ante incrementos en el índice de insatisfacción.
- **Colaboración en equipo:** Trabajé conjuntamente con los ingenieros encargados de la ingesta previa de audios y con el área de calidad del Contact Center en la homologación de las categorías de trámite.

---

## 3. La solución

El pipeline procesa las transcripciones conversacionales a través de etapas secuenciales de normalización, scoring y agregación analítica:

```
[ Transcripciones de Llamadas / Chats ]
                  │
                  ▼
┌────────────────────────────────────────┐
│  Limpieza & Normalización Lingüística  │
│  (Stop words regionales, SpaCy / NLTK) │
└────────────────────────────────────────┘
                  │
                  ▼
┌────────────────────────────────────────┐
│  Módulos Analíticos en Python          │
│  ├── Feelings.py (Scoring sentimiento) │
│  ├── trendAgent.py (Tendencia asesor)  │
│  ├── trendCategory.py (Tipologías)     │
│  └── trendFeelingsTown.py (Municipio)  │
└────────────────────────────────────────┘
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
┌───────────────┐   ┌──────────────────────────┐
│ Tableros &    │   │ Microservicio de Alertas │
│ Reportes JSON │   │ (Contenedor Docker)      │
└───────────────┘   └──────────────────────────┘
```

### Decisiones técnicas destacadas

- **Diccionario especializado de stop words regionales:** Las listas estándar de palabras vacías no filtraban fórmulas de saludo protocolarias repetitivas en centros de contacto colombianos (*"muy buenos días con quién tengo el gusto"*, *"en qué le podemos colaborar"*). Se construyó una lista de exclusión especializada para que estas frases no distorsionaran las nubes de palabras clave.
- **Modularidad e independencia de scripts:** Cada dimensión analítica (agentes, categorías, municipios, polaridad) se estructuró como un módulo desacoplado en Python, permitiendo ejecuciones independientes o programadas mediante tareas automatizadas.
- **Ponderación relativa por extensión del diálogo:** Se calculó la intensidad emocional de forma relativa al número total de términos de la conversación, evitando que interacciones extensas sesgaran los promedios globales de satisfacción.

---

## 4. Funcionalidades destacadas

- **Índice de polaridad de interacciones:** Cálculo de distribución porcentual de sentimiento por llamada y por jornada.
- **Historial de desempeño por asesor:** Trazabilidad de la evolución de la percepción de los usuarios atendidos por cada agente a lo largo de las semanas.
- **Mapeo municipal de inconformidades:** Identificación geográfica de localidades con concentraciones atípicas de quejas tras eventos climáticos o mantenimientos de red.
- **Nubes de términos emergentes:** Detección visual rápida de vocabulario crítico repetido en las conversaciones del día.
- **Disparador de alertas automatizadas:** Lógica de umbral para notificar a los supervisores cuando la proporción de insatisfacción supera límites de tolerancia predefinidos.

---

## 5. Tecnologías aplicadas

- **Lenguaje y Procesamiento de Datos:** Python 3.x, Pandas, NumPy.
- **Procesamiento de Lenguaje Natural (NLP):** NLTK, SpaCy, WordCloud, expresiones regulares (Regex).
- **Infraestructura y Despliegue:** Docker, Docker Compose.

---

## 6. Resultados y validación técnica

La solución fue evaluada y verificada mediante ejecuciones analíticas en entorno de desarrollo:

- **Validación con datasets de transcripciones de prueba:** Los scripts fueron validados procesando transcripciones locales, comprobando la consistencia del algoritmo de scoring frente a diálogos con diferentes niveles de extensión y ruido lingüístico.
- **Saneamiento de texto ruidoso:** Las reglas de normalización demostraron efectividad para filtrar errores fonéticos típicos de transcripciones automáticas y homogenizar términos eléctricos comunes.
- **Ejecución en contenedor Docker:** Se comprobó el empaquetado del microservicio de alertas en Docker, asegurando un entorno de ejecución aislado y reproducible para tareas programadas.

---

## 7. Aprendizajes y valor técnico

Este proyecto demuestra competencias en minería de texto y procesamiento de lenguaje natural aplicado a la gestión de operaciones y experiencia del cliente:

- Capacidad para adaptar herramientas estándar de NLP a modismos y terminología técnica local de un sector específico.
- Habilidad para estructurar pipelines de datos modulares y reproducibles en Python.
- Criterio para convertir el análisis de texto no estructurado en métricas cuantitativas accionables para la toma de decisiones operativas.
