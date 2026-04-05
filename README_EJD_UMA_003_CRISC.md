# EJD-UMA-003 v7.1 - Variables Contextuales CRISC en el Aprendizaje Federado

Ejercicio doctoral · Programa de Doctorado en Tecnologias Informaticas · Universidad de Malaga

| Campo | Detalle |
|-------|---------|
| Codigo | EJD-UMA-003 |
| Version | 7.1 |
| Referencia | Extension del EJD-UMA-003 v6.0 con variables de contexto organizacional CRISC |
| Autor | Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana |
| Directores propuestos | Prof. Ezequiel Lopez Rubio · Prof. Juan Miguel Ortiz de Lazcano, UMA |
| Dataset | NSL-KDD (Network Security Lab, Dalhousie University) |
| Fecha | Marzo 2026 |

---

## Objetivo

Los resultados del EJD-UMA-003 v6.0 confirmaron que promediar las distribuciones de los nodos elimina informacion util. La mezcla ponderada supero al promedio en los tres niveles de heterogeneidad evaluados. Eso abre una pregunta que el dataset de trafico de red no puede responder por si solo: si los nodos son distintos, que los hace distintos?

El trafico es el sintoma. La causa esta en el entorno organizacional de cada nodo. Con mas de 25 anos en infraestructura critica he aprendido que lo que diferencia a un nodo bancario de uno gubernamental no es el trafico que generan, sino como cada uno gestiona su exposicion al riesgo. El marco CRISC fue disenado para clasificar y gestionar exactamente esas variables. No es una importacion forzada al problema: es la herramienta natural para nombrarlo con precision.

Este ejercicio introduce tres variables de contexto organizacional derivadas de CRISC como entrada al modelo federado: madurez de capacidad (CMM), cobertura de controles (KCI) y efectividad de controles (FCI). El objetivo es verificar si esas variables, que reflejan la postura real de seguridad de cada nodo, mejoran la capacidad del modelo para diferenciar entre nodos y afinar su mecanismo de agregacion.

---

## Variables de contexto introducidas

| Variable | Que mide | Rango |
|----------|----------|-------|
| CMM | Madurez de capacidad del nodo en gestion de riesgos | 1.0 a 5.0 |
| KCI | Cobertura de controles implementados sobre los requeridos | 0.0 a 1.0 |
| FCI | Efectividad real de los controles implementados | 0.0 a 1.0 |
| ICC | Indice de Coherencia Contextual. Combina CMM, KCI y FCI en un solo valor por nodo | 0.0 a 1.0 |

El ICC es la variable central del ejercicio. Resume en un solo numero que tan coherente es la postura de seguridad declarada de un nodo con su comportamiento real en el sistema federado.

---

## Figuras del notebook

| Figura | Contenido |
|--------|-----------|
| 1 | Distribucion Dirichlet por nodo |
| 2 | Divergencia Jensen-Shannon entre nodos |
| 3 | Comparativa de F1-Score por configuracion |
| 4 | ICC vs Entropia por nodo (mapa de posicionamiento) |
| 5 | Huella digital radar 5D por nodo |
| 6 | Taxonomia de amenazas contextuales al modelo federado |
| 7 | Del riesgo dinamico al drift detection |

---

## Resultados obtenidos

| Metrica | Valor |
|---------|-------|
| Nodos federados | 3 (Financiero, Gubernamental, Salud) |
| ICC Financiero | 0.78 |
| ICC Gubernamental | 0.51 |
| ICC Salud | 0.63 |
| Estado de ejecucion | 100% |

> Los valores de F1-Score se calculan en tiempo real segun la ejecucion. Las conclusiones son dinamicas y no tienen valores fijos en el codigo.

---

## Limitaciones declaradas

- Los valores CMM, KCI y FCI son simulados con parametros representativos de cada sector. No provienen de auditorias reales.
- NSL-KDD data de 1998 y no representa patrones de ataque contemporaneos en infraestructura critica OT/ICS.
- El ICC como variable de ponderacion es una propuesta exploratoria. Su validez estadistica requiere validacion empirica en entornos reales.

---

## Como ejecutarlo

1. Abrir Google Colab (colab.research.google.com)
2. Ir a File, Open notebook, GitHub y pegar la URL de este repositorio
3. Ejecutar con Runtime, Run all

El notebook descarga NSL-KDD automaticamente. Si falla la descarga genera un dataset sintetico equivalente.

---

## Stack tecnico

Python 3.10 o superior, scikit-learn 1.3 o superior, NumPy, Pandas, Matplotlib, Seaborn, SciPy.

---

## Control de cambios

| Version | Fecha | Descripcion |
|---------|-------|-------------|
| 6.0 | Feb 2026 | Version base. Mezcla de distribuciones Naive Bayes con heterogeneidad por niveles. |
| 7.0 | Mar 2026 | Introduccion de variables CRISC (CMM, KCI, FCI) e ICC como indice de coherencia contextual. Figuras 1 a 5. |
| 7.1 | Mar 2026 | Taxonomia de amenazas contextuales. Figuras 6 y 7. Pregunta abierta hacia gobernanza de IA. |

---

## Repositorios del proceso doctoral

| Codigo | Repositorio | Contenido |
|--------|-------------|-----------|
| EJD-UMA-001 | [RF_Federado_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA) | Random Forest Federado, comparativa de configuraciones |
| EJD-UMA-001 v8.0 | [RF_Federado_Ejercicio_Doctoral_UMA_v8](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA_v8) | Fed-TRUST: Coeficiente de Veracidad V_i y agregacion ponderada |
| EJD-UMA-002 | [TED_MDS_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/TED_MDS_Ejercicio_Doctoral_UMA) | Tree Edit Distance y proyeccion MDS |
| EJD-UMA-003 | [EJD_UMA_003_NaiveBayes_Federado](https://github.com/eoherrera/EJD_UMA_003_NaiveBayes_Federado) | Clasificador Naive Bayes Federado con mezcla de distribuciones |
| EJD-UMA-003 v7.1 | Este repositorio | Variables contextuales CRISC en aprendizaje federado |

---

Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana
Analista Senior de Seguridad de la Informacion, IESS Ecuador
Candidato CRISC, ISACA
Candidato doctoral, Programa de Doctorado en Tecnologias Informaticas, Universidad de Malaga
