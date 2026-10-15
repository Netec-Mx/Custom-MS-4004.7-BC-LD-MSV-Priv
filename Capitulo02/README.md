# Laboratorio 2: Del modelo de datos a SQL y métricas de Power BI

**Duración:** 24 min

## Descripción
Usando el modelo interpretado, solicitarán a Copilot una consulta SQL que responda una pregunta concreta de servicio y explique las uniones, filtros y agregaciones utilizadas. Después, partirán de una necesidad o fórmula existente para generar o mejorar una expresión M o DAX para Power BI. Cerrarán la práctica definiendo pruebas sencillas que permitan comprobar que los resultados coinciden con el modelo y la regla de negocio.

## Pasos para ejecutar la práctica

1. **Generación y explicación de la consulta SQL:**
   * Abre Copilot Chat, adjunta el archivo `contexto_modelo_operativo.md` y envía:
     ```text
     Con base en la estructura descrita en contexto_modelo_operativo.md, redacta una consulta SQL estándar para identificar los 10 agentes con mayor tiempo promedio de respuesta en tickets de categoría "Crítica".
     
     Requisitos:
     - Incluye las cláusulas JOIN correspondientes entre Tickets_Servicio y Agentes.
     - Agrega una explicación paso a paso de las uniones, agrupaciones (GROUP BY) y filtros aplicados.
     ```

2. **Construcción de fórmulas M y expresiones DAX para Power BI:**
   * En el mismo chat, envía la siguiente instrucción:
     ```text
     A partir de la misma regla de negocio, genera dos artefactos para Power BI:
     1. Un script en lenguaje M (Power Query) para filtrar tickets cerrados con tiempos nulos.
     2. Una medida en DAX que calcule el Tiempo Promedio de Respuesta evaluando solo el contexto de filtro del mes actual.
     
     Incluye un bloque final de "Pruebas de Validación" con 2 comprobaciones para verificar que la medida DAX no duplique registros.
     ```

3. **Exportación de artefactos de código:**
   * Selecciona **Exportar a Word** y guarda el documento técnico con el nombre exacto: `Artefactos_SQL_DAX_Servicio.docx`.

## Resultado esperado
Un documento en Word llamado `Artefactos_SQL_DAX_Servicio.docx` con la consulta SQL, las transformaciones M, la medida DAX y el plan de validación técnica.
