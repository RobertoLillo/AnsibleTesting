
Docker
=========

Instalación de los paquetes necesarios para hacer uso de Docker para desplegar contenedores, primero se verifica si es que Docker se encuentra instalado en la máquina.

Requisitos
------------

 - **Permiso para ejecutar SUDO**
 - **Conexión a internet**
 - **Repositorio de paquetes actualizado**

Variables
--------------

En el archivo **main.yml** en la carpeta **defaults** se encuentran tres variables que determinan la versión que será instalada de los paquetes necesarios para utilizar Docker.

**Docker**
 - **docker_ce_version: 5:20.10.7\~3-0~ubuntu-focal**
 - **docker_ce_cli_version: 5:20.10.7\~3-0~ubuntu-focal**
 - **containerd_io_version: 1.4.9-1**

Es posible modificar estas versiones en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **package_facts**
 - **apt**
 - **apt_key**
 - **apt_repository**
 - **systemd**
