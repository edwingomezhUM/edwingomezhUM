# Caso de Estudio 5: Pipeline de Analítica de Voz del Cliente (VOC) y Calidad en Contact Center

## 1. Contexto y Origen

La evaluación de la calidad de servicio y la percepción del usuario en empresas de servicios públicos se concentra en gran medida en los canales de atención del Contact Center (llamadas telefónicas y chats de soporte). Este caso de estudio documenta el motor analítico de Procesamiento de Lenguaje Natural (NLP) y minería de texto desarrollado dentro de la iniciativa de **Voz del Cliente (VOC)** del [DataLab de la Universidad de Manizales](https://github.com/DataLab-UM) para una **empresa regional de servicios públicos (distribución de energía eléctrica)**.

---

## 2. Problema Técnico

El seguimiento tradicional de calidad y experiencia del cliente en centros de contacto presentaba tres limitaciones operativas:
1. **Muestreo manual reducido:** La auditoría manual por parte de supervisores alcanzaba a revisar únicamente entre el 1% y el 2% del volumen total de grabaciones, dejando la gran mayoría de interacciones sin evaluación analítica.
2. **Detección reactiva de anomalías:** Los picos de inconformidad provocados por fallas prolongadas de red o inconsistencias en facturación solo se identificaban días después, limitando la capacidad de respuesta proactiva.
3. **Ausencia de indicadores objetivos del tono conversacional:** Dificultad para cuantificar cómo evoluciona la polaridad emocional del usuario durante la interacción y su relación con el desempeño y tono de los asesores.

---

## 3. Objetivo

Diseñar e implementar un conjunto de módulos analíticos en Python para procesar automáticamente transcripciones de interacciones ciudadanas, calcular polaridad y estados emocionales, categorizar tipologías de servicio, generar series de tendencia por asesor y por municipio, y activar disparadores de alerta ante incrementos de insatisfacción.

---

## 4. Contribución Técnica y Diferenciación de Alcances

```
┌────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE RESPONSABILIDADES                       │
├───────────────────────────────────┬────────────────────────────────────┤
│ Contribución Individual           │ Trabajo Colaborativo e             │
│ Verificada en Código              │ Institucional                      │
├───────────────────────────────────┼────────────────────────────────────┤
│ • 24 commits individuales         │ • Ingesta de grabaciones y gestión │
│   verificados en los módulos      │   del motor Speech-to-Text (STT)   │
│   analíticos de Contact Center.   │   previo con proveedores de audio. │
│ • 4 commits verificados en el     │ • Definición de categorías de      │
│   microservicio de alertas        │   servicio con el área de calidad  │
│   empaquetado en Docker.          │   del Contact Center.              │
│ • Desarrollo del algoritmo de     │ • Configuración de la base de datos│
│   sentimientos (`Feelings.py`).   │   documental de transcripciones.   │
│ • Módulo de tendencias por        │                                    │
│   asesor (`trendAgent.py`).       │                                    │
│ • Módulo de categorización        │                                    │
│   temática (`trendCategory.py`).  │                                    │
│ • Cruce territorial de            │                                    │
│   sentimiento (`trendFeelingsTown`).                                   │
└───────────────────────────────────┴────────────────────────────────────┘
```

- **Pruebas y Validación Local:** Verificación funcional de scripts mediante conjuntos de transcripciones de prueba locales y empaquetamiento del servicio en contenedores Docker para ejecución reproducible.
- **Estado de Producción:** Módulos concebidos para alimentar los tableros analíticos de calidad y la detección temprana de insatisfacción del Contact Center.

---

## 5. Tecnologías y Arquitectura

```
                  PIPELINE ANALÍTICO DE VOZ DEL CLIENTE (VOC)
                                                               
    ┌───────────────────────────┐         ┌───────────────────────────┐
    │  Audios / Chats de Canal  │ ──────> │  Motor Speech-to-Text     │
    │     (Contact Center)      │         │ (Transcripción a Texto)   │
    └───────────────────────────┘         └─────────────┬─────────────┘
                                                        │ Transcripciones
                                                        ▼
    ┌─────────────────────────────────────────────────────────────────┐
    │              Pipeline de NLP en Python (DataLab)                │
    │                                                                 │
    │   1. Limpieza & Normalización (Stop words en español, lemas)    │
    │   2. Extracción de Entidades & Filtrado de Vocabulario          │
    │                                                                 │
    │      ┌──────────────────────┐     ┌───────────────────────┐     │
    │      │     Feelings.py      │     │    trendAgent.py      │     │
    │      │(Sentimiento/Emoción) │     │ (Tendencia por Asesor)│     │
    │      └──────────┬───────────┘     └───────────┬───────────┘     │
    │                 │                             │                 │
    │      ┌──────────┴───────────┐     ┌───────────┴───────────┐     │
    │      │   trendCategory.py   │     │  trendFeelingsTown.py │     │
    │      │(Tipologías de Queja) │     │(Sentimiento Municipio)│     │
    │      └──────────┬───────────┘     └───────────┬───────────┘     │
    └─────────────────┼─────────────────────────────┼─────────────────┘
                      │                             │
                      ▼                             ▼
    ┌───────────────────────────┐         ┌───────────────────────────┐
    │  WordClouds & Reportes    │         │  Microservicio Alertas    │
    │   (Métricas de Calidad)   │         │    (Contenedor Docker)    │
    └───────────────────────────┘         └───────────────────────────┘
```

- **Lenguaje y Entorno:** Python 3.x, Docker.
- **Librerías de NLP y Minería de Texto:** NLTK, SpaCy, WordCloud, Regex.
- **Procesamiento de Datos:** Pandas, NumPy.
- **Salidas:** Reportes tabulares para análisis operativo y payloads JSON para dashboards de gestión.

---

## 6. Decisiones de Diseño Técnico

1. **Diccionario Personalizado de Stop Words Regionales:** Las listas genéricas de palabras vacías no filtraban fórmulas de saludo protocolarias repetitivas en centros de contacto (*"muy buenos días con quién tengo el gusto"*, *"con mucho gusto para servirle"*). Se construyó una lista de exclusión especializada para evitar sesgos en las nubes de palabras clave.
2. **Modularidad e Independencia de Scripts:** Cada proceso analítico (agentes, categorías, municipios, polaridad) se aisló en módulos independientes para permitir su ejecución en paralelo o programada mediante tareas cron sin acoplamiento rígido.
3. **Ponderación Relativa de Longitud:** Se calculó la intensidad emocional de forma relativa a la extensión de la conversación, evitando que diálogos prolongados distorsionaran los promedios globales de satisfacción.

---

## 7. Funcionalidades Principales

- **Índice de Polaridad Emocional:** Cálculo de porcentaje de interacciones positivas, neutras y negativas.
- **Curva Temporal por Asesor:** Seguimiento cronológico de la evolución de la percepción de los usuarios atendidos por cada agente.
- **Mapeo Territorial de Sentimiento:** Agrupación de niveles de inconformidad por municipio para correlacionar con eventos climáticos o fallas técnicas de red.
- **Nubes de Palabras Clave:** Identificación visual rápida de conceptos emergentes en las conversaciones.
- **Disparador de Alertas por Umbral:** Lógica automatizada para emitir avisos cuando la proporción de insatisfacción supera límites de tolerancia predefinidos.

---

## 8. Desafíos Afrontados

- **Ruido y Variabilidad en Transcripciones Automáticas:** Tratar errores fonéticos y transcripciones incompletas generadas por ruido de fondo telefónico mediante reglas de saneamiento y normalización léxica.
- **Diferenciación entre Molestia Técnica y Trato del Asesor:** Aislar la frustración del usuario por la falta de servicio eléctrico de la evaluación sobre la amabilidad y diligencia del agente.

---

## 9. Evidencias Técnicas Verificables

- **Módulos implementados en Python:**
  - `Tendencias/trendAgent.py`
  - `Tendencias/trendCategory.py`
  - `Tendencias/trendFeelingsTown.py`
  - `emociones_sentimientos/Feelings.py`
  - `chats/individual.py`
- **Infraestructura:** Dockerfile para el microservicio de alertas.
- **Historial de Commits:** 24 commits individuales verificados en el motor analítico de Contact Center y 4 commits en el servicio de alertas.

---

## 10. Hechos Verificados vs. Aspectos a Confirmar

| Categoría | Detalle Técnico / Operativo | Estado |
| :--- | :--- | :--- |
| **Hecho Verificado** | Autoría comprobable de 24 commits en los módulos analíticos de Python. | Verificado en Git log. |
| **Hecho Verificado** | Existencia del código de análisis de tendencias, sentimientos y categorización. | Verificado en archivos del proyecto. |
| **Hecho Verificado** | Implementación del microservicio dockerizado de alertas. | Verificado en estructura local. |
| **Aspecto a Confirmar** | Motor específico de Speech-to-Text empleado para la transcripción inicial del audio. | Requiere confirmación si se desea citar públicamente. |
| **Aspecto a Confirmar** | Cifras estimadas de volumen mensual promedio de interacciones procesadas. | Requiere confirmación si se desea citar públicamente. |

---

## 11. Estrategia de Confidencialidad y Demo Sintética

- **Información Protegida:** Se excluyen totalmente audios originales, nombres reales de agentes, transcripciones con menciones de identidad o teléfonos de usuarios, y cuentas contrato.
- **Propuesta de Demostración Pública:** Crear un script autónomo en Python / Jupyter Notebook que procese un dataset sintético de 15 transcripciones ficticias de atención al cliente de servicios públicos, ejecutando la limpieza de texto, el cálculo de sentimiento, la clasificación en 4 categorías típicas y la generación de la nube de palabras representativa sin exponer ningún dato privado.
