# Fase 2: Diccionario de Datos y Consideraciones Eticas - CheckBefore

## 1. Introduccion

Este documento especifica de manera formal las variables, fuentes de datos, transformaciones e indicadores que la herramienta CheckBefore requiere para operar dentro del entorno Moodle de la Universidad de Cuenca. Adicionalmente, se desarrolla el marco etico y de privacidad que rige el tratamiento de la informacion, incluyendo las estrategias de anonimizacion, control de acceso y mitigacion de sesgos algoritmicos.

Las variables documentadas a continuacion se derivan directamente de las tablas internas de Moodle y del modulo de actividades (`mod_assign`, `mod_resource`, `mod_url`, `mod_page`) y del registro estandar de eventos (`logstore_standard_log`). Para la prueba de concepto (Fase 4), estas variables se mapean a las tablas equivalentes del dataset publico OULAD (Open University Learning Analytics Dataset).


## 2. Diccionario de Datos

### 2.1 Variables de origen (datos crudos extraidos de Moodle)

| ID | Variable | Descripcion | Tabla de origen en Moodle | Tipo de dato | Ejemplo | Granularidad | Frecuencia de actualizacion |
|----|----------|-------------|---------------------------|--------------|---------|--------------|----------------------------|
| V01 | `student_id` | Identificador unico anonimizado del estudiante matriculado en el curso. Se genera mediante un hash irreversible del ID real de Moodle para proteger la identidad. | `mdl_user` (campo `id`, transformado) | Entero (hash) | 48291 | Por estudiante | Al inicio del ciclo academico |
| V02 | `course_id` | Identificador del curso o asignatura en el que se encuentra matriculado el estudiante. | `mdl_course` (campo `id`) | Entero | 305 | Por curso | Al inicio del ciclo |
| V03 | `assignment_id` | Identificador unico de la tarea o actividad evaluable creada por el docente. | `mdl_assign` (campo `id`) | Entero | 1024 | Por tarea | Al crear la tarea |
| V04 | `assignment_name` | Nombre descriptivo de la tarea asignado por el docente. | `mdl_assign` (campo `name`) | Texto | "Tarea 2: Ejercicios de Algebra Lineal" | Por tarea | Al crear la tarea |
| V05 | `due_date` | Marca temporal (timestamp) del cierre programado de la tarea, definida por el docente al configurar la actividad. | `mdl_assign` (campo `duedate`) | Timestamp UNIX | 1730073600 | Por tarea | Al crear o modificar la tarea |
| V06 | `resource_id` | Identificador unico del recurso formativo (PDF, video, enlace, pagina) que el docente ha vinculado como material preparatorio del curso. | `mdl_resource` / `mdl_url` / `mdl_page` (campo `id`) | Entero | 2087 | Por recurso | Al subir el recurso |
| V07 | `resource_name` | Nombre descriptivo del recurso de estudio. | `mdl_resource` / `mdl_url` / `mdl_page` (campo `name`) | Texto | "Guia de Ejercicios Resueltos - Tema 3" | Por recurso | Al subir el recurso |
| V08 | `is_key_resource` | Indicador binario que senala si el docente ha marcado el recurso como prerequisito o material clave para la realizacion de una tarea especifica. Se configura mediante la funcionalidad de restricciones de acceso o completion tracking de Moodle. | `mdl_course_modules_completion` / Configuracion docente | Booleano (0/1) | 1 | Por recurso-tarea | Al configurar la tarea |
| V09 | `event_timestamp` | Marca temporal del momento exacto en que el estudiante interactuo con un recurso (visualizacion, descarga o clic). | `mdl_logstore_standard_log` (campo `timecreated`) | Timestamp UNIX | 1729987200 | Por evento | Tiempo real |
| V10 | `event_action` | Tipo de accion registrada en el log de Moodle: `viewed` (visualizo el recurso), `downloaded` (descargo el archivo) u otras acciones validas. | `mdl_logstore_standard_log` (campo `action`) | Texto categorico | "viewed" | Por evento | Tiempo real |
| V11 | `submission_status` | Estado actual de la entrega de la tarea por parte del estudiante: `submitted` (enviada), `draft` (borrador guardado) o `new` (sin entrega). | `mdl_assign_submission` (campo `status`) | Texto categorico | "submitted" | Por estudiante-tarea | Al enviar o modificar |
| V12 | `submission_timestamp` | Marca temporal del momento en que el estudiante envio formalmente la tarea. Es nulo si no ha realizado la entrega. | `mdl_assign_submission` (campo `timemodified`) | Timestamp UNIX / Nulo | 1730060000 | Por estudiante-tarea | Al enviar |
| V13 | `grade` | Calificacion numerica asignada por el docente a la tarea entregada. Es nulo si la tarea no fue calificada o no fue entregada. | `mdl_assign_grades` (campo `grade`) | Decimal / Nulo | 7.5 | Por estudiante-tarea | Al calificar |

### 2.2 Variables derivadas (indicadores calculados por CheckBefore)

| ID | Variable derivada | Formula de calculo | Variables de entrada | Tipo | Rango | Interpretacion |
|----|-------------------|-------------------|----------------------|------|-------|----------------|
| D01 | `total_key_resources` | Conteo de recursos marcados como clave para una tarea especifica. | V06, V08 | Entero | >= 0 | Cantidad total de materiales que el estudiante deberia consultar antes de la tarea. |
| D02 | `accessed_key_resources` | Conteo de recursos clave que el estudiante ha consultado (al menos un evento `viewed` o `downloaded`) antes de la ventana de alerta (48h previas al cierre). | V01, V06, V08, V09, V10, V05 | Entero | 0 a D01 | Cantidad de materiales efectivamente revisados. |
| D03 | `IPP` (Indice de Preparacion Previa) | `(D02 / D01) * 100`. Si D01 = 0 (no hay recursos clave asignados), el IPP se define como "No aplica" y el estudiante no recibe alerta. | D01, D02 | Decimal | 0.0 a 100.0 | Porcentaje de preparacion del estudiante. Cuanto mayor el IPP, mayor la probabilidad de que el estudiante este listo para la tarea. |
| D04 | `risk_level` (Semaforo de Riesgo) | Clasificacion basada en umbrales del IPP a 48 horas del cierre: Verde si IPP >= 70 o tarea ya entregada; Amarillo si 40 <= IPP < 70 sin entrega; Rojo si IPP < 40 sin entrega. | D03, V11, V05 | Categorico ordinal | {Verde, Amarillo, Rojo} | Nivel de urgencia de la intervencion docente. Rojo requiere accion inmediata. |
| D05 | `hours_before_deadline` | `(V05 - V12) / 3600`. Diferencia en horas entre el cierre de la tarea y el momento de la entrega. Es nulo si no hubo entrega. | V05, V12 | Decimal / Nulo | Negativo (entrega tardia) a positivo (entrega anticipada) | Cuanto tiempo antes (o despues) de la fecha limite envio la tarea el estudiante. |
| D06 | `last_access_gap` | Diferencia en horas entre el ultimo evento registrado sobre recursos clave y la fecha de cierre de la tarea. | V05, V09 | Decimal | >= 0 | Indica que tan reciente fue la ultima revision del estudiante. Un valor alto sugiere que el alumno no ha retomado la materia recientemente. |
| D07 | `on_time_delivery` | Variable binaria: 1 si el estudiante envio la tarea antes o en la fecha limite (V12 <= V05), 0 si no envio o envio tarde. | V05, V11, V12 | Binario (0/1) | {0, 1} | Variable dependiente principal para la validacion predictiva del modelo en la Fase 4. |


### 2.3 Mapeo al dataset OULAD (Prueba de Concepto - Fase 4)

Para la prueba de concepto se utilizara el dataset Open University Learning Analytics Dataset (OULAD), que contiene datos anonimizados de cursos de la Open University del Reino Unido. Las tablas de OULAD no replican exactamente la estructura de Moodle, pero permiten simular el calculo del IPP con equivalencias razonables.

| Variable CheckBefore | Tabla OULAD | Campo OULAD | Observaciones |
|----------------------|-------------|-------------|---------------|
| `student_id` (V01) | `studentInfo` | `id_student` | Ya viene anonimizado en el dataset. |
| `course_id` (V02) | `courses` | `code_module` + `code_presentation` | La combinacion de modulo y presentacion equivale al curso-periodo. |
| `assignment_id` (V03) | `assessments` | `id_assessment` | Cada evaluacion tiene un ID unico. |
| `due_date` (V05) | `assessments` | `date` | Expresado en dias relativos al inicio del curso (no en timestamp absoluto). |
| `resource_id` (V06) | `vle` | `id_site` | Cada recurso del entorno virtual tiene un ID de sitio. |
| `event_timestamp` (V09) | `studentVle` | `date` | Dia relativo al inicio del curso en que ocurrio la interaccion. |
| `event_action` (V10) | `vle` | `activity_type` | Tipo de recurso consultado (oucontent, resource, url, forumng, etc.). |
| `submission_timestamp` (V12) | `studentAssessment` | `date_submitted` | Dia relativo al inicio del curso en que se envio la evaluacion. |
| `grade` (V13) | `studentAssessment` | `score` | Calificacion normalizada de 0 a 100. |
| `is_key_resource` (V08) | No disponible directamente | Se simulara filtrando los tipos de actividad `resource`, `oucontent` y `url` como recursos de estudio, asumiendo que todos los recursos del modulo son relevantes para las evaluaciones. | Limitacion: OULAD no distingue cuales recursos son prerequisito de cada tarea. |


## 3. Flujo de Procesamiento de Datos

El calculo del Indice de Preparacion Previa (IPP) sigue la siguiente secuencia logica:

```
1. EXTRACCION
   Consultar los logs de Moodle (mdl_logstore_standard_log) para obtener
   todos los eventos de tipo "viewed" y "downloaded" sobre recursos del curso,
   filtrados por el student_id y el course_id correspondiente.

2. FILTRADO TEMPORAL
   Para cada tarea activa (assignment_id), calcular la ventana de alerta:
   ventana_inicio = due_date - 48 horas
   Retener unicamente los eventos de recursos clave ocurridos ANTES de
   ventana_inicio (es decir, la preparacion previa, no la de ultima hora).

3. CONTEO Y CALCULO
   Para cada par (student_id, assignment_id):
     a) Contar el total de recursos clave asignados (D01).
     b) Contar cuantos de esos recursos registro al menos un evento
        de visualizacion o descarga (D02).
     c) Calcular IPP = (D02 / D01) * 100.

4. CLASIFICACION
   Asignar el semaforo de riesgo (D04) segun los umbrales definidos:
     - Verde: IPP >= 70 o submission_status = "submitted"
     - Amarillo: 40 <= IPP < 70 y submission_status != "submitted"
     - Rojo: IPP < 40 y submission_status != "submitted"

5. PRESENTACION
   Generar la vista de panel docente con la lista de estudiantes
   ordenada por nivel de riesgo (Rojo primero) y la vista individual
   del estudiante con su barra de progreso y recursos pendientes.
```


## 4. Consideraciones Eticas y de Privacidad

### 4.1 Principios rectores

El diseno etico de CheckBefore se enmarca en los principios de la declaracion de Helsinki sobre investigacion con sujetos humanos y en las directrices del proyecto LALA para herramientas de Learning Analytics en Latinoamerica. Se adoptan los siguientes compromisos:

- **Beneficencia:** La herramienta existe para mejorar las oportunidades de aprendizaje del estudiante, no para generar mecanismos de control o sancion academica.
- **No maleficencia:** Los indicadores no deben producir consecuencias negativas directas sobre la calificacion sumativa del alumno.
- **Autonomia:** El estudiante conserva la decision final sobre su ritmo y estrategia de estudio; la alerta es una sugerencia, no una obligacion.
- **Justicia:** Los criterios de clasificacion deben ser transparentes y aplicarse de manera uniforme a todos los estudiantes del paralelo.

### 4.2 Anonimizacion y proteccion de identidad

| Aspecto | Estrategia implementada |
|---------|------------------------|
| Identificacion del estudiante | El `student_id` utilizado internamente por CheckBefore se genera mediante una funcion hash irreversible (SHA-256) aplicada al ID real de Moodle. El sistema nunca almacena nombres, cedulas ni correos electronicos en sus tablas de calculo. |
| Datos mostrados al docente | El panel del docente muestra los nombres completos de los estudiantes (ya disponibles en el calificador de Moodle), pero **no** genera archivos exportables ni permite descargas masivas de datos individuales fuera de la plataforma. |
| Datos mostrados al estudiante | El estudiante solo accede a sus propios indicadores. No puede ver el IPP, el semaforo ni los habitos de consulta de sus companeros bajo ninguna circunstancia. |
| Almacenamiento | Los indicadores calculados se almacenan temporalmente en la base de datos del plugin de Moodle y se eliminan automaticamente al finalizar el periodo academico, respetando la politica institucional de retencion de datos de Moodle. |

### 4.3 Matriz de permisos de acceso

| Rol en Moodle | Datos a los que accede | Datos que NO puede ver |
|---------------|----------------------|----------------------|
| Estudiante | Su propio IPP, su semaforo de riesgo, la lista de recursos pendientes de consulta para cada tarea activa. | Indicadores de cualquier otro estudiante, estadisticas agregadas del curso, modelo predictivo subyacente. |
| Docente titular | Panel de semaforo del curso completo con todos los estudiantes de su paralelo, IPP individual de cada alumno, opcion de enviar mensajes de orientacion. | Datos de estudiantes de otros paralelos o cursos, datos demograficos o socioeconomicos no disponibles en Moodle. |
| Coordinador / Director de carrera | Estadisticas agregadas por paralelo (porcentaje de estudiantes en rojo/amarillo/verde), sin identificacion individual. | Datos individuales de estudiantes ni sus nombres. |
| Administrador de Moodle | Acceso tecnico a las tablas del plugin para mantenimiento y depuracion, sin interfaz de consulta pedagogica. | No tiene una vista de panel pedagogico; su acceso es exclusivamente tecnico. |

### 4.4 Consentimiento informado

Al inicio de cada periodo academico, al ingresar por primera vez a un curso que tenga CheckBefore habilitado, el estudiante visualizara un aviso institucional que explique:

1. Que datos de navegacion se analizan (exclusivamente interacciones con recursos del aula virtual del curso).
2. Con que finalidad se analizan (apoyo formativo, no evaluacion sumativa).
3. Que el docente podra ver su nivel de preparacion de forma agregada.
4. Que puede solicitar al docente la desactivacion de las alertas personales si lo considera pertinente, sin consecuencia academica alguna.

Este aviso seguira el formato de los acuerdos institucionales de uso de Moodle vigentes en la Universidad de Cuenca.

### 4.5 Identificacion y mitigacion de sesgos

| Sesgo potencial | Descripcion del riesgo | Estrategia de mitigacion |
|----------------|----------------------|-------------------------|
| Sesgo de conectividad | Estudiantes con acceso limitado a internet pueden descargar los recursos una sola vez para estudiar sin conexion, registrando menos eventos en los logs que companeros con conexion permanente, lo que subestimaria su IPP real. | El sistema contabiliza el evento de descarga (`downloaded`) como interaccion valida equivalente a la visualizacion. Una sola descarga del recurso es suficiente para considerarlo como consultado. |
| Sesgo de navegacion pasiva | Un estudiante podria abrir un recurso brevemente sin leerlo realmente, inflando artificialmente su IPP. | Se reconoce esta limitacion. En la version actual, CheckBefore no mide la profundidad de la lectura, solo la interaccion registrada. Se propone como mejora futura incorporar el tiempo minimo de permanencia en la pagina como filtro complementario. |
| Efecto de estigmatizacion | Si el docente asocia sistematicamente el color rojo con "mal estudiante", podria generar un sesgo de trato diferenciado o profecia autocumplida. | La interfaz docente incluye una nota permanente que recuerda: "El semaforo refleja la consulta de recursos, no la capacidad del estudiante. Un IPP bajo puede deberse a multiples factores contextuales." |
| Sesgo de horario | Estudiantes que trabajan durante el dia y estudian de noche o en fines de semana podrian no haber consultado los recursos al momento de la revision de 48 horas, aunque planeen hacerlo esa misma noche. | La ventana de 48 horas se calcula como un margen razonable que permite multiples ciclos de estudio. No se generan alertas con menos de 24 horas de anticipacion para evitar falsos positivos en estudiantes nocturnos. |
| Sesgo de formato de recurso | Si el docente sube materiales en formatos que no generan evento de log (por ejemplo, enlaces externos que redirigen fuera de Moodle), esos recursos no se contabilizarian correctamente. | La documentacion de configuracion del plugin instruye al docente a utilizar exclusivamente los modulos nativos de Moodle (Archivo, URL, Pagina) para garantizar el registro de interacciones. |


## 5. Limitaciones conocidas del modelo de datos

1. **Granularidad temporal en OULAD:** El dataset de prueba registra las interacciones en dias relativos (no en horas), lo que impide simular la ventana de 48 horas con precision horaria. En la prueba de concepto se utilizara un umbral de 2 dias como aproximacion.

2. **Ausencia de prerequisitos explicitos:** Ni Moodle ni OULAD registran de forma nativa que recursos son prerequisito de que tarea. En produccion, esto dependeria de que el docente configure las restricciones de acceso o de que el plugin permita vincular recursos a tareas manualmente.

3. **Interacciones fuera de la plataforma:** Cualquier estudio realizado con materiales fisicos (libros, apuntes de clase, fotocopias) o comunicacion por WhatsApp/correo electronico no queda registrado en los logs del aula virtual y, por lo tanto, escapa al alcance de CheckBefore.

4. **Validez del umbral del semaforo:** Los umbrales actuales (70%, 40%) se han definido por criterio experto basandose en la literatura revisada en la Fase 0. Su calibracion empirica requiere la validacion estadistica que se abordara en la Fase 6 y en la Unidad 3 del curso.
