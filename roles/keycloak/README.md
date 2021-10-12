
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

En el archivo **main.yml** en la carpeta **defaults** se encuentran variables que contienen información de la máquina en la que se levanta el servicio y otras variables necesarias.

**Global**
- **samba_server_ip: 10.0.2.4**
- **keycloak_server_ip: 10.0.2.5**
- **samba_server_fqdn: samba.diinf.lan**
- **keycloak_server_fqdn: keycloak.diinf.tk**
- **files_path: /opt/sistema-centralizado**

**Keycloak Service**
- **keycloak_version: 14.0.0**
- **keycloak_container_name: keycloak-app**
- **keycloak_container_hostname: keycloak**
- **keycloak_container_port: 443**
- **keycloak_admin_user: admin**
- **keycloak_admin_password: Diinf1\***
- **keycloak_theme_name: DIINF**
- **keycloak_theme_repository: https://github.com/RobertoLillo/Tema-DIINF-Keycloak**

**PostgreSQL Service**
- **postgres_version: 13.3**
- **postgres_container_name: postgres-db**
- **postgres_container_hostname: postgres**
- **postgres_container_port: 5432**
- **postgres_database_name: keycloak**
- **postgres_user: keycloak**
- **postgres_password: keycloakPassword**

Es posible modificar esta versión en el archivo  **vars.yml**  que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **git**
 - **copy**
 - **template**
 - **shell**
 - **wait_for**
