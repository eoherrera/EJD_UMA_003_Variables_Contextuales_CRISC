# EJD-UMA-003 v7.1 -- Variables Contextuales en el Aprendizaje Federado
## Una propuesta desde la gestion de riesgos de TI

**Documento complementario al EJD-UMA-003 v7.1**
**Autor:** Edgar O. Herrera Logrono, M.Sc. Inteligencia Artificial, VIU Espana
**Directores propuestos:** Prof. Ezequiel Lopez Rubio y Prof. Juan Miguel Ortiz de Lazcano, UMA
**Fecha:** Abril 2026

---

## De donde nace esta propuesta

Los resultados del EJD-UMA-003 v6.0 confirmaron que promediar las distribuciones de los nodos elimina informacion util. La mezcla ponderada supero al promedio en los tres niveles de heterogeneidad evaluados.

Eso abre una pregunta que el dataset de trafico de red no puede responder: si los nodos son distintos, que los hace distintos? El trafico es el sintoma. La causa esta en el entorno organizacional de cada nodo.

Con mas de 25 anos en infraestructura critica he aprendido que lo que diferencia a un nodo bancario de uno gubernamental no es el trafico que generan, sino como cada uno gestiona su exposicion al riesgo. El marco CRISC fue disenado para clasificar y gestionar exactamente esas variables. No es una importacion forzada al problema: es la herramienta natural para nombrarlo con precision.

---

## Que son las variables contextuales

Se proponen 7 variables clasificadas en tres niveles segun su capacidad de incorporacion al modelo, mas una variable residual.

### Nivel 1 -- Variables observables y cuantificables

Cada nodo las declara localmente sin exponer datos sensibles.

| Variable | Que mide | Como afecta el peso |
|----------|----------|---------------------|
| Madurez CMM (0-5) | Nivel de madurez del programa de gestion de riesgos | Mayor madurez, mayor confianza en el modelo local |
| Severidad CVSS (0-10) | Promedio de vulnerabilidades en activos criticos | Mayor severidad, menor confianza (datos potencialmente comprometidos) |

### Nivel 2 -- Variables estimables por indicadores proxy

Se calculan localmente sin compartir los datos subyacentes.

| Variable | Que mide | Como afecta el peso |
|----------|----------|---------------------|
| Eficacia de KCI (0-1) | Que tan bien funcionan los controles del nodo | KCI debil indica entorno inestable, menor peso |
| Desviacion de KRI (0-1) | Frecuencia con que el nodo opera fuera de su tolerancia al riesgo | KRI alto indica inestabilidad operacional, menor peso |

### Nivel 3 -- Variables latentes no observables

Existen y condicionan el aprendizaje pero no pueden medirse sin centralizar datos sensibles. No se incorporan como pesos. Se declaran como limitacion estructural.

| Variable | Por que no se puede incorporar directamente |
|----------|---------------------------------------------|
| Cultura del Riesgo | Requiere acceso a registros internos de incidentes |
| Riesgo Inherente del Sector | Requiere fuentes externas que no siempre estan disponibles en tiempo real |

### Variable residual -- Factor Contextual Organizacional Residual (FCOR)

Captura todo lo que ningun marco puede modelar completamente: presiones regulatorias cambiantes, decisiones de arquitectura historicas, factores humanos. Su valor es cuantificable: es la brecha entre el mejor modelo federado y el modelo centralizado. En este ejercicio esa brecha es positiva, es decir, el modelo federado supera al centralizado.

---

## Como se incorporan las variables (el ICC)

Las cuatro variables de Nivel 1 y Nivel 2 se combinan en un Indice de Confianza Contextual (ICC) por nodo:

```
ICC = w_CMM  * (CMM / 5)
    + w_CVSS * (1 - CVSS / 10)
    + w_KCI  * KCI
    + w_KRI  * (1 - KRI)

Pesos utilizados: CMM=0.30, CVSS=0.25, KCI=0.25, KRI=0.20
```

Las variables de riesgo (CVSS y KRI) se invierten: mayor riesgo significa menor confianza y por tanto menor peso en la mezcla federada.

### ICC resultante por nodo

| Nodo | CMM | CVSS | KCI | KRI | ICC |
|------|-----|------|-----|-----|-----|
| Financiero | 4.0 | 6.5 | 0.78 | 0.22 | 0.679 |
| Salud | 3.0 | 4.0 | 0.82 | 0.31 | 0.673 |
| Gobierno | 2.0 | 7.5 | 0.51 | 0.64 | 0.382 |

El Gobierno recibe el ICC mas bajo por la combinacion de baja madurez (CMM=2) y alta severidad de vulnerabilidades (CVSS=7.5). El Financiero tiene el ICC mas alto, pero como se vera a continuacion, eso no lo convierte automaticamente en el nodo mas valioso para el modelo global.

---

## El hallazgo central -- ICC y Entropia capturan dimensiones distintas

Este ejercicio revela una tension que no habia aparecido en versiones anteriores.

El nodo Financiero tiene ICC=0.679 (el mas alto) pero entropia=0.021 (casi cero). Con la particion Dirichlet alpha=0.1, ese nodo recibio el 99.7% de trafico normal. Es maduro organizacionalmente, pero su modelo local es poco diverso en contenido. Darle mas peso por confianza amplifica su sesgo hacia la clase normal.

La siguiente tabla muestra como cambia el peso de cada nodo segun el criterio:

| Nodo | Peso por ICC | Peso por Entropia | Peso Combinado |
|------|-------------|------------------|----------------|
| Financiero | 0.391 | 0.040 | 0.044 |
| Salud | 0.388 | 0.762 | 0.833 |
| Gobierno | 0.220 | 0.199 | 0.123 |

Salud es el unico nodo en el cuadrante ideal: confiable (ICC=0.673) y diverso (entropia=0.407). Por eso el criterio combinado le asigna el mayor peso (0.833).

*(Ver Figura 4 -- Mapa de posicionamiento ICC vs Entropia)*
*(Ver Figura 5 -- Huella digital de cada nodo)*

---

## Resultados de la evaluacion

| Modelo | F1-macro | Accuracy | vs Baseline |
|--------|---------|---------|------------|
| NB Mezcla Entropia | 0.3938 | 0.5506 | +0.0198 |
| NB Mezcla Combinada | 0.3922 | 0.5608 | +0.0182 |
| NB Baseline (FedAvg) | 0.3740 | 0.4822 | referencia |
| NB Variables Contextuales | 0.3436 | 0.4690 | -0.0304 |
| NB Centralizado | 0.3307 | 0.4778 | superado por todos |

Tres observaciones directas:

**1.** El ICC solo no mejora al baseline porque la madurez organizacional no compensa el sesgo de datos cuando la heterogeneidad es extrema (alpha=0.1).

**2.** El criterio combinado (ICC x Entropia) supera al baseline en +0.018 puntos de F1-macro y no degrada respecto a la entropia sola. Integra ambas dimensiones sin penalizar ninguna.

**3.** Todos los modelos federados superan al modelo centralizado. El mejor federado lo supera en +0.063 puntos de F1-macro. Cuando los datos son heterogeneos, la federacion preserva la especificidad local en lugar de aplanarla.

---

## La pregunta que queda abierta

Si los valores de CMM, CVSS, KCI y KRI se actualizaran ronda a ronda, los pesos del modelo federado se ajustarian dinamicamente. Un nodo que sufre un incidente en la ronda k recibiria menor peso en la ronda k+1.

Como implementar ese ajuste sin centralizar los registros de incidentes es la linea que queda abierta para el siguiente ejercicio.

---

## Reproducibilidad

```python
SEMILLA         = 42
TEST_SIZE       = 0.3
ALPHA_DIRICHLET = 0.1
```

---

*Edgar O. Herrera Logrono, M.Sc. Inteligencia Artificial, VIU Espana*
*EJD-UMA-003 v7.1 -- Universidad de Malaga -- Abril 2026*
