
Keycloak
=========

Rol encargado de desplegar el servicio de Keycloak en un estado limpio sin configuraciones. Esto se realiza mediante la ejecución de un archivo de Docker-Compose que se encuentra en la carpeta **files** de este rol. Además, se descarga el repositorio del tema a ser aplicado posteriormente en la interfaz de usuarios.

Requisitos
------------

-   **Permiso para ejecutar SUDO**
-   **Conexión a internet**
-   **Repositorio de paquetes actualizado**
-   **Docker**
-   **Docker-Compose**

Variables
--------------

En el archivo **defaults.yml** se encuentran tres variables que contienen información de la máquina en la que se levanta el servicio.

-   **keycloak_server_ip: 10.0.2.5**
-   **keycloak_server_fqdn: keycloak.diinf.tk**
-   **theme_repository: https://github.com/RobertoLillo/Tema-DIINF-Keycloak**

Es posible modificar esta versión en el archivo  **vars.yml**  que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **git**
 - **copy**
 - **shell**
 - **wait_for**
