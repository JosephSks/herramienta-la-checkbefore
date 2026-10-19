# Fase 3: Diseño de la Herramienta (Wireframes y Flujos)

En esta carpeta se alojan la especificación de diseño, arquitectura y prototipos de interfaz de usuario de **CheckBefore**.

## Documentos y Estructura:

- [diseno_y_arquitectura.md](diseno_y_arquitectura.md): Especificación técnica de la arquitectura de alto nivel, flujo de intervención pedagógica y vinculación de indicadores con las pantallas.
- **pantallas/**: Carpeta destinada a guardar las imágenes / capturas de los wireframes (Figma, Canva, Balsamiq o dibujo).

## Contenido del Diseño:
1. **Wireframes (3 pantallas principales):**
   - **Pantalla 1:** Dashboard general del docente (vista por paralelo con gráfico de dona, semáforo e intervención masiva).
   - **Pantalla 2:** Vista del estudiante (Widget de autorregulación "Mi CheckBefore" en Moodle).
   - **Pantalla 3:** Modal de intervención docente para envío de retroalimentación pedagógica a escala.
2. **Anotaciones e Indicadores:**
   - Mapeo directo entre elementos de la UI y las variables derivadas (D01 a D07).
3. **Flujo de Intervención Pedagógica:**
   - Detonador a 48h pre-entrega -> Clasificación semafórica -> Nudge/Mensaje de refuerzo -> Log de auditoría.
4. **Arquitectura de Alto Nivel:**
   - Flujo de datos desde logs de Moodle hasta la capa de interfaz.
