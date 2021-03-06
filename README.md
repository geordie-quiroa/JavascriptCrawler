# Python Crawler
> Este sistema es un crawler que permite indexar y buscar objetos en Python (versión > 3.5). Esto lo hace a través de la inspección del código fuente e identificación de relaciones entre archivos utilizando las expresiones  ```import``` en el código, con el fin de ubicar los demás archivos que serán parseados y crear un path de recorrido en el grafo. Luego de parsear el código fuente, se almacenan las variables, funciones y clases identificadas en una base de datos Mongo, para luego facilitar las consultas personalizadas de la data. 

## Instalación e implementación
### Requerimientos
* Docker (Linux, MacOS, Docker for Windows ToolBox, etc.)

### Librerías utilizadas
* Pygments: Lexer for Python
* Pymongo

Clonar el repositorio, o el tag release más reciente:

`$ git clone https://github.com/Jotade100/JavascriptCrawler.git`

Acceder al directorio clonado:

`$ cd JavascriptCrawler/`

Agregar en este directorio los archivos y carpetas python a inspeccionar con el crawler:

`$ cp -R path_to_sourceFiles JavascriptCrawler/`

Modificar en el archivo de configuración `config.yml`, el valor de la llave `archivo` con el nombre del archivo python que se va a utilizar como el archivo root: (el archivo root debe estar a nivel del directorio Ej. JavascriptCrawler/nombreArchivo.py )

`$ nano config.yml`

`archivo: nombreArchivo.py`  

(Guardar los cambios realizados)

Construir imagen a partir del Dockerfile en el directorio JavascriptCrawler/:

`docker image build -t algoritmia2:0.0 .`

Levantar el container a partir de la imagen con nombre `pythonCrawler`:

`docker run -i -t algoritmia2:0.0 bash`

En este punto, el programa inicia con el parseo del código fuente especificado, almacenando la información útil en la base de datos y continua con el recorrido de los demás archivos utilizando los imports.

## Información de la Base de Datos (MongoDB)

* Nombre de la base de datos: pythonParser
* ip address: localhost
* puerto: 27017

### Colecciones
> Las colecciones son el equivalente de las tablas utilizadas en las RDBs, y almacenan objetos (documentos) que comparten características o atributos entre sí.

PythonCrawler utiliza una base de datos Mongo, en la que almacena los objetos relevantes parseados en cada uno de los archivos del grafo. Los objetos los almacena en distintas colecciones dependiendo de su naturaleza (variables, clases o funciones). Para acceder a estos datos, la información relevante se detalla a continuación:

#### Detalles de cada colección

##### Nombre de colección en db: `classes`

Las llaves de los objetos en esta colección son las siguientes:

* `className` : nombre de la clase registrada.
* `definedAt` : archivo en el cual se definió dicha clase.
* `inheritsClass` : clase heredada por la clase.
* `isParentClass` : indica (0 , 1) si la clase es clase padre.

##### Nombre de colección en db: `variables`

Las llaves de los objetos en esta colección son las siguientes:

* `variable` : nombre de la variable registrada.
* `declaredAt` : nombre del archivo en el que se definió la variable.

##### Nombre de colección en db: `functions`

Las llaves de los objetos en esta colección son las siguientes:

* `function` : nombre de la función registrada.
* `params` : nombre de los parametros que recibe la función.
* `createdAt` : nombre del archivo en el que se creó la función.
* `paramsNumber` : cantidad de parámetros que recibe la función.

## Queries básicos
En esta sección se definen los queries básicos para la base de datos con nombre `pythonParser`. Estos queries permiten tener noción de cómo navegar y realizar consultas básicas en la base de datos, para que, posteriormente, pueda realizar consultas más avanzadas con los datos en las distintas colecciones disponibles.

En la consola de MongoDb, seleccionar la base de datos correcta:

`use pythonParser`

### Seleccionar todos los `documentos` registrados, por el crawler, en una colección específica:

`db.coleccion.find( {} ).pretty()`

#### Ejemplos:

* `db.variables.find( {} ).pretty()` Encuentra todas las `variables`, con los atributos mencionados anteriormente, registradas por el crawler.
* `db.classes.find( {} ).pretty()` Encuentra todas las `clases`, con los atributos mencionados anteriormente, registradas por el crawler.
* `db.functions.find( {} ).pretty()` Encuentra todas las `funciones`, con los atributos mencionados anteriormente, registradas por el crawler.

### Seleccionar todos los `documentos` que tienen, en sus atributos, el valor especificado:

`db.coleccion.find( { atributo: valor } ).pretty()`

#### Ejemplos:

* `db.functions.find( { paramsNumber: 1 } ).pretty()` Encuentra todas las `funciones` que aceptan un sólo parámetro.
* `db.variables.find( { declaredAt: "testFile.py" } ).pretty()` Encuentra todas las `variables` definidas en el archivo `testFile.py`.
* `db.classes.find( {inheritsClass:"data"} ).pretty()` Encuentra todas las `clases` que heredan la clase `data`.

Para queries más avanzados, puede consultar el [manual de queries](https://docs.mongodb.com/manual/tutorial/query-documents/) de MongoDB Shell.
