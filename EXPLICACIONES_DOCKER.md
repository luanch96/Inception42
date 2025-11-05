# Guía de Explicaciones - Docker y Docker Compose

## 1. ¿Cómo funcionan Docker y docker-compose?

### Docker
**Docker** es una plataforma de contenedorización que permite empaquetar una aplicación y sus dependencias en un contenedor ligero y portable.

- **Imagen**: Plantilla de solo lectura que define cómo crear un contenedor
- **Contenedor**: Instancia ejecutable de una imagen
- **Dockerfile**: Archivo de texto con instrucciones para construir una imagen
- **Volumen**: Almacenamiento persistente que sobrevive a la eliminación del contenedor
- **Red**: Permite comunicación entre contenedores

**Ejemplo en este proyecto:**
- Cada servicio (mariadb, nginx, wordpress) tiene su propio Dockerfile
- Estos Dockerfiles construyen imágenes personalizadas
- Los contenedores se crean a partir de estas imágenes

### Docker Compose
**Docker Compose** es una herramienta para definir y ejecutar aplicaciones multi-contenedor.

- **docker-compose.yml**: Archivo YAML que define servicios, redes, volúmenes
- Orquesta múltiples contenedores como una sola aplicación
- Gestiona dependencias entre servicios automáticamente
- Simplifica comandos (un solo `docker-compose up` en lugar de múltiples `docker run`)

**Ejemplo en este proyecto:**
- `docker-compose.yml` define 3 servicios: mariadb, wordpress, nginx
- Define una red compartida para que se comuniquen
- Define volúmenes para persistencia de datos
- Gestiona el orden de inicio (wordpress depende de mariadb)

---

## 2. Diferencia entre Docker image con docker-compose y sin docker-compose

### Sin Docker Compose
**Uso manual de imágenes Docker:**

```bash
# Crear red manualmente
docker network create mynetwork

# Crear volúmenes manualmente
docker volume create mariadb_data
docker volume create wordpress_data

# Ejecutar contenedores uno por uno
docker run -d --name mariadb --network mynetwork -v mariadb_data:/var/lib/mysql ...
docker run -d --name wordpress --network mynetwork --link mariadb ...
docker run -d --name nginx --network mynetwork -p 80:80 -p 443:443 ...
```

**Desventajas:**
- Muchos comandos manuales
- Gestión manual de redes y volúmenes
- Difícil recordar todas las opciones
- No hay gestión automática de dependencias
- Más propenso a errores

### Con Docker Compose
**Uso con docker-compose.yml:**

```bash
# Un solo comando para todo
docker-compose up -d
```

**Ventajas:**
- Un solo archivo define toda la infraestructura
- Gestión automática de redes y volúmenes
- Dependencias definidas (depends_on)
- Comandos simplificados
- Configuración versionada (docker-compose.yml)
- Más fácil de reproducir y mantener

**Ejemplo en este proyecto:**
- `docker-compose.yml` define todo: servicios, volúmenes, red, secretos
- `make` ejecuta `docker-compose build` y `docker-compose up` automáticamente
- Las dependencias están claras: nginx depende de wordpress, wordpress depende de mariadb

---

## 3. Beneficios de Docker comparado con VMs (Máquinas Virtuales)

### Comparación Técnica

| Aspecto | VMs | Docker |
|---------|-----|--------|
| **Overhead** | Alto (cada VM tiene su propio OS) | Bajo (comparte el kernel del host) |
| **Recursos** | Muchos (RAM, CPU, disco) | Pocos (solo lo necesario) |
| **Tiempo de inicio** | Minutos | Segundos |
| **Aislamiento** | Completo (nivel hardware) | Proceso (nivel OS) |
| **Portabilidad** | Limitada (necesita virtualización) | Alta (cualquier sistema con Docker) |

### Beneficios Clave de Docker

1. **Eficiencia de Recursos:**
   - VMs: Necesitan OS completo (2-4 GB RAM por VM)
   - Docker: Comparte el kernel, solo ~100-200 MB por contenedor
   - **Ejemplo**: Puedes correr 10 contenedores en la misma máquina donde 2-3 VMs serían problemáticas

2. **Velocidad:**
   - VMs: Inicio en 1-5 minutos
   - Docker: Inicio en segundos
   - **Ejemplo**: Reiniciar servicios es instantáneo con Docker

3. **Portabilidad:**
   - VMs: Dependen del hipervisor (VirtualBox, VMware, etc.)
   - Docker: Funciona igual en Linux, Windows, Mac, servidores en la nube
   - **Ejemplo**: Este proyecto funciona igual en tu máquina local y en un servidor de producción

4. **Desarrollo y Despliegue:**
   - VMs: "Funciona en mi máquina" sigue siendo un problema
   - Docker: "Funciona en mi máquina = funciona en todas partes"
   - **Ejemplo**: El evaluador puede ejecutar `make` y obtiene exactamente el mismo entorno

5. **Escalabilidad:**
   - VMs: Difícil escalar (crear nueva VM toma tiempo)
   - Docker: Escalar es crear más contenedores (muy rápido)
   - **Ejemplo**: Puedes correr múltiples instancias de nginx fácilmente

6. **Mantenimiento:**
   - VMs: Actualizar requiere actualizar el OS completo
   - Docker: Actualizar solo el contenedor específico
   - **Ejemplo**: Actualizar PHP en WordPress solo requiere reconstruir el contenedor wordpress

**En este proyecto:**
- Los 3 servicios (mariadb, wordpress, nginx) corren como contenedores ligeros
- Si fueran VMs, necesitarías 3 sistemas operativos completos
- Con Docker, solo necesitas el host OS y Docker

---

## 4. Pertinencia de la Estructura de Directorios del Proyecto

### Estructura Actual
```
Inception42-main/
├── srcs/
│   ├── docker-compose.yml
│   └── requirements/
│       ├── mariadb/
│       │   ├── Dockerfile
│       │   └── configuration/
│       │       ├── entrypoint.sh
│       │       └── init-db.sql
│       ├── nginx/
│       │   ├── Dockerfile
│       │   ├── conf/
│       │   │   └── nginx.conf
│       │   └── tools/
│       │       └── generate-ssl.sh
│       └── wordpress/
│           ├── Dockerfile
│           ├── conf/
│           │   └── wp-config.php
│           └── tools/
│               └── setup-admin.sh
├── secrets/
│   ├── mariadb_root_passwd.txt
│   └── ...
└── Makefile
```

### ¿Por qué esta estructura es pertinente?

#### 1. **Separación por Servicio**
- Cada servicio tiene su propio directorio (`mariadb/`, `nginx/`, `wordpress/`)
- **Ventaja**: Fácil encontrar y modificar la configuración de un servicio específico
- **Ventaja**: Cada servicio es independiente y mantenible

#### 2. **Organización de Configuración**
- `configuration/` para scripts de inicialización
- `conf/` para archivos de configuración
- `tools/` para scripts auxiliares
- **Ventaja**: Fácil distinguir entre configuración, scripts y herramientas
- **Ventaja**: Mantenimiento claro y organizado

#### 3. **Centralización de docker-compose.yml**
- Un solo archivo en `srcs/docker-compose.yml` define toda la orquestación
- **Ventaja**: Vista completa de la infraestructura en un lugar
- **Ventaja**: Fácil de entender las relaciones entre servicios

#### 4. **Secrets Separados**
- Directorio `secrets/` fuera de `srcs/`
- **Ventaja**: Seguridad (no se versiona en git normalmente)
- **Ventaja**: Fácil de gestionar y rotar credenciales

#### 5. **Escalabilidad**
- Estructura permite agregar nuevos servicios fácilmente
- **Ejemplo**: Agregar Redis sería solo crear `requirements/redis/`
- **Ventaja**: El proyecto crece de forma organizada

#### 6. **Portabilidad**
- Toda la configuración está en `srcs/`
- **Ventaja**: Fácil copiar/mover el proyecto completo
- **Ventaja**: El evaluador puede ejecutar `make` desde cualquier lugar

#### 7. **Mantenibilidad**
- Cada Dockerfile está junto a su configuración
- **Ventaja**: Fácil entender qué hace cada servicio
- **Ventaja**: Cambios en un servicio no afectan a otros

#### 8. **Cumplimiento del Subject**
- La estructura sigue el ejemplo del PDF del subject
- **Ventaja**: Cumple con los requisitos del proyecto
- **Ventaja**: Familiar para evaluadores que conocen el subject

### Comparación con Estructuras Alternativas

**❌ Estructura mala:**
```
proyecto/
├── Dockerfile
├── nginx.conf
├── wp-config.php
└── init-db.sql
```
**Problemas**: Todo mezclado, difícil saber qué corresponde a qué servicio

**✅ Estructura actual (buena):**
```
srcs/
├── docker-compose.yml
└── requirements/
    ├── mariadb/
    ├── nginx/
    └── wordpress/
```
**Ventajas**: Organizado, escalable, mantenible

---

## Resumen Rápido para la Evaluación

### Docker vs Docker Compose
- **Docker**: Herramienta para contenedorizar aplicaciones individuales
- **Docker Compose**: Herramienta para orquestar múltiples contenedores juntos

### Docker vs VMs
- **Docker**: Más ligero, más rápido, más eficiente, más portable
- **VMs**: Más pesado, más lento, menos eficiente, menos portable

### Estructura del Proyecto
- **Organizada por servicio**: Fácil de mantener y escalar
- **Separación clara**: Configuración, scripts y herramientas en sus lugares
- **Centralizada**: docker-compose.yml define toda la orquestación
- **Portable**: Todo el proyecto es fácil de mover y reproducir

---

## Puntos Clave para Recordar

1. **Docker** empaqueta aplicaciones en contenedores ligeros
2. **Docker Compose** orquesta múltiples contenedores como una aplicación
3. **Docker es más eficiente** que VMs porque comparte el kernel del host
4. **La estructura del proyecto** facilita mantenimiento, escalabilidad y portabilidad
5. **Cada servicio** tiene su propio directorio con Dockerfile y configuración

---

## Ejemplos Prácticos del Proyecto

### Ejemplo 1: Red Docker
```yaml
# En docker-compose.yml
networks:
  network:
    driver: bridge
```
- Todos los servicios están en la misma red
- Pueden comunicarse usando el nombre del servicio (ej: `mariadb`)
- Sin docker-compose, necesitarías crear la red manualmente

### Ejemplo 2: Volúmenes
```yaml
volumes:
  wordpress_data:
    driver: local
    driver_opts:
      device: /home/luisanch/data/wordpress
```
- Los datos persisten en el sistema de archivos del host
- Si el contenedor se elimina, los datos permanecen
- Sin docker-compose, gestionarías volúmenes manualmente

### Ejemplo 3: Dependencias
```yaml
wordpress:
  depends_on:
    - mariadb
```
- Docker Compose espera a que mariadb esté listo antes de iniciar wordpress
- Sin docker-compose, tendrías que esperar manualmente o usar scripts

---

¡Buena suerte en la evaluación! 🚀

