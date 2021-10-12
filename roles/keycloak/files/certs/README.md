# Configuracion TLS (SSL)

Esta es la ubicación en donde colocar el certificado y la llave privada para Keycloak, deben tener los siguientes nombres:

-  **tls.crt**: para el certificado

-  **tls.key**: para la llave privada

La integración ocurre automáticamente, el servicio se desplegará con certificados autofirmados en el caso de que no se hayan incluido los archivos, por otro lado, cuando son incluidos son montados en la ruta **`/etc/x509/https`** del contenedor de Keycloak, detectados por el servicio y configurados automáticamente.

Ambos archivos están incluidos en el ***gitignore***.