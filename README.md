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
