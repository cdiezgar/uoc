# PEC4 - Análisis de datos y creación de un proyecto real

## 1. Descripción
El presente código presenta una aplicación para el análisis de la distribución de licencias, armas de mano y armas
largas en los diferentes estados de EEUU. El proyecto está inicialmente indicado para los estados de eeuu en el 2014,
pero podría utilizarse para otros años adaptando ligeramente el código y los dataset de entrada, con algunas restricciones.



## 2. Instalación:
Para la instalación del proyecto lo primero es descomprimir la carpeta pec4_diezgarcia.tar.gz desde una consola de
linux con el comando:

    tar -xzf pec4_diezgarcia.tar.gz

Una vez descomprimido el proyecto, es necesario navegar a la ruta donde se encuentre el proyecto ya descomprimido.
Por ejemplo si se encuentra en la ruta "/PycharmProyects"

Habrá que navegar con:

    cd /PycharmProyects/pec4_diezgarcia/

Una vez dentro, para que el proyecto pueda ejecutarse con normalidad, es necesario instalar las dependencias requeridas
con el comando:

    pip install -r requirements.txt



## 3. Configuración inicial:
Se sirve un fichero config.properties donde se han establecido algunas propiedades globales para la aplicación. Pueden
dejarse como están o modificarse si se desea:

    test_path: indica la ruta donde se encuentran los tests a ejecutar.
    data_path: indica la ruta desde la que se empiezan a localizar los ficheros csv cuando son leídos. Si no
        se especifica ruta absoluta en la función de lectura se buscará desde la ruta definida aquí.
    html_path: indica la ruta donde se guardarán los ficheros html generados
    figures_path: indica la ruta donde se guardarán las imágenes generadas.
    folium_example_url: indica la url de folium desde la que se descargarán los mapas
    key_on: especifica el parametro del json que se utiliza para mixear los mapas con los datos del dataset y poder construir el mapa coropletico correctamente

Además, para poder acceder a la funcionalidad de generación de las imágenes png de los mapas coropléticos, es necesario
disponer del navegador Firefox y de un GeckoDriver compatible. La lista de compatibilidades se puede consultar aquí:

    https://firefox-source-docs.mozilla.org/testing/geckodriver/Support.html

Una vez descargado un driver compatible, debe alojarse en una ruta conocida y después configurarse la siguiente
variable:

    gecko_driver_path: ruta donde se encuentra el driver de gecko utilizado
    auto_detect_firefox: si está en True, el webdriver buscará automáticamente la ruta. Si se especifica en False,
        se buscará el firefox en la ruta definida en firefox_path

Si no se consigue capturar la imagen png correctamente, es posible alterar los parámetros:

    zoom: especifica el zoom inicial
    driver_delay: especifica el tiempo de renderizado del mapa


Para cambiar los parámetros, es posible utilizar primero el comando de visualización

    cat config.properties

Y después el de edition. Por ejemplo, para alterar la propiedad test_log:

    sed -i 's/test_log=True/test_log=False/' config.properties




## 4. Ejecución
La función principal main ejecuta secuencialmente todas las instrucciones necesarias para el análisis, y finaliza
elaborando una serie de mapas coropléticos que se guardan tanto en formato png como html para su utilización (ver
apartado configuración).

La manera de ejecutarlo es accediendo desde la terminal a la ruta del proyecto:

    cd /PycharmProyects/pec4_diezgarcia/

Y mediante el comando:

    python3 main.py

Hay 2 puntos en los que se solicitarán como inputs las rutas de los ficheros csv:
	1. Al comienzo del codigo para cargar el dataset con las columnas 'month', 'state', 'permit', 'handgun' y 'long_gun'
	2. A la hora de calcular los valores relativos, se solicitará otro dataset al menos con las columnas 'state' y 'pop_14'




## 5. Tests
Se han definido en la ruta tests las pruebas unitarias test_commons.py, test_fich.py y test_process.py para la
verificación de las funcionalidades implementadas. Estas pruebas no hacen comprobaciones sobre el conjunto de datos
cargado sino que son pruebas unitarias que comprueban el correcto funcionamiento de cada módulo de forma individual,
asegurando una cobertura del código del 69%.

Desde la ruta origen del proyecto, pueden ejecutarse todos los test con:

        coverage run -m unittest discover -s tests/

        coverage report

Por comodidad se ha deshabilitado la salida de los prints al ejecutar los Tests desde el config.properties, con la
variable:

    tests_log=False

No obstante, si se desea puede habilitarse esta variable para ver la salida.



## 6. Estructura del código

El código se ha estructurado de forma modular en los paquetes:

    -fich, que incluye funciones necesarias para la lectura de ficheros csv, carga, y limpieza de datos:

	a) read_csv: lee el fichero csv especificado y devuelve un df
	b) clean_csv: elimina del dataframe proporcionado las columnas especificadas
	c) rename_cols: renombra en el dataframe proporcionado las columnas por los nuevos nombres especificados en el diccionario
	d) es_ruta_absoluta: comprueba si la ruta proporcionada es absoluta o relativa
	e) es_csv: comprueba si el fichero proporcionado acaba en .csv
	f) existe_csv: comprueba si existe el fichero indicado
	g) mostrar_info_basica: muestra la información básica del dataframe proporcionado así como las n primeras filas indicadas
	h) existe_la_columna: comprueba si en la lista de columnas indicadas existe la columna específica

    -process, que se encarga del tema de análisis de datos y manipulación de los mismos:

	a) breakdown_date: del dataframe proporcionado, convierte la columna month en formato aaaa-mm en dos columnas: year y month, eliminando los datos que no coinciden con el formato.
	b) erase_month: elimina especificamente la columna month del dataframe proporcionado.
	c) group_by_state_and_year: agrupa el dataframe proporcionado según las columnas "year" y "state"
	d) group_by_year: agrupa el dataframe proporcionado según la columna "year"
	e) group_by_state: agrupa el dataframe proporcionado según la columna "state"
	g) print_biggest_longguns: escribe en consola el estado y año con mas armas largas
	h) print_biggest_handguns: escribe en consola el estado y año con mas armas de mano
	i) merge_datasets_on_state: fusiona los 2 dataframes proporcionados según la columna "state"
	j) calculate_relative_values: calcula los valores relativos a las poblaciones totales de las columnas "permit", "hand_gun", "long_gun"
	k) clean_states: elimina del dataframe proporcionado la lista de estados indicada
	l) promedio: calcula el promedio de la columna espeficiada en el dataframe proporcionado
	m) info_estado: muestra toda la información de los estados en el df proporcionado
	n) replace_value_in_column_for_state: reemplaza el valor de la columna para el estado especificado, del df proporcionado por el nuevo valor
	o) validar_formato_month: valida que el mes esté en el formato correcto
	p) fila_max_columna: devuelve la fila del dataframe en la que se encunetra el máximo valor para la columna especificada

    -graphics, cuya función es pintar la evolución temporal de algunas columnas de datos:

	a) time_evolution: dibuja el grafico temporal de permit, handgun y longgun
	b) dibuja_evolucion_temporal: configura y dibuja el grafico temporal del dataframe proporcionado y para el diccionario {columna-etiqueta} especificados

    -coropléticos, encargada de generar los mapas de colores de acuerdo a los porcentajes de las variables:

	a) genera_mapa_coropletico: genera los htmls con los mapas coropleticos segun los estados y columnas especificadas y devuelve un listado con las rutas de los htmls para poderlos utilizar para generar imagenes
	b) genera_imagenes_mapas_coropleticos: genera las imagenes segun las rutas de los htmls indicadas en .png
	c) generate_png_from_map: configura el gecko y selenium para renderizar el html y generar el png.

    -commons, que incluye algunas funciones comunes como calculos, comprobaciones rutinarias...



## 7. Licencia
Este proyecto está licenciado bajo la licencia MIT - ver fichero LICENSE.txt