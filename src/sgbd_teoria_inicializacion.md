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
