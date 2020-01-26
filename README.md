# Práctica Docker Compose

**Enlace a github pages** 

https://sanesga.github.io/practica_docker_compose.github.io/

***

![logo](./img/logo.png)

***

**Docker Compose** es una herramienta para definir y ejecutar las aplicaciones Docker de múltiples contenedores. Su funcionamiento se basa en una plantilla (docker-compose.yml). Dicha plantilla, es un fichero YAML que contiene la configuración necesaria para crear cada contenedor y cuya estructura y variables utilizadas en esta práctica, vemos a continuación:

```
version: "3"

services:

 nombreDelServicio:
  image:
  container_name:
  environment:
  ports:
  volumes:
  restart:
  networks:
  depends_on:
  healthcheck:
   test:
   interval:
   timeout:
   retries:
```

- **version**: Especifica la versión del fichero de docker-compose que se está utilizando. En función de la versión, la sintaxis del fichero, puede cambiar. Este es el listado de versión disponibles a fecha de hoy:

  ![](./img/captura21.png)

  Nosotros utilizaremos la versión 3.

- **services**: Contiene cada contendor con sus variables de configuración.

- **nombreDelServicio**: Nombre del servicio que vamos a crear.

- **image**: Nombre de la imagen y tag que queremos descargar y/o utilizar (si ya la tenemos descargada) para crear el contenedor.

- **container_name**: Nombre que le asignaremos al contenedor creado.

- **environment**: Las variables de entorno del contenedor que se va a crear. 

- **ports**: Ejemplo: "3307:3306", a la izquierda se especifica el puerto por el que escucha la máquina y a la derecha por donde se expone el contenedor (puede ser el mismo). Podemos especificar tantas parejas de puertos como necesitemos.

- **volumes**: Permite crear un volumen para el contenedor. Un contenedor puede tener varios volúmenes y este/os volúmen/es pueden ser compartidos por varios contenedores. Ejemplo de sintaxis: - ./wordpressVol:/var/www/html. A la izquierda especificamos la ruta donde queremos crear el volumen en el host y a la derecha la ruta de los archivos a mapear del contenedor.

- **restart**: Establece la política de reinicio del servicio en caso de que este se detenga. Puede adquirir los valores: 

  - no: Valor por defecto. Si el servicio se detiene, no se reiniciará.
  - always: Se reiciniciará siempre.
  - on-failure: Se reiniciará si se detiene debido a un fallo.
  - unless-stopped: Se reiniciará siempre, a menos que se haya detenido de manera intencionada.

- **networks**: Permite especificar nuestra propia red para que pueda ser utilizada por los distintos contenedores.

- **depends_on**: Permite especificar una dependencia entre servicios.

- **healthcheck**: Se trata de un test para verificar que el contenedor esté arrancado. Valores:

  - test: El test que se realizará sobre el contenedor para saber si está correctamente arrancado.
  - interval: El intervalo de tiempo entre dos healthchecks. El valor por defecto son 30 segundos.
  - timeout: El límite de tiempo para ejecutar el test. El valor por defecto son 30 segundos.
  - retries: Las veces que se reintentará realizar el test si este falla. El valor por defecto son 5 segundos.

***

**COMANDOS ÚTILES**

Lanzar los servicios:

```
sudo docker-compose up
```

Parar todos los contenedores:

ctr+c

Borrar todos los contenedores parados:

```
sudo docker container prune
```

Borrar todos los volúmenes:

```
sudo docker volume prune
```

Ver todos los volúmenes:

```
sudo docker volume ls
```

***

**OBJETIVO DE LA PŔACTICA**

Crear un sistema de monitorización de losg en Graylog para un WordPress

***

Creamos un directorio nuevo para albergar la práctica en la ruta /home/sandra/Escritorio

```
sudo mkdir practica_docker_compose.github.io
```

Creamos el archivo docker-compose.yml en el directorio:

```
sudo touch docker-compose.yml
```

![](./img/captura22.png)

Y damos permisos a la carpeta y a todo su contenido:

```
sudo chmod 777 -R practica_docker_compose.github.io
```

A continuación iremos editando el archivo, añadiendo los servicios necesarios y las variables de configuración especificadas en el enunciado de la práctica.


**1. ELASTIC SEARCH**

<p align="center">
<img src="./img/logo2.png" alt="elastic_search_logo" style="width:300px;"/>
</p>

Añadimos al fichero docker-compose.yml la configuración para Elasticsearch. **Elasticsearch** es un motor open source de analítica y análisis distribuido para todo tipo de datos. Por su capacidad de indexar muchos tipos de contenido, es utilizado en búsquedas, analíticas de log, monitoreo de rendimiento, etc.

  ![](./img/captura23.png)

Elasticsearch utiliza un directorio mmapfs para guardar sus índices. Por defecto el sistema operativo tiene el límite de memoria virtual de mmap demasiado bajo y si no lo incrementamos, nos aparecerá un error de falta de memoria, por lo tanto, seguiremos estos pasos para aumentar este límite en Linux:

Modificamos el archivo sysctl.conf ubicado en el directorio /etc, añadiendo la siguiente línea: 

```
vm.max_map_count=262144
```

![](./img/captura25.png)

Y una vez añadida, recargamos la configuración:

```
sudo sysctl -p
```

Una vez hecho esto, ya podemos lanzar nuestro contenedor. Desde terminal vamos a la carpeta donde se encuentra el archivo docker-compose.yml y ejecutamos:

```
sudo docker-compose up
```

Si nos aparece el siguiente error: 

```
org.elasticsearch.bootstrap.StartupException: java.lang.IllegalStateException: Failed to create node environment
```

tendremos que darle permisos al volumen creado en el directorio de la práctica.


Verificamos que el container se ha creado y está funcionando correctamente:

```
sudo docker ps -a
```

![](./img/captura24.png)

Podemos observar que el estado del container es healthy, porque ha superado el healthcheck.

Si vamos a localhost:9200, verificamos que está funcionando:

![](./img/captura25.png)

Verificamos que se ha creado el volumen:

![](./img/captura26.png)

***

**2. MONGO DB**

<p align="center">
<img src="./img/logo3.png" alt="mongo_logo" style="width:300px;"/>
</p>

Añadimos al fichero docker-compose.yml la configuración para mongoDB. **MongoDB** es un sistema de base de datsos NoSQL orientado a documentos de código abierto.

  ![](./img/captura27.png)


Paramos el contenedor anterior con ctrl+c y volvemos a ejecutar el comando:

```
sudo docker-compose up
```

Verificamos que el container se ha creado y está funcionando correctamente:

```
sudo docker ps -a
```

![](./img/captura28.png)

Podemos observar que el estado del container es healthy, porque ha superado el healthcheck.

Si vamos a localhost:27017, verificamos que está funcionando:

![](./img/captura29.png)

Podemos acceder interactivamente al container ejecutando los siguiente comandos:

```
sudo docker exec -it mongo
```

Una vez hemos accedido:

```
show databases;
```
![](./img/captura31.png)


Verificamos que se ha creado el volumen:

![](./img/captura30.png)

***

**3. GRAYLOG**

<p align="center">
<img src="./img/logo4.png" alt="graylog_logo" style="width:300px;"/>
</p>

Añadimos al fichero docker-compose.yml la configuración para graylog. **Graylog** es una plataforma que permite la gestión de registros de datos estructurados y no estructurados junto con aplicaciones de depuración. Se basa en Elasticsearch, MongoDB y Scala.

  ![](./img/captura33.png)

MODIFICARRRR--------------

hacemos este comando para obtener el SHA

echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1

nos pide la contraseña

admin

y lo copiamos en el docker

MODIFICARRRRR------------

Paramos los contenedores anteriores con ctrl+c y volvemos a ejecutar el comando:

```
sudo docker-compose up
```

Verificamos que el container se ha creado y está funcionando correctamente:

```
sudo docker ps -a
```

![](./img/captura32.png)


Si vamos a localhost:9000, verificamos que está funcionando:

![](./img/captura4.png)

Hacemos login con el usuario admin y la contraseña admin:

![](./img/captura5.png)


Verificamos que se ha creado el volumen:

![](./img/captura34.png)

***

**4. MYSQL**

<p align="center">
<img src="./img/logo5.png" alt="mongo_logo" style="width:300px;"/>
</p>

Añadimos al fichero docker-compose.yml la configuración para MySQL. **MySQL** es un sistema de gestión de bases de datos relacional.

  ![](./img/captura35.png)


Paramos el contenedor anterior con ctrl+c y volvemos a ejecutar el comando:

```
sudo docker-compose up
```

Verificamos que el container se ha creado y está funcionando correctamente:

```
sudo docker ps -a
```

![](./img/captura36.png)

Podemos observar que el estado del container es healthy, porque ha superado el healthcheck.

Para verificar que está funcionando, accedemos al container interactivamente a través de los siguientes comandos:

```
sudo docker exec -it mysql mysql -p
```

Nos pide la contraseña que hemos indicado en la variable de entorno MYSQL_ROOT_PASSWORD (12345678).

Una vez hemos accedido:

```
show databases;
```

Y observamos que se ha creado la base de datos wordpress:

![](./img/captura37.png)


Verificamos que se ha creado el volumen:

![](./img/captura2.png)

***


**5. WORDPRESS**

<p align="center">
<img src="./img/logo6.png" alt="wordpress_logo" style="width:300px;"/>
</p>

Añadimos al fichero docker-compose.yml la configuración para Wordpress. **WordPress** es una plataforma para la creación de blogs, tiendas online y sitios web en general.

  ![](./img/captura3.png)

Paramos los contenedores anteriores con ctrl+c y volvemos a ejecutar el comando:

```
sudo docker-compose up
```

Verificamos que el container se ha creado y está funcionando correctamente:

```
sudo docker ps -a
```

![](./img/captura38.png)


Si vamos a localhost:9090, verificamos que está funcionando:

![](./img/captura6.png)

Tras elegir el idioma, rellenamos el siguiente formulario:

![](./img/captura7.png)

Nos informa que WordPress se ha instalado correctamente:

![](./img/captura8.png)

Accedemos al sitio con el usuario y la contraseña que hemos indicado en el formulario anterior (wordpress, wordpress).

![](./img/captura10.png)

Y nos muestra el nuestro sitio de prueba:

![](./img/captura11.png)

Para verificar que está correctamente conectado a nuestra base de datos mysql, vamos a Herramientas -- Salud del sitio -- Información -- Base de datos y obtenemos toda la información de nuestra base de datos:

![](./img/captura12.png)


Verificamos que se ha creado el volumen:

![](./img/captura9.png)

***


**6. VERIFICAR QUE LOS LOGS DE WORDPRESS SE MUESTRAN EN GRAYLOG**

Añadimos un input global de tipo gelf udp en Greylog:

Vamos a la página de Greylog: localhost:9000

Tras acceder, en el menú de la página principal, accedemos a System -- Inputs y seleccinamos el input GELF UDP:

![](./img/captura13.png)

Rellenamos el formulario:

Hacemos check en global, añadimos un título y cambiamos el puerto. El puerto que especificamos debe ser el mismo que hemos indicado en el servicio de Graylog como UDP y el mismo que hemos indicado para la configuración de gelf-address en Wordpress en nuestro docker-compose.yml.

![](./img/captura14.png)

Se nos muestra el input creado:

![](./img/captura15.png)

A continuacióń vamos a la página de Search y observamos que no tenemos logs:

![](./img/captura16.png)

Sin embargo, cuando accedemos a WordPress (localhost:9090), actualizamos la página y podemos ver los logs creados:

![](./img/captura17.png)

En el menú de la izquierda, podemos filtrar los logs según queramos ver más información o menos:

![](./img/captura18.png)

En la parte superior podemos ver el histograma de logs:

![](./img/captura19.png)

***

**DOCKER-COMPOSE.YML FINAL**

```
version: "3"

services:

#Elasticsearch
 elasticsearch:
  image: docker.elastic.co/elasticsearch/elasticsearch-oss:6.8.2
  container_name: elasticSearch
  environment:
   - http.host=0.0.0.0
   - network.host=0.0.0.0
   - transport-host=0.0.0.0
   - "ES_JAVA_OPTS=-Xmx256m -Xms256m"
  ports:
   - "9200:9200"
   - "9300:9300"
  volumes:
   - ./elasticVol:/usr/share/elasticsearch/data
  networks:
   - practica_network
  healthcheck:
   test: ["CMD", "curl", "-f", "http://0.0.0.0:9200"]
   interval: 30s
   timeout: 10s
   retries: 5 
  ulimits:
   memlock:
     soft: -1
     hard: -1

#Mongo
 mongo:
  image: mongo:3
  container_name: mongo
  volumes:
   - ./mongoVol:/data/db
  ports:
   - "27017:27017"
  networks:
   - practica_network
  healthcheck:
   test: echo 'db.runCommand("ping").ok' | mongo mongo:27017/test --quiet
   interval: 30s
   timeout: 10s
   retries: 5 

#Graylog
 graylog:
  image: graylog/graylog:3.1
  container_name: graylog
  environment:
   - GRAYLOG_PASSWORD_SECRET=graylogpasswordsecret
   - GRAYLOG_ROOT_PASSWORD_SHA=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
   - GRAYLOG_HTTP_EXTERNAL_URI=http://127.0.0.1:9000/
  volumes:
   - ./grayVol:/usr/share/elasticsearch/data
  restart: on-failure
  networks:
   - practica_network
  ports:
   - "9000:9000"
   - "12201:12201/udp"
   - "12201:12201"
   - "1514:1514"
   - "1514:1514/udp"
  depends_on:
   - elasticsearch
   - mongo

 #Mysql
 mysql:
  image: mysql:5.7
  container_name: mysql
  volumes:
   - ./mysqlVol:/var/lib/mysql
  environment:
   MYSQL_ROOT_PASSWORD: 12345678
   MYSQL_DATABASE: wordpress
   MYSQL_USER: wordpress
   MYSQL_PASSWORD: wordpress
  networks:
   - practica_network
  healthcheck:
   test: "/usr/bin/mysql --user=wordpress --password=wordpress --execute \"SHOW DATABASES;\""
   interval: 30s
   timeout: 10s
   retries: 5

 #Wordpress
 wordpress:
  image: wordpress:latest
  container_name: wordpress
  volumes:
   - ./wordpressVol:/var/www/html
  restart: on-failure
  environment:
   WORDPRESS_DB_HOST: mysql:3306
   WORDPRESS_DB_USER: wordpress
   WORDPRESS_DB_PASSWORD: wordpress
   WORDPRESS_DB_NAME: wordpress
  networks:
   - practica_network
  ports:
   - "9090:80"
  logging:
   driver: "gelf"
   options:
    gelf-address: "udp://localhost:12201"
    tag: "primeros_logs"
  depends_on:
   - mysql

networks:
 practica_network:

volumes: 
 elasticVol:
 mongoVol:
 graylogVol:
 mysqlVol:
 wordpressVol:

```
***