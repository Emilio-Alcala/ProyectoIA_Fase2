# Actividad 5 – Predicción de churn (Telco Customer Churn)

Proyecto de la materia Gestión de Proyectos de Inteligencia Artificial (Tecmilenio). 

Continúa el business case de la Fase I (Sierra Móvil): clasificar clientes con alta probabilidad de baja y priorizar el 15% de mayor riesgo.

## Dataset
- Fuente: IBM Telco Customer Churn (Kaggle, `blastchar`).
- 7,043 clientes, 21 columnas; objetivo `Churn` (26.5% de bajas).
- Original en `datos/datos_ini/` (no se modifica)
- limpio en `datos/datos_limp/`

## Estructura
```
datos/datos_ini/     datos originales
datos/datos_limp/    datos limpios + reporte_calidad.md
fuentes/datos_prep.py  limpieza
fuentes/train.py       entrenamiento, Grid Search y registro en MLflow
fuentes/entrena.ipynb  EDA, pruebas estadísticas y comparación
evidencias/            gráficas, resultados, capturas de MLflow
CHANGELOG.md           versiones del proyecto y de los datos
```

## Cómo reproducir
```powershell
(terminal de powershell)
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python fuentes\datos_prep.py
python fuentes\train.py
```
```powershell
(terminal adicional de powershell)
mlflow ui --backend-store-uri sqlite:///mlflow.db   

# En el navegador --->  http://localhost:5000
```

Luego ejecutar `fuentes/entrena.ipynb` (de arriba abajo).

## Decisiones principales
- Limpieza: TotalCharges a numérico (11 vacíos con tenure=0 imputados con 0), categorías redundantes colapsadas, Churn a 0/1.
- Escalado y one-hot dentro de un Pipeline (evita fuga de datos).
- Partición 80/20 estratificada, semilla 42.
- Modelos: regresión logística y random forest; Grid Search con 5-fold estratificado y métrica ROC-AUC.
- Métricas: accuracy, precision, recall, F1, ROC-AUC, precision@15% y recall@15%.
- Comparación: prueba t pareada, Wilcoxon y t corregida (Nadeau-Bengio) sobre 15 puntajes de validación cruzada.

## Resultados (conjunto de prueba)
| Métrica | Reg. logística | Random forest |
|---|---|---|
| ROC-AUC | 0.8415 | 0.8429 |
| Precision@15% | 0.7123 | 0.6745 |
| Recall | 0.5588 | 0.4144 |

Sin diferencia significativa en AUC (p > 0.05). Se elige regresión logística por interpretabilidad y simplicidad.

## Versiones
Ver `CHANGELOG.md`. 

Etiquetas Git: 
- `v1.0-datos` 
- `v1.1-modelos`
- `v1.2-entrega`

# Actividad 6 – Evaluación y validación de modelos

Continúa de la Actividad 5 (mismos datos y mismos hiperparámetros) 

El business case de la Fase I (supuestos del Excel `PIA - Sierra Movil ROI v2.xlsx`)

## Qué contiene
```
datos/datos_limp/telco_churn_limpio.csv    datos limpios de la Actividad 5
fuentes/evaluacion.ipynb                   notebook completo (ejecutado)
evidencias/                                tablas CSV y gráficas generadas por el notebook
requirements.txt  
CHANGELOG.md
```

## Cómo reproducir (Windows, PowerShell)
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
cd fuentes
jupyter notebook evaluacion.ipynb      # Run All, de arriba abajo 
```
Se crea `mlflow.db` en la raíz del proyecto. Para ver los experimentos (en otra terminal, desde la raíz del proyecto):
```powershell
mlflow ui --backend-store-uri sqlite:///mlflow.db     # http://localhost:5000
```
Experimento: `Churn_Telco_Eval_Actividad6`.

## Supuestos (Excel de la Fase I)
| Parámetro | Valor |
|---|---|
| Costo de contacto (C_FP) | $46 MXN |
| Margen por cliente retenido | 300 × 40 % × 12 = $1,440 MXN |
| Aceptación × atribución | 0.20 × 0.85 = 0.17 |
| Beneficio esperado por VP (C_FN) | $244.80 MXN |
| Umbral teórico | 46 / 244.80 = 0.188 |
| Capacidad de contacto | 15 % de la base |

## Decisiones principales
- El umbral se elige con predicciones fuera de muestra del entrenamiento; la prueba se usa una sola vez.
- Validación cruzada estratificada 5×3, `Pipeline` con escalado y one-hot dentro de cada fold.
- Líneas base: Dummy, regla "contrato mes a mes" y "contactar a todos".
- Comparación: t pareada con corrección de Nadeau-Bengio y Wilcoxon.
- A/B simulado: 40,000 clientes (remuestreo del conjunto de prueba), Z de dos proporciones, 500 réplicas para estimar potencia.

## Resultado resumido (conjunto de prueba)
* Modelo seleccionado: regresión logística
* AUC 0.8415
* umbral 0.188 
* recall 0.874
* precisión 0.457
* $33.44 netos por cliente frente a $25.96 con umbral 0.5
* A/B con presupuesto igual: 1.04 % vs 1.77 % de retención
* Z = 6.20, p < 0.001.
