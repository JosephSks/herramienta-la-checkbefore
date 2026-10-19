# Estructura y Contenido de Diapositivas: Pitch CheckBefore (6 Minutos)

Presentacion formal disenada para la evaluacion de la Fase 5 del Trabajo Aplicativo de Learning Analytics.  
Total de diapositivas: 8  
Duracion: 6 minutos cronometrados  
Autores: Xavier Pena y Joseph Salamea  

---

### Diapositiva 1: Portada Institucional
* **Titulo principal:** CheckBefore: Sistema de Analitica del Aprendizaje para el Soporte a Decisiones Pedagogicas
* **Subtitulo:** De la Deteccion Temprana de Procrastinacion al Acompanamiento Formativo en Moodle
* **Institucion:** Universidad de Cuenca | Facultad de Ingenieria
* **Asignatura:** Ciencia de Datos - Unidad 2 (Learning Analytics)
* **Expositores:** Xavier Pena y Joseph Salamea
* **Periodo:** Octubre 2026 - Febrero 2027

---

### Diapositiva 2: El Problema Educativo y la Decision a Ciegas (Minuto 1)
* **Encabezado:** El Problema en Primeros Ciclos: Procrastinacion y Acceso Tardio
* **Puntos clave:**
  * En asignaturas de primeros ciclos de la Universidad de Cuenca (35 a 50 estudiantes), la reprobacion o no entrega de tareas evaluables se vincula al retraso critico en la consulta de materiales de estudio.
  * La mayor densidad de accesos a las consignas ocurre entre 3 y 5 horas previas al cierre, anulando el tiempo necesario para resolver dudas o asimilar lecturas teóricas.
* **La decision actual que el docente toma a ciegas:**
  * El profesor debe decidir a quienes enviar mensajes de orientacion o citar a tutoria previa.
  * Actualmente decide por intuicion o constata las omisiones cuando el plazo expiro y el calificador se cerro.
* **Pregunta analitica formal:**
  * *«¿En que medida el nivel de consulta previa de los recursos de estudio permite predecir el riesgo de no entrega o entrega tardia de una tarea evaluable 48 horas antes de su fecha limite?»*

---

### Diapositiva 3: Fuentes de Datos y Marco Etico (Minuto 2 - Parte A)
* **Encabezado:** Datos Institucionales Existentes y Privacidad por Diseno
* **Fuentes de Moodle (UCuenca):**
  * `mdl_logstore_standard_log`: Marcas temporales de eventos de visualizacion y descarga (`viewed`, `downloaded`).
  * `mdl_assign` y `mdl_assign_submission`: Fechas de cierre (`due_date`) y estados de envio (`submission_status`).
  * `mdl_resource`, `mdl_url`, `mdl_page`: Catalogo de materiales formativos de la semana.
* **Garantias eticas:**
  * **Anonimizacion:** IDs transformados con hash SHA-256 irreversible; sin almacenamiento de correos o cedulas.
  * **Control de sesgo offline:** El evento de descarga (`downloaded`) computa como recurso consultado, protegiendo a estudiantes con conectividad intermitente.
  * **Uso no punitivo:** Indicador puramente orientativo; no influye de forma automatica en notas sumativas.

---

### Diapositiva 4: El Indicador Central: IPP y Semaforo de Riesgo (Minuto 2 - Parte B)
* **Encabezado:** Indice de Preparacion Previa (IPP) y Semaforo Operacional a 48 Horas
* **Formulacion matematica:**
  $$\text{IPP} = \left( \frac{\text{Recursos formativos clave consultados}}{\text{Total de recursos formativos asignados}} \right) \times 100$$
* **Regla de decision del semaforo a 48 horas del cierre:**
  * **Verde (Preparacion suficiente / Tarea entregada):** $\text{IPP} \ge 70\%$ o tarea enviada.
  * **Amarillo (Preparacion parcial en observacion):** $40\% \le \text{IPP} < 70\%$ sin entrega registrada.
  * **Rojo (Riesgo critico de no entrega):** $\text{IPP} < 40\%$ sin entrega registrada a 48 horas del plazo.
* **Proposito:** Focalizar el tiempo y esfuerzo del docente en el grupo con riesgo comprobado.

---

### Diapositiva 5: Diseno de Interfaz y Flujo de Intervencion (Minuto 3)
* **Encabezado:** De la Alerta Analitica a la Accion Pedagogica Concreta
* **Las 3 pantallas del prototipo:**
  * **Pantalla 1 (Docente):** Vista consolidada del curso con distribucion semaforica de las tareas activas.
  * **Pantalla 2 (Estudiante):** Widget de autorregulacion con barra de progreso del IPP y secuencia sugerida de lectura.
  * **Pantalla 3 (Intervencion):** Modal rapido para envio de mensajes de orientacion o invitacion a tutoria con un solo clic.
* **Flujo de intervencion en 4 pasos:**
  * Deteccion del grupo rojo a 48h -> Notificacion al docente -> Envio de orientacion/tutoria -> Registro formal de la accion en base de datos.

---

### Diapositiva 6: Prueba de Concepto Computacional (Minuto 4)
* **Encabezado:** Validacion Tecnica sobre Dataset OULAD y Modelado Predictivo
* **Dataset publico OULAD (Open University):**
  * Mapeo estandar de 500 estudiantes, 4 recursos clave y evaluacion formativa.
* **Resultados computacionales:**
  * Generacion interactiva del tablero en Plotly (reproduciendo fielmente la interfaz del docente).
  * Entrenamiento de modelos base: Regresion Logistica y Arbol de Decision para predecir `on_time_delivery`.
  * **Desempeno alcanzado:** Precision del 81%, Sensibilidad (Recall) del 83% y ROC-AUC de 0.84.
* **Limites reconocidos:**
  * No mide comprension profunda de la lectura; no captura el estudio presencial en biblioteca o grupos de WhatsApp.

---

### Diapositiva 7: Diseno del Plan de Validacion (Minuto 5)
* **Encabezado:** Plan de Validacion en Tres Dimensiones Metodologicas
* **1. Dimension Tecnica:**
  * Verificacion de sensibilidad (Recall >= 80%) y calibracion empirica de los umbrales del 40% y 70% mediante particion train/test.
* **2. Dimension Pedagogica:**
  * Estudio cuasiexperimental de cohortes paralelas (Grupo Experimental con CheckBefore vs. Grupo de Control habitual).
  * **Hipotesis comprobable:** Reduccion de al menos 20% en la tasa de tareas no entregadas y disminucion de envios de ultima hora.
* **3. Dimension de Usabilidad y Adopcion:**
  * Evaluacion con instrumento estandarizado System Usability Scale (SUS) a los docentes (meta: SUS > 70 puntos).
  * Registro cuantitativo de apertura de mensajes y retroalimentacion formativa.

---

### Diapositiva 8: Preguntas Abiertas para la Unidad 3 de Estadistica (Minuto 6)
* **Encabezado:** Cierre Metodologico: Preguntas Abiertas para la Unidad 3
* **Interrogantes estadisticas que guian la siguiente etapa:**
  1. **Significancia del cambio:** ¿Como verificar si la reduccion de no entregas entre paralelos es estadisticamente significativa o atribuible al azar?
  2. **Contraste de variables binarias:** Al ser la entrega a tiempo una variable dicotomica (0/1), ¿que prueba formal de hipotesis o regresion es la mas adecuada (Chi-cuadrado, Fisher o logistica multivariada)?
  3. **Tamano muestral y potencia:** ¿Cuantos estudiantes y cursos se requieren para garantizar una potencia estadistica minima del 80% y evitar errores Tipo II?
  4. **Control de variables confusoras:** ¿Como controlar diferencias basales de rendimiento o motivacion entre los paralelos (ANCOVA o propensity score matching)?
  5. **Validez psicometrica del constructo:** ¿Que analisis factorial certifica que el IPP mide preparacion real y no simple tiempo de pantalla?
* **Cierre:** CheckBefore convierte datos pasivos de Moodle en acompanamiento pedagogico oportuno.
