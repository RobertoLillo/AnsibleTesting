Common
=========

Rol encargado de realizar tareas básicas de preparación en las máquinas, como agregar las IP y FQDN al archivo *hosts* e instalar Docker y Docker-Compose.

Requisitos
------------

 - **Permiso para ejecutar SUDO**
 - **Conexión a internet**

Variables
--------------

Utiliza variables configuradas en el archivo **hosts.yml** como ***ansible_host***, ***groups*** y ***host***. No posee ninguna variable definida en **default.yml** o **vars.yml**.

Módulos utilizados
------------

 - **apt**
 - **lineinfile**
 - **file**
