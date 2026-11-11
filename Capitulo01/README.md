# Laboratorio 1: Del problema operativo al contexto de datos

**Duración:** 19 min

## Descripción
A partir de una imagen o PDF con un modelo de datos de servicio, los participantes pedirán a Copilot que identifique entidades, claves y relaciones, vincule el modelo con la pregunta operativa, detecte ambigüedades y proponga los campos necesarios. Consolidarán el PDF o las notas derivadas, el enunciado y las decisiones en un Copilot Notebook como contexto reutilizable.

## Pasos para ejecutar la práctica

1. **Generación de la especificación del modelo de datos de servicio:**
   * Abre Microsoft 365 Copilot Chat y envía el siguiente prompt para estructurar la definición del modelo:
     ```text
     Actúa como un Arquitecto de Datos Operativos.
     
     Genera la especificación técnica en texto formateado de un modelo de datos para el área de Servicio al Cliente.
     
     Incluye:
     - 4 Entidades principales: Clientes, Tickets_Servicio, Canales, Agentes.
     - Claves primarias (PK) y claves foráneas (FK) para relacionar las tablas.
     - Campos clave: ID_Ticket, Fecha_Creacion, Tiempo_Respuesta_Min, Estado, Satisfaccion_CSAT.
     ```
   * Exporta o guarda el resultado generado en un archivo PDF con el nombre exacto: `Modelo_Datos_Operaciones.pdf`.

2. **Interpretación y análisis del modelo de datos:**
   * Abre **Copilot Notebooks** (Bloc de notas de Copilot).
   * Adjunta el archivo `Modelo_Datos_Operaciones.pdf` y envía el siguiente prompt:
     ```text
     Analiza el modelo de datos adjunto en Modelo_Datos_Operaciones.pdf para diagnosticar una caída de 15 puntos en el indicador de Tiempo de Respuesta Operativo.
     
     Identifica:
     1. Entidades, claves primarias y foráneas que conectan el flujo de atención.
     2. Ambigüedades estructurales o posibles campos faltantes para medir tiempos de espera.
     3. Selección de los 5 campos indispensables para el análisis.
     ```

3. **Consolidación del contexto en Markdown:**
   * En el mismo Copilot Notebook, envía la siguiente instrucción:
     ```text
     Estructura la interpretación anterior en sintaxis Markdown (.md) conservando las decisiones y los hallazgos clave.
     ```
   * Copia o guarda el contenido generado en un archivo llamado: `contexto_modelo_operativo.md`.

## Resultado esperado
Un archivo de contexto guardado como `contexto_modelo_operativo.md` que interpreta las entidades, claves y relaciones del modelo de datos `Modelo_Datos_Operaciones.pdf` para el diagnóstico de servicio.
