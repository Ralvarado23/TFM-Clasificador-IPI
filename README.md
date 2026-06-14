# Análisis de Vulnerabilidad y Estrategias de Defensa contra Inyección Indirecta de Prompts en entornos Edge AI

[![Trabajo Fin de Máster](https://img.shields.io/badge/TFM-%F0%9F%8E%93-lightblue?style=flat-square)](#)
[![Código](https://img.shields.io/badge/Código-%F0%9F%92%8e-lightgrey?style=flat-square)](#)

Este repositorio contiene el código fuente asociado al Trabajo Fin de Máster titulado **"Análisis de Vulnerabilidad y Estrategias de Defensa contra Inyección Indirecta de Prompts en entornos Edge AI"**, presentado en la Universidad Internacional de La Rioja (UNIR) como parte del programa de Máster Universitario en Inteligencia Artificial.

## Descripción General
El despliegue de modelos pequeños de lenguaje (SLMs) en entornos de Edge AI permite procesar información de forma local y eficiente. Sin embargo, su integración con fuentes de información externas (ej. arquitecturas RAG) introduce vulnerabilidades críticas, destacando los **ataques de inyección indirecta de prompts (IPI)**. En estos ataques, instrucciones maliciosas se ocultan dentro del contenido externo recuperado con el objetivo de alterar el comportamiento del modelo sin que el usuario interactúe directamente con ellas.

Este proyecto propone un **sistema de detección de caja negra, ligero y eficiente**, diseñado para actuar como un filtro de seguridad previo a la fase de inferencia. La solución está optimizada para dispositivos con recursos limitados, mitigando la amenaza sin comprometer la latencia ni requerir modificaciones en la arquitectura interna del modelo de lenguaje.

## Metodología
El sistema propuesto evalúa la coherencia entre la intención original del usuario y el contexto externo recuperado. El diseño de la solución se fundamenta en los siguientes pilares:

* **Fusión Tardía:** Procesamiento independiente de las instrucciones del usuario y del contexto externo utilizando modelos de *embeddings* de baja dimensionalidad y código abierto (`GTE-small` y `MiniLM`).

* **Fusión Enriquecida:** Combinación de la información semántica de los *embeddings* con características heurísticas extraídas de los textos (similitud coseno, longitud del contexto, recuento de caracteres especiales y frecuencia en un lexicón de ataques).

* **Clasificadores de Aprendizaje Automático Clásicos:** Evaluación comparativa de algoritmos de aprendizaje automático de bajo coste computacional (Regresión Logística, Random Forest, LightGBM y XGBoost) para garantizar tiempos de inferencia imperceptibles para su uso en entornos Edge AI.

## Conjunto de Datos
La experimentación se basa en el conjunto de datos [Indirect-Prompt-Injection-BIPIA-GPT](https://huggingface.co/datasets/MAlmasabi/Indirect-Prompt-Injection-BIPIA-GPT), que contiene 70.000 muestras balanceadas:

* **35.000 muestras maliciosas** derivadas del benchmark BIPIA.

* **35.000 muestras benignas** generadas de forma sintética mediante GPT-4o-mini.

## Resultados Principales
Tras evaluar 36 configuraciones distintas, el modelo compuesto por el algoritmo **XGBoost, los *embeddings* GTE-small y la estrategia de Fusión Enriquecida** demostró el mejor desempeño global:

* **Métricas predictivas:** PR-AUC del 0,9880 y F1-Score de 0,9556, con una Tasa de Falsos Positivos (FPR) del 5%.

* **Eficiencia (Edge AI):** Requiere un espacio en disco inferior a 0,5 MB y presenta un tiempo de inferencia del clasificador de 4 µs por muestra.

**Despliegue en Producción Simulado:**

Al integrar esta configuración como *guardrail* del modelo de lenguaje de última generación **Phi-4-mini**, el sistema logró reducir la Tasa de Éxito de Ataque (ASR) de un **41,4%** a apenas un **1,5%**. Además, el sistema introduce una latencia media de preprocesamiento de tan solo **32 ms**, resultando imperceptible para el usuario final.

## Estructura del Repositorio
* **01_Entrenamiento_y_Evaluacion.ipynb**: Cuaderno de experimentación que abarca el preprocesamiento de datos, extracción de características, estudio de ablación y entrenamiento comparativo de los clasificadores.

* **02_Evaluacion_Produccion.ipynb**: Cuaderno de simulación que despliega el modelo final junto a Phi-4-mini y evalúa su robustez automatizada mediante la técnica *LLM-as-a-Judge*.

## Instalación y Uso
Para reproducir los experimentos detallados en este repositorio:

1. Clona el repositorio

2. Los cuadernos están diseñados para ser ejecutados en Google Colab o entornos Jupyter. Las dependencias necesarias se instalan automáticamente en el bloque inicial de cada archivo.

Nota: Para ejecutar el cuaderno *02_Evaluacion_Produccion.ipynb*, es necesario disponer de [Ollama](https://ollama.com/) instalado localmente para el despliegue del SLM, y configurar una API Key válida de Groq para la auditoría con modelos juez.

## Publicación
*Pendiente - INCLUIR VERSIÓN FINAL DEL TRABAJO.*

## Licencia y Transparencia

### Código Fuente
El código desarrollado en este repositorio está disponible bajo la licencia **MIT**, permitiendo su uso, modificación y distribución con fines académicos y comerciales, siempre que se mantenga la atribución correspondiente.

### Conjuntos de Datos
El conjunto de datos Indirect-Prompt-Injection-BIPIA-GPT se distribuye bajo la licencia **Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)**, en conformidad con los términos originales de sus componentes subyacentes:

* **Benchmark BIPIA (Microsoft Corporation):** Código e infraestructura bajo licencia MIT.

* **WikiTableQuestions:** Licencia CC BY-SA 4.0.

* **Stack Exchange Data:** Licencia CC BY-SA 4.0.

* **OpenAI Evals (Invoices Data):** Licencia MIT.
