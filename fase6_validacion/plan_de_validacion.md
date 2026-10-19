# Fase 6: Plan de Validacion - CheckBefore

**Herramienta:** CheckBefore  
**Institucion:** Universidad de Cuenca - Facultad de Ingenieria  
**Integrantes:** Xavier Pena y Joseph Salamea  
**Nota importante:** Este documento describe el *diseno* del plan de validacion. El experimento no se ejecuta en esta etapa; se formula el protocolo metodologico que se aplicaria en un piloto institucional real.

---

## 1. Hipotesis Central

> *"La implementacion de intervenciones docentes basadas en el Indice de Preparacion Previa (IPP) reduce en al menos un 20% la tasa de tareas no entregadas y disminuye la concentracion de envios realizados durante la ultima hora previa al cierre del plazo."*

**Variable independiente:** Disponibilidad de la herramienta CheckBefore (habilitada vs. no habilitada).  
**Variable dependiente:** Tasa de tareas no entregadas (`on_time_delivery`, variable binaria 0/1).  
**Variable de control:** Rendimiento academico historico del estudiante antes del inicio del ciclo.

---

## 2. Diseno del Estudio

Estudio **cuasiexperimental** con dos paralelos de la misma asignatura impartida por el mismo docente en el mismo periodo academico:

| Grupo | Condicion | Descripcion |
|---|---|---|
| Grupo A (Experimental) | Con CheckBefore activo | El docente accede al panel de semaforo y puede enviar mensajes de orientacion al grupo rojo a 48 horas del cierre. Los estudiantes ven su barra de IPP y recursos sugeridos. |
| Grupo B (Control) | Moodle estandar | El docente y los estudiantes usan la plataforma sin ninguna modificacion adicional. No hay alertas ni indicadores de preparacion. |

**Duracion:** Un ciclo academico completo (al menos 4 tareas evaluables con recursos formativos asociados).  
**Asignacion:** No aleatoria (cuasiexperimental); se asegura equivalencia inicial entre grupos mediante la variable de control de rendimiento previo.

---

## 3. Niveles de Validacion

### 3.1 Validacion Tecnica

Evalua si el algoritmo de CheckBefore calcula el IPP de forma estable, precisa y util como predictor del incumplimiento.

| Metrica | Descripcion | Umbral aceptable y justificacion |
|---|---|---|
| **Sensibilidad (Recall)** | Proporcion de estudiantes que efectivamente no entregaron y que el sistema clasifico como "Rojo" con anticipacion. | **>= 80%.** *Por que:* Es preferible tener un falso positivo y guiar a alguien que quiza no lo necesitaba, a omitir a un estudiante en riesgo real de reprobar. |
| **Precision** | Proporcion de alertas "Rojo" que correspondieron a no entregas reales. | **>= 75%.** *Por que:* Evita que el docente reciba demasiadas falsas alarmas, previniendo la "fatiga de alertas" y la perdida de confianza en el sistema. |
| **Especificidad** | Proporcion de estudiantes que si entregaron y que el sistema NO alerto falsamente. | **>= 70%.** |
| **ROC-AUC** | Capacidad discriminatoria global del modelo predictivo basado en el IPP. | **>= 0.80.** *Por que:* Es el estandar en analitica educativa para considerar un modelo como "buen discriminador". |
| **Calibracion de umbrales** | Verificar empiricamente si los cortes del 40% y 70% maximizan la relacion sensibilidad/especificidad mediante curvas ROC sobre datos reales. | Ajuste posterior al piloto. |

**Particion de datos:** 80% de los registros historicos para entrenamiento del modelo y 20% para prueba. Se aplica validacion cruzada de 5 pliegues (5-fold cross-validation) para evitar sobreajuste.

---

### 3.2 Validacion Pedagogica

Evalua si la herramienta produce un cambio real en el comportamiento de estudio y en los resultados academicos.

**Periodo de medicion:** Inicio y fin del ciclo academico de pilotaje.  
**Tamano minimo de muestra estimado:** 2 paralelos de aproximadamente 40 estudiantes cada uno (n total $\approx$ 80).  
**Amenazas a la validez del experimento:**  
* **Sesgo de seleccion:** Que los paralelos no sean equivalentes y un grupo tenga mejores bases academicas previas.  
* **Efecto Hawthorne:** Que el docente o los alumnos cambien positivamente su comportamiento de estudio solo por el hecho de saber que estan participando en un experimento y siendo monitoreados.

| Indicador | Fuente de datos | Forma de medir |
|---|---|---|
| Tasa de tareas no entregadas | Calificador de Moodle | Porcentaje de tareas sin envio sobre el total de tareas activas por paralelo. |
| Concentracion de envios en ultima hora | Logs de `mdl_assign_submission` | Porcentaje de entregas realizadas en la ultima hora antes del `due_date`. |
| Nota promedio en las tareas con CheckBefore | Calificador de Moodle | Media aritmetica de calificaciones en las tareas vinculadas con recursos clave. |
| Tasa de apertura de mensajes docentes | Logs de mensajeria de Moodle | Porcentaje de mensajes de orientacion enviados por el docente que fueron abiertos por el alumno. |

**Criterio de exito pedagogico:** Reduccion estadisticamente significativa de al menos 20% en la tasa de no entrega en el Grupo A respecto al Grupo B al final del ciclo.

---

### 3.3 Validacion de Usabilidad y Adopcion

Evalua si la interfaz de CheckBefore es comprensible, util y adoptada por los usuarios reales.

**Instrumento:** *System Usability Scale* (SUS) — cuestionario estandarizado de 10 items en escala Likert de 5 puntos. Produce una puntuacion entre 0 y 100.

| Participantes | Metodo de aplicacion | Meta |
|---|---|---|
| Docentes titulares de los paralelos piloto | Cuestionario SUS aplicado al finalizar el primer mes de uso. | Puntuacion SUS > 70 puntos (rango "Bueno" segun la escala de adjetivos de Bangor et al., 2009). |
| Estudiantes del Grupo A | Encuesta corta de 5 preguntas sobre claridad del widget de IPP y utilidad percibida. | Al menos el 70% percibe el indicador como util o muy util. |

**Metricas de adopcion complementarias:**
- Porcentaje de sesiones docentes en que se consulto el panel de semaforo (frecuencia de uso real).
- Numero promedio de mensajes de orientacion enviados por docente por tarea activa.
- Porcentaje de estudiantes que accedieron al widget de IPP al menos una vez por semana.

---

## 4. Cronograma de Validacion Propuesto

| Semana | Actividad |
|---|---|
| 1 - 2 | Configuracion del plugin en paralelos piloto. Capacitacion de 30 minutos a docentes sobre el uso del panel. |
| 3 | Primera tarea con CheckBefore activo. Registro de linea base: accesos, entregas y calificaciones. |
| 4 - 10 | Ciclo normal de uso. Recoleccion continua de logs de interaccion del Grupo A y el Grupo B. |
| 11 | Aplicacion del cuestionario SUS a docentes y encuesta de percepcion a estudiantes del Grupo A. |
| 12 | Calculo de metricas tecnicas (Recall, Precision, AUC) y metricas pedagogicas (tasas de entrega, promedios). |
| 13 | Analisis estadistico comparativo entre Grupo A y Grupo B. |
| 14 | Redaccion del informe de resultados y ajuste de umbrales del modelo para un segundo ciclo. |

---

## 5. Cinco Preguntas Estadisticas Abiertas para la Unidad 3

Estas preguntas surgen directamente del diseno de validacion y no pueden responderse con las herramientas analiticas desarrolladas hasta la Fase 4. Se trasladan formalmente a la Unidad 3 de Estadistica para Ciencia de Datos.

**Pregunta 1: Significancia estadistica del cambio observado**
> Si al finalizar el piloto el Grupo A tiene un 18% de tasa de no entrega y el Grupo B tiene un 28%, ¿como determinar si esa diferencia de 10 puntos porcentuales es estadisticamente significativa o producto de la variacion aleatoria entre paralelos?

*Variables involucradas:* Tasa de no entrega (variable de proporcion). Se anticipa que la prueba adecuada involucra comparacion de proporciones independientes, pero aun no se conoce el tamano de efecto real ni si se cumplen los supuestos de normalidad para elegir la prueba correcta.

---

**Pregunta 2: Eleccion de la prueba de hipotesis para variables binarias**
> La variable dependiente `on_time_delivery` es dicotomica (0 = no entrego / 1 = entrego a tiempo). ¿Cual es la prueba de contraste mas apropiada para comparar los dos grupos: la prueba Chi-cuadrado de Pearson, la prueba exacta de Fisher (para muestras pequenas) o un modelo de regresion logistica binaria que controle variables confusoras?

*Motivacion:* La eleccion depende del tamano muestral efectivo y de si se desea solo contrastar proporciones o tambien cuantificar el efecto de CheckBefore controlando por rendimiento previo.

---

**Pregunta 3: Tamano muestral y potencia estadistica**
> ¿Cuantos estudiantes por paralelo y cuantas tareas evaluables se necesitan para que el estudio tenga una potencia estadistica minima del 80% (es decir, que la probabilidad de no detectar un efecto real sea menor al 20%) asumiendo un tamano de efecto pequeno a moderado (d de Cohen entre 0.2 y 0.5)?

*Motivacion:* Con paralelos tipicos de 35 a 50 estudiantes, existe el riesgo de que el estudio este subdimensionado y no detecte diferencias reales (error Tipo II). El calculo formal del tamano muestral requiere definir el tamano de efecto esperado y el nivel de significancia alfa.

---

**Pregunta 4: Control de variables confusoras entre grupos**
> Los dos paralelos piloto podrian diferir en rendimiento academico historico, motivacion inicial o nivel de experiencia con Moodle antes del inicio del estudio. ¿Que metodo estadistico permite aislar el efecto real de CheckBefore de esas diferencias previas: el modelo de covarianza (ANCOVA), el emparejamiento por puntuacion de propension (propensity score matching) o un modelo mixto de efectos fijos y aleatorios?

*Motivacion:* Si el Grupo A tenia un rendimiento historico superior al Grupo B antes del inicio del piloto, cualquier diferencia en la tasa de entrega podria atribuirse a esa ventaja previa y no a la herramienta.

---

**Pregunta 5: Validez de constructo del Indice de Preparacion Previa**
> El IPP se define como el porcentaje de recursos clave consultados antes de las 48 horas previas al cierre. ¿Que analisis estadistico permite verificar que este indicador mide realmente la preparacion formativa del estudiante y no simplemente el tiempo total de navegacion en la plataforma o un comportamiento de apertura superficial de archivos?

*Motivacion:* Si el IPP no posee validez de constructo, las intervenciones basadas en el estarian orientando al docente con un indicador que no refleja el fenomeno de aprendizaje que pretende medir. Se anticipa que la respuesta involucra analisis factorial o correlacion con variables de aprendizaje independientes (como la calificacion final del examen presencial), pero el metodo formal aun no se conoce.
