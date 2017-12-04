## Mapeo y Análisis de Datos de Víctimas del Conflicto Colombiano - Conferencia Build Peace, 2017

Estas son las notas para el taller de mapeo y análisis de datos de víctimas del conflicto colombiano, dictado durante la conferencia [Build Peace](http://howtobuildpeace.org/) en la Universidad de Los Andes, Bogotá, Diciembre 2017. En este taller usaremos Python y QGIS para analizar y mapear datos del Registro Único de Víctimas.

### Software
En este taller utilizaremos Python y QGIS. Python es un lenguaje de programación muy popular para el manejo y análisis de datos. QGIS es un programa de sistemas de información geográfica. Ambos son gratis y open-source.
* **Python**:
  * Los computadores Mac ya vienen con Python. Sin embargo, en este taller vamos a utilizar una versión más reciente y una plataforma llamada Jupyter Notebooks. Tanto para los Mac como para los computadores Windows, es recomendable instalar Python y Jupyter Notebooks a través de Anaconda.
  * Para instalar Anaconda deben ir a las siguientes páginas y seguir las instrucciones, incluido las pruebas para verificar la instalación:
    * [Windows](https://conda.io/docs/user-guide/install/windows.html)
    * [Mac](https://conda.io/docs/user-guide/install/macos.html)
    * [Linux](https://conda.io/docs/user-guide/install/linux.html)
  * Una de las gran ventajas de instalar Python a través de Anaconda es que en esta instalación están incluidas las bibliotecas Pandas, NumPy y SciPy, las cuales son clave para el análisis de datos.
* **QGIS**:
  * QGIS no es el programa para mapas más reconocido o desarrollado. Este sería ArcGIS. Sin embargo, ArcGIS sólo funciona en computadores Windows y no es ni open-source ni gratis.
  * QGIS, a pesar de que no es tan avanzado, funciona bien para tareas simples de mapeo y análysis geográfico.
  * Para instalar QGIS, vayan a la [página de instalación](http://download.qgis.org/en/site/forusers/download.html) y escojan la versión correspondiente a su sistema operativo.
  * Si están trabajando en un Mac, asegúrense de instalar todos los componentes incluidos en el paquete de instalación (GDAL, NumPy, Matplotlib y QGIS), así ya tengan algunos de esos programas instalados.

### Datos
En este taller vamos a utilizar los siguientes datos:
* [Número de Personas por Lugar de Ocurrencia - Hecho Victimizante, Genero, Ciclo Vital, Discapacidad, Pertenencia Etnica y Año Ocurrencia](https://rni.unidadvictimas.gov.co/RUV): Datos del Registro Único de Víctimas sobre las víctimas del conflicto colombiano.
* [Estimación y proyección de población nacional, departamental y municipal total por área 1985-2020](http://www.dane.gov.co/index.php/estadisticas-por-tema/demografia-y-poblacion/proyecciones-de-poblacion): Datos del Departamento Nacional de Estadísticas sobre la población de cada municipio en 2005 y las proyecciones hasta el 2020.

### Importar y Explorar los Datos
En esta primera parte del tallver vamos a importar los datos no geográficos al Jupyter Notebook para hacer una exploración inicial. Luego los filtraremos y los exportaremos listos para mapear.
* Primero navegen hacia el folder donde guardaron los datos e inicien una sesión de Jupyter Notebooks:
  * Esto lo pueden hacer en Mac a través del Terminal navegando hasta el folder y escribiendo el comando: `jupyter notebook`
  * O en Windows y en Mac abriendo la aplicación de Anaconda e iniciando ahí un Jupyter Notebook.
  * En cualquiera de los dos casos, una ventana nueva de su navegador (Chrome, Safari o Firefox) se abrirá con el menú principal de Jupyter Notebooks.
* En el menú principal de Jupyter Notebooks, inicien un nuevo Python 3 Notebook (`New`, `Python 3`).
* Lo primero que hay que hacer es importar las bibliotecas adecuadas para poder leer y procesar los datos:
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
plt.style.use('ggplot')
%matplotlib inline
print('Done importing libraries...')
```
* Luego debemos importar la base de datos de víctimas y convertirla a un `dataframe` de Pandas:
  * Uno de los problemas que vamos a tener es que el archivo de Excel que bajamos del Registro Único de Víctimas tiene dos hojas y la segunda, no tiene títulos en las columnas.
  * Por eso debemos importar las dos hojas por separado, anotando que la segunda no tiene títulos, para luego asignarle títulos a la segunda y unirl las dos hojas en un solo dataframe.
  * Importar la primera hoja de Excel:
  ```python
  datosVictimas = pd.read_excel('datos/Reporte Ocurrencia Hecho Victimizante.xlsx', sheet_name='Exportar Hoja de Trabajo')
  datosVictimas.head()
  ```
  * `head()` imprime las primeras 10 lineas del `dataframe`. O también se puede usar `datosVictimas.shape` para saber cuantas filas y cuantas columnas tiene el `dataframe`.
  * Importar la segunda hoja de Excel (anotando que no tiene nombres de columnas):
  ```python
  datosVictimas2 = pd.read_excel('datos/Reporte Ocurrencia Hecho Victimizante.xlsx', sheetname='Sheet1', header=None)
  datosVictimas2.head()
  ```
  * Sacar los nombres de las columnas de la primera hoja y asignárselos a la segunda hoja:
  ```python
  nombresColumnas = list(datosVictimas)
  datosVictimas2.columns = nombresColumnas
  datosVictimas2.tail()
  ```
  * Adicionarle la segunda hoja a la primera:
  ```python
  datosVictimas.append(datosVictimas2)
  ```
* Una vez tenemos el `dataframe` con todos los datos podemos empezar a explorarlos y a sacar estadísticas:
  * Por ejemplo, cuántos reportes hay de cada crimen: `datosVictimas.groupby('HECHO VICTIMIZANTE').count()`
  * O cuántas víctimas hubo en cada año y ordenarlo de mayor a menor: `datosVictimas.groupby('ANIO OCURRENCIA').count().sort_values('TIPO DE VICTIMA', ascending=False)`
  * O cuántos municipios fueron afectados: `len(datosVictimas['DANE OCURRENCIA'].unique())`
  * También podemos averiguar cuántos municipios estuvieron afectados por desplazamiento en cada año:
  ```python
  for x in range(1985,2018):
    thisData = datosVictimas[(datosVictimas['ANIO OCURRENCIA'] == x) & (datosVictimas['HECHO VICTIMIZANTE'] == 'Desplazamiento')]
    print(str(x) + ' - ' + str(len(thisData['DANE OCURRENCIA'].unique())))
  ```

### Filtrando y Seleccionando Datos
Ahora vamos a crear un `dataframe` nuevo, sólo con los datos que vamos a mapear. En este caso vamos a seleccionar sólo datos de desplazamiento forzado del año 2005 y le vamos a agregar los datos de población de DANE.
* Primero creemos un `dataframe` nuevo con los datos que queremos:
```python
desplazamiento2005 = datosVictimas[(datosVictimas['ANIO OCURRENCIA'] == 2002) & (datosVictimas['HECHO VICTIMIZANTE'] == 'Desplazamiento')]
desplazamiento2005
```
* Ahora, seleccionemos sólo las columnas que queremos (`DANE OCURRENCIA` y `TOTAL`) y agrupemoslos por municipio, sumando las víctimas:
```python
desplazamiento2005 = desplazamiento2005[['DANE OCURRENCIA', 'TOTAL']]
desplazamientoMunicipio2005 = desplazamiento2005.groupby(['DANE OCURRENCIA']).sum()
desplazamientoMunicipio2005.sort_values('TOTAL', ascending=False)
```
* Para importar los datos de población de los municipios utilizamos la misma fórmula que usamos para importar los datos de las víctimas, sólo que tenemos que ignorar algunas filas al comienzo y algunas filas al final:
```python
datosMunicipios = pd.read_excel('datos/Municipal_area_1985-2020.xls', sheetname='Mpios', skiprows=9, skip_footer=14)
datosMunicipios
```
* Ahora, miremos qué columnas tiene este archivo y seleccionemos sólo las que nos interesan:
```python
list(datosMunicipios)
```
```python
datosMunicipios = datosMunicipios[['DPNOM', 'DPMP', 'MPIO', 2005]]
datosMunicipios
```
* Finalmente, vamos a unir los dos `dataframes`, basados en la columna `DANE OCURRENCIA` del `dataframe` de las víctimas y `DPMP` del `dataframe` de los municipios. Sin embargo, primero debemos asegurarnos de que ambos `dataframes` tengan las columnas organizadas y sean del mismo tipo de datos.
* Primero adicionemosle un índice al `dataframe` de las víctimas: `desplazamientoMunicipio2005 = desplazamientoMunicipio2005.reset_index()`
* Ahora revisemos el tipo de datos de ambos `dataframes`: `desplazamientoMunicipio2005.dtypes` y `datosMunicipios.dtypes`.
* Aquí nos damos cuenta que la columna `DANE OCURRENCIA` es de tipo `object`. Esto es porque hay unas filas en las que en vez del número del municipio dice `Sin Informacion`. Para poder cambiar el tipo de datos a `int64` y que se pueda unir con los datos del municipio debemos eliminar esas filas: `desplazamientoMunicipio2005 = desplazamientoMunicipio2005[desplazamientoMunicipio2005['DANE OCURRENCIA'] != 'Sin Informacion']`
* Ahora podemos cambiar el tipo de datos de esa columna: `desplazamientoMunicipio2005['DANE OCURRENCIA'] = desplazamientoMunicipio2005['DANE OCURRENCIA'].astype(int)`
* Y finalmente unir ambos `dataframes`:
```python
datosDesplazamiento = pd.merge(desplazamientoMunicipio2005, datosMunicipios, left_on='DANE OCURRENCIA', right_on='DPMP')
datosDesplazamiento
```
* Podemos ordenar los datos por el municipio con mayor desplazamiento: `datosDesplazamiento.sort_values('TOTAL', ascending=False)`
* Y debemos seleccionar sólo las columnas que necesitemos:
```python
datosDesplazamiento = datosDesplazamiento[['DANE OCURRENCIA', 'TOTAL', 'DPNOM', 'MPIO', 2005]]
datosDesplazamiento
```
* Y por último, exportamos el `dataframe` listo para mapear: `datosDesplazamiento.to_csv('datosDesplazamiento.csv')`
