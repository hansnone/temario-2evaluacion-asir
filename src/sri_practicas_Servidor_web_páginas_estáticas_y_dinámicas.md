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

### 1. Cambio de Hostname (GUI)

1.  Abrir **Server Manager** > **Local Server**.
2.  Clic en el nombre actual del equipo (junto a "Computer name").
3.  En la ventana **System Properties**, pestaña **Computer Name**, clic en **Change...**
4.  En "Computer name", escribir: `WIN-tunombreinicialapellido`.
5.  Clic **OK** > **OK** > **Close**.
6.  Clic en **Restart Now**.

### 2. Configuración DNS

1.  Abrir **Server Manager** > **Tools** > **DNS**.
2.  Expandir el servidor. Clic derecho en **Forward Lookup Zones** > **New Zone...**
3.  Wizard: **Next** > **Primary zone** > **Next**.
4.  Zone name: `tunombre.tuapellido` > **Next** > **Next** > **Finish**.
5.  Entrar en la zona creada (panel izquierdo).
6.  Clic derecho en espacio en blanco (panel derecho) > **New Host (A or AAAA)...**
7.  Name: `www`.
8.  IP address: La IP estática de tu servidor.
9.  Clic **Add Host** > **Done**.

### 3. Instalación de Roles IIS y CGI

1.  **Server Manager** > **Manage** > **Add Roles and Features**.
2.  **Next** hasta "Server Roles".
3.  Marcar **Web Server (IIS)**. Aceptar características requeridas.
4.  **Next**. En "Role Services" (dentro de Web Server > Application Development), marcar **CGI**. (Necesario para PHP FastCGI).
5.  **Next** > **Install**.

### 4. Configuración del Sitio Web y Bindings

1.  **Explorador de Archivos**: Ir a `C:\inetpub\wwwroot`.
2.  Crear carpeta: `www.tunombre.tuapellido`.
3.  Dentro, crear archivo de texto y renombrar a `index.html`. Pegar contenido HTML.
4.  **Server Manager** > **Tools** > **Internet Information Services (IIS) Manager**.
5.  Clic derecho en **Sites** > **Add Website...**
    * **Site name**: `www.tunombre.tuapellido`.
    * **Physical path**: Seleccionar la carpeta creada en el paso 2.
    * **Binding**:
        * Type: `http`.
        * IP address: `All Unassigned` o la IP del servidor.
        * Port: `80`.
        * **Host name**: `www.tunombre.tuapellido`.
6.  Clic **OK**.

### 5. Instalación y Configuración PHP (GUI)

**Preparación de archivos:**
1.  Descargar e instalar **Visual C++ Redistributable (x64)** (ejecutable).
2.  Descargar **PHP (NTS x64)** .zip.
3.  Extraer el .zip en `C:\php`.
4.  En `C:\php`, buscar `php.ini-production`. Clic derecho > **Rename** a `php.ini`.
5.  Abrir `php.ini` con Notepad:
    * Buscar y quitar el punto y coma (;) inicial de:
        * `extension_dir = "ext"`
        * `cgi.force_redirect = 0`
        * `fastcgi.impersonate = 1`
        * `date.timezone = "Europe/Madrid"`
    * Guardar y cerrar.

**Configuración en IIS Manager:**
1.  Clic en el nombre del servidor (panel izquierdo).
2.  Doble clic en icono **Handler Mappings**.
3.  En panel derecho (Actions), clic **Add Module Mapping...**
    * **Request path**: `*.php`
    * **Module**: Seleccionar `FastCgiModule` (si no aparece, revisa paso 3 de esta guía).
    * **Executable**: Clic `...` y buscar `C:\php\php-cgi.exe`.
    * **Name**: `PHP_FastCGI`.
4.  Clic **OK**. Confirmar con **Yes** en el diálogo que aparece.
5.  Volver al servidor (panel izquierdo). Doble clic en **Default Document**.
6.  Panel derecho > **Add...** > Escribir `index.php` > **OK**.

### 6. Verificación Cliente (Windows 11)

1.  En Windows 11: **Configuración** > **Red e Internet** > **Ethernet** (o adaptador Host-Only).
2.  Editar asignación de servidor DNS.
3.  Establecer la **IP del Windows Server** como DNS preferido.
4.  Abrir navegador en cliente y visitar `http://www.tunombre.tuapellido/phpinfo.php`.