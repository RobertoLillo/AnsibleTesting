
Samba
=========

Rol encargado de desplegar el servicio de Samba Active Directory en un estado limpio sin configuraciones. Esto se realiza mediante la ejecución de un archivo de Docker-Compose que se encuentra en la carpeta **files** de este rol.

Requisitos
------------

-   **Permiso para ejecutar SUDO**
-   **Conexión a internet**
-   **Repositorio de paquetes actualizado**
-   **Docker**
-   **Docker-Compose**

Variables
--------------

En el archivo **main.yml** en la carpeta **defaults** se encuentran dos variables que contienen información de la máquina en la que se levanta el servicio.

 - **samba_server_ip: 10.0.2.4**
 - **samba_server_fqdn: samba.diinf.lan**

Es posible modificar esta versión en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **copy**
 - **shell**
 - **wait_for**
