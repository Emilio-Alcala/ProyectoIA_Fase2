# CHANGELOG
´
## [v2.0-evaluacion] - 2026-10-24
### Agregado
- `fuentes/evaluacion.ipynb`
  - matriz de confusión
  - métricas
  - ajuste de umbral por costos
  - ROC/AUC con IC bootstrap
  - validación cruzada 5x3 con líneas base
  - pruebas de significancia (t corregida de Nadeau-Bengio y Wilcoxon)
  - prueba A/B simulada (Z de dos proporciones) 
  - registro en MLflow (experimento `Churn_Telco_Eval_Actividad6`).

- `evidencias/` 
  - tablas CSV 
  - gráficas generadas por el notebook


### Sin cambios
- Datos (`v1.0-datos`) 
- modelos de la Actividad 5 (`v1.1-modelos`)
- mismos hiperparámetros 
- misma partición 80/20
- semilla 42.

## [v1.2-entrega] - 2026-10-18
### Agregado
- README, requirements.txt y capturas del panel de MLflow en `evidencias/mlflow/`.


## [v1.1-modelos] - 2026-10-18
### Agregado
- `fuentes/train.py`: Grid Search (5-fold estratificado, AUC) para regresión logística y random forest, con registro en MLflow.
- Notebook `entrena.ipynb`: EDA (Exploratory Data Analisys), pruebas estadísticas (t pareada, Wilcoxon, Nadeau-Bengio) y comparación de modelos.
- `evidencias/`: gráficas, puntajes de validación cruzada y tabla comparativa.

## [v1.0-datos] - 2026-10-17
### Agregado
- Dataset original Telco Customer Churn en `datos/datos_ini/` (sin modificar).
- `fuentes/datos_prep.py`: limpieza y estandarización reproducibles.
- `datos/datos_limp/telco_churn_limpio.csv` y `reporte_calidad.md`.
### Cambiado (respecto al original)
- TotalCharges a numérico (11 vacíos con tenure = 0 imputados con 0).
- SeniorCitizen 0/1 a No/Yes; categorías "No internet/phone service" a "No".
- Churn a 0/1.