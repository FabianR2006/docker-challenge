# Docker Challenge — Repositorio plantilla

Este repositorio contiene una aplicación Flask ya implementada y un evaluador para el taller. Tu tarea es crear `Dockerfile` y `docker-compose.yml`.

1. Lee `ENUNCIADO.md
2. Crea los dos archivos requeridos en la raíz.
3. No modifiques `app.py`, `requirements.txt` ni el evaluador.
4. Sube el proyecto a tu repositorio individual de GitHub.
5. Revisa la pestaña **Actions** para consultar las pruebas.

Estructura final: añade `Dockerfile` y `docker-compose.yml` a los archivos existentes.

No subas tokens, contraseñas reales ni otros secretos. El workflow es una ayuda de verificación; el docente también revisará el repositorio y tu comprensión.
# Docker Challenge — De código a contenedores

## Descripción

Este proyecto consiste en contenerizar una aplicación Flask que permite registrar y consultar tareas utilizando PostgreSQL como base de datos.

Se utilizaron Docker y Docker Compose para ejecutar ambos servicios de manera independiente y permitir su comunicación mediante una red interna.

## Mi solución

### 1. Dockerfile

Se creó un Dockerfile utilizando la imagen `python:3.11-slim`.

Se configuró el directorio de trabajo `/app`, se instalaron las dependencias desde `requirements.txt`, se copió el código de la aplicación, se expuso el puerto 5000 y se configuró el inicio mediante `python app.py`.

### 2. Docker Compose

Se creó un archivo `docker-compose.yml` con dos servicios:

- **web:** ejecuta la aplicación Flask y publica el puerto 5000.
- **db:** utiliza la imagen oficial `postgres:16` para almacenar las tareas.

Se configuró `DATABASE_URL` para conectar Flask con PostgreSQL.

### 3. Comunicación entre servicios

La conexión utiliza el hostname `db` en lugar de `localhost` porque Docker Compose crea una red interna donde los servicios pueden comunicarse utilizando sus nombres.

Dentro del contenedor web, `localhost` hace referencia al propio contenedor Flask, no al contenedor de PostgreSQL.

### 4. Persistencia de datos

Se creó un volumen nombrado llamado `postgres_data`, montado en `/var/lib/postgresql/data`.

Este volumen permite conservar la información de PostgreSQL aunque se eliminen los contenedores.

Al ejecutar `docker compose down`, los contenedores se eliminan, pero el volumen permanece. Al ejecutar nuevamente `docker compose up -d`, los datos siguen disponibles.

En cambio, `docker compose down -v` elimina también los volúmenes declarados por el proyecto.

### 5. Pruebas realizadas

Se ejecutaron los siguientes comandos:

```bash
docker compose config
docker compose up --build -d
docker compose ps
curl http://localhost:5000/health
curl http://localhost:5000/tasks
```

Se comprobó que los dos servicios iniciaron correctamente y que PostgreSQL respondió satisfactoriamente.

También se registró una tarea mediante la API:

```bash
curl -X POST http://localhost:5000/tasks -H "Content-Type: application/json" -d '{"title":"Mi primera tarea creada docker"}'
```

Respuesta obtenida:

```json
{"id":1,"title":"Mi primera tarea creada docker"}
```

### 6. Prueba de persistencia

Se ejecutó `docker compose down` y posteriormente `docker compose up -d`.

Después de reiniciar los servicios, se consultaron nuevamente las tareas y se comprobó que el registro anterior seguía almacenado.

### 7. Tecnologías utilizadas

- Docker
- Docker Compose
- Python 3.11
- Flask
- PostgreSQL 16
