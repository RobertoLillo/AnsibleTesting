# Prototipo de sistema multiprotocolo moderno para la administración, autenticación y autorización de clientes en el Departamento de Ingeniería Informática.

Este repositorio contiene el proyecto de titulación para el semestre 1-2021 de la Universidad de Santiago de Chile, este recibe el nombre de ***Prototipo de sistema multiprotocolo moderno para la administración, autenticación y autorización de clientes en el Departamento de Ingeniería Informática*** y permite establecer un **sistema centralizado** al que se pueden conectar clientes de sistemas operativos Windows o Linux y aplicaciones Web o móviles, con el fin de consultar por autenticación y autorización de usuarios.

La forma de desplegar este sistema es mediante el *Playbook* de [Ansible](https://www.ansible.com/) que contiene el repositorio, que se encarga de levantar [Samba Active Directory](https://www.samba.org/) como servicio de directorio, [Keycloak](https://www.keycloak.org/) como punto de conexión entre distintas partes del sistema y finalmente integrando a [Google](https://developers.google.com/identity/protocols/oauth2/openid-connect) como proveedor de identidad para los usuarios de la universidad.

En una vista general, este *playbook* prepara los servidores actualizando el gestor de paquetes y la lista de *hosts* conocidos, instala Docker y Docker-Compose, despliega en dos servidores distintos los servicios de Samba AD y Keycloak mediante Docker-Compose y finalmente se configura los sistemas de federación de usuarios y proveedores de identidad en Keycloak.

## Requerimientos

Para la ejecución de este *playbook* se necesita lo siguiente:

 - **2 máquinas Ubuntu 20.04 LTS Server** (a ser configuradas por Ansible)
 - **1 máquina con Ansible 2.9.x** (que ejecuta el *playbook*)
 - **Conexión SSH a las 2 máquinas a configurar**
 - **Conexión a internet en las 3 máquinas**

## Configuración

El despliegue del servicio se puede configurar mediante distintos archivos que se encuentran en el repositorio, principalmente mediante **hosts.yml** y **vars.yml** ubicados en la raíz del repositorio.

### Hosts

En el archivo **hosts.yml** se encuentran las direcciones de las máquinas en las que se ejecuta el *playbook*. Las máquinas están divididas en los grupos **`directoryServer`** y **`authServer`**, en cada uno de estos es necesario ingresar el **FQDN** del servidor como también su **dirección IP**.

El archivo de hosts por defecto es el siguiente:

    all:
      children:
      
        directoryServer:  # Group
          hosts:
            samba.diinf.lan:          # FQDN
              ansible_host: 10.0.2.4  # Server IP
              
        authServer:       # Group
          hosts:
            keycloak.diinf.tk:        # FQDN
              ansible_host: 10.0.2.5  # Server IP

### Vars

En **vars.yml** se pueden encontrar multiples variables que afectan la ejecución del *playbook* y modifican valores de los servicios desplegados. 

Primero en la sección ***Toggles*** se encuentran las variables que determinan si las tareas relacionadas a la federación de usuarios o proveedores de identidad serán ejecutadas, esto implica si estas configuraciones serán o no aplicadas al servidor de Keycloak. Esto permite desplegar el servidor de Keycloak limpio de configuraciones o sólo con la configuración que se desea probar, también, deshabilitar la federación permite desplegar Keycloak sin la necesidad de tener previamente un servidor de Samba AD.

    # --- Toggles ---
    enable_federation: true	        # Turns ON or OFF the federation configuration on Keycloak
    enable_identity_provider: true	# Turns ON or OFF the identity provider configuration on Keycloak

La segunda sección corresponde a las variables utilizadas en el rol ***Common***, aquí es necesario establecer las versiones de Docker y Docker-Compose a ser instaladas en las máquinas, Docker particularmente requiere determinar la versión de 3 paquetes. Es posible utilizar el símbolo **`*`** para instalar la última versión disponible.

    # --- Variables used in common role ---
    # Docker installation
    docker_ce_version: 5:20.10.7~3-0~ubuntu-focal
    docker_ce_cli_version: 5:20.10.7~3-0~ubuntu-focal
    containerd_io_version: 1.4.9-1
    
    # Docker-Compose installation
    docker_compose_version: 1.29.2

Finalmente, la última sección contiene las variables que son utilizadas en los roles **Samba**, **Keycloak** y **KeycloakConfig**. Aquí es necesario establecer los valores ingresados previamente en el archivo **hosts.yml**.

    # --- Variables used in samba, keycloak and keycloakConfig roles ---
    # Server IPs
    samba_server_ip: 10.0.2.4
    keycloak_server_ip: 10.0.2.5
    
    # Server FQDNs
    samba_server_fqdn: samba.diinf.lan
    keycloak_server_fqdn: keycloak.diinf.tk
