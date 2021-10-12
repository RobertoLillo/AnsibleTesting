
Docker-Compose
=========

Instalación de los paquetes necesarios para hacer uso de Docker-Compose para desplegar contenedores, primero se verifica si es que Docker-Compose se encuentra instalado en la máquina.

Requisitos
------------

 -  **Permiso para ejecutar SUDO**
-   **Conexión a internet**
-   **Repositorio de paquetes actualizado**
-   **Docker**

Role Variables
--------------

En el archivo **main.yml** en la carpeta **defaults** se encuentra una variable que determina la versión que será instalada de Docker-Compose.

**Docker-Compose**
 - **docker_compose_version: 1.29.2**

Es posible modificar esta versión en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Módulos utilizados
----------------

 - **stat**
 - **get_url**

