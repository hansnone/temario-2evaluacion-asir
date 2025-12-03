# Historia WWW
> Dos trabajos servicio web y ftp. para el 15 y el 19 de Diciembre.

# Historia WWW

Ambos (la primera web, y la WWW) son obra de un mismo autor: el científico británico Tim Berners-Lee, en aquel entonces investigador del prestigioso Laboratorio Europeo de Física de Partículas (CERN) de Ginebra.
La primera página web de la Historia sigue funcionando y puedes navegar por ella Reproducción de la primera página The World Wide Web project
The WorldWideWeb (W3) is a wide-area hypermedia information retrieval initiative aiming to give universal access to a large universe of documents.
Accede a la réplica de la primera página web que se configuró The World Wide Web project.
Accede mediante el comando curl para poder descargar la primera página en un fichero index.html y visualizarlo localmente.
Activa en el Windows Server el rol de IIS (Servicios web) y monta esta página.
Sube una captura de un navegador del Windows Server accediendo a http://localhost donde se puede ver el contenido de esta página.

> Enlaces de **Interes**:

- https://www.xataka.com/privacidad/tim-berners-lee-creo-web-ahora-quiere-salvarla-vez
- https://www.genbeta.com/web/primera-pagina-web-historia-sigue-funcionando-puedes-navegar-ella-1
- https://info.cern.ch/hypertext/WWW/TheProject.html
- https://info.cern.ch/hypertext/WWW/WhatIs.html
- https://info.cern.ch/hypertext/WWW/TheProject.html

## Comando CURL.
curl en una herramienta para la transferencia de datos desde un servidor o una web usando un URLs. Soporta los siguientes protocolos:  DICT,  FILE,  FTP,  FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB,  SMBS, SMTP, SMTPS, TELNET, TFTP, WS and WSS.
``` bash
curl https://download.fedoraproject.org/pub/fedora/linux/releases/43/Server/x86_64/iso/Fedora-Server-dvd-x86_64-43-1.6.iso
```
### Parametros de inteneres
-v: se utiliza para obtener una salida más detallada del proceso de comunicación.
El parámetro **`-v`** (o **`--verbose`**) en el comando `curl` se utiliza para obtener una salida **más detallada** del proceso de comunicación.
La salida **verbosa** incluye:

- **Detalles de la conexión:** Información sobre la resolución DNS, el intento de conexión y el puerto.
- **Encabezados de solicitud (Request Headers):** Los encabezados HTTP que `curl` envía al servidor (marcados con `>`).
- **Información de negociación SSL/TLS** (si aplica).
- **Encabezados de respuesta (Response Headers):** Los encabezados HTTP que el servidor devuelve (marcados con `<`).
- **Notas informativas** del proceso de transferencia (marcadas con `*`).