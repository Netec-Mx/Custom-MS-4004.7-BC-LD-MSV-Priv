# Laboratorio 4: Convertir el análisis técnico en decisiones y comunicación

**Duración:** 25 min

## Descripción
Tomando el Copilot Notebook como contexto del caso, los participantes prepararán en Word una nota técnica con supuestos, consulta, fórmulas, controles de privacidad y validaciones; generarán en PowerPoint un resumen ejecutivo de hallazgos, riesgos y acciones; y redactarán en Outlook un mensaje para los responsables del servicio con decisiones, pendientes y seguimiento. Copilot adaptará la profundidad del contenido sin cambiar la evidencia validada.

## Pasos para ejecutar la práctica

1. **Redacción de la Nota Técnica Formal en Microsoft Word:**
   * Abre **Microsoft Word** y activa Copilot.
   * Adjunta los archivos creados en el flujo: `contexto_modelo_operativo.md`, `Artefactos_SQL_DAX_Servicio.docx` y `Datos_Operativos_Anonimizados.xlsx`.
   * Envía el siguiente prompt:
     ```text
     Con base en los archivos adjuntos, redacta la Nota Técnica de Diagnóstico Operativo y Gobierno de Datos.
     
     Estructura el documento en:
     1. Resumen Ejecutivo y Diagnóstico de Tiempos de Servicio.
     2. Artefactos Técnicos Desarrollados (Resumen de SQL, DAX y Script de Anonimización).
     3. Controles de Privacidad de Datos Aplicados.
     4. Matriz de Recomendaciones y Próximos Pasos.
     ```
   * Guarda el documento con el nombre exacto: `Nota_Tecnica_Diagnostico_Operativo.docx`.

2. **Construcción de la Presentación Ejecutiva en PowerPoint:**
   * Abre **Microsoft PowerPoint** y activa Copilot.
   * Adjunta el archivo `Nota_Tecnica_Diagnostico_Operativo.docx` y envía:
     ```text
     Crea una presentación ejecutiva de 4 diapositivas basada en el archivo adjunto:
     - Diapositiva 1: Diagnóstico de Servicio y Causas Raíz
     - Diapositiva 2: Hallazgos Cuantitativos y Métricas Clave
     - Diapositiva 3: Estrategia de Protección de Datos y Artefactos
     - Diapositiva 4: Plan de Acción y Decisiones Requeridas
     ```
   * Guarda la presentación como: `Resumen_Ejecutivo_Operaciones.pptx`.

3. **Preparación de comunicado en Outlook y prueba interactiva de cierre:**
   * Abre **Microsoft Outlook** (o Copilot Chat), adjunta `Nota_Tecnica_Diagnostico_Operativo.docx` y envía:
     ```text
     Con base en la Nota Técnica adjunta, redacta un correo de actualización para el Gerente de Operaciones que resuma los hallazgos principales, las acciones de mitigación y las decisiones pendientes.
     ```
   * En Copilot Chat, envía el siguiente prompt para ejecutar la evaluación de opción múltiple final:
     ```text
     Con base en el trabajo completado en la Nota Técnica de Diagnóstico Operativo, actúa como un Auditor de Calidad y Operaciones y evalúa mi aprendizaje.

     Reglas:
     1. Formula 3 preguntas consecutivas de selección múltiple (A, B, C, D) sobre la validación de fórmulas DAX, controles de privacidad con Python y diagnóstico de servicio.
     2. Haz una pregunta a la vez y espera mi respuesta de una sola letra.
     3. Entrega retroalimentación inmediata sobre la opción seleccionada antes de pasar a la siguiente pregunta.
     ```

## Resultado esperado
Un documento en Word llamado `Nota_Tecnica_Diagnostico_Operativo.docx`, una presentación ejecutiva en PowerPoint `Resumen_Ejecutivo_Operaciones.pptx`, el borrador de correo preparado en Outlook y la prueba de opción múltiple completada en Copilot Chat.
