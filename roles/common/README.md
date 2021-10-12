Common
=========

Rol encargado de realizar tareas básicas de preparación en las máquinas, como agregar las IP y FQDN al archivo *hosts* e instalar Docker y Docker-Compose.

Requisitos
------------

 - **Permiso para ejecutar SUDO**
 - **Conexión a internet**

Variables
--------------

Utiliza variables configuradas en el archivo **hosts.yml** como ***ansible_host***, ***groups*** y ***host***. En el archivo **main.yml** en la carpeta **defaults** se encuentra una variable que especifica el directorio a crear para la instalación de archivos en las máquinas.

 - **files_path: /opt/sistema-centralizado**

Es posible modificar esta variable en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **apt**
 - **lineinfile**
 - **file**
