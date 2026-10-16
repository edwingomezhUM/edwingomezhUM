# Caso de Estudio 1: NexUM — Plataforma EdTech con RAG Híbrido y Tutores Conversacionales

## 1. Contexto y Origen

Este proyecto se desarrolló dentro de las líneas de investigación aplicada e inteligencia artificial del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM).

A diferencia de los entornos virtuales de aprendizaje (LMS) convencionales que integran asistentes como un complemento periférico, **NexUM** fue concebida para ofrecer una experiencia pedagógica conversacional nativa, en la que cada materia cuente con un tutor virtual especializado dotado de conocimiento contextualizado, aislado y con capacidad de responder en tiempo real mediante texto y streaming continuo.

---

## 2. Problema Técnico

El despliegue de asistentes conversacionales basados en modelos de lenguaje en educación universitaria presenta tres limitaciones fundamentales:
1. **Riesgo de alucinación y pérdida de límites temáticos:** Los modelos de lenguaje generalistas pueden inventar conceptos técnicos o responder a preguntas ajenas al programa académico, desviando el objetivo formativo.
2. **Insuficiencia del RAG puramente denso (vectorial):** La búsqueda basada únicamente en similitud semántica (embeddings densos) con frecuencia pierde precisión ante fórmulas, acrónimos, términos técnicos exactos o nombres de algoritmos específicos del plan de estudios.
3. **Latencia en respuestas conversacionales:** El encadenamiento de llamadas síncronas a APIs externas de embeddings y generación suele introducir demoras de varios segundos antes de emitir la primera palabra (*Time-to-First-Token*), interrumpiendo el flujo natural de la sesión de estudio.

---

## 3. Objetivo

Diseñar e implementar una arquitectura desacoplada de backend y recuperación de información (**RAG Híbrido**) de ejecución local, caracterizada por baja latencia, alta fidelidad conceptual, estricto aislamiento de conocimiento por materia, mecanismos de abstención frente a consultas fuera de dominio y transmisión fluida de respuestas integrada con un frontend web reactivo.

---

## 4. Contribución Técnica y Diferenciación de Alcances

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE RESPONSABILIDADES                       │
├───────────────────────────────────┬────────────────────────────────────┤
│ Contribución Individual           │ Trabajo Colaborativo e             │
│ Verificada en Código              │ Institucional                      │
├───────────────────────────────────┼────────────────────────────────────┤
│ • Inicialización del repositorio. │ • Definición de objetivos          │
│ • Implementación del recuperador  │   curriculares y pedagógicos.      │
│   RAG híbrido (BGE-M3 + BM25 +    │ • Revisión temática de contenidos  │
│   Reciprocal Rank Fusion RRF).    │   con docentes universitarios.     │
│ • Construcción de la compuerta de │ • Conceptualización de diseño      │
│   dominio (Domain Gate) y del     │   gráfico e identidad del avatar.  │
│   enrutador de intenciones.       │                                    │
│ • Servidor local de embeddings en │                                    │
│   Python para inferencia BGE-M3.  │                                    │
│ • Controlador de streaming con    │                                    │
│   Server-Sent Events (SSE).       │                                    │
│ • Creación y ejecución de suite   │                                    │
│   de benchmarks de latencia y     │                                    │
│   pruebas adversariales empíricas.│                                    │
└───────────────────────────────────┴────────────────────────────────────┘
```

- **Entorno de Pruebas y Validación Local:** La arquitectura fue evaluada mediante una batería de 25 scripts de medición y pruebas automatizadas en el backend, analizando latencia de respuesta, suficiencia de evidencia y resistencia ante preguntas ambiguas o fuera de temario.
- **Estado de Producción:** El proyecto se estructuró como un desarrollo técnico validado funcionalmente y probado con cursos piloto en entorno de laboratorio.

---

## 5. Tecnologías y Arquitectura

```
                        ARQUITECTURA DE RAG HÍBRIDO LOCAL
                                                               
┌──────────────────────────┐       HTTP / SSE        ┌────────────────────────────┐
│   Frontend React 18      │ ──────────────────────> │    Backend Express / TS    │
│  (Vite + Tailwind CSS)   │ <────────────────────── │ (streamingAvatarController)│
└──────────────────────────┘                         └─────────────┬──────────────┘
                                                                   │
                                                   ┌───────────────┴───────────────┐
                                                   ▼                               ▼
                                       ┌───────────────────────┐       ┌───────────────────────┐
                                       │   Local Domain Gate   │       │  Local Intent Router  │
                                       │ (Control Abstención)  │       │ (Clasificación Duda)  │
                                       └───────────┬───────────┘       └───────────────────────┘
                                                   │
                                     Consulta Válida de Curso
                                                   │
                                                   ▼
                         ┌───────────────────────────────────────────────────┐
                         │              RECUPERADOR RAG HÍBRIDO              │
                         │                                                   │
                         │  ┌───────────────────────┐ ┌───────────────────┐  │
                         │  │   BGE-M3 Dense (Py)   │ │    BM25 Sparse    │  │
                         │  │  (Similitud Semántica)│ │(Coincidencia Lex) │  │
                         │  └───────────┬───────────┘ └─────────┬─────────┘  │
                         │              │                       │            │
                         │              └───────────┬───────────┘            │
                         │                          ▼                        │
                         │             Reciprocal Rank Fusion (RRF)          │
                         └──────────────────────────┬────────────────────────┘
                                                    │
                                             Fragmentos Top-K
                                                    │
                                                    ▼
                                       ┌─────────────────────────┐
                                       │ Generador LLM + Contexto│
                                       │   (Streaming SSE Chunk) │
                                       └─────────────────────────┘
```

- **Frontend:** React 18, TypeScript, Vite, Tailwind CSS, Lucide React, Context API.
- **Backend:** Node.js, Express, TypeScript, Python 3.10 (servidor de inferencia local de embeddings).
- **Motores de Búsqueda:** BGE-M3 (`BAAI/bge-m3`) para similitud vectorial densa y BM25 para concordancia léxica exacta.
- **Algoritmo de Fusión:** *Reciprocal Rank Fusion* (RRF) para combinar los rankings de ambos recuperadores sin distorsión por diferencias de escala numéricas.
- **Canal de Datos:** Server-Sent Events (SSE) para emisión continua de tokens.

---

## 6. Decisiones de Diseño Técnico

1. **RRF frente a Normalización Lineal:** En lugar de intentar normalizar puntuaciones de similitud coseno con valores de frecuencia inversa de documentos (BM25), se utilizó RRF:
   $$RRF(d) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$
   Esto garantizó una combinación estable e inmune a discrepancias numéricas entre modelos.
2. **Inferencia Local de Embeddings:** El despliegue local de BGE-M3 eliminó la dependencia de cuotas por llamada en APIs externas y proporcionó un comportamiento determinístico y reproducible para textos técnicos en español.
3. **Compuerta Previa de Dominio (Domain Gate):** Se implementó una verificación léxico-semántica previa antes de invocar la generación completa. Si la consulta no guarda relación con los contenidos curriculares, el sistema se abstiene de responder y orienta al estudiante hacia los temas del curso.

---

## 7. Funcionalidades Principales

- **Navegación Curricular:** Exploración modular de contenidos y materiales de estudio.
- **Tutor Virtual Interactivo:** Ventana de diálogo interactiva con streaming de respuesta token a token.
- **Aislamiento de Bases de Conocimiento:** Segmentación documental estricta para garantizar que el tutor de una asignatura no consulte documentos de otra.
- **Trazabilidad de Fuentes:** Vinculación de respuestas con los fragmentos de origen para permitir la contrastación por parte del estudiante.

---

## 8. Desafíos Afrontados

- **Gestión de Concurrencia en Streaming:** Administrar la recepción de fragmentos asíncronos en el cliente React asegurando que las actualizaciones de estado no provocaran saltos visuales ni congelamiento de la interfaz.
- **Calibración de la Sensibilidad de Abstención:** Ajustar los umbrales de coincidencia para que el sistema reconozca preguntas válidas redactadas de forma coloquial sin habilitar consultas fuera de materia.

---

## 9. Evidencias Técnicas Verificables

- **Archivos fuente desarrollados:**
  - `backend/src/services/hybridRetrieverBge.ts`
  - `backend/src/services/bm25.ts`
  - `backend/src/services/localDomainGate.ts`
  - `backend/src/services/streamingProfessorService.ts`
  - `backend/src/controllers/streamingAvatarController.ts`
- **Artefactos y Reportes Empíricos:**
  - `backend/data/hybrid_evaluation_scorecard.json`
  - `backend/data/adversarial_evaluation_report.json`
  - `backend/data/latency_baseline_measurement.json`
- **Documentación Técnica:** 14 especificaciones técnicas y 25 scripts de pruebas de rendimiento y robustez.

---

## 10. Hechos Verificados vs. Aspectos a Confirmar

| Categoría | Detalle Técnico / Operativo | Estado |
| :--- | :--- | :--- |
| **Hecho Verificado** | Autoría comprobable de la inicialización y desarrollo de la arquitectura RAG híbrida. | Verificado en Git log y código fuente. |
| **Hecho Verificado** | Existencia del servidor local de embeddings, compuertas de dominio y streaming SSE. | Verificado en archivos del proyecto. |
| **Hecho Verificado** | Existencia de reportes de pruebas adversariales y scorecards de latencia. | Verificado en artefactos del repositorio. |
| **Aspecto a Confirmar** | Menciones a asignaturas específicas o métricas de adopción de estudiantes en cohortes reales. | Requiere validación institucional para divulgación pública. |

---

## 11. Estrategia de Confidencialidad y Demo Sintética

- **Información Protegida:** Se excluyen del caso de estudio los materiales de clase protegidos por derechos de autor, datos identificativos de estudiantes y claves de API de servicios de voz o IA.
- **Propuesta de Demostración Pública:** Construir un repositorio de demostración (*showcase*) que utilice un contenido formativo de dominio público (por ejemplo, un curso abierto sobre *"Estructuras de Datos en Python"*), desplegando el backend de RAG híbrido y la interfaz React sobre datos sintéticos para evidenciar la capacidad técnica sin divulgar contenidos privados.
