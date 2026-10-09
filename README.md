# CheckBefore: Herramienta de Learning Analytics

> **Universidad de Cuenca**  
> **Facultad de Ingeniería**  
> **Asignatura:** Ciencia de Datos · Unidad 2 (Learning Analytics)  
> **Equipo de Trabajo:** Xavier Peña y Joseph Salamea
> **Período Lectivo:** Octubre 2026 - Febrero 2027  

---

## Descripcion General del Proyecto

**CheckBefore** es una propuesta de herramienta de *Learning Analytics* diseñada específicamente para el entorno virtual de aprendizaje **eVirtual (Moodle)** de la Universidad de Cuenca. Su objetivo es diagnosticar el nivel de interacción previa y preparación de los estudiantes frente a tareas y actividades evaluables complejas, habilitando alertas tempranas para el docente 48 horas antes de la fecha límite de entrega.

A diferencia de los recordatorios tradicionales basados en calendario (que únicamente notifican fechas de vencimiento), CheckBefore analiza la consulta efectiva de los recursos pedagógicos de base (guías, lecturas clave, videos instructivos) para apoyar decisiones docentes oportunas que mitiguen la procrastinación académica y reduzcan la tasa de tareas no entregadas.

---

## Canvas de Diseno de Learning Analytics (Fase 1)

### Bloque 1: Actor y decisión
* **Actor principal:** Docente titular de asignaturas de grado en la Universidad de Cuenca. *(Actor secundario: Estudiantes matriculados).*
* **Decisión que hoy toma a ciegas:** El docente debe decidir a qué estudiantes contactar para brindar orientación de refuerzo o tutoría previa al vencimiento de un trabajo relevante. Actualmente toma esta decisión a ciegas basándose únicamente en su intuición o esperando a que el plazo expire para constatar en el calificador quiénes no enviaron la tarea. Por su parte, el estudiante decide cuándo comenzar la actividad consultando la fecha límite en el calendario sin verificar si cuenta con la preparación teórica necesaria.
* **Frecuencia:** Semanal, coincidiendo con la apertura y cierre de las asignaciones de trabajo independiente.

### Bloque 2: Problema educativo en la Universidad de Cuenca
* **Evidencia en la institución:** En los primeros ciclos universitarios, las asignaturas de ciencias básicas y formación profesional registran porcentajes elevados de no entrega de tareas y calificaciones deficientes atribuibles a la postergación sistemática del trabajo autónomo (procrastinación académica). Los registros del aula virtual demuestran que la mayor densidad de accesos a las consignas se concentra en las últimas 3 a 5 horas previas al cierre, momento en el cual el alumno carece de tiempo para asimilar las lecturas o consultar inquietudes en las tutorías docentes.
* **Alcance:** Cursos masivos con paralelos de 35 a 50 estudiantes, donde el seguimiento manual del avance formativo individual resulta inviable para el docente.

### Bloque 3: Pregunta analítica
* **Formulación:** *«¿En qué medida el nivel de consulta previa de los recursos de estudio permite predecir el riesgo de no entrega o entrega tardía de una tarea evaluable 48 horas antes de su fecha límite?»*
* **Nivel analítico:** Diagnóstica (establece el estado real de interacción con los materiales) y Predictiva (estima la probabilidad de retraso o incumplimiento de entrega).
* **Tipo de analítica:** Apoyo a la enseñanza (orientación instruccional docente) y apoyo al aprendizaje (autorregulación del estudiante).

### Bloque 4: Datos e indicadores
* **Fuentes institucionales:** Registros de eventos de navegación (*logs*) de Moodle (eVirtual UCuenca) y base de datos del módulo de tareas (tablas `mdl_assign_submission` y `mdl_logstore_standard_log`).
* **Variables clave:** Identificador anónimo de estudiante, ID de la tarea, marca temporal de cierre programado, registros de visualización/descarga de materiales formativos (PDFs, guías, videos) y estado de entrega de la tarea.
* **Indicador central - Índice de Preparación Previa (IPP):**
  $$\text{IPP} = \left( \frac{\text{Recursos formativos clave consultados}}{\text{Total de recursos formativos asignados}} \right) \times 100$$
* **Indicador operacional - Semáforo de Riesgo a 48 Horas:**
  * **Verde (Preparación suficiente / Tarea ya entregada):** $\text{IPP} \ge 70\%$ o tarea enviada.
  * **Amarillo (Preparación parcial):** $40\% \le \text{IPP} < 70\%$ sin entrega registrada.
  * **Rojo (Riesgo crítico de no entrega):** $\text{IPP} < 40\%$ sin entrega registrada a 48 horas del cierre.

### Bloque 5: Intervención pedagógica
* **Acción habilitada para el docente:** Acceso a un panel sintético con el listado de estudiantes clasificados por el semáforo de riesgo a 48 horas del cierre. Mediante una opción de acción rápida en la interfaz, el docente puede remitir un mensaje pedagógico focalizado al segmento en rojo, recordando la ruta de estudio sugerida, aclarando conceptos clave o convocando a un espacio voluntario de resolución de dudas.
* **Acción habilitada para el estudiante:** Notificación reflexiva en su panel de eVirtual alertando sobre los insumos pendientes de lectura antes de acometer la tarea.
* **Ejecutores:** El docente lidera la orientación pedagógica; el estudiante ejecuta la autorregulación del tiempo de estudio.
* **Riesgo de no actuar:** Mantenimiento de altos índices de abandono de tareas, entregas apresuradas de baja calidad y desaprovechamiento de las instancias de tutoría académica.

### Bloque 6: Ética, privacidad y control de sesgos
* **Datos requeridos:** Restringidos a registros estrictos de interacción dentro del aula virtual institucional del curso. No se incorporan datos socioeconómicos, demográficos ni sensibles.
* **Privacidad y visibilidad:** El docente únicamente accede a los datos de los estudiantes formalmente matriculados en su paralelo. El estudiante solo visualiza sus propios indicadores individuales.
* **Mitigación de sesgos:** Se contempla la brecha de conectividad domiciliaria; el sistema reconoce la descarga del archivo como interacción válida y no computa tiempos continuos de permanencia para no penalizar el estudio fuera de línea. La herramienta posee carácter estrictamente formativo y no afecta la calificación sumativa del estudiante.

### Bloque 7: Prueba de concepto
* **Dataset:** *Open University Learning Analytics Dataset* (OULAD), utilizando las tablas de interacciones con el entorno virtual (`studentVle`) y registros de entregas de evaluaciones (`studentAssessment`).
* **Demostración técnica:** Implementación en Python (Google Colab) del cálculo automatizado del IPP contrastando la fecha de corte analítico con el momento real de envío de la asignación.
* **Limitaciones del prototipo:** Al provenir de un entorno de educación a distancia pura, OULAD no captura las interacciones presenciales de aula que ocurren en la Universidad de Cuenca, asumiendo que toda la preparación se realiza exclusivamente en el aula virtual.

### Bloque 8: Plan de validación preliminar
* **Hipótesis:** *«El uso de intervenciones docentes basadas en el Índice de Preparación Previa reduce en al menos un 20% la tasa de tareas no entregadas y disminuye los envíos realizados en la última hora previa al cierre»*.
* **Diseño de evaluación:** Estudio cuasiexperimental de dos paralelos de la misma asignatura con el mismo docente (paralelo experimental con CheckBefore y paralelo de control con Moodle habitual).
* **Niveles de validación:**
  * **Técnica:** Precisión y sensibilidad (*recall*) en la identificación temprana de estudiantes en riesgo de no entrega.
  * **Pedagógica:** Comparación de tasas de entrega a tiempo y notas promedio entre grupo experimental y control.
  * **Usabilidad:** Aplicación del instrumento *System Usability Scale* (SUS) a los docentes y medición de la tasa de apertura de avisos en estudiantes.
* **Criterio de éxito:** Escala SUS superior a 70 puntos y diferencia estadística favorable en la puntualidad de entregas.

### Bloque 9: Preguntas abiertas para la Unidad de Estadística
1. ¿Cómo se determina si la reducción observada en la tasa de no entrega entre el grupo experimental y el de control es estadísticamente significativa o producto de la variabilidad aleatoria?
2. Al ser la variable dependiente un resultado dicotómico (entregó a tiempo vs. no entregó), ¿cuál es la prueba estadística o modelo resulta metodológicamente apropiado (Chi-cuadrado, prueba exacta de Fisher o regresión logística binaria)?
3. ¿Cuál es el tamaño muestral mínimo de estudiantes y cursos necesario para detectar un tamaño de efecto pequeño a moderado con una potencia estadística mínima del 80%?
4. ¿Cómo controlar las variables de confusión previas entre los dos paralelos evaluados (por ejemplo, promedio académico histórico o nivel de motivación inicial) mediante modelos ANCOVA o emparejamiento por puntuación de propensión?
5. ¿De qué manera se puede comprobar estadísticamente que el Índice de Preparación Previa (IPP) posee validez de constructo y no refleja únicamente el tiempo total de navegación en la plataforma?

---

## Estructura del Repositorio

El proyecto se organiza en las siguientes carpetas correspondientes a las fases del trabajo aplicativo:

```text
herramienta-la-checkbefore/
├── .gitignore               # Configuracion de exclusiones de Git
├── README.md                # Canvas de diseno y documentacion general del proyecto
├── fase2_datos/             # Fase 2: Diccionario de datos y consideraciones eticas
├── fase3_wireframes/        # Fase 3: Disenos de interfaz, flujo de intervencion y arquitectura
├── fase4_notebook/          # Fase 4: Prueba de concepto en Python (Google Colab)
├── fase5_pitch/             # Fase 5: Pitch oral de 6 minutos, guion tecnico y presentacion PPTX
└── fase6_validacion/        # Fase 6: Diseno del plan de validacion en tres niveles
```

---

## Declaracion de Uso de IA Generativa

En cumplimiento con los requerimientos académicos del curso:
* **Herramientas utilizadas:** Asistentes de IA generativa basados en modelos LLM.
* **Propósito de uso:** Apoyo en la estructuración de la documentación del proyecto, formateo de tablas comparativas de casos de estudio y redacción técnica preliminar de los bloques del Canvas de Learning Analytics.
* **Procedimiento de verificación:** Toda la información técnica, nombres de variables de Moodle, citas del consorcio LALA y formulaciones analíticas fueron revisadas, contrastadas y validadas manualmente por los autores frente a los boletines oficiales del proyecto LALA y la literatura académica de la asignatura.
