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