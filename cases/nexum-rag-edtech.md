# NexUM — Plataforma EdTech con RAG Híbrido y Tutores Conversacionales

Plataforma educativa orientada a la interacción conversacional nativa con inteligencia artificial, diseñada para que cada curso cuente con un tutor virtual especializado dotado de conocimiento aislado, contextualizado y capaz de responder en tiempo real mediante streaming. Desarrollada en el marco de iniciativas de investigación aplicada del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM).

---

## 1. El reto

El despliegue de asistentes conversacionales basados en modelos de lenguaje en educación universitaria presenta tres limitaciones críticas:

1. **Alucinaciones y pérdida de límites temáticos:** Los modelos de lenguaje generalistas tienden a inventar explicaciones sobre conceptos técnicos o responder a preguntas ajenas al programa académico, desviando el objetivo pedagógico.
2. **Insuficiencia del RAG puramente vectorial:** La búsqueda semántica basada exclusivamente en similitud de embeddings densos pierde precisión ante fórmulas, acrónimos, sintaxis de código y términos técnicos exactos del plan de estudios.
3. **Latencia en la interacción conversacional:** El encadenamiento de llamadas síncronas a múltiples APIs externas de embeddings y generación introduce demoras de varios segundos antes de emitir la primera palabra (*Time-to-First-Token*), degradando la experiencia de estudio.

---

## 2. Mi contribución

En este proyecto trabajé en la arquitectura de recuperación, el backend de inferencia y la integración del flujo conversacional:

- **Diseñé e implementé el motor de RAG híbrido:** Combiné búsqueda semántica densa (**BGE-M3**) y búsqueda léxica dispersa (**BM25**), integradas mediante el algoritmo **Reciprocal Rank Fusion (RRF)** para equilibrar similitud conceptual con concordancia exacta de palabras clave.
- **Desarrollé la compuerta de dominio y el enrutador de intenciones:** Construí los módulos `localDomainGate` y `localIntentRouter` para evaluar la pertinencia temática de la consulta antes de invocar la generación completa, asegurando una abstención determinística frente a temas ajenos al curso.
- **Configuré el servidor local de embeddings:** Desplegué un microservicio en Python para ejecutar la inferencia de BGE-M3 de forma local y autónoma, eliminando la dependencia de cuotas de red y garantizando consistencia en el procesamiento de textos técnicos en español.
- **Implementé el controlador de streaming conversacional:** Diseñé el servicio de transmisión token a token mediante **Server-Sent Events (SSE)** en Node.js y Express (`streamingAvatarController` y `streamingProfessorService`), reduciendo la latencia percibida por el usuario en el frontend.
- **Construí la suite de validación empírica:** Creé y ejecuté una batería de 25 scripts de medición técnica en el backend para auditar latencia, suficiencia de evidencia y resistencia ante preguntas ambiguas o fuera de temario.
- **Colaboración en equipo:** Trabajé conjuntamente con docentes en la estructuración de los contenidos curriculares y con el equipo de diseño en la conceptualización visual de las interfaces del avatar.

---

## 3. La solución

El sistema opera bajo un pipeline modular de cuatro etapas que valida, recupera, clasifica y transmite la información antes de generar una respuesta:

```
[ Consulta del Estudiante ]
           │
           ▼
┌────────────────────────────────────────┐
│  Compuerta de Dominio & Enrutador      │ ── Fuera de temario ──> [ Respuesta de ]
│  (Evaluación léxico-semántica previa)  │                         [  Abstención  ]
└────────────────────────────────────────┘
           │ Consulta válida
           ▼
┌────────────────────────────────────────┐
│  Recuperador RAG Híbrido               │
│  ├── BGE-M3 (Embeddings densos locales)│
│  └── BM25 (Búsqueda léxica exacta)     │
└────────────────────────────────────────┘
           │
           ▼
┌────────────────────────────────────────┐
│  Fusión de Rankings (RRF)              │
│  (Selección de fragmentos Top-K)       │
└────────────────────────────────────────┘
           │
           ▼
┌────────────────────────────────────────┐
│  Generador LLM + Streaming SSE         │ ── Tokens en vivo ──> [ Frontend React ]
└────────────────────────────────────────┘
```

### Decisiones técnicas destacadas

- **Reciprocal Rank Fusion (RRF) frente a suma lineal de puntuaciones:** Las escalas de similitud coseno de los embeddings densos y las frecuencias de términos de BM25 son numéricamente incompatibles. Se implementó RRF para combinar los resultados basándose en el orden relativo de los fragmentos recuperados:
  $$RRF(d) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$
  Esto proporciona una combinación robusta e independiente de la calibración de cada modelo.
- **Inferencia local de embeddings:** El uso de un servidor local de embeddings aseguró un rendimiento predecible y reproducible, evitando llamadas de red adicionales por cada consulta y reduciendo la variabilidad en los tiempos de respuesta.
- **Control de abstención previo:** Filtrar las consultas a nivel de dominio antes de consultar la base de conocimiento vectorial reduce el consumo de cómputo y previene respuestas incorrectas sobre temas no cubiertos en el programa académico.

---

## 4. Funcionalidades destacadas

- **Aislamiento estricto de conocimiento:** Cada asignatura gestiona su propia base documental segmentada, impidiendo que el tutor virtual de un curso utilice fragmentos de otra materia.
- **Streaming continuo token a token:** La interfaz actualiza el texto de forma progresiva conforme se generan los tokens, entregando retroalimentación visual inmediata al estudiante.
- **Mecanismo de abstención contextual:** Cuando una pregunta no corresponde al temario del curso, el asistente reconoce el límite temático y orienta al estudiante hacia los contenidos pertinentes.
- **Trazabilidad de procedencia:** Las respuestas se vinculan con los fragmentos documentales utilizados como sustento, facilitando la verificación conceptual.

---

## 5. Tecnologías aplicadas

- **Frontend:** React 18, TypeScript, Vite, Tailwind CSS, Context API.
- **Backend y APIs:** Node.js, Express, TypeScript, Server-Sent Events (SSE).
- **Inteligencia Artificial y NLP:** Python 3.10, BGE-M3 (`BAAI/bge-m3`), BM25, Reciprocal Rank Fusion (RRF).
- **Pruebas y Benchmarks:** Suites automatizadas de evaluación de latencia, pruebas de estrés y validación adversarial.

---

## 6. Resultados y validación técnica

La arquitectura fue evaluada mediante un conjunto de 25 pruebas y scripts de medición ejecutados en entorno de desarrollo y laboratorio:

- **Medición de latencia de primer token (TTFT):** La arquitectura de inferencia local junto con el canal SSE permitió emitir los primeros fragmentos de texto en fracciones de segundo, eliminando los cuellos de botella de llamadas secuenciales a servicios en la nube.
- **Evaluación adversarial de dominio:** En pruebas con baterías de preguntas fuera de temario y consultas ambiguas, la compuerta de dominio demostró una abstención consistente, canalizando adecuadamente las interacciones sin inducir alucinaciones.
- **Calidad de recuperación:** La combinación de BGE-M3 y BM25 resolvió las pérdidas de contexto léxico que se presentaban al emplear únicamente búsqueda vectorial sobre terminología especializada.

---

## 7. Aprendizajes y valor técnico

Este proyecto demuestra competencias en el diseño de arquitecturas de IA aplicada donde la precisión conceptual y el control de alucinaciones son prioritarios. Evidencia capacidad para:

- Integrar técnicas avanzadas de recuperación de información (Dense + Sparse) sin depender exclusivamente de soluciones comerciales cerradas.
- Diseñar sistemas desacoplados orientados a baja latencia y alta reactividad en el frontend.
- Validar empíricamente sistemas de software mediante pruebas automatizadas de robustez y rendimiento.
