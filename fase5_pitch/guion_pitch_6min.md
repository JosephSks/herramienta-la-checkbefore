# Fase 5: Guion Técnico de Presentacion (Pitch de 6 Minutos) - CheckBefore

## Informacion General
* **Herramienta:** CheckBefore (Sistema de Soporte a Decisiones Pedagogicas basado en el Indice de Preparacion Previa).
* **Institucion:** Universidad de Cuenca - Facultad de Ingenieria.
* **Asignatura:** Ciencia de Datos - Unidad 2 (Learning Analytics).
* **Equipo expositor:** Xavier Pena y Joseph Salamea.
* **Tiempo asignado:** Exactamente 6 minutos (evaluado con penalizacion por exceso o defecto).
* **Modalidad de evaluacion:** Evaluacion docente (70%), coevaluacion entre pares (15%) y autoevaluacion (15%).

---

## Estructura Fija del Pitch (Cronograma Segundo a Segundo)

| Segmento | Tiempo | Bloque tematico | Orador principal | Diapositiva / Apoyo visual |
|---|---|---|---|---|
| Bloque 1 | 0:00 - 1:00 (1 min) | El problema educativo y la decision a ciegas | Xavier Pena | Diapositiva 1 y 2 |
| Bloque 2 | 1:00 - 2:00 (1 min) | Fuentes de datos, variables e indicador IPP | Joseph Salamea | Diapositiva 3 y 4 |
| Bloque 3 | 2:00 - 4:00 (2 min) | Demostracion del prototipo (Wireframes + Notebook) | Xavier Pena (UI) y Joseph Salamea (ML) | Diapositiva 5 + Demostracion en vivo |
| Bloque 4 | 4:00 - 6:00 (2 min) | Plan de validacion (3 niveles) y 5 preguntas de estadistica | Joseph Salamea | Diapositiva 6, 7 y 8 |

---

## Guion Detallado de Intervencion

### Bloque 1: Problema Educativo y Decision a Ciegas (0:00 a 1:00)
**Orador: Xavier Pena**
**Visual:** Diapositiva 1 (Titulo y equipo) -> Diapositiva 2 (Problema y Decision).

*(0:00 - 0:15) Apertura y definicion del problema:*
> "Buenos dias profesor y companeros. Hoy presentamos CheckBefore, una herramienta de Learning Analytics concebida para la plataforma Moodle de la Universidad de Cuenca.  
> En nuestros primeros ciclos universitarios enfrentamos una realidad constante: los estudiantes no reprueban las tareas complejas por falta de capacidad o por olvido de la fecha, sino por procrastinacion critica. Los registros demuestran que abren las actividades entre tres y cinco horas antes del cierre, sin haber revisado previamente las guias ni los ejercicios base."

*(0:15 - 0:40) La decision que hoy se toma a ciegas:*
> "Actualmente, ¿que decision toma el docente frente a esto? Toma una decision a ciegas: no sabe a quienes brindar orientacion oportuna o convocar a una tutoria antes de la fecha limite. Solo se entera de quienes tuvieron dificultades cuando el plazo vence y el calificador se llena de tareas en blanco o con notas deficientes. En ese punto, ya no existe oportunidad de intervencion pedagogica."

*(0:40 - 1:00) Pregunta analitica y propuesta de valor:*
> "CheckBefore responde formalmente a esta pregunta analitica:  
> *'¿En que medida el nivel de consulta previa de los recursos de estudio permite predecir el riesgo de no entrega o entrega tardia de una tarea evaluable 48 horas antes de su fecha limite?'*  
> No somos una alarma de calendario que dice 'tu tarea vence manana'; somos un sistema de soporte analitico que evalua preparacion formativa."

---

### Bloque 2: Datos, Variables e Indicador IPP (1:00 a 2:00)
**Orador: Joseph Salamea**
**Visual:** Diapositiva 3 (Arquitectura de datos Moodle) -> Diapositiva 4 (Formulacion del IPP y Semaforo).

*(1:00 - 1:25) Fuentes institucionales y etica:*
> "Para responder a esta pregunta, CheckBefore extrae datos que ya existen en la infraestructura de la universidad: los logs estandares de Moodle (`mdl_logstore_standard_log`) y el modulo de tareas (`mdl_assign`).  
> Bajo un diseno etico riguroso, los datos se anonimizan mediante identificadores hash SHA-256. No usamos datos socioeconomicos ni penalizamos el tiempo de pantalla para proteger a quienes estudian sin conexion; un evento de descarga se valida como consulta efectiva."

*(1:25 - 1:45) El indicador central: Indice de Preparacion Previa (IPP):*
> "Nuestro indicador central es el IPP: el porcentaje de recursos clave asignados por el docente que el estudiante ha consultado al menos una vez antes de la ventana critica de 48 horas:  
> $$IPP = \left( \frac{\text{Recursos formativos clave consultados}}{\text{Total de recursos clave asignados}} \right) \times 100$$"

*(1:45 - 2:00) Semaforo operacional:*
> "A partir del IPP, el sistema clasifica al paralelo en un semaforo operacional de tres estados:  
> Verde si el IPP es mayor o igual al 70% o si ya entrego la tarea;  
> Amarillo si la preparacion es parcial, entre el 40% y 70%; y  
> Rojo si el IPP es menor al 40% sin entrega registrada. Este grupo rojo es el que demanda atencion inmediata."

---

### Bloque 3: Demostracion del Prototipo y Prueba de Concepto (2:00 a 4:00)
**Oradores: Xavier Pena (Wireframes) y Joseph Salamea (Notebook y Modelo)**
**Visual:** Diapositiva 5 (Wireframes SVG) -> Pestaña de Navegador con Grafico Plotly / Notebook.

*(2:00 - 2:40) Demostracion de interfaz y accion pedagogica (Xavier Pena):*
> "El diseno de la herramienta contempla tres interfaces integradas en Moodle:  
> En la Pantalla 1, el docente accede al panel general de su paralelo donde visualiza el estado de las tareas activas y la distribucion porcentual del semaforo a 48 horas del cierre.  
> En la Pantalla 2, el estudiante visualiza su widget de autorregulacion con su barra de IPP y la ruta secuencial de lecturas pendientes.  
> Y en la Pantalla 3, se materializa la intervencion: con un solo clic sobre el grupo rojo, el docente abre una plantilla prediseñada para enviar un mensaje de orientacion pedagogica o convocar a una tutoria voluntaria, quedando registro formal de la accion."

*(2:40 - 3:30) Prueba de concepto computacional con datos (Joseph Salamea):*
> "Para demostrar la viabilidad computacional, implementamos un notebook reproducible en Python mapeando la estructura al dataset publico OULAD, con una muestra de 500 estudiantes y 4 recursos formativos clave.  
> El calculo del IPP permitio generar una visualizacion interactiva en Plotly que replica exactamente la vista semaforica del docente.  
> Adicionalmente, entrenamos un modelo base de Regresion Logistica y un Arbol de Decision para predecir la entrega puntual (`on_time_delivery`) a partir del IPP y los dias de anticipacion.  
> El modelo alcanzo una precision del 81% y un area bajo la curva ROC (AUC) de 0.84, confirmando que la interaccion temprana con materiales es un predictor estadistico potente del cumplimiento de entregas."

*(3:30 - 4:00) Lo que el prototipo no puede decir todavia (Joseph Salamea):*
> "Sin embargo, reconocemos los limites del prototipo: no puede medir la calidad cognitiva de la lectura, no captura el estudio colaborativo presencial fuera del aula virtual y depende de que el docente configure adecuadamente cuales recursos son prioritarios."

---

### Bloque 4: Plan de Validacion y Preguntas Abiertas de Estadistica (4:00 a 6:00)
**Orador: Joseph Salamea**
**Visual:** Diapositiva 6 (Matriz de 3 niveles) -> Diapositiva 7 (Las 5 preguntas de la Unidad 3) -> Diapositiva 8 (Conclusiones y cierre).

*(4:00 - 4:45) Diseno del plan de validacion en tres niveles:*
> "Para constatar que CheckBefore realmente produce un cambio positivo, disenamos un plan de validacion en tres dimensiones complementarias:  
> Primero, Validacion Tecnica: medir la estabilidad del algoritmo evaluando sensibilidad (recall) en la deteccion del grupo en riesgo y calibrando los umbrales del 40% y 70%.  
> Segundo, Validacion Pedagogica: un diseno cuasiexperimental de dos cohortes paralelas con el mismo profesor (grupo experimental con CheckBefore y grupo de control sin la herramienta) para probar si la tasa de tareas no entregadas se reduce en al menos un 20%.  
> Y tercero, Validacion de Usabilidad: aplicacion del cuestionario estandarizado System Usability Scale (SUS) a los docentes, exigiendo un puntaje superior a 70 puntos para certificar su adopcion."

*(4:45 - 5:40) Cierre metodologico: Las 5 preguntas abiertas para la Unidad 3:*
> "Al disenar este plan, identificamos cinco interrogantes analiticas criticas que aun no podemos responder y que abren el estudio de la Unidad 3 de Estadistica:  
> 1. ¿Como determinar si una diferencia de 15% en las entregas entre el grupo experimental y control es estadisticamente significativa o atribuible al azar?  
> 2. Al ser la entrega a tiempo una variable binaria (si/no), ¿que prueba formal de contraste debemos aplicar: Chi-cuadrado, Fisher o un modelo de regresion logistica multivariado?  
> 3. ¿Cual es el tamano de muestra minimo de estudiantes y tareas para garantizar una potencia estadistica del 80% y evitar un error tipo II?  
> 4. ¿Como controlar estadisticamente variables confusoras basales, como el rendimiento academico historico o la motivacion inicial, mediante tecnicas como ANCOVA o emparejamiento por propension?  
> 5. Y finalmente: ¿Que analisis psicometrico o factorial certifica que el IPP posee validez de constructo sobre el aprendizaje y no es solo un artefacto de navegacion?"

*(5:40 - 6:00) Cierre formal:*
> "En conclusion, CheckBefore transforma registros inertes de Moodle en decisiones pedagogicas preventivas, cerrando la brecha entre la analitica descriptiva y la accion docente oportuna. Muchas gracias, quedamos atentos a sus preguntas y comentarios."

---

## Banco de Preguntas y Respuestas para la Evaluacion (Defensa)

Este banco prepara al equipo para responder a las preguntas tipicas del profesor o de los companeros evaluadores durante los 2 a 3 minutos de retroalimentacion:

### Pregunta 1: ¿Por que eligieron la ventana de 48 horas y no 24 o 72 horas?
* **Respuesta tecnica:**  
  "Una ventana de 72 horas es demasiado prematura porque muchos estudiantes planifican su trabajo en los dias intermedios de la semana, lo que generaria falsas alarmas (falsos positivos de riesgo). Por otro lado, una ventana de 24 horas no deja margen de maniobra pedagogica: el docente no alcanzaria a responder dudas ni el estudiante a asimilar lecturas extensas. Cuarenta y ocho horas representa el equilibrio optimo entre pertinencia predictiva y tiempo de reaccion formativa."

### Pregunta 2: ¿Que pasa si un estudiante descarga el PDF y lo lee desconectado toda la semana?
* **Respuesta tecnica:**  
  "Ese es exactamente el sesgo de conectividad que contemplamos en el Bloque 6 del Canvas. CheckBefore registra el evento `downloaded` como interaccion valida equivalente a `viewed`. Con una sola descarga registrada en los logs de Moodle, el recurso se computa como consultado dentro del IPP. No exigimos tiempo de permanencia conectado en el aula virtual."

### Pregunta 3: ¿En que se diferencia CheckBefore de las notificaciones que Moodle ya tiene?
* **Respuesta tecnica:**  
  "Moodle solo tiene recordatorios de calendario condicionados a la fecha: avisa que una tarea vence pronto independientemente de quien sea el estudiante. CheckBefore es un sistema analitico condicional: cruza el estado de entrega con el consumo previo de materiales de aprendizaje para habilitar una decision docente (intervencion diferenciada al grupo rojo) y autorregulacion estudiantil. Si no hay decision habilitada, no es Learning Analytics."

### Pregunta 4: ¿Cual fue el desempeno del modelo en el notebook y que significa para el profesor?
* **Respuesta tecnica:**  
  "El modelo de Regresion Logistica obtuvo un ROC-AUC de 0.84 y un Recall del 83% en la clase de entrega puntual. Para el docente, un Recall alto significa que el sistema casi no pasa por alto a los estudiantes que van a cumplir, permitiendo focalizar su atencion y esfuerzo docente exclusivamente en el grupo con bajo IPP que verdaderamente corre riesgo de reprobacion."
