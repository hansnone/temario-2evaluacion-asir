# Practicas

Utiliza el equipo Windows Server de la práctica anterior que ya dispone de rol de DHCP y DNS. Tendrás que cambiar el hostname por **WIN-tunombreinicialapellido**, esto requerirá que adaptes los registros de DNS para que recojan los cambios.

Instala el rol de **IIS** en el Windows Server. Presenta una captura con la página **www.tunombre.tuapellido** que será una réplica de la primera página de Internet (mira los debates para saber más sobre esto). *(4 puntos contenido mínimo)*

Configura los enlaces del sitio (**site bindings**) para que sea accesible sólo por **www.tunombre.tuapellido**. Accede desde el propio Windows Server con `http://localhost`. Presenta captura del site binding, explica qué ocurre. *(2 puntos)*

Realiza una instalación de **PHP** para disponer de una página dinámica (no estática como la anterior). Ten en cuenta los siguientes manuales:  
- Paso 0. Últimas descargas de Visual C++ Redistributable compatibles | Microsoft Learn  (https://learn.microsoft.com/es-es/cpp/windows/latest-supported-vc-redist?view=msvc-170#latest-supported-redistributable-version)
- PHP For Windows: Binaries and sources Releases (https://windows.php.net/download/)

**IIS**  
Si usas PHP como **FastCGI** con IIS debes usar las versiones **Non-Thread Safe (NTS)** de PHP.  
- FastCGI `<fastCgi>` | Microsoft Learn (https://learn.microsoft.com/en-us/iis/configuration/system.webserver/fastcgi/)
- Step 1: Install IIS and PHP | Microsoft Learn (https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh994592(v=ws.11)#13-download-and-install-php-manually)
- PHP: Instalación con IIS para Windows - Manual (https://www.php.net/manual/es/install.windows.iis.php)

Captura (donde se vea autoría - nombre del servidor con tu nombre y apellidos) semejante a esta con el acceso a la página `http://www.tunombre.tuapellido/phpinfo.php`. *(3 puntos)*  
Si no consigues llegar hasta aquí, presenta captura/vídeo con el último paso dado: directorio con la instalación, ejecución de script, etc. Captura del mismo acceso desde un equipo cliente (puedes utilizar el Windows 11 de la otra práctica). Requiere configuración de **host-only** en VirtualBox. Adapta el php para que muestre información del cliente *(1 punto)*  
- Información del cliente y servidor con PHP - Victor Robles | Victor (https://victorroblesweb.es/2013/11/18/informacion-del-cliente-y-servidor-con-php/)

![img_1](images/img_1.png)

En total cuatro capturas.

Aquí un blog con los pasos que ha dado el autor para unir todos los pasos:

Como instalar PHP sobre IIS 10 en Windows Server 2019 Datacenter – Mtro. Guillermo Humberto Vera Amaro (https://www.uv.mx/personal/gvera/2022/02/26/como-instalar-php-sobre-iis-10-en-windows-server-2019-datacenter/)