# Fase 4: Prueba de Concepto en Python (Notebook)

En esta carpeta se aloja el cuaderno ejecutable de Jupyter / Google Colab (`prueba_de_concepto_CheckBefore.ipynb`) que valida computacionalmente el **Índice de Preparación Previa (IPP)** y la clasificación semafórica de riesgo a 48 horas del cierre.

## Contenido del Notebook:

- [prueba_de_concepto_CheckBefore.ipynb](prueba_de_concepto_CheckBefore.ipynb): Cuaderno interactivo reproducible.

### Estructura de los 5 componentes obligatorios:
1. **Carga y descripción del dataset:** Mapeo de la estructura del dataset público OULAD (`studentInfo`, `assessments`, `vle`, `studentVle`, `studentAssessment`).
2. **Limpieza y transformación:** Construcción del IPP ($D03$), semáforo operacional ($D04$) y variable objetivo de entrega a tiempo (`on_time_delivery` - $D07$).
3. **Visualización tipo tablero:** Gráficos interactivos en Plotly (gráfico de dona de distribución del semáforo y gráfico de dispersión de anticipación en entregas).
4. **Modelo predictivo base:** Clasificadores supervisados en Scikit-Learn (Regresión Logística y Árbol de Decisión) con reportes de clasificación, matriz de confusión y ROC-AUC.
5. **Sección reflexiva:** «Lo que este prototipo no puede decir todavía» (enlace directo con el Plan de Validación de la Fase 6).
