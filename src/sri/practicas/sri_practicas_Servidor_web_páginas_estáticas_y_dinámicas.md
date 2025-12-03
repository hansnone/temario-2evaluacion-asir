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

## Solucion

### Cambio de Hostname y Ajuste DNS

Acceder a **Administrador del servidor** \> Servidor local.
Clic en el nombre del equipo actual.
En Propiedades del sistema \> Cambiar.
Asignar nuevo nombre: **WIN-[Inicial][Apellido]** (ej. WIN-JPEREZ).
Reiniciar el servidor.

Abrir **Administrador de DNS** (`dnsmgmt.msc`).
En Zonas de búsqueda directa \> Dominio local.
Eliminar el registro A antiguo del servidor.
Verificar o crear nuevo registro A para **WIN-[Inicial][Apellido]** apuntando a la IP estática del servidor.
Crear un registro CNAME (alias) o A para **www.tunombre.tuapellido** apuntando a la misma IP.

### Instalación de IIS y CGI

En **Administrador del servidor** \> Administrar \> Agregar roles y características.
Seleccionar tipo de instalación basada en características o en roles.
En Roles del servidor, marcar **Servidor web (IIS)**.
En Servicios de rol (dentro de Servidor web \> Desarrollo de aplicaciones), marcar **CGI** (necesario para FastCGI/PHP).
Completar la instalación.

### Configuración del Sitio Web (Réplica)

Crear carpeta física: `C:\inetpub\wwwroot\websitename`.
Crear archivo `index.html` con el código HTML de la réplica de la primera página web.
Abrir **Administrador de Internet Information Services (IIS)**.
Clic derecho en Sitios \> Agregar sitio web.
Nombre del sitio: **www.tunombre.tuapellido**.
Ruta de acceso física: Seleccionar la carpeta creada.
En Enlace \> Nombre de host: escribir **www.tunombre.tuapellido**.

### Site Bindings y Test Localhost

En IIS, seleccionar el sitio creado.
En el panel de acciones (derecha), clic en **Enlaces...** (Bindings).
Verificar que el tipo es `http`, puerto `80` y Nombre de host `www.tunombre.tuapellido`.

**Prueba localhost:**
Abrir navegador en el servidor y acceder a `http://localhost`.
**Resultado:** Error 404 o página por defecto de IIS (IIS Welcome), no el sitio creado.
**Explicación:** El servidor web recibe la petición, pero al tener un *Host Header* definido en el enlace ("www..."), IIS descarta las peticiones que llegan con la cabecera "localhost". Solo sirve el contenido si la cabecera HTTP coincide estrictamente.

### Instalación y Configuración de PHP (NTS)

**Descarga y Preparación:**
Descargar e instalar **Visual C++ Redistributable** (vc\_redist.x64.exe).
Descargar **PHP Non-Thread Safe (NTS)** x64 zip.
Descomprimir en `C:\PHP`.
Renombrar `php.ini-production` a `php.ini`.

**Edición de php.ini:**
Abrir `php.ini` con editor de texto.
Descomentar y ajustar `extension_dir = "ext"`.
Descomentar `extension=curl`, `extension=mbstring`, `extension=mysqli` (según necesidad).
Configurar zona horaria: `date.timezone = "Europe/Madrid"`.
Guardar cambios.

**Variables de Entorno:**
Sistema \> Configuración avanzada del sistema \> Variables de entorno.
En Variables del sistema, editar **Path**.
Agregar nueva ruta: `C:\PHP`.

**Integración en IIS:**
Abrir Administrador de IIS.
Clic en el nombre del servidor (panel izquierdo).
Doble clic en **Asignaciones de controlador** (Handler Mappings).
En el panel derecho, clic en **Agregar asignación de módulo**.
Ruta de acceso de solicitudes: `*.php`
Módulo: **FastCgiModule** (Seleccionar de la lista. Si no aparece, revisar instalación del rol CGI).
Ejecutable: `C:\PHP\php-cgi.exe`.
Nombre: **PHP\_FastCGI**.
Confirmar diálogo de autorización.

Mover `index.php` a Documento predeterminado (Default Document) si se desea prioridad sobre html.

### Script PHP y Acceso Cliente

**Creación de Scripts:**
En `C:\inetpub\wwwroot\websitename`, crear archivo `phpinfo.php`:

```php
<?php phpinfo(); ?>
```

Crear archivo para info cliente (ej. `info_cliente.php`):

```php
<?php
echo "<h1>Información del Cliente</h1>";
echo "IP del Cliente: " . $_SERVER['REMOTE_ADDR'] . "<br>";
echo "Navegador: " . $_SERVER['HTTP_USER_AGENT'] . "<br>";
echo "Servidor: " . $_SERVER['SERVER_NAME'];
?>
```

**Configuración Cliente (Windows 11):**
Configurar adaptador de red en VirtualBox como **Red interna** o **Adaptador solo anfitrión** (Host-only), asegurando visibilidad con el servidor.
Configurar DNS del cliente: Asignar la IP estática del Windows Server como DNS preferido.
Alternativa sin DNS: Editar `C:\Windows\System32\drivers\etc\hosts` en el cliente y añadir:
`<IP_SERVER> www.tunombre.tuapellido`

**Validación:**
Navegador cliente \> `http://www.tunombre.tuapellido/phpinfo.php`.
Navegador cliente \> `http://www.tunombre.tuapellido/info_cliente.php`.