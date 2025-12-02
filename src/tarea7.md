# Tarea Nº 7 - Despliegue Nginx
**1. Detener el servidor Apache2**

```bash
sudo systemctl stop apache2
```

**2. Deshabilitar el inicio automático de Apache2**

```bash
sudo systemctl disable apache2
```

**3. Instalar el paquete Nginx**

```bash
sudo apt update
sudo apt install nginx -y
```

**4 y 8. Configuración de Virtual Hosts (Puerto 80 y 81) y creación de contenido**

Creación de directorios y ficheros `index.html` para diferenciar los sitios:

```bash
# Sitio Puerto 80
sudo mkdir -p /var/www/sitio80
echo "<h1>Web en Puerto 80 - Nginx</h1>" | sudo tee /var/www/sitio80/index.html

# Sitio Puerto 81
sudo mkdir -p /var/www/sitio81
echo "<h1>Web en Puerto 81 - Nginx</h1>" | sudo tee /var/www/sitio81/index.html
```

Creación del fichero de configuración `/etc/nginx/sites-available/multi-port`:

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        root /var/www/sitio80;
        index index.html;
    }
}

server {
    listen 81;
    server_name localhost;

    location / {
        root /var/www/sitio81;
        index index.html;
    }
}
```

Habilitar la configuración y eliminar el enlace por defecto para evitar conflictos:

```bash
sudo ln -s /etc/nginx/sites-available/multi-port /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
```

**5. Comandos de gestión del servicio Nginx**

  * **Arranque:** `sudo systemctl start nginx`
  * **Parada:** `sudo systemctl stop nginx`
  * **Estado:** `sudo systemctl status nginx`
  * **Recarga (sin corte de servicio):** `sudo systemctl reload nginx`

**6. Rutas de ficheros**

  * **Configuración principal:** `/etc/nginx/nginx.conf`
  * **Configuración de sitios (vhosts):** `/etc/nginx/sites-available/` y `/etc/nginx/sites-enabled/`
  * **Directorio Web (root):** `/var/www/`
  * **Logs de acceso:** `/var/log/nginx/access.log`
  * **Logs de error:** `/var/log/nginx/error.log`

**7. Verificación de sintaxis**

```bash
sudo nginx -t
```

*Si la salida es "syntax is ok" y "test is successful", proceder con el reinicio o recarga.*

-----

**Solución al problema de visualización de la página de Apache**

Si al entrar al puerto 80 sigue apareciendo la página de "Apache2 Default Page", se debe a una de las siguientes causas:

1.  **Caché del navegador:** El navegador ha guardado la versión anterior.
      * *Solución:* Probar acceso con `curl localhost` desde la terminal o usar modo incógnito (Ctrl+Shift+N).
2.  **Raíz por defecto compartida:** Si no se elimina el sitio `default` de Nginx y este apunta a `/var/www/html`, mostrará el `index.html` que dejó Apache instalado en esa ruta.
      * *Solución:* Asegurar la ejecución del comando `sudo rm /etc/nginx/sites-enabled/default` indicado en el punto 4 y recargar Nginx (`sudo systemctl reload nginx`).
3.  **Proceso Zombie:** Apache no se detuvo correctamente y sigue ocupando el puerto 80.
      * *Diagnóstico:* `sudo lsof -i :80` o `sudo netstat -tulpn | grep :80`
      * *Solución:* Si aparece `apache2` en el resultado, ejecutar `sudo killall apache2`.

**Verificación final (Simulación de capturas)**

Ejecutar en terminal para validar funcionamiento:

```bash
curl http://localhost:80
# Salida esperada: <h1>Web en Puerto 80 - Nginx</h1>

curl http://localhost:81
# Salida esperada: <h1>Web en Puerto 81 - Nginx</h1>
```

## Script automatico

Guardar el siguiente contenido en un fichero llamado `setup_nginx.sh`, dar permisos de ejecución y lanzar con privilegios de root.

```bash
#!/bin/bash

# Comprobación de privilegios
if [ "$EUID" -ne 0 ]; then
  echo "Error: Este script debe ejecutarse como root."
  exit 1
fi

echo "--- INICIO DE CONFIGURACIÓN AUTOMATIZADA ---"

# 1 & 2. Gestión de Apache2 (Detener y Deshabilitar)
echo "[1/5] Deteniendo y deshabilitando Apache2..."
systemctl stop apache2
systemctl disable apache2

# Liberación forzosa del puerto 80 si Apache se quedó "zombie"
fuser -k 80/tcp 2>/dev/null

# 3. Instalación de Nginx
echo "[2/5] Instalando Nginx..."
apt-get update -qq
apt-get install nginx -y -qq

# 4 & 8. Creación de contenido web (Directorios e index.html)
echo "[3/5] Generando contenido web para puertos 80 y 81..."
mkdir -p /var/www/sitio80
mkdir -p /var/www/sitio81

echo "<h1>Sitio Web Puerto 80 - Script</h1>" > /var/www/sitio80/index.html
echo "<h1>Sitio Web Puerto 81 - Script</h1>" > /var/www/sitio81/index.html

# Generación del fichero de configuración de Nginx
echo "[4/5] Escribiendo configuración de VHosts..."
cat <<EOF > /etc/nginx/sites-available/multi-port
server {
    listen 80;
    server_name localhost;

    location / {
        root /var/www/sitio80;
        index index.html;
    }
}

server {
    listen 81;
    server_name localhost;

    location / {
        root /var/www/sitio81;
        index index.html;
    }
}
EOF

# Enlace simbólico y limpieza de default
ln -sf /etc/nginx/sites-available/multi-port /etc/nginx/sites-enabled/
rm -f /etc/nginx/sites-enabled/default

# 7. Verificación y aplicación
echo "[5/5] Verificando y reiniciando servicio..."
if nginx -t; then
    systemctl restart nginx
    echo "--- CONFIGURACIÓN COMPLETADA CON ÉXITO ---"
    
    # Test local
    echo "Test Puerto 80:"
    curl -s http://localhost:80
    echo ""
    echo "Test Puerto 81:"
    curl -s http://localhost:81
    echo ""
else
    echo "Error: La configuración de Nginx no es válida."
    exit 1
fi
```

**Instrucciones de ejecución:**

```bash
chmod +x setup_nginx.sh
sudo ./setup_nginx.sh
```