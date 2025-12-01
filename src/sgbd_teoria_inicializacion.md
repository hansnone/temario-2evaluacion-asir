# Inicializacion de base de datos
 
 ## Comandos de la teoria


| Comando                                                                                                   | Descripción                                                                                     |
|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| **startup nomount**                                                                                      | Inicia la instancia sin montar la base de datos (solo memoria y procesos).                   |
| **SELECT status FROM v$instance**                                                                        | Muestra el estado actual de la instancia (STARTED, MOUNTED, OPEN).                           |
| **alter database mount**                                                                                 | Monta la base de datos, accediendo a los ficheros de control, sin abrirla.                   |
| **alter database open**                                                                                  | Abre la base de datos para operaciones de lectura/escritura.                                  |
| **SELECT name FROM v$controlfile**                                                                       | Muestra las rutas de los ficheros de control actuales.                                        |
| **ALTER SYSTEM SET control_files = 'C:\ORACLEXE\APP\ORACLE\ORADATA\XE\CONTROL.DBF', 'C:\ORACLEXE\APP\ORACLE\ORADATA\XE\CONTROLC1.DBF' SCOPE=SPFILE** | Modifica el parámetro `control_files` en el SPFILE para definir las rutas de los ficheros.   |
| **SELECT name, value, isdefault FROM v$parameter ORDER BY name**                                         | Lista parámetros dinámicos de la instancia con su valor y si son por defecto.                |
| **SELECT name, value, isspecified FROM v$spparameter WHERE value IS NOT NULL ORDER BY name**             | Muestra parámetros definidos en el SPFILE que tienen valor asignado.                         |
| **CREATE PFILE='C:\clase\02_AdmonBD\mis_parametros.ora' FROM SPFILE**                                    | Genera un fichero PFILE a partir del SPFILE actual.                                           |
| **SELECT name, value, isbasic, isdefault FROM v$parameter WHERE name LIKE '%memory%'**                   | Consulta parámetros relacionados con memoria (SGA, PGA, etc.).                               

### 1. Marco de Gestión de la Base de Datos

El marco de gestión de una base de datos Oracle 10g/11g consta de tres componentes principales:
* **Instancia de la base de datos**.
* **Listener** (permite las conexiones).
* **Interfaz de gestión**: Puede ser **Database Control** (versión autónoma de Oracle Enterprise Manager) o un **Agente de gestión** (al usar Grid Control).

El primer componente en iniciarse debe ser la **interfaz de gestión**, que luego puede iniciar los demás componentes.

### 2. Inicialización (Arranque) de la Base de Datos

"Inicializar" la base de datos generalmente significa arrancar la instancia y la base de datos para que acepten conexiones. El proceso de arranque implica cargar en memoria:
* Parámetros de inicialización (SPFILE o INIT.ORA).
* Estructuras internas (SGA, PGA).
* Procesos de fondo.

Técnicamente, el comando `STARTUP` ejecuta automáticamente los siguientes tres pasos:

| Comando | Estado (Modo) | Descripción |
| :--- | :--- | :--- |
| `STARTUP NOMOUNT` | **STARTED** (NOMOUNT) | Se crea la instancia: se leen parámetros y se inicializa la SGA y procesos de fondo. **No** se leen archivos de control ni se abre la base. Se usa para crear una BD nueva o recrear control files. |
| `STARTUP MOUNT` | **MOUNTED** (MOUNT) | Se montan los archivos de control. La instancia conoce la ubicación de datafiles y redo logs, pero la base **no** está abierta para usuarios. Se usa para backups/recovery (RMAN) o cambiar a modo ARCHIVELOG. |
| `STARTUP OPEN` (o `STARTUP`) | **OPEN** (OPEN) | **Estado normal de operación**. Se abren los datafiles y redo logs, y la BD está lista para usuarios y transacciones. |
| `STARTUP UPGRADE` | **OPEN MIGRATE** | Estado especial: BD abierta, pero solo accesible para usuarios con privilegios especiales. Se usa para procesos de migración o actualización del diccionario. |

El estado de la instancia se puede verificar con `SELECT status FROM v$instance;`.

### 3. Parada (Apagado) de la Base de Datos

Existen cuatro modos principales de apagar la base de datos desde $SQL*Plus$:

| Comando | Uso | Descripción |
| :--- | :--- | :--- |
| `SHUTDOWN IMMEDIATE` | **Recomendado**. | Cierra sesiones activas de forma segura y apaga rápido. |
| `SHUTDOWN NORMAL` | Espera a que los usuarios se desconecten. | Puede tardar mucho si hay usuarios conectados. |
| `SHUTDOWN TRANSACTIONAL` | Espera a que terminen las transacciones activas, luego desconecta usuarios. |
| `SHUTDOWN ABORT` | **Solo en emergencias**. | Mata procesos inmediatamente. Requiere *RECOVER* o *checkpoint* al levantar la BD. |

### 4. Ficheros Clave de la Base de Datos

#### A. Fichero de Configuración: SPFILE

* **SPFILE** (Server Parameter File) es un fichero binario recomendado que Oracle usa al arrancar la instancia.
* Se modifica con comandos SQL como `ALTER SYSTEM SET parámetro valor SCOPE = SPFILE;`.
* Los parámetros pueden ser **dinámicos** (se cambian con la BD abierta, `SCOPE=BOTH`) o **estáticos** (requieren reinicio, `SCOPE=SPFILE`).
* Se puede ver si la instancia usa SPFILE consultando `SHOW PARAMETER spfile;`.
* El contenido del SPFILE se puede ver en la vista `v$spparameter`.

#### B. Ficheros de Control: Control Files

* Archivos **críticos** que contienen el mapa y metadata esencial de la base de datos.
* Almacenan el nombre de la BD, el SCN, la ubicación y estado de datafiles y redo logs, y el modo ARCHIVELOG.
* Oracle recomienda al menos dos copias (**multiplexación**) para evitar pérdidas.
* Se leen en `STARTUP MOUNT` y `OPEN`.
* Su ubicación se consulta con `SHOW PARAMETER control_files;` o `SELECT name FROM v$controlfile;`.

#### C. Ficheros de Redo Log: Redo Log Files

* Registran todas las modificaciones (DML y DDL) hechas en la base de datos para permitir la **recuperación** tras un fallo.
* Están organizados en **Redo Log Groups**, donde cada grupo puede contener uno o más archivos idénticos (multiplexación).
* El proceso de rotar de un grupo a otro se llama **log switch**.

---

¿Te gustaría que profundice en alguno de los estados de la base de datos o en los comandos de parada?