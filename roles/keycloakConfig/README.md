
KeycloakConfig
=========

Rol encargado de configurar el servicio de Keycloak previamente desplegado mediante el rol **Keycloak**.  Se cambia el idioma al español, se crea el ***realm*** y se aplica el tema a la interfaz de usuarios, dependiendo de las configuraciones en las variables ***toggles*** también se configura la federación de usuarios y los proveedores de identidad. Todas estas configuraciones son realizadas enviando configuraciones mediante JSON a la API de Keycloak.

Requisitos
------------

-   **Permiso para ejecutar SUDO**
-   **Conexión a internet**
-   **Servicio de Keycloak ya desplegado**

Variables
--------------
En el archivo **main.yml** de la carpeta **defaults** se encuentran dos sets de variables.

**Toggles** (determinan si se activan ciertos componentes de Keycloak)

 - **enable_federation: true**
 - **enable_identity_provider: true**

**Otras variables**

 - **keycloak_base_url: "https://keycloak.diinf.tk"**
 - **keycloak_admin_user: "admin" keycloak_admin_pass: "Diinf1\*"**
 - **keycloak_realm_data_file: "master_realm.json" realm_name: "DIINF"**
 - **realm_data_file: "diinf_realm.jso federation_name: "Samba"**
 - **federation_data_file: "samba_federation.json"**
 - **id_provider_name: "google" id_provider_data_file: "google_id_provider.json"**

Es posible modificar esta versión en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **uri**
 - **shell**
