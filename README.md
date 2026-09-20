# Cómo el desempleo se relaciona con la morosidad de consumo

## Resumen

Este trabajo muestra el ciclo de vida completo de un modelo de series tiempo para el pronóstico de la morosidad consumo 90+(%) usando como regresor el desempleo. Parte desde el análisis descriptivo de las variables, búsqueda de relaciones a largo plazo entre variables, desarrollo del modelo y validación. 

Como resultado se obtiene evidencia de una cointegración entre la morosidad consumo 90+(%) y el desempleo (rezagado 8 meses). Se determinó que el modelo seleccionado para representar esta relación es un ECM y que el ajuste hacia la relación de largo plazo ocurre de forma gradual, con un horizonte cercano a 11 meses. Asimismo, tras tres observaciones out-of-time, se determinó que la validación del modelo sigue siendo preliminar; con solo tres observaciones los resultados no son concluyentes.

El principal problema para el desarrollo de este trabajo fue el tratamiento del periodo de pandemia entre el año 2020 y 2022, durante ese periodo se disparó el desempleo y a la vez la mora 90+(%) consumo registró una baja histórica. Si bien el aumento del desempleo es esperable por los confinamientos y cierres de empresas durante esa época, la fuerte disminución de la morosidad coincidió con un periodo de elevada liquidez de los hogares, asociado a ayudas gubernamentales y retiros extraordinarios de los fondos individuales de pensiones.


## Problema de negocio

El principal objetivo de este trabajo fue generar una herramienta que permita anticipar la morosidad, para que áreas de riesgo puedan modificar sus políticas de riesgo con anticipación. Como variable explicativa se eligió el desempleo. Si bien es posible agregar otras variables macroeconómicas, se optó solo por una para facilitar la interpretación por distintas audiencias dentro de las áreas de riesgo, siendo el desempleo un indicador fácilmente entendible para todo público.

Este tipo de metodología puede ser útil dentro de un marco IFRS 9 como herramienta de análisis macroeconómico forward-looking, permitiendo incorporar escenarios y relaciones entre variables económicas y deterioro de cartera. En linea con la anterior la parte 3 de este trabajo incorpora un esquema de monitoreo y validación continua mediante comparación contra benchmark, seguimiento out-of-time y controles de estabilidad de los supuestos en que se basa el modelo, esto no pretende converstise en un criterio absoluto e para validación y monitores, sino que mostrar herramientas útiles que permiten no solo evaluar la capacidad predictora y explñicativa del modelo, sino que advertir de posibles cambios en el mercado.

El objetivo final de este trabajo no es mostrar un modelo estático, sino presentar una herramienta de gestión transversal que permita generar alertas tempranas para el ajuste preventivo de políticas de riesgo.


## Fuentes de datos

Como fuentes de datos se optó por alternativas que no requieran autentificación con el objetivo de facilitar la replicabilidad. Las fuentes usadas fueron:

- **Desempleo:** API pública de `mindicador.cl`
- **Mora 90+(%):** web scraping al sitio `best.cmfchile.cl`

Debido a que la API de `mindicador.cl` puede presentar intermitencias, el proceso de descarga de los datos ha sido comentado y se han dejado guardados los datos en formato CSV.

## Metodología

El presente trabajo se divide en 3 partes:

* **Parte 1:** Análisis exploratorio y búsqueda de cointegración mora-desempleo. 
* **Parte 2:** Búsqueda de modelo ECM.
* **Parte 3:** Validación y monitoreo de la calidad del modelo.

## Principales Resultados

### Parte 1

* Se obtiene evidencia de una cointegración entre la morosidad consumo 90+(%) y el desempleo rezagado 8 meses. Esta cointegración presenta un quiebre estructural y fue estimada mediante Gregory-Hansen. Engle-Granger no detectó cointegración bajo el supuesto de una relación estable, mientras que Gregory-Hansen permitió identificar una relación de cointegración incorporando un quiebre estructural.

* Otro punto importante fue que para detectar la cointegración se optó por omitir la ventana de pandemia, ya que en dicho periodo las series mostraron un comportamiento anómalo.

### Parte 2

* Dentro de las especificaciones evaluadas, se seleccionó un ECM como modelo principal para pronosticar la morosidad 90+(%) de consumo.

* También se establece, replicando el modelo en cada origen, que el modelo tarda 14 meses en detectar un quiebre estructural. Para tener una alerta frente a un posible cambio estructural se establece un monitoreo mediante una función CUSUM, la cual, al realizarla retrospectivamente, tardó 5 meses en levantar la alerta.

### Parte 3

* Esta parte muestra un proceso mensual de monitoreo. Al momento de ejecutar este monitoreo solo se contaba con tres observaciones, por lo que los resultados deben tomarse con precaución. Aun así, se puede concluir preliminarmente que:

  * El RMSE del ECM se mantiene dentro del rango esperado según el backtest histórico y presenta un bias reducido. Sin embargo, en las tres observaciones disponibles no supera al benchmark Random Walk.

  * Al ejecutar el monitoreo mediante CUSUM este no se dispara y, al realizar una búsqueda de quiebres estructurales en el ECT, no se detecta ninguno, con excepción de dos candidatos que caen dentro de la ventana pandémica omitida para el desarrollo del ECM.

## Limitaciones

* El modelo utiliza únicamente el desempleo como variable macroeconómica explicativa. Esto facilita su interpretación, pero no captura todos los factores que pueden influir en la morosidad.

* El periodo de pandemia presenta un comportamiento excepcional en ambas series, por lo que fue excluido de determinadas etapas de estimación. Esta decisión debe considerarse al interpretar los resultados.

* La identificación de quiebres estructurales requiere acumular nuevas observaciones. En el backtesting en tiempo real, el quiebre identificado retrospectivamente presentó un retraso considerable en su detección.

* La validación out-of-time disponible actualmente cuenta con solo tres observaciones, por lo que los resultados deben considerarse preliminares.

* Los resultados representan relaciones estadísticas y predictivas entre desempleo y morosidad y no deben interpretarse como evidencia causal.

## Estructura del repositorio

- `01_EDA_Mora_Consumo_desempleo.ipynb`: análisis exploratorio y cointegración.
- `02_modelamiento_desmpleo_mora.ipynb`: desarrollo del ECM y backtesting.
- `03_Validacion.ipynb`: validación out-of-time y monitoreo.
- `mora_desempleo_202604.csv`: snapshot usado para desarrollo.
- `mora_desempleo_202607.csv`: actualización usada para validación.

## Requerimientos

Las dependencias necesarias se encuentran en `requirements.txt`.

```bash
pip install -r requirements.txt


## Autor

Arturo Díaz Salgado  
[LinkedIn](https://www.linkedin.com/in/arturodiazsalgado/)