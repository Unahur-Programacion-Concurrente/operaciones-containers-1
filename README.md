# operaciones-containers-1
Laboratorio de Contenedores 1

## Instalación de Docker Engine en Linux

En Linux es posible instalar directamente Docker Engine ya que es nativo, mientras que en MacOS y Windows se instala Docker Desktop (que incluye Docker Engine). También hay una versión de Docker Desktop para Linux.

El procedimiento que se detalla a continuación es general y aplica a cualquier tipo de máquina (virtual o física) corriendo Linux

1. Revisar Prerrequisitos de Instalación   
   
   1.1. Requerimientos de SO  
      
      Ver el listado de versiones soportadas en [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)  
      
      Comprobar la versión de ubuntu instalada  
      ```
      lsb\_release \-a
      ```

   1.2. Desinstalar paquetes conflictivos (versiones antiguas)  
    ``` 
    for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done 
    ``` 
        
2. Configurar repositorio apt de Docker  
   2.1. Instalar paquetes de requisitos previos para que apt pueda usar paquetes por HTTPS  
   ```
   sudo apt update  
   sudo apt install apt-transport-https ca-certificates curl software-properties-common  
   ```
   2.2. Instalar el repositorio de Docker

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```       
3. Instalar paquetes de Docker
```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

4. Comprobar que el demonio Docker está corriendo
```
sudo systemctl status docker
```
   
5. Verificar la instalación de Docker Engine ejecutando la imagen “hello-world”
```
sudo docker run hello-world
```
6. Configurar Docker para usuarios no-root (sin sudo)  
   6.1. Crear el grupo docker  
   ```
   sudo groupadd docker 
   ```
   6.2. Agregar el usuario al grupo docker  
   ```
   sudo usermod -aG docker $USER  
   ```
   6.3. Cerrar terminal y volver a abrirla  
   
   6.4. Probar   
   ```
   docker run hello-world  
   ```
   Si ve un mensaje como el siguiente:  
   ```   
   WARNING: Error loading config file: /home/user/.docker/config.json -  
   stat /home/user/.docker/config.json: permission denied  
   ```
   Este error se debe a que los permisos del directorio \~/.docker/ son incorrectos debido a que se utilizó el comando sudo anteriormente.  
   
   Este error se puede resolver de dos maneras:  
   * borrando el directorio \~/.docker/ (se crea automáticamente)
   ```
    sudo rm \-r \~/.docker/
   ```
   * cambiando  dueño y permisos de \~/.docker/
   ```
   sudo chown "$USER":"$USER" /home/"$USER"/.docker \-R
   sudo chmod g+rwx "$HOME/.docker" \-R
   ```
  5. Configurar Docker para arrancar automáticamente cuando se reinicia el sistema
   ```  
   sudo systemctl enable docker.service  
   sudo systemctl enable containerd.service  
   ```    

   

## Imágenes y Contenedores

1. Creación de Contenedor hello-world (Hola Mundo)  
   1. En un explorador abrir docker hub [https://hub.docker.com/](https://hub.docker.com/)  
   2. En search buscar Hello World (seleccionar hello-world)  
   3. Bajar la imagen a su máquina local:  
      docker pull hello-world  
   4. Verificar que la imagen fue descargada (pull)  
      docker images  
   5. Arrancar un contenedor con esta imagen (observar los mensajes de error)  
      docker run hello-world  
   6. Ver los contenedores que se crearon y su estado  
      docker ps \-a  
        
2. Creación de un contenedor Ubuntu (sistema operativo base en contenedor)  
   1. Buscar la Imagen  
      La imagen se puede buscar en  [https://hub.docker.com](https://hub.docker.com/search?q=ubuntu&type=image)  
      o directamente por comando:  
      docker search ubuntu  
   2. Descargar la imagen (opcional)  
      docker pull ubuntu  
      Si no se especifica la versión, descarga la última:  
      ubuntu:latest  
   3. Para ver las imagenes descargadas en nuestra máquina local  
      docker images  
   4. Crear contenedor ubuntu  
      sudo docker run \-it ubuntu  
      A terminar queda en el shell del contenedor

   root@e2138ee6f5f9:/\# 

      Para salir del contenedor (deteniendolo)

   exit

   5. Para arrancar un contenedor parado  
      docker start \<id\>  \# o primera letra del id  
   6. Para salir del contenedor sin detenerlo

   Ctrl+P+Q

   7. Para ingresar a un contenedor en ejecución  
      docker attach \<id\>  \# o primera letra del id  
        
3. Instalación de mysql en el contenedor  
   1. Ingresar al contenedor Ubuntu  
      sudo docker ps \- a  
      sudo docker start \<id\>  
      sudo docker attach \<id\>

   2. Instalación de mysql  
      apt update  
      apt install mysql-server  
        
   3. Arrancar mysql  
      service mysql start  
      service mysql status  
        
4. Eliminar contenedores e imágenes  
   1. Listar los contenedores en ejecución  
      docker ps  
   2. Detener los contenedores en ejecución  
      docker stop \<id\>  
   3. Listar todos los contenedores  
      docker ps \-a  
   4. Eliminar todos los contenedores detenidos  
      Eliminar uno o mas:  
      docker rm \<id1\> \<id2\>...  
      Eliminar todos (stopped):  
      docker container prune  
   5. Listar las imágenes  
      docker images  
   6. Borrar todas la imágenes no utilizadas  
      Eliminar una o mas  
      docker rmi \<id1\>...  
      Eliminar todas (no utilizadas)  
      docker image prune \-a


5. Creación de un contenedor MySQL Server  
   1. Entrar a docker hub y buscar la imagen oficial de MySQL y buscar las instrucciones para arrancar contenedores (NO hacer docker pull)  
   2. Arrancar un contenedor MySQL  
      docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD=1234 \-d mysql  
   3. Listar las imágenes y contenedores, comprobar que el contenedor mysql-server esta activo  
      docker ps  
      docker images  
   4. Probar alternativas para conectarse a la línea de comandos mysql:  
      1. **Conectarse desde “dentro” del propio contenedor**  
         1. Abrir una sesión de bash en el contenedor  
            docker exec \-it mysql-server bash  
            Aparecerá el prompt del contenedor (por ejemplo:  
            bash-5.1\#  
            Nota: en los contenedores suele existir solamente el usuario root.  
         2. Iniciar una sesión de cliente mysql **dentro el contenedor**  
            mysql \-uroot \-p  
            Aparecerá el prompt del cliente mysql  
            mysql\>  
            Probar comandos sql (por ejemplo):  
            mysql\> show databases  
            mysql\> use mysql  
            mysql\> show tables  
            mysql\> exit  
         3. Cerrar la sesión de bash del contenedor  
            exit  
              
      2. **Conectarse desde un cliente mysql en el host**  
         1. Instalar cliente mysql en el host  
            sudo apt install mysql-client    
         2. Conectarse con el servidor en forma remota  
            mysql \-u root \-p \-h 127.0.0.1 \-P 3306  
            Nota: si falla con ERROR 2003 (HY000): Can't connect to MySQL server on '127.0.0.1:3306' (111)  
            Revisar si el puerto mysql (3306) está abierto en el host  
            ss \-ln | grep 3306  
            Revisar el mapeo de puertos en el Contenedor  
            docker port mysql-server  
            Revisar los puertos expuestos por el Contenedor  
            docker ps  
      3. **Conectarse desde un contenedor cliente mysql**  
         docker run \-it \--rm mysql mysql \-h\<hostIP\> \-uroot \-p

   5. Eliminar el contenedor actual y volver a crearlo con los puertos abiertos  
      docker stop mysql-server  
      docker rm mysql-server  
      docker ps \-a  
      docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-d \-p 3306:3306 mysql  
   6. Volver a comprobar los puertos en host, contenedor y mapeo

   ss \-ln | grep 3306

   docker ps

      docker port mysql-server

   7. Probar conexion mysql remota  
      1. Desde el host  
         mysql \-u root \-p \-h 127.0.0.1 \-P 3306  
      2. Desde un contenedor cliente mysql  
         docker run \-it \--rm mysql mysql \-h\<hostIP\> \-uroot \-p  \-P3306

    


6. Ejemplos de variables de entorno (ENV)  
   1. Borrar el contenedor mysql-server  
      docker stop mysql-server  
      docker rm mysql-server  
   2. Volver a arrancar un contenedor mysql-server con la variable de entorno MYSQL\_DATABASE  
      docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-e MYSQL\_DATABASE=mibase \-d \-p 3306:3306 mysql  
        
7. Agregar nuevos datos en la base de datos  
   1. Iniciar sesión mysql de línea de comandos  
      docker run \-it \--rm mysql mysql \-h\<hostIP\> \-uroot \-p1234 \-P3306  
   2. Cargar datos en la base de datos mibase  
      use mibase;  
      CREATE USER 'usuario1'@'localhost' IDENTIFIED BY '1234';  
      GRANT ALL PRIVILEGES ON mibase.\* TO 'usuario1'@'localhost';  
      FLUSH PRIVILEGES;  
      create table tabla1 (id INT AUTO\_INCREMENT PRIMARY KEY, nombre VARCHAR(255));  
      insert into tabla1 (nombre) values ('daniel'), ('karina');  
      select \* from tabla1;  
        
8. Borrar el contenedor y volver a arrancar uno idéntico y observar qué ocurrió con los datos cargados en el punto anterior

   docker stop mysql-server

   docker rm mysql-server

   docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-e MYSQL\_DATABASE=mibase \-d \-p 3306:3306 mysql

   

   Iniciar sesión mysql de línea de comandos

   docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3306

   Verificar los datos cargados en el punto anterior

   select user from mysql.user;

   select \* from tabla1;

9. Borrar el contenedor y volver a arrancar uno idéntico pero con un “named” volume asociado a la ruta del contenedor /var/lib/mysql donde se almacena la base de datos.  
   1. Borrar el contenedor en ejecución  
      docker stop mysql-server  
      docker rm mysql-server  
   2. Arrancar nuevo contenedor indicando un volumen  
      docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-e MYSQL\_DATABASE=mibase \-v voldatos:/var/lib/mysql \-d \-p 3306:3306 mysql  
   3. Iniciar sesión mysql de línea de comandos  
      docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3306  
   4. Modificar datos en mibase  
      use mibase;  
      CREATE USER 'usuario1'@'localhost' IDENTIFIED BY '1234';  
      GRANT ALL PRIVILEGES ON mibase.\* TO 'usuario1'@'localhost';  
      FLUSH PRIVILEGES;  
      create table tabla1 (id INT AUTO\_INCREMENT PRIMARY KEY, nombre VARCHAR(255));  
      insert into tabla1 (nombre) values ('daniel'), ('karina');  
      select \* from tabla1;  
      quit;

10. Borrar el contenedor y volver a arrancar uno idéntico y observar qué ocurrió con los datos cargados en el punto anterior

    docker stop mysql-server

    docker rm mysql-server

    docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-e MYSQL\_DATABASE=mibase \-v voldatos:/var/lib/mysql \-d \-p 3306:3306 mysql

    Iniciar sesión mysql de línea de comandos

    docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3306

    Verificar los datos cargados en el punto anterior

    select user from mysql.user;

    select \* from tabla1;

    

11. Borrar el contenedor y volver a arrancar uno idéntico pero con un “bind mount” asociado a la ruta del contenedor /etc/mysql/conf.d donde se almacena configuración personalizada (customized) de mysql  
    1. Detener y borrar el contenedor  
       docker stop mysql-server  
       docker rm mysql-server  
    2. Crear directorio para configuración y archivo de configuración customizada  
       mkdir misdatos  
       cd misdatos  
       nano config-file.cnf  
       Agregar las siguientes líneas:  
       \[mysqld\]  
       port=3307  
       Arrancar el contenedor agregando el mapeo al puerto nuevo  
       docker run \--name mysql-server \-e MYSQL\_ROOT\_PASSWORD='1234' \-e MYSQL\_DATABASE=mibase \-v voldatos:/var/lib/mysql \-v /home/daniel/miconfig:/etc/mysql/conf.d \-d \-p 3307:3307 \-p 3306:3306 mysql  
    3. Verificar los puertos expuestos y mapeados  
       ss \-ln | grep 330  
       docker port mysql-server  
       docker ps  
    4. Iniciar conexión remota al nuevo puerto  
       docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3307  
    5. Editar el archivo config-file.cnf configurando port=3306  
       nano config-file.cnf  
       \[mysqld\]  
       port=3306  
    6. Reiniciar el contenedor y verificar que funciona el puerto correcto  
       docker stop mysql-server  
       docker start mysql-server  
       docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3306

12. Inicialización de una instancia nueva mysql colocando archivos y scripts sql en el directorio /docker-entrypoint-initdb.d del contenedor.  
    1. Como paso preliminar, creamos un sqldump   
       1. (verificar que la base de datos tenga información antes)  
          docker run \-it \--rm mysql mysql \-h\<hostIP\>) \-uroot \-p \-P3306  
          use mibase;  
          show tables;  
          quit;  
       2. Realizar un mysqldump y volcarlo a un archivo en el directorio actual en el host  
          docker exec \-it mysql-server mysqldump \-u root \-p1234 mibase \> mibasedump.sql  
    2. Editar el archivo Dockerfile (sin extensión)  
       nano Dockerfile  
       Agregar:  
       FROM mysql:latest  
         
       ENV MYSQL\_ROOT\_PASSWORD=1234  
       ENV MYSQL\_DATABASE=mibase  
       ENV MYSQL\_USER=usuario  
       ENV MYSQL\_PASSWORD=1234  
         
       COPY mibasedump.sql /docker-entrypoint-initdb.d/  
       EXPOSE 3306  
    3. Crear una imagen nueva mysql-mibase  
       docker build \-t mysql-mibase .  
       Verificar que se creo una imagen nueva  
       docker images  
    4. Crear un contenedor con la imagen nueva  
       docker run \--name mysql-server \-v  misdatos:/var/lib/mysql \--rm \-d \-p 3306:3306 mibase-mysql  
    5. Conectarse a la base de datos y verificar que la información del mysqldump está cargada.

# Docker Files

13. Crear un contenedor servidor web utilizando como base la imagen oficial de Apache y alojar la página web estática del siguiente link:  
    wget https://github.com/Unahur-Programacion-Concurrente/StaticWebsite/raw/master/staticweb.tar  
    Nota: Tambien lo puede bajar clonando el repositorio github: [https://github.com/Unahur-Programacion-Concurrente/StaticWebsite](https://github.com/Unahur-Programacion-Concurrente/StaticWebsite)  
      
14. Crear una imagen que pueda distribuir para servidores en producción

15. Crear una imagen que pueda utilizar como desarrollador
