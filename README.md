# Prototipo de sistema multiprotocolo moderno para la administración, autenticación y autorización de clientes en el Departamento de Ingeniería Informática.

Este repositorio contiene el proyecto de titulación para el semestre 1-2021 de la Universidad de Santiago de Chile, este recibe el nombre de ***Prototipo de sistema multiprotocolo moderno para la administración, autenticación y autorización de clientes en el Departamento de Ingeniería Informática*** y permite establecer un **sistema centralizado** al que se pueden conectar clientes de sistemas operativos Windows o Linux y aplicaciones Web o móviles, con el fin de consultar por autenticación y autorización de usuarios.

La forma de desplegar este sistema es mediante el *Playbook* de [Ansible](https://www.ansible.com/) que contiene el repositorio, que se encarga de levantar [Samba Active Directory](https://www.samba.org/) como servicio de directorio, [Keycloak](https://www.keycloak.org/) como punto de conexión entre distintas partes del sistema y finalmente integrando a [Google](https://developers.google.com/identity/protocols/oauth2/openid-connect) como proveedor de identidad para los usuarios de la universidad.

En una vista general, este *playbook* prepara los servidores actualizando el gestor de paquetes y la lista de *hosts* conocidos, instala Docker y Docker-Compose, despliega en dos servidores distintos los servicios de Samba AD y Keycloak mediante Docker-Compose y finalmente se configura los sistemas de federación de usuarios y proveedores de identidad en Keycloak.

## Requerimientos

Para la ejecución de este *playbook* se necesita lo siguiente:

 - **2 máquinas Ubuntu 20.04 LTS Server** (a ser configuradas por Ansible)
 - **1 máquina con Ansible 2.9.x** (que ejecuta el *playbook*)
 - **Conexión SSH a las 2 máquinas a configurar**
 - **Cuenta de Google Cloud Platform** (preferiblemente cuenta USACH)
 - **Conexión a internet en las 3 máquinas**

### > Requisitos del sistema

En relación a las especificaciones de las máquinas, se utilizaron para prueba dos creadas virtualmente en [VirtualBox](https://www.virtualbox.org/) con las siguientes características:

- 1 núcleo
- 1GB de RAM
- 10GB de SSD

Para un ambiente de producción es recomendable aumentar las especificaciones de las máquinas a los siguientes requisitos mínimos:

 1. **Máquina de Samba AD** (*valores para un dominio de menos de 1000 usuarios*)
	 - 2 núcleos (por sobre 1000 usuarios aumentar a 4 núcleos)
	 - 2GB de RAM (por cada 1000 usuarios aumentar en 4GB de RAM)
	 - 10GB de SSD (aumentar en base a las políticas de información guardada, cantidad de usuarios y máquinas)

 2. **Máquina de Keycloak**
	 - 1 núcleo
	 - 1GB de RAM
	 - 15GB de SSD (aumentar en base a la necesidad de la base de datos PostgreSQL)

### > Red y puertos

En un sistema de pruebas local (como el implementado con VirtualBox) no es necesario realizar configuraciones de red o abrir puertos, por otro lado, si el despliegue se realizará en la nube  u otro tipo de ambiente es posible que sea necesario realizar este tipo de configuración. 

Los puertos utilizados por cada máquina y que requieren ser abiertos son los siguientes:

 1. **Máquina de Samba AD** (*no pueden ser modificados*)
 
| Servicio | Puerto |
|:---:|:---:|
| domain | 53 |
| kerberos | 88 |
| msrpc | 135 |
| netbios-ssn | 139 |
| ldap | 389 |
| microsoft-ds | 445 |
| kpasswd5 | 464 |
| ldapssl | 636 |
| kdm | 1024 |
| globalcatLDAP | 3268 |
| globalcatLDAPssl | 3269 |

 2. **Máquina de Keycloak** (*pueden ser modificados, explicado en la siguiente sección*)

| Servicio | Puerto |
|:---:|:---:|
| https| 443|
| postgresql| 5432|

## Configuración

El despliegue del servicio se puede configurar mediante distintos archivos que se encuentran en el repositorio, principalmente mediante **hosts.yml** y **vars.yml** ubicados en la raíz del repositorio, también es posible configurar el uso de certificados TLS correctamente firmados.

### > Hosts

En el archivo **hosts.yml** se encuentran las direcciones de las máquinas en las que se ejecuta el *playbook*. Las máquinas están divididas en los grupos **`directoryServer`** y **`authServer`**, en cada uno de estos es necesario ingresar el nombre con el que se puede ubicar la máquina ya sea un **nombre**, una **IP** o un **FQDN**.

El archivo de hosts por defecto es el siguiente:

    all:
      children:
      
        directoryService:     # Group
          hosts:
            samba.diinf.lan:  # Name, IP or FQDN
              
        authService:            # Group
          hosts:
            keycloak.diinf.tk:  # Name, IP or FQDN

### > Vars

En **vars.yml** se pueden encontrar multiples variables que afectan la ejecución del *playbook* y modifican valores de los servicios desplegados. Los valores que se encuentran por defecto en este archivo están también repartidos en el archivo **main.yml** dentro de la carpeta **Defaults** de cada rol.

En la sección ***Toggles*** se encuentran las variables que determinan si las tareas relacionadas a la federación de usuarios o proveedores de identidad serán ejecutadas, esto implica si estas configuraciones serán o no aplicadas al servidor de Keycloak. Esto permite desplegar el servidor de Keycloak limpio de configuraciones o sólo con la configuración que se desea probar, deshabilitar la federación permite desplegar Keycloak sin la necesidad de tener previamente un servidor de Samba AD.

    # ----- Toggles -----
    enable_federation: true	        # Turns ON or OFF the federation configuration on Keycloak
    enable_identity_provider: true	# Turns ON or OFF the identity provider configuration on Keycloak

\
La siguiente sección corresponde a las variables que se utilizan de manera **Global** en el *playbook*, aquí es necesario ingresar las **IP** de las máquinas como también los **FQDN** que serán asignados. También es posible determinar el directorio que será utilizado para guardar archivos de instalación en cada máquina.

	# ----- Global -----
	# Server IP
	samba_server_ip: 10.0.2.4
	keycloak_server_ip: 10.0.2.5

	# Server FQDN
	samba_server_fqdn: samba.diinf.lan
	keycloak_server_fqdn: keycloak.diinf.tk

	# Files
	files_path: /opt/sistema-centralizado

\
En ***Package Installation*** se establecen las versiones de Docker y Docker-Compose a ser instaladas en las máquinas, Docker particularmente requiere determinar la versión de 3 paquetes. Es posible utilizar el símbolo **`*`** para instalar la última versión disponible.

    # ----- Package Installation -----
	# Docker
	docker_ce_version: 5:20.10.7~3-0~ubuntu-focal
	docker_ce_cli_version: 5:20.10.7~3-0~ubuntu-focal
	containerd_io_version: 1.4.9-1

	# Docker-Compose
	docker_compose_version: 1.29.2

\
***Samba Service*** incluye las variables para el despliegue del contenedor de Samba AD y principalmente las necesarias para el funcionamiento del servicio. **`samba_domain_name`**, **`samba_domain_suffix`** y **`samba_domain_password`** tienen indicaciones especiales de cómo deben ser ingresadas.

	# ----- Samba Service -----
	# Container configuration
	samba_container_name: samba-ad
	samba_container_hostname: samba

	# Samba Active Directory configuration
	samba_domain_name: DIINF  	# Always uppercase
	samba_domain_suffix: LAN  	# Always uppercase
	samba_domain_password: Diinf1*  # At least one uppercase, number & symbol
	samba_dns_forwarder: 8.8.8.8
	samba_users_DN: CN=Users,DC={{ samba_domain_name }},DC={{ samba_domain_suffix }}
	samba_bind_DN: CN=Administrator,CN=Users,DC={{ samba_domain_name }},DC={{ samba_domain_suffix }}

\
La sección de ***Keycloak Service*** incluye una gran cantidad de variables al estar asociada tanto al rol de instalación como al de configuración. Algunas variables tienen indicaciones específicas y opciones posibles, es importante en esta sección especificar los valores de **`id_provider_client_id`** y **`id_provider_client_secret`** si es que se activa el *toggle* para configurar el proveedor de identidad, la explicación de cómo obtener estas credenciales se encuentra en el documento [Obtención de credenciales OAuth en Google Cloud Platform](https://github.com/RobertoLillo/Sistema-Centralizado/blob/main/docs/Obtenci%C3%B3n%20de%20credenciales%20OAuth%20en%20Google%20Cloud%20Platform.pdf) dentro de la carpeta ***Docs*** de este repositorio.

	# ----- Keycloak Service -----
	# Container configuration
	keycloak_version: 14.0.0
	keycloak_container_name: keycloak-app
	keycloak_container_hostname: keycloak
	keycloak_container_port: 443

	# Keycloak configuration
	keycloak_admin_user: admin
	keycloak_admin_password: Diinf1*
	keycloak_base_url: "https://{{ keycloak_server_fqdn }}"

	# New Realm
	keycloak_realm_name: DIINF

	# Federation
	federation_id: Samba		# ID
	federation_name: Samba-AD	# Display name
	federation_edit_mode: WRITABLE	# Options: READ_ONLY, WRITABLE & UNSYNCED

	# Identity Provider
	id_provider_name: google		# Lowercase
	id_provider_client_id: clientId		# Obtained through Google Cloud Platform
	id_provider_client_secret: clientSecret	# Obtained through Google Cloud Platform
  
	# Theme
	keycloak_theme_name: DIINF	# Name of the folder on the repository
	keycloak_theme_repository: https://github.com/RobertoLillo/Tema-DIINF-Keycloak

\
Finalmente, la sección de ***PostgreSQL Service*** contiene variables necesarias para configurar la base de datos **postgres** que es desplegada junto al servidor de Keycloak.

	# ----- PostgreSQL Service -----
	# Container configuration
	postgres_version: 13.3
	postgres_container_name: postgres-db
	postgres_container_hostname: postgres
	postgres_container_port: 5432

	# PostgreSQL
	postgres_database_name: keycloak
	postgres_user: keycloak
	postgres_password: keycloakPassword


### > Configuración TLS (SSL)

Este *playbook* permite desplegar el servicio de Keycloak con certificados autofirmados permitiendo así rápidamente generar un entorno de prueba, no obstante, es posible especificar un certificado y una llave privada para Keycloak, para esto se necesita proveer los siguientes dos archivos:

-  **tls.crt**: el certificado

-  **tls.key**: la llave privada

La ubicación de estos archivos es en la ruta **`/Sistema-Centralizado/roles/keycloak/files/certs`** del repositorio y por seguridad ambos están incluidos en el ***gitignore***.

La integración ocurre automáticamente, el servicio se desplegará con certificados autofirmados en el caso de que no se hayan incluido los archivos, por otro lado, cuando son incluidos son montados en la ruta **`/etc/x509/https`** del contenedor de Keycloak, detectados por el servicio y configurados automáticamente.

Samba AD es desplegado con certificados autofirmados y no es configurable en esta versión del *playbook*.

## Ejecución

Luego de realizar las configuraciones necesarias en **hosts.yml** y **vars.yml** es posible ejecutar el *playbook* mediante el comando:

    ansible-playbook -K setup.yml

La opción **-K** (o también **-\-ask-become-pass**) consultará por la contraseña de la cuenta utilizada para ingresar a la máquina, de esta forma se pueden ejecutar tareas que requieren **sudo**.

### > Tags

El comando anterior ejecuta el *playbook* en su totalidad, pero en casos que sea necesario ejecutar secciones específicas se puede utilizar una serie de ***Tags*** que determinan las tareas que serán realizadas. Esto se puede realizar mediante el siguiente comando:

    ansible-playbook -K setup.yml --tags tag1,tag2,...

Lista de *tags* definidos:

 - **basic_setup**: ejecuta las tareas del rol ***Common***. Actualiza el repositorio de paquetes, actualiza las direcciones IP del archivo de *hosts* e instala Docker y Docker-Compose.

 - **samba_setup**: ejecuta las tareas del rol ***Common*** y ***Samba***. Despliega el servicio de Samba Active Directory.

 - **keycloak_setup**: ejecuta las tareas del rol ***Common***, ***Keycloak*** y ***KeycloakConfig***. Despliega el servicio de Keycloak, configurando el idioma y el tema de la interfaz de usuarios. Las configuraciones de federación y proveedores de identidad también son ejecutadas pero son dependientes de los valores ingresados en los *toggles* en el archivo **vars.yml**.

 - **host_update**: ejecuta una de las tareas del rol ***Common***. Actualiza las IP y FQDN del archivo *hosts*, util si es que se hicieron cambios en las direcciones de las máquinas.

 - **theme_update**: ejecuta una de las tareas del rol ***Keycloak***. Descarga la última versión del tema especificado en el archivo de variables, util si es que se hicieron cambios en la interfaz.

Además de los anteriores, se definieron otros *tags* principalmente para hacer *testing* a etapas específicas del *playbook*, estas pueden ser utilizadas pero no aseguran el correcto funcionamiento del servicio final al no estar diseñadas para levantar el servicio completo.

 - **common**: ejecuta las tareas del rol ***Common***.
 - **docker**: ejecuta las tareas del rol ***Docker***.
 - **docker_compose**: ejecuta las tareas del rol ***DockerCompose***.
 - **samba**: ejecuta las tareas del rol ***Samba***.
 - **keycloak**: ejecuta las tareas del rol ***Keycloak***.
 - **keycloak_config**: ejecuta las tareas del rol ***KeycloakConfig***.
 - **federation**: ejecuta las tareas del rol ***KeycloakConfig*** relacionadas a la federación de usuarios.
 - **identity_provider**: ejecuta las tareas del rol ***KeycloakConfig*** relacionadas a los proveedores de identidad.

## Tema DIINF para la interfaz de usuarios

Junto al *playbook* se desarrolló una versión de la consola de usuarios con traducciones al español, colores institucionales y *branding* del Departamento de Ingeniería Informática, este tema se encuentra en el siguiente repositorio:

 - [Tema-DIINF-Keycloak](https://github.com/RobertoLillo/Tema-DIINF-Keycloak)

Este tema se encuentra incluido por defecto en el archivo **vars.yml** y también en los archivos **main.yml** de la carpeta **Defaults** de los roles **Keycloak** y **KeycloakConfig**.

## Documentación

Durante el periodo de investigación e implementación de este proyecto se generaron variados documentos que comparan distintas funciones entre servicios y también explican como instalar manualmente cada uno de los *software*, estos documentos se encuentran en la carpeta ***Docs*** del repositorio, también se incluye la memoria escrita junto al desarrollo del proyecto.
