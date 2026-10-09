# Fase 3: Diseño de la Herramienta (Wireframes y Arquitectura de CheckBefore)

## 1. Introducción y Enfoque del Prototipo

CheckBefore es una herramienta de Learning Analytics integrada en la plataforma institucional eVirtual (Moodle) de la Universidad de Cuenca. Su objetivo central es apoyar la toma de decisiones pedagógicas docentes y la autorregulación estudiantil 48 horas antes del cierre de una tarea evaluable.

CheckBefore evita actuar como una simple alarma de calendario (que Moodle ya incluye de forma nativa). En su lugar, analiza el comportamiento analítico previo: evalúa mediante el Índice de Preparación Previa (IPP) si el estudiante ha consultado los recursos clave de estudio (guías, PDFs teóricos, videos explicativos) antes de que venza el plazo.

---

## 2. Arquitectura de Alto Nivel

La arquitectura de CheckBefore conecta la extracción de logs de navegación de Moodle con el procesamiento batch del IPP y la presentación de vistas adaptadas a cada actor.

```
+-------------------------------------------------------------------------+
|                       1. FUENTES DE DATOS (Moodle)                       |
|  - mdl_logstore_standard_log (Eventos viewed/downloaded de recursos)    |
|  - mdl_assign / mdl_assign_submission (Metadatos de tarea y entregas)   |
|  - mdl_resource / mdl_url / mdl_page (Insumos formativos clave)          |
|  - mdl_user / mdl_course (Usuarios, roles y matrículas)                 |
+-------------------------------------------------------------------------+
                                     │
                                     ▼ (Extracción vía Cron cada 6h)
+-------------------------------------------------------------------------+
|                      2. MOTOR DE PROCESAMIENTO                          |
|  - Filtrado temporal: Ventana estricta a 48h previas al due_date.       |
|  - Cálculo de IPP = (Recursos clave consultados / Total asignados)*100. |
|  - Clasificación en Semáforo Operacional (Verde, Amarillo, Rojo).       |
+-------------------------------------------------------------------------+
                                     │
                                     ▼ (Persistencia en DB del plugin)
+-------------------------------------------------------------------------+
|                    3. CAPA DE INTERFAZ / VISTAS                         |
|  - Pantalla 1: Vista General del Docente (Dashboard de Paralelo).       |
|  - Pantalla 2: Vista del Estudiante (Widget de Autorregulación).        |
|  - Pantalla 3: Modal de Intervención Docente (Envío rápido a Grupo Rojo).|
+-------------------------------------------------------------------------+
```

### Componentes Técnicos de la Arquitectura:

1. Capa de Extracción de Datos:
   - Consulta periódica a las tablas nativas de Moodle (mdl_logstore_standard_log, mdl_assign).
2. Motor de Cálculo de IPP:
   - Procesa los eventos ocurridos dentro de la ventana previa a las 48 horas de la entrega programada (V05).
   - Computa las variables derivadas (D01, D02, D03, D04).
3. Persistencia Temporal:
   - Registra métricas en la tabla interna mdl_checkbefore_metrics para renderizado ágil del tema de eVirtual.
4. Capa de Presentación:
   - Vistas adaptadas a los permisos del usuario (docente vs. estudiante).

---

## 3. Diagrama de Flujo de Intervención Pedagógica

El flujo describe el ciclo operativo desde que se activa la alerta hasta que el docente ejecuta una acción de orientación pedagógica y esta se registra.

```
[48 Horas Antes del Cierre de la Tarea]
                   │
                   ▼
     [Cálculo Automático del IPP]
                   │
         ┌─────────┴─────────┐
         ▼                   ▼
  [Panel Docente]    [Widget Estudiante]
         │                   │
  Visualiza alumnos    Visualiza barra de IPP
  en riesgo (Rojo)     y recursos pendientes
         │                   │
         ▼                   │
 [Modal de Intervención]     │
 (Selecciona plantilla y     │
  envía mensaje a grupo)     │
         │                   │
         └─────────┬─────────┘
                   ▼
      [Registro en Log de Moodle]
     (Auditoría y seguimiento posterior)
```

### Pasos del Flujo:
1. Detonador de Alerta (48h pre-cierre): El sistema calcula automáticamente el IPP de todos los matriculados.
2. Visualización y Clasificación:
   - Nivel Verde (IPP >= 70% o entregado): Preparación adecuada.
   - Nivel Amarillo (40% <= IPP < 70%): Preparación parcial.
   - Nivel Rojo (IPP < 40% sin entrega): Riesgo crítico de no entrega o entrega de baja calidad.
3. Acción Pedagógica: El docente abre el Modal de Intervención y envía una guía/recordatorio formativo al grupo en riesgo (Rojo) con un solo clic.
4. Registro de Intervención: Se guarda en la base de datos la fecha, hora, destinatarios y contenido de la intervención para evaluar su impacto en las entregas finales.

---

## 4. Pantallas Principales y Vinculación con Indicadores

CheckBefore incluye 3 pantallas esenciales para cumplir con la rúbrica de evaluación:

### Pantalla 1: Vista General del Docente (Dashboard de Paralelo)
* Propósito: Brindar al docente una radiografía inmediata de las tareas activas y la distribución del semáforo de riesgo a 48 horas del vencimiento.
* Elementos Visuales:
  - Resumen gráfico de dona con la proporción de alumnos en Nivel Rojo, Nivel Amarillo y Nivel Verde.
  - Tabla de tareas activas con contador regresivo de horas restantes.
  - Botón de acceso directo: "Intervenir grupo en riesgo (Nivel Rojo)".
* Indicadores Vinculados (Diccionario de Datos):
  - Tasa de Riesgo del Curso: Porcentaje de alumnos con D04 = Rojo.
  - IPP Promedio del Paralelo: Promedio de la variable derivada D03.
  - Días/Horas Restantes para el Cierre: Calculado a partir de V05.

### Pantalla 2: Vista del Estudiante (Widget de Autorregulación)
* Propósito: Ofrecer al estudiante una herramienta visual de autorregulación que indique su avance de lectura antes de resolver la tarea.
* Elementos Visuales:
  - Barra de progreso interactiva del Índice de Preparación Previa (IPP).
  - Semáforo personal (Verde / Amarillo / Rojo).
  - Checklist de Recursos Sugeridos con enlaces directos para consulta previa.
* Indicadores Vinculados:
  - IPP Personal (D03): Porcentaje propio de recursos consultados sobre asignados (D02 / D01).
  - Semáforo Operacional (D04): Nivel personal de preparación.
  - Recursos Pendientes (D01 - D02): Conteo de materiales clave por revisar.

### Pantalla 3: Modal de Intervención Docente (Envío Rápido de Orientación)
* Propósito: Permitir al docente enviar un mensaje formativo masivo pero personalizado al grupo clasificado en Nivel Rojo sin salir del aula virtual.
* Elementos Visuales:
  - Selección de plantilla pedagógica (ej: "Recordatorio de lectura clave", "Invitación a tutoría de refuerzo").
  - Listado de estudiantes destinatarios (pre-seleccionados automáticamente aquellos en Nivel Rojo).
  - Botón de confirmación: "Enviar mensaje de acompañamiento".
* Indicadores Vinculados:
  - Destinatarios Filtrados (D04 = Rojo): Estudiantes con IPP < 40% a 48h del cierre.
  - Registro de Intervención: Generación de un evento en el log para trazabilidad.

---

## 5. Ubicación de Artefactos de Diseño (Imágenes)

Las imágenes exportadas de los wireframes (diseñadas en Figma, Canva o Balsamiq) deben ubicarse en la carpeta fase3_wireframes/pantallas/ con la siguiente nomenclatura:

1. pantalla_1_dashboard_docente.png: Mockup del Dashboard General por Paralelo.
2. pantalla_2_widget_estudiante.png: Mockup del Widget de Autorregulación del Estudiante.
3. pantalla_3_modal_intervencion.png: Mockup del Modal de Intervención Docente.
