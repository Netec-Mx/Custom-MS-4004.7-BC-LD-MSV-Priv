# Práctica: Interpretar un modelo de datos y construir el contexto del caso

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 19 minutos |
| **Complejidad** | Media |
| **Nivel Bloom** | Aplicar |

## Descripción General

En este laboratorio práctico, asumirás el rol de Analista de Operaciones de Servicio en **GlobalLogistics S.A.** Tu objetivo es abordar un problema operativo ambiguo y común: quejas recurrentes de clientes clave sobre retrasos en las entregas de mercancía. 

Utilizando **Microsoft 365 Copilot Chat** y **Microsoft Word**, cargarás un esquema del modelo de datos de la empresa, estructurarás la queja cualitativa en tres requerimientos analíticos cuantitativos y documentarás un marco de análisis formal. Este entregable técnico y de negocio servirá como base directa para la generación de código y consultas avanzadas en los siguientes laboratorios.

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [x] Extraer entidades, llaves (primarias/foráneas) y relaciones clave a partir de la documentación de un modelo de datos operativos utilizando Microsoft 365 Copilot Chat.
- [x] Traducir una queja de negocio ambigua ("retrasos críticos en clientes de alta prioridad") en tres requerimientos analíticos estructurados utilizando el framework de cuatro componentes (Métrica, Dimensión, Filtro y Criterio de Aceptación).
- [x] Construir y estructurar un documento de contexto ejecutivo en Microsoft Word que relacione la pregunta de negocio con las restricciones del modelo físico y de calendario comercial.

## Prerrequisitos

Para completar con éxito este laboratorio, requieres:
1. **Conocimientos teóricos**: Comprensión de conceptos de bases de datos relacionales (llaves primarias, foráneas, tablas de hechos y dimensiones) y familiaridad con el ciclo de traducción de requerimientos analíticos de servicio.
2. **Licenciamiento y Acceso**: 
   - Cuenta activa con licencia de **Microsoft 365 Copilot Premium** (versión de escritorio o web con protección de datos comerciales).
   - Acceso a la suite de Microsoft 365 con Word y Teams habilitados.
3. **Archivos locales**: Haber creado el directorio de trabajo unificado `C:\CopilotLabs\` (o `~/CopilotLabs/` en macOS/Linux).

## Entorno de Laboratorio

Este laboratorio requiere el uso de las siguientes herramientas de software en sus versiones exactas o compatibles:

| Software / Herramienta | Versión Exacta (Recomendada) | Fuente Oficial |
| :--- | :--- | :--- |
| **Microsoft Windows** | Windows 11 Enterprise (Versión 23H2, x64) | [Evaluación Windows 11](https://www.microsoft.com/es-es/evalcenter/evaluate-windows-11-enterprise) |
| **Microsoft Word** | Word para Microsoft 365 (Versión 2408 - Build 17928.20156, Click-to-Run) | [Historial de Microsoft 365](https://learn.microsoft.com/es-es/officeupdates/update-history-microsoft365-apps-by-date) |
| **Microsoft 365 Copilot Premium** | Service Update 2408 | [Documentación de Copilot M365](https://learn.microsoft.com/es-es/copilot/microsoft-365/) |

### Configuración del Directorio de Trabajo

Ejecuta el siguiente comando en tu terminal (PowerShell en Windows o Terminal en macOS) para asegurar la existencia del directorio unificado:

```powershell
## En Windows (PowerShell)
New-Item -ItemType Directory -Force -Path "C:\CopilotLabs\"

## En macOS/Linux (Terminal)
mkdir -p ~/CopilotLabs/
```

*(Nota: En las instrucciones siguientes utilizaremos la ruta genérica de Windows `C:\CopilotLabs\`. Si estás en macOS o Linux, adáptala a `~/CopilotLabs/`).*

---

## Instrucciones Paso a Paso

### Paso 1: Preparar el archivo del modelo de datos locales de GlobalLogistics S.A.

**Objetivo**: Crear el documento de origen del modelo de datos en formato PDF dentro de la carpeta local de trabajo para simular la carga de archivos corporativos en un entorno seguro de Copilot.

1. Abre **Microsoft Word** en tu estación de trabajo.
2. Crea un nuevo documento en blanco y copia exactamente el siguiente contenido que define la estructura relacional del sistema logístico de **GlobalLogistics S.A.**:

```text
DOCUMENTO DE ESQUEMA TÉCNICO: SISTEMA DE ENTREGAS GLOBALLOGISTICS S.A.
Clasificación de Información: Uso Interno Confidencial
Versión del Esquema: v2.4

Este documento define las entidades físicas y lógicas que componen el módulo de control de distribución y SLA en la base de datos operativa de GlobalLogistics S.A.

1. TABLA DE HECHOS: Fact_Entregas
Esta tabla registra cada evento de entrega gestionado por el departamento logístico.
Campos:
- ID_Entrega (INT, Primary Key): Identificador único de la transacción de entrega.
- ID_Pedido (VARCHAR(20)): Código del pedido originado en el ERP.
- ID_Cliente (INT, Foreign Key): Referencia al cliente destino.
- ID_Transportista (INT, Foreign Key): Referencia a la empresa transportista.
- Fecha_Prometida (DATETIME): Fecha y hora límite acordadas con el cliente para la entrega.
- Fecha_Entrega_Real (DATETIME): Fecha y hora exacta en la que el cliente firmó la recepción física.
- Estado_Entrega (VARCHAR(15)): Estados posibles: ['Entregado', 'En Tránsito', 'Devuelto', 'Cancelado'].
- Minutos_Retraso (INT): Cálculo automático de minutos transcurridos después de la Fecha_Prometida (solo si Fecha_Entrega_Real > Fecha_Prometida).

2. TABLA DE DIMENSIÓN: Dim_Clientes
Registra los metadatos y niveles de servicio contratados por cada cliente.
Campos:
- ID_Cliente (INT, Primary Key): Código único del cliente.
- Nombre_Cliente (VARCHAR(100)): Razón social de la organización.
- Segmento_SLA (VARCHAR(15)): Clasificación del cliente. Valores permitidos: ['Platino', 'Oro', 'Estándar'].
- Ciudad_Entrega (VARCHAR(50)): Ciudad principal registrada para entregas.

3. TABLA DE DIMENSIÓN: Dim_Transportistas
Almacena el catálogo de proveedores de transporte tercerizados.
Campos:
- ID_Transportista (INT, Primary Key): Identificador del proveedor de transporte.
- Nombre_Transportista (VARCHAR(100)): Nombre comercial de la transportista.
- Tipo_Transporte (VARCHAR(20)): Métodos de transporte: ['Terrestre', 'Aéreo', 'Marítimo'].
```

3. Haz clic en **Archivo > Guardar como**.
4. Navega hasta la ruta `C:\CopilotLabs\`.
5. Nombra el archivo exactamente como `modelo_datos_logistica.pdf`. En la opción "Tipo", selecciona **PDF (*.pdf)** y presiona **Guardar**.
6. Cierra Microsoft Word.

**Resultado esperado**: Un archivo PDF válido y estructurado en la ruta física local `C:\CopilotLabs\modelo_datos_logistica.pdf`.

**Verificación**: Abre el explorador de archivos y valida la presencia de `C:\CopilotLabs\modelo_datos_logistica.pdf` con un peso superior a 0 KB.

---

### Paso 2: Interacción con Copilot Chat para extraer el esquema operativo

**Objetivo**: Utilizar las capacidades de visión y análisis de documentos de Microsoft 365 Copilot Chat para documentar y mapear de forma lógica el esquema de bases de datos.

1. Abre tu navegador web de preferencia y accede a la interfaz de **Microsoft 365 Copilot** (en `https://copilot.microsoft.com/` o mediante tu portal de Teams de la organización, asegurándote de usar la cuenta corporativa protegida).
2. Asegúrate de estar en una sesión con **Protección de Datos Comerciales** activa (verificarás un escudo de color verde o la etiqueta "Protegido" junto a tu perfil).
3. Localiza el botón de carga de archivos (ícono de clip o "+" en la caja de entrada del chat) y selecciona tu archivo local `C:\CopilotLabs\modelo_datos_logistica.pdf`.
4. Copia, adapta y ejecuta el siguiente prompt optimizado en la caja de texto para enviarlo a Copilot:

```text
Actúa como un arquitecto de bases de datos de operaciones y analista de datos senior de GlobalLogistics S.A. 

Analiza el archivo PDF adjunto 'modelo_datos_logistica.pdf' que contiene nuestro modelo lógico. Realiza las siguientes tareas de forma estructurada:
1. Identifica y extrae el listado completo de entidades (tablas) indicando si actúan como Tablas de Hechos o Tablas de Dimensión.
2. Detalla cada campo de las tablas incluyendo su tipo de dato, y especifica explícitamente cuáles son las Llaves Primarias (PK) y Llaves Foráneas (FK).
3. Mapea gráficamente con texto estructurado (formato Markdown) cómo se relacionan estas tablas entre sí (por ejemplo, indicando la relación de uno a muchos: 1:N).

Entrega la respuesta en un formato profesional, claro y listo para ser usado por un equipo de analistas que programará métricas en SQL y DAX.
```

5. Presiona **Enviar** y espera la generación completa de la respuesta del asistente.

**Resultado esperado**: Una respuesta estructurada en formato Markdown donde se identifique claramente que `Fact_Entregas` es la tabla de hechos central, con relaciones de muchos a uno (N:1) hacia las dimensiones `Dim_Clientes` (a través de `ID_Cliente`) y `Dim_Transportistas` (a través de `ID_Transportista`).

**Verificación**: Revisa visualmente que la respuesta entregada por Copilot mapee correctamente los campos `ID_Cliente` e `ID_Transportista` en la tabla de hechos como llaves foráneas (`FK`), vinculadas directamente a sus contrapartes de llave primaria (`PK`) en las tablas de dimensiones correspondientes.

---

### Paso 3: Traducir la queja de negocio en requerimientos analíticos cuantitativos

**Objetivo**: Utilizar técnicas de ingeniería de prompts para estructurar el problema ambiguo en requerimientos verificables basados en el esquema técnico validado.

1. En la misma ventana de conversación de Copilot Chat, vas a plantear el problema del negocio para que el asistente aplique el framework de traducción estructurada.
2. Copia y pega el siguiente prompt detallado en la barra de entrada del chat:

```text
Nuestra Gerencia de Operaciones tiene el siguiente problema urgente expresado por su Directora:
"Los clientes con cuentas prioritarias se están quejando constantemente en las últimas semanas de que las entregas están tardando demasiado y nadie les da una respuesta cuantitativa. Necesitamos aislar cuáles de nuestros transportistas clave están fallando en cumplir los acuerdos de nivel de servicio (SLA) para tomar decisiones contractuales inmediatas."

Basándote en el esquema del modelo de datos extraído en la conversación anterior de 'modelo_datos_logistica.pdf', traduce esta queja ambigua en exactamente tres (3) Requerimientos Analíticos Cuantitativos.

Para cada uno de los 3 requerimientos, debes estructurar la salida usando rigurosamente la siguiente plantilla de 4 componentes:
- Componente 1: Métrica Principal (Nombre técnico de la métrica y fórmula conceptual matemática).
- Componente 2: Dimensiones de Análisis (Atributos de qué tablas se usarán para segmentar o agrupar el reporte).
- Componente 3: Filtros y Alcance (Condiciones exactas para incluir o excluir registros, alineadas a las columnas reales de nuestras tablas).
- Componente 4: Criterio de Aceptación (El umbral operativo que define si el desempeño es aceptable o no para el negocio).

Además, considera las siguientes restricciones del negocio para incorporarlas en los requerimientos:
- Solo nos interesan las transacciones completadas de manera efectiva (excluir órdenes que no estén entregadas con éxito, como canceladas o devueltas).
- El nivel de cliente prioritario equivale a un segmento específico en nuestra dimensión de clientes (el segmento 'Platino').
- Define un criterio donde un retraso superior a 15 minutos en la entrega respecto a la Fecha_Prometida se considere una falla en el SLA.
```

3. Envía el prompt y analiza con detenimiento el desglose proporcionado por Copilot.

**Resultado esperado**: Tres requerimientos lógicamente separados. Por ejemplo:
1. *Porcentaje de Entregas a Tiempo de Clientes Platino* por transportista (Métrica: `% Entregas a Tiempo = (Conteo de entregas donde Minutos_Retraso <= 15) / (Conteo de entregas totales entregadas con éxito) * 100`).
2. *Minutos Promedio de Retraso de Clientes Platino* segmentado por Transportista y Ciudad.
3. *Volumen Total y Proporción de Entregas con Estado 'Cancelado' o 'Devuelto'* asociadas a clientes Platino por transportista (para validar si la cancelación es un síntoma de un retraso extremo).

**Verificación**: Asegúrate de que las métricas y filtros sugeridos usen exclusivamente las columnas declaradas en el modelo (`Segmento_SLA`, `Estado_Entrega`, `Minutos_Retraso`, `Nombre_Transportista`). Si la IA alucina agregando un campo inexistente, procede con la corrección interactiva.

---

### Paso 4: Diseñar y estructurar el entregable ejecutivo y técnico en Word

**Objetivo**: Generar un documento estructurado de contexto de caso que consolide los hallazgos técnicos del modelo y el marco de análisis operativo propuesto para su presentación con stakeholders de GlobalLogistics S.A.

1. Escribe en Copilot Chat la siguiente instrucción para consolidar la información en un formato apto para copiar a un informe oficial:

```text
Genera la versión final de un documento titulado "DOCUMENTO DE REQUERIMIENTOS ANALÍTICOS DE SLA - GLOBALLOGISTICS S.A.". 

El documento debe estar formateado rigurosamente en Markdown y debe contener:
1. Introducción Ejecutiva (Resumen del problema comercial reportado por la Directora de Operaciones).
2. Glosario Técnico del Modelo (Mapeo de Entidades, Llaves y Relaciones del modelo de datos obtenidos en el Paso 2).
3. Matriz de Requerimientos Analíticos (La especificación detallada de los 3 requerimientos del Paso 3 estructurados con sus 4 componentes).
4. Restricciones de Negocio Identificadas (Menciona las reglas de negocio críticas aplicadas como el filtro de 'Segmento_SLA' = 'Platino', la exclusión de entregas canceladas/devueltas, y el umbral de los 15 minutos).

Asegúrate de que la redacción sea formal, ejecutiva y completamente alineada con la realidad operativa de la empresa.
```

2. Una vez que Copilot termine de escribir el contenido, haz clic en el botón de "Copiar" (ubicado al final del mensaje de respuesta de Copilot).
3. Abre **Microsoft Word**.
4. Crea un nuevo documento en blanco y pega la respuesta copiada.
5. Guarda el archivo con el nombre `C:\CopilotLabs\Marco_Contexto_Logistica.docx`.

**Resultado esperado**: Un documento consolidado de aproximadamente 2 páginas que asocia los problemas del negocio real con la arquitectura física del modelo relacional de GlobalLogistics S.A.

**Verificación**: Abre el archivo `C:\CopilotLabs\Marco_Contexto_Logistica.docx` y confirma que contenga las 4 secciones solicitadas estructuradas correctamente con títulos, viñetas y tablas de datos limpias.

---

## Validación y Pruebas

Para garantizar la precisión de los requerimientos analíticos estructurados antes de continuar a la fase de construcción de código (DAX/SQL), se debe someter al entregable a un proceso de validación cruzada.

### Prueba de Consistencia con el Modelo (Humano en el Bucle)
Compara las métricas generadas por Copilot en el documento contra el modelo físico del PDF:
- ¿Todas las columnas referenciadas en las fórmulas matemáticas del *Paso 3* existen en el esquema definido en el *Paso 1*? 
  - *Ejemplo de validación*: Si la métrica propone calcular la tasa sobre el volumen de kilómetros de entrega, esta métrica debe rechazarse, ya que el campo "kilometraje" no existe en `modelo_datos_logistica.pdf`.

### Prueba Adversaria de AI (Simulación de Brecha de Información)
Con el propósito de evaluar la robustez y capacidad crítica de tu entorno de Copilot, ejecuta la siguiente prueba de inyección de dudas:

1. Introduce el siguiente prompt específico en tu chat actual:

```text
Analiza si es posible calcular la métrica "Tiempo de Despacho desde Almacén hasta Ruta de Transporte" usando exclusivamente los campos definidos en la tabla 'Fact_Entregas' del archivo 'modelo_datos_logistica.pdf'. 

Si es posible, define la fórmula conceptual. Si no es posible, argumenta de forma crítica qué datos o columnas hacen falta en nuestro modelo actual.
```

2. **Evaluación de la respuesta**:
   - **Comportamiento Esperado de Copilot**: El asistente debe indicar de forma clara que **no es posible** realizar este cálculo. Argumentará que la tabla `Fact_Entregas` solo cuenta con los campos de marcas temporales `Fecha_Prometida` y `Fecha_Entrega_Real`, careciendo de un campo que indique el momento de salida del almacén (por ejemplo, una columna `Fecha_Despacho`).
   - **Comportamiento Erróneo (Alucinación)**: Si Copilot asume falsamente que puedes calcularlo promediando o inventando la existencia de campos no definidos en el archivo PDF original, debes rechazar esa respuesta e instruir al chat: *"Ese campo no existe en nuestro modelo de datos físico. Vuelve a analizar basándote únicamente en los campos provistos en modelo_datos_logistica.pdf."*

---

## Solución de Problemas

Aquí se presentan dos de los fallos más comunes identificados al realizar este laboratorio, detallando sus causas y formas de resolución:

### Caso 1: Copilot no reconoce el archivo PDF adjunto o indica un error de lectura
* **Síntoma**: Al enviar el prompt en el Paso 2, Copilot responde: *"No puedo ver el archivo que adjuntaste"* o *"No tengo acceso a este documento en este momento"*.
* **Causa**: Limitación temporal del sandbox de carga de archivos en el cliente de Copilot o falta de permisos en el almacenamiento en la nube de OneDrive asociado a la cuenta corporativa del estudiante.
* **Solución**:
  1. Copia directamente todo el texto sin procesar (RAW) definido dentro de la estructura del Paso 1.
  2. Pégalo directamente al principio de tu prompt en Copilot envuelto en etiquetas XML del siguiente modo:
     ```text
     <modelo_datos>
     [Pega aquí el texto del modelo de datos]
     </modelo_datos>
     ```
  3. Ejecuta el prompt de análisis sustituyendo las referencias al "archivo PDF" por "los datos contenidos en la etiqueta <modelo_datos>".

### Caso 2: El formato de salida del documento en Word pierde la alineación o tablas en Markdown al pegarse
* **Síntoma**: El texto estructurado se pega en Microsoft Word como texto plano continuo o rompe la estructura de las tablas de los 4 componentes.
* **Causa**: El cliente de destino de Microsoft Word no interpretó correctamente el renderizado de HTML/Markdown del portapapeles del sistema operativo.
* **Solución**:
  1. En Word, ve a la opción de **Pegar Especial** (o presiona `Ctrl + Alt + V`).
  2. Selecciona la opción **Texto con formato (RTF)** o **Página Web (HTML)**.
  3. Alternativamente, puedes usar el botón de "Exportar a Word" si está disponible de forma nativa en tu interfaz de Microsoft 365 Copilot para guardar el archivo directamente en tu OneDrive sincronizado con tu equipo.

---

## Limpieza

Al finalizar tus tareas, es vital mantener limpio y seguro el entorno de trabajo:

1. Cierra todas las pestañas activas del navegador web donde interactuaste con el chat de Copilot.
2. Abre la carpeta `C:\CopilotLabs\` y asegúrate de mantener guardados únicamente los siguientes archivos necesarios para las siguientes prácticas de la serie:
   - `modelo_datos_logistica.pdf` (Esquema físico)
   - `Marco_Contexto_Logistica.docx` (Documento de requerimientos analíticos)
3. Elimina del directorio cualquier archivo de borrador temporal (como archivos con extensión `.tmp` o copias duplicadas de prueba).

---

## Resumen

En este laboratorio has completado con éxito la fase más crucial de un ciclo analítico de negocio: **la traducción de requerimientos**. 

A partir de un esquema de base de datos relacional (PDF) de **GlobalLogistics S.A.**, lograste interpretar de forma automatizada las tablas y sus relaciones con Copilot Chat. Posteriormente, guiaste a la inteligencia artificial mediante técnicas estructuradas de prompting para convertir una queja operativa ambigua en requerimientos analíticos precisos sustentados en métricas, dimensiones, filtros lógicos reales y criterios de aceptación específicos de SLA. 

Finalmente, consolidaste estos requerimientos en un entregable ejecutivo estructurado en Word (`Marco_Contexto_Logistica.docx`). Este documento será el mapa de ruta definitivo que te permitirá en los siguientes laboratorios generar, validar y ejecutar código SQL de extracción y medidas DAX sin el riesgo de caer en alucinaciones o interpretaciones erróneas.

### Recursos Adicionales recomendados para profundizar:
- [Guía de Microsoft: Mapeo de datos relacionales](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-modeling-view)
- [Mejores prácticas para la estructuración de prompts ejecutivos con Copilot](https://learn.microsoft.com/es-es/copilot/microsoft-365/microsoft-365-copilot-prompts)
