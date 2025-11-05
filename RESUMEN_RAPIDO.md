# RESUMEN RÁPIDO PARA LA DEFENSA

## 🚀 COMANDOS RÁPIDOS

### Estado General
```bash
docker-compose -f srcs/docker-compose.yml ps          # Estado contenedores
docker-compose -f srcs/docker-compose.yml logs        # Logs todos
./demo.sh all                                          # Demostración completa
```

### Docker Network
```bash
docker network ls                                      # Listar redes
docker network inspect <nombre-red>                   # Detalles red
docker exec nginx ping wordpress                      # Conectividad
```

### SSL/TLS
```bash
docker port nginx                                      # Puertos expuestos
curl -k https://luisanch.42.fr                       # Probar HTTPS
openssl s_client -connect localhost:443              # Verificar certificado
```

### WordPress
```bash
docker exec wordpress wp core is-installed --allow-root
docker exec wordpress wp user list --allow-root
docker exec wordpress ps aux | grep php-fpm
```

### MariaDB
```bash
docker exec mariadb mysql -u root -p                  # Login root
docker exec mariadb mysql -u <user> -p wordpress      # Login usuario
docker exec mariadb mysql -u root -p -e "USE wordpress; SHOW TABLES;"
```

### Volúmenes
```bash
docker volume ls                                       # Listar volúmenes
docker volume inspect <nombre-volumen>                # Detalles volumen
ls -la /home/luisanch/data/wordpress/                 # Ver datos host
ls -la /home/luisanch/data/mariadb/                   # Ver datos host
```

---

## 📝 RESPUESTAS RÁPIDAS

### ¿Cómo funciona Docker?
- **Contenedores:** Unidades ligeras que empaquetan aplicaciones y dependencias
- **Imágenes:** Plantillas en capas para crear contenedores
- **Aislamiento:** Cada contenedor tiene su propio espacio, pero comparte el kernel

### ¿Docker vs docker-compose?
- **Docker:** Comandos individuales (`docker run`, `docker build`)
- **docker-compose:** Orquestación multi-contenedor con un archivo YAML

### ¿Docker vs VMs?
- **Menor recursos:** Contenedores comparten kernel, VMs necesitan OS completo
- **Más rápido:** Contenedores inician en segundos, VMs en minutos
- **Más densidad:** Más contenedores que VMs en el mismo hardware

### ¿Por qué esta estructura de directorios?
- Separación por servicios (mariadb, nginx, wordpress)
- Fácil mantenimiento y escalabilidad
- Secrets separados del código

### ¿Cómo funciona la red Docker?
- Los contenedores en la misma red se comunican por nombre de servicio
- NGINX → `wordpress:9000` (PHP-FPM)
- WordPress → `mariadb:3306` (Base de datos)

### ¿Por qué root no puede hacer login sin contraseña?
- El `entrypoint.sh` crea root con contraseña obligatoria
- Scripts en background verifican periódicamente la seguridad
- Se eliminan usuarios root sin contraseña

### ¿Cómo persisten los datos?
- **Bind mounts:** Datos en `/home/luisanch/data/` en el host
- Después de reiniciar VM, los datos siguen ahí
- Al levantar docker-compose, se conectan a los mismos directorios

### ¿Por qué WordPress no muestra página de instalación?
- Script `setup-admin.sh` verifica si ya está instalado
- Si está instalado, salta la instalación
- Si no, instala automáticamente con WP-CLI

---

## ⚠️ PUNTOS CRÍTICOS A RECORDAR

1. **Puerto 443 solo:** Verificar con `docker port nginx`
2. **Usuario admin:** No debe contener "admin" → `luisanch` ✓
3. **Root con contraseña:** Intentar login sin contraseña debe fallar
4. **Volúmenes:** Paths deben ser `/home/luisanch/data/`
5. **Base de datos:** No debe estar vacía, debe tener tablas de WordPress
6. **No NGINX en Dockerfiles:** WordPress y MariaDB no deben tener NGINX

---

## 🔍 CHECKLIST PRE-DEFENSA

- [ ] Todos los contenedores corriendo: `docker-compose ps`
- [ ] Red creada: `docker network ls`
- [ ] Volúmenes con paths correctos: `/home/luisanch/data/`
- [ ] WordPress instalado: `wp core is-installed`
- [ ] Usuario no contiene "admin": `wp user list`
- [ ] Root requiere contraseña: Intentar login sin contraseña
- [ ] Solo puerto 443: `docker port nginx`
- [ ] SSL funciona: `curl -k https://luisanch.42.fr`
- [ ] Base de datos tiene tablas: `SHOW TABLES`

---

## 📚 ARCHIVOS IMPORTANTES

- `DEFENSA_PROYECTO.md` - Guía completa con explicaciones
- `demo.sh` - Script de demostración
- `RESUMEN_RAPIDO.md` - Este archivo (cheat sheet)

---

¡Éxito! 🚀

