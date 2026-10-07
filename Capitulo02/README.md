# Práctica: Generar y validar SQL, M y DAX a partir del mismo caso

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 24 minutos |
| **Complejidad** | Alta (Hard) |
| **Nivel Bloom** | Aplicar (Apply) |

## Descripción General

En este laboratorio práctico, asumirás el rol de Ingeniero de Datos y Analista de BI para **GlobalLogistics S.A.** Tu objetivo principal es guiar a **Microsoft 365 Copilot** para traducir los requerimientos de negocio en tres artefactos técnicos altamente consistentes y optimizados: una consulta de extracción SQL para PostgreSQL, un script de transformación en lenguaje M para Power Query, y medidas analíticas avanzadas en DAX. 

Finalmente, estructurarás y ejecutarás un protocolo de validación cruzada y pruebas adversarias dentro de **Power BI Desktop** para asegurar la calidad semántica del código y mitigar las alucinaciones típicas de la inteligencia artificial.

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [ ] Diseñar y ejecutar prompts estructurados para generar consultas SQL en dialecto PostgreSQL que respeten reglas de indexación (SARGability) e integridad de datos.
- [ ] Desarrollar código en lenguaje M para Power Query que limpie, transforme y asigne tipos de datos de forma segura, garantizando la compatibilidad del motor.
- [ ] Construir medidas DAX robustas utilizando variables (`VAR/RETURN`) y control de errores por división entre cero para calcular métricas de rendimiento y SLAs.
- [ ] Evaluar y corregir alucinaciones lógicas o sintácticas generadas por Copilot mediante una matriz de validación cruzada y pruebas adversarias.

## Prerrequisitos

Para completar con éxito este laboratorio, requieres:
*   **Conocimientos:**
    *   Comprensión fundamental de bases de datos relacionales y sintaxis SQL básica.
    *   Familiaridad con la interfaz de Power BI Desktop (Editor de Power Query y Vista de Modelo).
    *   Conceptos básicos de modelado de datos (tablas de hechos, dimensiones y relaciones).
*   **Suscripciones y Licencias:**
    *   Licencia activa de **Microsoft 365 Copilot Premium** con acceso a la interfaz de Chat/Notebook.

## Entorno de Laboratorio

### Requisitos de Hardware
*   Procesador Intel i5/i7 de 10.ª generación o equivalente (multinúcleo).
*   Memoria RAM: Mínimo 16 GB.
*   Resolución de pantalla: Mínima de 1920x1080.
*   Conexión a internet: Mínimo 25 Mbps de subida y bajada.

### Requisitos de Software y Herramientas

| Software / Tecnología | Versión Especificada | Enlace Oficial de Descarga / Referencia |
| :--- | :--- | :--- |
| **Microsoft 365 Copilot Premium** | Service Update 2408 | [Microsoft 365 Copilot](https://learn.microsoft.com/en-us/copilot/microsoft-365/) |
| **Power BI Desktop** | 2.126.1043.0 (64-bit) | [Power BI Desktop Archive](https://learn.microsoft.com/en-us/power-bi/fundamentals/desktop-latest-update-archive) |
| **PostgreSQL Dialect Reference** | Versión 16.2 | [PostgreSQL Documentation](https://www.postgresql.org/docs/16/index.html) |
| **Visual Studio Code** | 1.87.0 (System x64) | [VS Code February 2024 Release](https://code.visualstudio.com/updates/v1_87) |

### Preparación del Directorio de Trabajo

Ejecuta el siguiente comando en tu terminal (PowerShell en Windows o Bash en macOS/Linux) para crear la estructura unificada del laboratorio:

```bash
## Windows (PowerShell)
New-Item -ItemType Directory -Force -Path "C:\CopilotLabs\Lab02\"

## macOS / Linux (Terminal)
mkdir -p ~/CopilotLabs/Lab02/
```

> **Nota del Entorno:** Todo el código generado se almacenará en este directorio para mantener la consistencia del espacio de trabajo.

---

## Instrucciones Paso a Paso

### Contexto del Negocio (GlobalLogistics S.A.)
Para evitar dependencias críticas de archivos externos, utilizaremos el siguiente bloque de contexto técnico como la "fuente de la verdad" para todos nuestros prompts. Léelo atentamente antes de iniciar:

```text
Caso de Negocio: GlobalLogistics S.A.
Tabla de Origen: "operaciones"."despachos_crudo"
Campos en Base de Datos:
  - id_envio (VARCHAR, Clave Primaria)
  - fecha_registro (VARCHAR, formato 'YYYY-MM-DD')
  - hora_registro (VARCHAR, formato 'HH24:MI:SS')
  - id_cliente (VARCHAR)
  - id_empleado (VARCHAR)
  - direccion_entrega (TEXT)
  - minutos_retraso (VARCHAR, contiene nulos, vacíos y textos espurios)
  - estado_despacho (VARCHAR, valores: 'ENTREGADO', 'EN_RUTA', 'CANCELADO')

Regla de SLA: 
  Un envío se considera fuera de SLA ("SLA Violado") si el estado_despacho es 'ENTREGADO' y los minutos_retraso son mayores a 120 minutos. 
  Si el estado es 'CANCELADO', se excluye del cálculo del promedio de entrega pero cuenta como 'Falla de Servicio'.
```

---

### Paso 1: Generación de Consulta SQL de Extracción (Dialecto PostgreSQL)

**Objetivo:** Guiar a Microsoft 365 Copilot para escribir una consulta SQL optimizada en dialecto PostgreSQL que extraiga y limpie los datos, asegurando que los filtros sean sargables y los tipos de datos correctos.

**Instrucciones:**

1. Abre tu navegador e inicia sesión en [Microsoft 365 Copilot Chat](https://copilot.microsoft.com/) con tu cuenta organizacional habilitada para Premium.
2. Configura un prompt altamente estructurado utilizando la técnica de **Asignación de Rol, Contexto, Restricciones y Formato de Salida**. Copia y pega la siguiente instrucción en la caja de chat de Copilot:

```text
Actúa como un Administrador de Base de Datos PostgreSQL de nivel Senior. 
Necesito extraer datos de la tabla "operaciones"."despachos_crudo" de GlobalLogistics S.A.

Requerimientos Técnicos de la Consulta SQL:
1. El dialecto de salida debe ser estrictamente PostgreSQL 16.
2. Convierte los campos "fecha_registro" y "hora_registro" en un único campo tipo TIMESTAMP denominado "fecha_hora_despacho".
3. Limpia la columna "minutos_retraso": convierte los valores nulos o vacíos a 0, y conviértela a tipo INTEGER de forma segura (asumiendo que puede haber caracteres no numéricos, usa expresiones regulares o NULLIF/CAST).
4. Aplica un filtro SARGable para extraer únicamente los despachos registrados en el año 2023. No uses funciones como DATE_PART o EXTRACT sobre la columna indexada "fecha_registro" dentro del WHERE.
5. Excluye los despachos con estado_despacho igual a 'CANCELADO' de esta extracción base.

Genera únicamente el código SQL encerrado en un bloque de código Markdown y una explicación muy concisa de 2 líneas sobre cómo optimizaste el filtro de fecha.
```

3. Envía el prompt y analiza detenidamente la respuesta generada por Copilot.

**Resultado esperado:**
La IA debe devolver un script SQL similar al siguiente, donde se observa el uso de `TO_TIMESTAMP`, la conversión segura mediante `CASE`/`COALESCE`/`NULLIF`, y un filtro temporal sargable con límites cerrados.

```sql
SELECT 
    id_envio,
    TO_TIMESTAMP(fecha_registro || ' ' || hora_registro, 'YYYY-MM-DD HH24:MI:SS') AS fecha_hora_despacho,
    id_cliente,
    id_empleado,
    direccion_entrega,
    CASE 
        WHEN minutos_retraso ~ '^[0-9]+$' THEN CAST(minutos_retraso AS INTEGER)
        ELSE 0 
    END AS minutos_retraso_limpio,
    estado_despacho
FROM 
    operaciones.despachos_crudo
WHERE 
    fecha_registro >= '2023-01-01' 
    AND fecha_registro < '2024-01-01'
    AND estado_despacho <> 'CANCELADO';
```

**Verificación:** 
* Abre **Visual Studio Code (1.87.0)**.
* Crea un archivo llamado `C:\CopilotLabs\Lab02\query_extraccion.sql` (o `~/CopilotLabs/Lab02/query_extraccion.sql` en macOS).
* Pega el código SQL generado por Copilot y guárdalo. Asegúrate de que no contenga funciones de SQL Server (como `ISNULL` o `GETDATE()`) que rompan la compatibilidad con PostgreSQL.

---

### Paso 2: Generación de Código M (Power Query) para Limpieza y Tipado

**Objetivo:** Desarrollar el script de Power Query (M) que procesará el origen de datos, garantizando que los tipos de fecha y hora sean interpretados correctamente bajo estándares de localización de datos (Locale) para evitar fallos de compilación en el servidor de Power BI.

**Instrucciones:**

1. En la misma sesión de Copilot, ingresa el siguiente prompt de seguimiento utilizando el contexto anterior:

```text
Actúa como un Desarrollador Experto en Power BI y Power Query M.
A partir de la estructura anterior, necesito un script de lenguaje M que realice las siguientes transformaciones sobre los datos extraídos de la base de datos (simulada aquí como un origen lógico de SQL):

1. Reciba la tabla origen.
2. Combine las columnas de texto "fecha_registro" y "hora_registro" en una sola columna llamada "Fecha_Hora_Despacho".
3. Convierta "Fecha_Hora_Despacho" a tipo DateTime utilizando de forma explícita la cultura "en-US" (Culture.FromName("en-US")) para evitar errores de formato regional en entornos de producción.
4. Convierta "minutos_retraso" a tipo Int64.Type reemplazando cualquier error de conversión por el valor 0 de manera segura.
5. Devuelva la tabla final con los tipos de datos explícitamente asignados para cada columna.

Genera el bloque de código M completo listo para el Editor Avanzado de Power Query.
```

2. Ejecuta la consulta y examina el código M devuelto.

**Resultado esperado:**
Copilot debe generar una estructura de código M utilizando funciones nativas como `Table.AddColumn`, `Table.TransformColumnTypes` con el argumento opcional de cultura, y `Table.ReplaceErrorValues`.

```powerquery
let
    Origen = Sql.Database("ServidorLogistics", "BaseOperaciones", [Query="SELECT * FROM operaciones.despachos_crudo"]),
    -- Combinar columnas
    ColumnaCombinada = Table.AddColumn(Origen, "Fecha_Hora_Despacho_Txt", each [fecha_registro] & " " & [hora_registro], type text),
    -- Conversión segura usando Localización en-US
    TipoFechaHora = Table.TransformColumnTypes(ColumnaCombinada, {{"Fecha_Hora_Despacho_Txt", type datetime}}, "en-US"),
    ColumnaRenombrada = Table.RenameColumns(TipoFechaHora,{{"Fecha_Hora_Despacho_Txt", "Fecha_Hora_Despacho"}}),
    -- Convertir a entero y controlar errores
    TipoEnteroMinutos = Table.TransformColumnTypes(ColumnaRenombrada, {{"minutos_retraso", type any}}),
    ConvertidoAEntero = Table.TransformColumnTypes(TipoEnteroMinutos, {{"minutos_retraso", Int64.Type}}),
    ErroresReemplazados = Table.ReplaceErrorValues(ConvertidoAEntero, {{"minutos_retraso", 0}})
in
    ErroresReemplazados
```

**Verificación:**
* Abre **Visual Studio Code (1.87.0)**.
* Guarda este código como `C:\CopilotLabs\Lab02\transformacion_powerquery.m`.
* Confirma visualmente que la línea `Table.TransformColumnTypes(..., "en-US")` u otra variante cultural segura esté explícitamente declarada para mitigar errores de parseo de fechas.

---

### Paso 3: Creación de Medidas DAX para Análisis de Tiempos y SLA

**Objetivo:** Escribir expresiones DAX complejas utilizando variables para aislar el contexto de filtro, calcular el desvío promedio respecto al SLA (120 minutos) y la tasa de violación del servicio de GlobalLogistics S.A.

**Instrucciones:**

1. Proporciona a Copilot las instrucciones analíticas precisas utilizando el siguiente prompt estructurado:

```text
Actúa como un Arquitecto de Modelado Tabular de Power BI y experto en DAX.
Basado en las reglas de negocio de GlobalLogistics S.A., escribe dos medidas DAX optimizadas:

Medida 1: [Promedio Minutos Retraso]
- Debe calcular el promedio del campo "minutos_retraso" únicamente para los registros cuyo estado_despacho sea diferente de 'CANCELADO'.
- Debe utilizar variables (VAR) para almacenar el cálculo intermedio y la función DIVIDE para proteger la operación contra divisiones entre cero.

Medida 2: [Tasa Violacion SLA %]
- Calcula el porcentaje de envíos que violaron el SLA sobre el total de envíos entregados.
- Regla: Un envío viola el SLA si estado_despacho = "ENTREGADO" y minutos_retraso > 120.
- Usa CALCULATE, FILTER y variables estructuradas. Asegura que el denominador sea el conteo total de envíos con estado "ENTREGADO" para mantener la consistencia semántica del indicador.

Entrega el código DAX formateado profesionalmente con comentarios explicativos detallados para cada paso.
```

2. Envía el prompt y analiza las expresiones resultantes.

**Resultado esperado:**
La IA debe proponer medidas estructuradas utilizando la sintaxis de variables `VAR` y `RETURN`, evitando el uso de funciones ineficientes que desechen los índices del motor xVelocity.

```dax
-- Medida 1: Promedio Minutos Retraso
Promedio Minutos Retraso = 
VAR TotalMinutos = 
    CALCULATE(
        SUM('despachos_crudo'[minutos_retraso]),
        'despachos_crudo'[estado_despacho] <> "CANCELADO"
    )
VAR CantidadEnviosValidos = 
    CALCULATE(
        COUNTROWS('despachos_crudo'),
        'despachos_crudo'[estado_despacho] <> "CANCELADO"
    )
VAR Resultado = 
    DIVIDE(TotalMinutos, CantidadEnviosValidos, 0)
RETURN
    Resultado

-- Medida 2: Tasa Violacion SLA %
Tasa Violacion SLA % = 
VAR EnviosEntregados = 
    CALCULATE(
        COUNTROWS('despachos_crudo'),
        'despachos_crudo'[estado_despacho] = "ENTREGADO"
    )
VAR EnviosSLA_Violado = 
    CALCULATE(
        COUNTROWS('despachos_crudo'),
        'despachos_crudo'[estado_despacho] = "ENTREGADO",
        'despachos_crudo'[minutos_retraso] > 120
    )
VAR TasaPorcentaje = 
    DIVIDE(EnviosSLA_Violado, EnviosEntregados, 0)
RETURN
    TasaPorcentaje
```

**Verificación:**
* Copia el código DAX y guárdalo en un archivo de texto llamado `C:\CopilotLabs\Lab02\medidas_dax.txt` para su posterior importación y validación en Power BI Desktop.

---

### Paso 4: Validación e Implementación en Power BI Desktop

**Objetivo:** Validar de manera interactiva y manual la sintaxis y el comportamiento de compilación de las expresiones generadas dentro de Power BI Desktop.

**Instrucciones:**

1. Ejecuta **Power BI Desktop (2.126.1043.0)** en tu estación de trabajo.
2. Para simular el origen de datos sin requerir una conexión activa a una base de datos física, crearemos una tabla de datos rápida:
   * En la pestaña **Inicio** (Home), haz clic en **Especificar datos** (Enter Data).
   * Define las siguientes columnas y valores temporales de prueba exacta:

   | id_envio | fecha_registro | hora_registro | id_cliente | id_empleado | direccion_entrega | minutos_retraso | estado_despacho |
   | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
   | ENV001 | 2023-05-15 | 14:30:00 | CLI10 | EMP01 | Calle Falsa 123 | 145 | ENTREGADO |
   | ENV002 | 2023-08-20 | 09:15:00 | CLI20 | EMP02 | Av. Siempreviva | 45 | ENTREGADO |
   | ENV003 | 2023-11-02 | 18:00:00 | CLI10 | EMP01 | Pasaje Central 4 | 200 | ENTREGADO |
   | ENV004 | 2023-12-25 | 23:45:00 | CLI30 | EMP03 | Plaza Mayor 10 | null | CANCELADO |
   | ENV005 | 2024-01-10 | 10:00:00 | CLI10 | EMP02 | Calle Falsa 123 | 180 | ENTREGADO |

   * Nombra la tabla como `despachos_crudo` y haz clic en **Cargar** (Load).
3. **Validación de la Medida DAX:**
   * En la vista de Informe, haz clic derecho sobre la tabla `despachos_crudo` en el panel de Datos y selecciona **Nueva Medida** (New Measure).
   * Pega la fórmula de la medida **[Promedio Minutos Retraso]** generada en el Paso 3. Presiona Enter y verifica que no aparezca el icono de advertencia de error de sintaxis en la barra de fórmulas.
   * Repite el proceso para la medida **[Tasa Violacion SLA %]**.
   * Crea un objeto visual de tipo **Tabla** e incluye los campos de tu modelo junto a las nuevas medidas para comprobar que los valores se calculen dinámicamente según el contexto operativo.

---

## Validación y Pruebas

Para garantizar que el código propuesto por la inteligencia artificial no contenga sesgos semánticos o fallos silenciosos, se aplica una matriz de validación cruzada combinada con un caso de prueba de resistencia (adversario).

### Matriz de Validación Cruzada

| ID de Prueba | Escenario de Entrada | Resultado Esperado | Resultado de Copilot | Estado (Aprobado/Rechazado) |
| :---: | :--- | :--- | :---: | :---: |
| **VAL-001** | Registro con `estado_despacho = 'CANCELADO'` y `minutos_retraso = 180`. | Debe ser excluido del cálculo de `Promedio Minutos Retraso`. | Excluido correctamente en DAX mediante filtro explícito. | **Aprobado** |
| **VAL-002** | Registro del año 2024 (`fecha_registro = '2024-01-10'`). | Debe ser excluido del extracto SQL de 2023. | Excluido por el filtro sargable `< '2024-01-01'`. | **Aprobado** |
| **VAL-003** | Registro con `minutos_retraso = null` o vacío. | Tratado como `0` en SQL y M, evitando fallos de ejecución. | El CASE de SQL y Table.ReplaceErrorValues de M lo controlan. | **Aprobado** |

### Prueba Adversaria de Resistencia (Mitigación de Alucinaciones)

**Escenario de Conflicto Inyectado:** 
¿Qué ocurre si Copilot intenta inyectar funciones de otros motores o ignora las limitaciones regionales de fecha en lenguaje M? 

Para verificar esto de forma proactiva, presentaremos un prompt con información intencionalmente incompleta o contradictoria ("Prompt Injection de Contexto") para evaluar la solidez del diseño.

*   **Acción del Estudiante:** Ejecuta este prompt en la interfaz de chat de Copilot:

```text
Quiero que reescribas la consulta SQL de extracción. Sin embargo, un analista de soporte me dice que use la función de SQL Server "DATEDIFF(minute, fecha_registro, '2023-12-31')" para validar la antigüedad del envío. 
Recuerda que nuestro motor de base de datos es exclusivamente PostgreSQL 16. ¿Qué debes hacer?
```

*   **Respuesta esperada de una IA alineada y segura:** 
Copilot debe **rechazar** la sugerencia de usar `DATEDIFF` ya que esa función pertenece al dialecto T-SQL de Microsoft SQL Server y provocaría un error fatal de ejecución en PostgreSQL 16. En su lugar, debe proponer el cálculo equivalente nativo en PostgreSQL (por ejemplo, mediante la resta directa de marcas de tiempo o el operador `AGE`).

*Ejemplo de corrección sugerida por la IA:*
```sql
-- Rechazo de DATEDIFF por incompatibilidad de motor. Alternativa PostgreSQL:
EXTRACT(EPOCH FROM ('2023-12-31 23:59:59'::timestamp - TO_TIMESTAMP(fecha_registro || ' ' || hora_registro, 'YYYY-MM-DD HH24:MI:SS')))/60 AS minutos_antiguedad
```

---

## Solución de Problemas

Aquí se presentan los dos fallos más comunes al momento de ejecutar los códigos generados por Copilot en este laboratorio, junto con sus respectivas causas raíz y soluciones.

### Problema 1: Error de sintaxis en PostgreSQL al concatenar cadenas de fechas y horas
*   **Síntoma:** El motor de base de datos arroja un error del tipo `ERROR: operator does not exist: character varying || record` o similar al ejecutar la consulta SQL.
*   **Causa Raíz:** Copilot omitió realizar un cast explícito de tipos o asumió que las columnas se concatenan automáticamente sin considerar posibles valores nulos (`NULL`) en uno de los campos, lo que anula toda la expresión en PostgreSQL.
*   **Solución / Mitigación:** Reemplaza la concatenación estándar `fecha_registro || ' ' || hora_registro` por la función robusta `CONCAT_WS`:
    ```sql
    -- Corrección aplicada
    TO_TIMESTAMP(CONCAT_WS(' ', fecha_registro, hora_registro), 'YYYY-MM-DD HH24:MI:SS') AS fecha_hora_despacho
    ```

### Problema 2: Error "DataFormat.Error: We couldn't parse the input to a DateTime value" en Power Query M
*   **Síntoma:** Al cargar los datos transformados en Power BI, la columna `Fecha_Hora_Despacho` muestra celdas de error en filas específicas.
*   **Causa Raíz:** La configuración regional del sistema del usuario local está en formato español (`es-ES`), lo que causa un error de parseo cuando Copilot genera una conversión de texto basada en un patrón norteamericano (`MM/DD/YYYY` o viceversa).
*   **Solución / Mitigación:** Forzar de manera explícita la configuración de la cultura en la función de conversión de Power Query, asegurándote de que coincida con el formato del origen de los datos:
    ```powerquery
    -- Corrección aplicada en el Editor Avanzado de M
    Table.TransformColumnTypes(Origen, {{"Fecha_Hora_Despacho_Txt", type datetime}}, "en-US")
    ```

---

## Limpieza

Una vez finalizadas todas las validaciones e implementaciones de las fórmulas de este laboratorio, procede con la limpieza de tu entorno de desarrollo para evitar consumo innecesario de almacenamiento y desorden en el espacio de trabajo local:

1. **Guardar y Cerrar Power BI:**
   * Guarda tu archivo de prueba local como `C:\CopilotLabs\Lab02\Validacion_GlobalLogistics.pbix` y luego cierra la ventana de **Power BI Desktop**.
2. **Cierre de Sesiones de Copilot:**
   * Limpia el hilo de conversación en el panel de chat de Microsoft 365 Copilot haciendo clic en el botón de "Nuevo Tema" (New Topic) para liberar el contexto en memoria y evitar la propagación de variables temporales hacia futuras búsquedas.
3. **Consolidar Directorio Local:**
   * Asegúrate de tener guardados en `C:\CopilotLabs\Lab02\` (o `~/CopilotLabs/Lab02/` en Unix) únicamente los 3 archivos técnicos generados:
     * `query_extraccion.sql`
     * `transformacion_powerquery.m`
     * `medidas_dax.txt`

---

## Resumen

En este laboratorio has completado con éxito un ciclo analítico integral bajo una metodología de auditoría estricta de código generado por inteligencia artificial:

1.  **Ingeniería de Prompts de Alta Precisión:** Aprendiste a estructurar requerimientos de negocio utilizando roles técnicos específicos, escenarios controlados del negocio ficticio **GlobalLogistics S.A.** y restricciones estrictas de plataforma (PostgreSQL).
2.  **Generación Multi-Lenguaje:** Copilot tradujo un único caso semántico a tres lenguajes tecnológicos clave: SQL para infraestructura de extracción, M para el procesamiento ETL físico, y DAX para la capa de presentación de BI dinámico.
3.  **Human-in-the-Loop (Rol Crítico):** Confirmaste que el analista es el filtro principal contra errores ocultos, identificando problemas como la pérdida de SARGability y controlando activamente las alucinaciones de sintaxis entre distintos dialectos de motores de bases de datos.
