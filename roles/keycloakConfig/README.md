
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
En el archivo **main.yml** en la carpeta **defaults** se encuentran dos sets de variables.

**Toggles** (determinan si se activan ciertos componentes de Keycloak)

 - **enable_federation: true**
 - **enable_identity_provider: true**

**Global**
 - **samba_server_ip: 10.0.2.4**
 - **keycloak_server_ip: 10.0.2.5**
 - **samba_server_fqdn: samba.diinf.lan**
 - **keycloak_server_fqdn: keycloak.diinf.tk**
 - **files_path: /opt/sistema-centralizado**

**Samba Service**
 - **samba_domain_name: DIINF**
 - **samba_domain_suffix: LAN**
 - **samba_domain_password: Diinf1\***
 - **samba_dns_forwarder: 8.8.8.8**
 - **samba_users_DN: CN=Users,DC={{ samba_domain_name }},DC={{ samba_domain_suffix }}**
 - **samba_bind_DN: CN=Administrator,CN=Users,DC={{ samba_domain_name }},DC={{ samba_domain_suffix }}**

**Keycloak Service**
 - **keycloak_container_name: keycloak-app**
 - **keycloak_admin_user: admin**
 - **keycloak_admin_password: Diinf1\***
 - **keycloak_base_url: "https://{{ keycloak_server_fqdn }}"**
 - **keycloak_realm_name: DIINF**
 - **federation_id: Samba**
 - **federation_name: Samba-AD**
 - **federation_edit_mode: WRITABLE**
 - **id_provider_name: google**
 - **id_provider_client_id: clientId**
 - **id_provider_client_secret: clientSecret**
 - **keycloak_theme_name: DIINF**
 - **keycloak_theme_repository: https://github.com/RobertoLillo/Tema-DIINF-Keycloak**

Es posible modificar esta versión en el archivo **vars.yml** que se encuentra en la raíz del repositorio.

Módulos utilizados
------------

 - **uri**
 - **shell**
