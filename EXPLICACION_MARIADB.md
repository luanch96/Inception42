# Explicación de Archivos de Configuración de MariaDB

## Resumen de los Archivos de MariaDB

---

### 1. `entrypoint.sh` - Script Principal de Inicialización

**¿Qué hace?**
- Es el script principal que se ejecuta cuando arranca el contenedor MariaDB
- Si es la primera vez que se ejecuta (cuando no existe `/var/lib/mysql/mysql`), inicializa toda la base de datos desde cero
- Lee las contraseñas desde los secretos de Docker (`/run/secrets/`)
- Configura la seguridad: elimina usuarios anónimos y usuarios root sin contraseña
- Crea el usuario `root` con contraseña para `localhost` y `%` (acceso remoto)
- Crea la base de datos `wordpress` y el usuario de WordPress con todos los permisos
- Inicia el servidor `mysqld` y ejecuta scripts en segundo plano para mantener la seguridad

**Orden de ejecución:**
1. Verifica si la base de datos ya existe
2. Si no existe → inicializa todo desde cero
3. Si ya existe → solo inicia el servidor
4. Scripts en background aseguran que root siempre tenga contraseña

---

### 2. `init-db.sql` - Script SQL Simple

**¿Qué hace?**
- Solo crea la base de datos `wordpress` si no existe
- Está copiado en `/docker-entrypoint-initdb.d/` pero en realidad no se usa directamente porque `entrypoint.sh` hace todo el trabajo

**Contenido:**
```sql
CREATE DATABASE IF NOT EXISTS wordpress;
```

---

### 3. `settings.sh` - Script de Configuración (No se usa actualmente)

**¿Qué hace?**
- Está diseñado para crear usuarios y bases de datos adicionales
- Tiene errores: intenta usar `$MYSQL_ROOT_PASSWORD` sin leerlo del archivo secreto
- Está copiado como template pero no se ejecuta porque `entrypoint.sh` ya hace todo

**Nota:** Este archivo está presente pero no se utiliza en el flujo actual.

---

### 4. `setup-database.sh` - Script del HOST (ejecutado desde fuera del contenedor)

**¿Qué hace?**
- Se ejecuta desde tu máquina (no dentro del contenedor)
- Espera a que MariaDB esté listo (hace ping al servidor)
- Crea la base de datos `wordpress` y el usuario `wpuser` con contraseña `wppassword`
- Usa valores hardcodeados (`wpuser`/`wppassword`), no los secretos del docker-compose

**Problema:** Este script usa credenciales diferentes a las del `docker-compose.yml`, lo que puede causar conflictos.

---

## Flujo Completo de Inicialización

```
1. Contenedor inicia
   ↓
2. entrypoint.sh se ejecuta
   ↓
3. Lee secretos: root_password, user, password
   ↓
4. Si BD no existe:
   - Inicializa MariaDB
   - Crea usuario root con contraseña
   - Crea BD wordpress
   - Crea usuario de WordPress
   ↓
5. Inicia mysqld
   ↓
6. Scripts en background mantienen seguridad
```

---

## Problemas Detectados

1. **`settings.sh`** no se usa y tiene errores
2. **`setup-database.sh`** usa credenciales diferentes a las del docker-compose
3. **`init-db.sql`** es redundante porque `entrypoint.sh` ya crea la BD

---

## Conclusión

El archivo más importante es **`entrypoint.sh`**, que maneja toda la inicialización y configuración de MariaDB. Los otros archivos son redundantes o no se usan en el flujo actual.

