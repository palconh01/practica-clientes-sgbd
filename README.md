# Práctica: Instalación y Conectividad de Clientes para SGBD (Oracle, PostgreSQL y MariaDB)

**Autor:** Pablo Alcón Hernández

**Sistema Base:** Linux Mint (Base Ubuntu 24.04 LTS 'Noble')

---

## 1. Descripción del Entorno y Servidores

El entorno de laboratorio consta de tres Sistemas Gestores de Bases de Datos (SGBD) ejecutándose localmente sobre la máquina virtual:

- **Oracle Database:** Oracle AI Database 26ai Free escuchando en el puerto `1521` (Servicio `FREEPDB1`).
- **PostgreSQL:** PostgreSQL `16.15`  escuchando en el puerto `5432`.
- **MariaDB:** MariaDB `10.11.14` escuchando en el puerto `3306`.

---

## 2. Resumen de Clientes Instalados y Versiones

| SGBD Objetivo | Cliente de Consola (CLI) | Cliente Gráfico (GUI) | Cliente Web |
| --- | --- | --- | --- |
| **Oracle Database** | SQL*Plus (`23.26.4.0.0`) | Oracle SQL Developer | - |
| **PostgreSQL** | psql (`16.15`) | pgAdmin 4 | Adminer (`6.1.2`) |
| **MariaDB** | mariadb (`Ver 15.1 Distrib 10.11.14`) | DBeaver (`26.2.3`) | phpMyAdmin / Adminer (`6.1.2`) |
| **Multi-SGBD** | - | DBeaver Community (`26.2.3`) | Adminer (`6.1.2`) |

---

## 3. Parámetros de Conexión Configurados

Para garantizar la seguridad, en todas las herramientas de línea de comandos se ha forzado la solicitud interactiva de credenciales, evitando exponer contraseñas en el historial de la shell o en texto plano:

- **Oracle:**
    - **Host:** `localhost` | **Puerto:** `1521` | **Service Name:** `FREEPDB1` | **Usuario:** `orauser`
    - **Comando CLI:** `sqlplus orauser@//localhost:1521/FREEPDB1`
- **PostgreSQL:**
    - **Host:** `localhost` / `127.0.0.1` | **Puerto:** `5432` | **Base de datos:** `dbpg` | **Usuario:** `pguser`
    - **Comando CLI:** `psql -h localhost -U pguser -d dbpg -W`
- **MariaDB:**
    - **Host:** `localhost` | **Puerto:** `3306` | **Base de datos:** `dbm` | **Usuario:** `muser`
    - **Comando CLI:** `mariadb -h localhost -u muser -p dbm`

---

## 4. Evidencias de Funcionamiento

### 4.1. Clientes de Oracle Database

#### SQL*Plus (Consola)

Verificación de versión (

```
23.26.4.0.0
```

) e inicio de sesión interactivo contra el servicio

```
FREEPDB1
```

:

![conexion](ConexionSQL.png)

#### Oracle SQL Developer (Gráfico)

Conexión

```
Oracle-local
```

establecida mostrando el árbol de objetos y diccionarios de datos del esquema:

![conexion](SQLdev.png)

---

### 4.2. Clientes de PostgreSQL

#### psql (Consola)

Comprobación de versión (

```
16.15
```

) y conexión cifrada vía TLSv1.3 a la base de datos

```
dbpg
```

:

![conexion](PostgreSQLC.png)

#### pgAdmin 4 (Gráfico)

Servidor

```
PostgreSQL Local
```

registrado, mostrando el panel de actividad en tiempo real y el esquema

```
public
```

de la base de datos

```
dbpg
```

:

![conexion](PGadmin.png)

---

### 4.3. Clientes de MariaDB

#### MariaDB Client (Consola)

Verificación del cliente (

```
Ver 15.1 Distrib 10.11.14-MariaDB
```

) y acceso al monitor interactivo sobre la base de datos

```
dbm
```

:

![conexion](Mariadb.png)

#### phpMyAdmin (Web)

Acceso mediante navegador web (

```
http://localhost/phpmyadmin/
```

) autenticado como

```
muser@localhost
```

visualizando la base de datos

```
dbm
```

:

![conexion](PHPmyAD.png)

---

### 4.4. Clientes Multi-SGBD

#### DBeaver Community (Gráfico Unificado)

Interfaz de DBeaver

```
26.2.3
```

gestionando de manera simultánea las conexiones activas a MariaDB (

```
dbm
```

en

```
localhost:3306
```

), PostgreSQL (

```
dbpg
```

en

```
localhost:5432
```

) y Oracle (

```
FREEPDB1
```

en

```
localhost:1521
```

):

![conexion](MultiSGBD.png)

#### Adminer (Web Ligero)

Ejecución de Adminer

```
6.1.2
```

sobre el servidor embebido de PHP (

```
http://localhost:8080/
```

), conectado al esquema

```
public
```

de la base de datos

```
dbpg
```

en PostgreSQL (

```
127.0.0.1
```

):

![conexion](adminer.png)

---

## 5. Problemas Encontrados y Soluciones Aplicadas

Durante el despliegue y configuración de los clientes en Linux Mint (base Ubuntu 24.04 'Noble'), encontre y solucione las siguientes incidencias técnicas:


### 1. Error con JavaFX al abrir Oracle SQL Developer

- **Problema:** Cuando intenté abrir SQL Developer ejecutando `/opt/sqldeveloper/sqldeveloper.sh`, el programa no arrancaba y me salía el error `Module javafx.base not found`. Esto me pasó porque la versión normal de Java que viene en Ubuntu no trae incluidas las librerías gráficas de JavaFX.
- **Solución:** Descargué e instalé **BellSoft Liberica Full JDK 17** (`bellsoft-jdk17-full`), que es una versión de Java que ya trae JavaFX incluido. Después, indiqué la ruta de este nuevo Java (`SetJavaHome /usr/lib/jvm/bellsoft-java17-full-amd64`) dentro de los archivos de configuración `product.conf` y `sqldeveloper.conf`.

### 2. Error `404 Not Found` al entrar a phpMyAdmin

- **Problema:** Después de instalar `phpmyadmin`, al entrar en `http://localhost/phpmyadmin/` desde el navegador me salía el error `404 Not Found` porque Apache no había activado automáticamente la configuración de phpMyAdmin durante la instalación.
- **Solución:** Activé la configuración manualmente con el comando `sudo a2enconf phpmyadmin` y reinicié Apache con `sudo systemctl restart apache2` para que aplicara los cambios.

### 3. Falta de la extensión de PostgreSQL para PHP en Adminer

- **Problema:** Al intentar conectarme a PostgreSQL desde Adminer, me saltó el aviso *"Ninguna de las extensiones PHP soportadas (PgSQL, PDO_PgSQL) está disponible"*, ya que a PHP le faltaba el complemento para comunicarse con PostgreSQL.
- **Solución:** Instalé el paquete necesario con `sudo apt install -y php-pgsql` y volví a arrancar el servidor de PHP (`php -S 127.0.0.1:8080 adminer.php`).

### 4. Fallo de autenticación `Peer` en PostgreSQL desde Adminer

- **Problema:** Al poner `localhost` como servidor en Adminer, me daba el error `FATAL: Peer authentication failed for user "pguser"`. Esto ocurría porque, con `localhost`, PostgreSQL intenta comprobar si mi usuario de Linux se llama igual que el usuario de la base de datos (`pguser`), en vez de pedirme la contraseña normal.
- **Solución:** Cambié `localhost` por la IP `127.0.0.1` en el campo *Servidor* de Adminer. De esta forma obligué a que se conectara por red local usando la contraseña del usuario y ya me dejó entrar sin problemas.
