# Proyecto 5
## Chatbot Conversacional Multimodal para Servicio al Cliente (Banco Digital)
### Problema:
Alto volumen de consultas repetitivas (~80%) y necesidad de atención 24/7.
### Solución:
Chatbot inteligente con NLU (BERT + clasificación de intenciones + extracción de entidades), gestión de diálogo basada en Transformer con memoria, pipeline multimodal (texto + OCR de imágenes), API + interfaz web (Gradio) y sistema de evaluación.
### Conjuntos de datos: Banking77

### Análisis Detallado de los Resultados
Prueba de validación métrica
- Precisión 84,42% 84,58% 
- Macro-F1 83,45% 84,38%
### Interpretación:
Consistencia excelente entre validación y prueba (diferencia < 0.5%) 
F1 cercano al exactitud sugiere buen equilibrio entre precisión y recuperación 
Resultados sobresalientes para un problema multiclase con 77 etiquetas
### Experimentación
Proceso de experimentación simple y fácil de comprender para comparar diferentes arquitecturas de redes neuronales, funciones de activación e hiperparámetros para la clasificación de intenciones bancarias basadas en texto y OCR, utilizando como baseline una regresión logística. 
Guarda los resultados de rendimiento y las curvas de entrenamiento. Asegurarse de tener listos los datos vectorizados y codificados del OCR y dividirlos en conjuntos de entrenamiento y prueba.
### Plataformas utilizadas
- Google Colab: ejecuta este notebook (GPU opcional para BERT)
- Conjuntos de datos de Hugging Face y transformadores para NLU (Banking77 + BERT).
- Scikit learn para línea base TF IDF + Regresión logística.
- FastAPI + Uvicorn + pyngrok para exponer la API y obtener una URL pública temporal.
- Grado para la interfaz web.
- Pytesseract para OCR (tubería multimodal).
### Problemas detectados
- Rendimiento lento (0,16 it/s)
- Un conjunto de datos de imágenes pequeño y no diverso.
- Preprocesamiento de imágenes más avanzado.
- Posiblemente técnicas de OCR más sofisticadas o modelos de clasificación más complejos.
### Causas posibles:
- Hardware insuficiente
- Tamaño de lote muy pequeño


## Decisiones de diseño y justificación

**NLU híbrido (BERT + baseline + NN simple).**  
- BERT como principal por su robustez semántica; baseline TF-IDF+LR como respaldo por velocidad y estabilidad; NN simple (embeddings básicos) para cumplir con el requerimiento de “red para clasificación multiclase”. Esto garantiza disponibilidad (fallback) y calidad (BERT).

**Extracción de entidades ligera + spaCy opcional.**  
- Reglas (IBAN, tarjetas, montos, fechas, email) cubren la mayoría de casos bancarios con coste cero de entrenamiento.
- spaCy `en_core_web_sm` se usa como refuerzo si está disponible, sin volver obligatorio el modelo pesado.

**Gestión de diálogo con Transformer + memoria corta.**  
- DialoGPT-small permite respuestas naturales con latencia razonable; memoria de ~6 turnos evita desbordes y preserva contexto inmediato.

**Pipeline multimodal (OCR).**  
- `pytesseract` extrae texto de vouchers/capturas; luego se corren reglas de entidades sobre el texto OCR. Aporta cobertura de casos reales (comprobantes) sin costos de entrenar modelos de visión.

**API FastAPI + Uvicorn + ngrok.**  
- FastAPI por tipado, rendimiento y documentación automática `/docs`.
- ngrok para exponer temporalmente en Colab o entornos de prueba sin infraestructura adicional.

**Evaluación automática.**  
- Modelos: accuracy y macro-F1 (útil ante desbalance de intents) + matriz de confusión y reporte por clase.
- Satisfacción: endpoints `/feedback` y `/feedback_stats` para CSAT/NPS/helpful; base CSV simple que puede migrar a DB.

**UI Gradio.**  
- Pestañas separadas (intents, entidades, chat, OCR, satisfacción) para validar fin-a-fin con mínima fricción para negocio y QA.

**Portabilidad.**  
- Notebooks para prototipado; scripts (train/eval/app) para producción en Cloud Run/Railway sin cambios de lógica.
