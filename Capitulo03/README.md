# Laboratorio 3: Proteger y analizar datos antes de tomar decisiones

**Duración:** 34 min

## Descripción
Con un archivo de ejemplo, pedirán a Copilot que genere un script de Python para anonimizar nombres, correos o identificadores, conservar la estructura necesaria para el análisis y producir un archivo de salida separado. Solicitarán parámetros, comentarios, controles de errores y una comprobación del resultado antes de considerar el script utilizable. Con los datos anonimizados, utilizarán Analista y Copilot en Excel para comparar conteos, identificar patrones, excepciones y prioridades de intervención. Incorporarán al Notebook los hallazgos, la tabla de seguimiento y las decisiones.

## Pasos para ejecutar la práctica

1. **Generación del script de Python para anonimización:**
   * Abre Copilot Chat y envía el siguiente prompt:
     ```text
     Actúa como un Especialista en Seguridad y Privacidad de Datos.
     
     Escribe un script de Python utilizando la librería `pandas` que reciba un dataset de tickets de servicio y anonimice las columnas 'Nombre_Cliente' y 'Correo_Contacto' reemplazándolas por un hash sintético o ID anonimizado (ej. CLI_001).
     
     Requisitos:
     - Incluye comentarios explicativos en cada bloque de código.
     - Conserva intactos los campos cuantitativos: 'ID_Ticket', 'Tiempo_Respuesta_Min' y 'Satisfaccion_CSAT'.
     - Agrega un control de errores que verifique que la tabla resultante no contenga valores nulos en los IDs.
     ```
   * Copia el código generado y guárdalo en un archivo de texto con la extensión: `script_anonimizacion.py`.

2. **Generación y análisis del dataset anonimizado en Microsoft Excel:**
   * En Copilot Chat, solicita un conjunto de datos ficticios anonimizados:
     ```text
     Genera una tabla de datos de 6 filas que simule el archivo de salida anonimizado con las columnas: ID_Ticket, ID_Cliente_Anonimizado, Categoria, Tiempo_Respuesta_Min y Satisfaccion_CSAT.
     ```
   * Exporta la tabla a Microsoft Excel y guárdala como: `Datos_Operativos_Anonimizados.xlsx`.

3. **Análisis cuantitativo con el agente Analista en Excel:**
   * Activa el agente **Analista (Analyst)** en Microsoft 365 Copilot, adjunta `Datos_Operativos_Anonimizados.xlsx` y envía:
     ```text
     Analiza la tabla adjunta en Datos_Operativos_Anonimizados.xlsx.
     
     Identifica:
     1. El tiempo promedio de respuesta por Categoría de ticket.
     2. La correlación entre tiempos de respuesta elevados y niveles bajos de Satisfacción (CSAT).
     3. Tres prioridades de intervención operacional.
     ```
   * Guarda los resultados consolidándolos en una nueva hoja del archivo `Datos_Operativos_Anonimizados.xlsx`.

## Resultado esperado
El archivo de código `script_anonimizacion.py` para la protección de datos y el libro de Excel `Datos_Operativos_Anonimizados.xlsx` enriquecido con el análisis de patrones del agente Analista.
