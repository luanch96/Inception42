# 📚 DOCUMENTACIÓN PARA LA DEFENSA

Este directorio contiene toda la documentación necesaria para defender el proyecto INCEPTION.

## 📄 ARCHIVOS DISPONIBLES

### 1. `DEFENSA_PROYECTO.md` ⭐ **PRINCIPAL**
   - **Guía completa** con todas las explicaciones conceptuales
   - **Respuestas detalladas** a cada sección del proyecto
   - **Comandos de verificación** organizados por sección
   - **Preguntas frecuentes** y sus respuestas
   - **Checklist** de verificación pre-defensa

   👉 **Usa este archivo para estudiar y preparar tus respuestas.**

### 2. `RESUMEN_RAPIDO.md` ⚡ **RÁPIDO**
   - **Cheat sheet** con comandos esenciales
   - **Respuestas rápidas** a preguntas comunes
   - **Checklist** de verificación
   - **Puntos críticos** a recordar

   👉 **Usa este archivo durante la defensa como referencia rápida.**

### 3. `demo.sh` 🎬 **DEMOSTRACIÓN**
   - **Script ejecutable** para demostraciones
   - Verifica automáticamente todas las funcionalidades
   - Opciones: `network`, `ssl`, `wordpress`, `mariadb`, `persistence`, `all`

   👉 **Usa este script para demostrar el proyecto en vivo.**

## 🚀 CÓMO USAR

### Antes de la Defensa (Preparación)

1. **Lee completamente:** `DEFENSA_PROYECTO.md`
   - Estudia las explicaciones conceptuales
   - Practica los comandos
   - Memoriza las respuestas a preguntas frecuentes

2. **Practica los comandos:**
   ```bash
   # Ejecuta el script de demostración
   ./demo.sh all
   
   # Prueba cada sección individualmente
   ./demo.sh network
   ./demo.sh ssl
   ./demo.sh wordpress
   ./demo.sh mariadb
   ./demo.sh persistence
   ```

3. **Prepara tu entorno:**
   ```bash
   # Asegúrate de que todo está corriendo
   make run
   
   # Verifica que todo funciona
   ./demo.sh all
   ```

### Durante la Defensa

1. **Ten abierto:** `RESUMEN_RAPIDO.md`
   - Consulta comandos rápidos
   - Revisa respuestas rápidas
   - Verifica checklist

2. **Usa el script de demostración:**
   ```bash
   # Demostración completa
   ./demo.sh all
   
   # O por secciones según te pidan
   ./demo.sh ssl
   ```

3. **Demuestra comandos manualmente:**
   - Usa los comandos de `DEFENSA_PROYECTO.md` sección 9
   - Explica lo que estás haciendo mientras ejecutas

## 📋 ESTRUCTURA DE LA DEFENSA

### 1. Project Overview (Explicación)
- Docker y docker-compose
- Diferencia con/sin docker-compose
- Docker vs VMs
- Estructura de directorios

**Preparación:** Lee sección 1 de `DEFENSA_PROYECTO.md`

### 2. Simple Setup (Verificación)
- NGINX solo puerto 443
- SSL/TLS funcionando
- WordPress configurado

**Comandos:** Sección 2 de `DEFENSA_PROYECTO.md`

### 3. Docker Basics (Verificación)
- Dockerfiles existen
- No hay NGINX en WordPress/MariaDB
- Imágenes con nombres correctos

**Comandos:** Sección 3 de `DEFENSA_PROYECTO.md`

### 4. Docker Network (Explicación + Verificación)
- Red configurada
- Explicación de docker-network
- Conectividad entre contenedores

**Comandos:** Sección 4 de `DEFENSA_PROYECTO.md`

### 5. NGINX with SSL/TLS (Verificación)
- Certificado SSL
- TLS v1.2/v1.3
- Solo puerto 443 accesible

**Comandos:** Sección 5 de `DEFENSA_PROYECTO.md`

### 6. WordPress with php-fpm (Verificación)
- Volumen configurado
- Usuario admin correcto
- PHP-FPM funcionando

**Comandos:** Sección 6 de `DEFENSA_PROYECTO.md`

### 7. MariaDB and its volume (Verificación)
- Volumen configurado
- Root con contraseña
- Base de datos no vacía

**Comandos:** Sección 7 de `DEFENSA_PROYECTO.md`

### 8. Persistence (Verificación)
- Reiniciar VM
- Verificar que datos persisten
- WordPress y MariaDB configurados

**Comandos:** Sección 8 de `DEFENSA_PROYECTO.md`

## 💡 CONSEJOS PARA LA DEFENSA

1. **Sé claro y conciso:** Explica conceptos de forma simple
2. **Demuestra, no solo expliques:** Ejecuta comandos mientras explicas
3. **Anticípate a preguntas:** Revisa "Preguntas Frecuentes" en `DEFENSA_PROYECTO.md`
4. **No te quedes en blanco:** Si no sabes algo, di "déjame verificar" y consulta `RESUMEN_RAPIDO.md`
5. **Practica antes:** Ejecuta `./demo.sh all` varias veces

## 🎯 COMANDOS MÁS IMPORTANTES

```bash
# Estado general
docker-compose -f srcs/docker-compose.yml ps

# Demostración completa
./demo.sh all

# Verificar volúmenes
docker volume inspect <nombre-volumen>

# Verificar red
docker network inspect <nombre-red>

# Verificar WordPress
docker exec wordpress wp core is-installed --allow-root

# Verificar MariaDB
docker exec mariadb mysql -u root -p
```

## ⚠️ RECORDATORIOS IMPORTANTES

1. **Usuario admin:** `luisanch` (no contiene "admin") ✓
2. **Puerto 443 solo:** Verificar con `docker port nginx`
3. **Root con contraseña:** Login sin contraseña debe fallar
4. **Paths de volúmenes:** `/home/luisanch/data/`
5. **Base de datos:** Debe tener tablas de WordPress

## 📞 SI ALGO FALLA

1. **Contenedores no corren:** `make run`
2. **Errores de permisos:** `make set-permissions`
3. **Puerto 80 en uso:** `make stop-apache2`
4. **Certificados faltantes:** `make setup`

---

**¡Buena suerte en tu defensa! 🚀**

Recuerda: Estás preparado, has estudiado el proyecto, y tienes todos los recursos necesarios. ¡Confía en ti mismo!

