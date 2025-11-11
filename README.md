# Entorno de Bases de Datos con Docker Compose

Este entorno levanta múltiples bases de datos y herramientas de administración listas para desarrollo local, ideal para pruebas, desarrollo de microservicios y prototipos.

---

## Arquitectura y Capacidades

- **Red interna:** Todos los servicios están conectados a la red `dev-net`, permitiendo comunicación entre contenedores usando el nombre del servicio como hostname.
- **Persistencia:** Todo se guarda en memoria RAM así que es totalmente temporal.
- **Herramientas web:** Incluye Adminer, Mongo Express y Redis Commander para administración visual.
- **Compatibilidad:** Listo para usarse con cualquier lenguaje o framework que soporte conexiones estándar (Node.js, Python, Java, etc).

---

## Servicios y Accesos

### PostgreSQL
- **URL externa:**  
  `localhost:5432`
- **Usuario:**  
  `dev`
- **Contraseña:**  
  `devpass`
- **Base de datos:**  
  `devdb`
- **Nombre de host interno (Docker):**  
  `postgres`

### MySQL
- **URL externa:**  
  `localhost:3306`
- **Usuario root:**  
  `root`
- **Contraseña:**  
  `devpass`
- **Base de datos:**  
  `devdb`
- **Nombre de host interno (Docker):**  
  `mysql`

### MongoDB
- **URL externa:**  
  `localhost:27017`
- **Usuario root:**  
  `dev`
- **Contraseña:**  
  `devpass`
- **Nombre de host interno (Docker):**  
  `mongo`

### Redis
- **URL externa:**  
  `localhost:6379`
- **Sin autenticación por defecto**
- **Nombre de host interno (Docker):**  
  `redis`

---

## Herramientas de Administración

### Adminer
- **URL:**  
  [http://localhost:8080](http://localhost:8080)
- **Acceso:**  
  Selecciona el motor de base de datos (MySQL o PostgreSQL) e ingresa las credenciales correspondientes.

### Mongo Express
- **URL:**  
  [http://localhost:8081](http://localhost:8081)
- **Usuario:**  
  `dev`
- **Contraseña:**  
  `devpass`

### Redis Commander
- **URL:**  
  [http://localhost:8082](http://localhost:8082)
- **Sin autenticación por defecto**

---

## Strings de conexión de ejemplo

> **IMPORTANTE:**  
> Si tu aplicación corre en otro contenedor de Docker en la misma red, usa el **nombre del servicio** como host.  
> Si tu aplicación corre en tu máquina local, usa `localhost` y el puerto publicado.

### PostgreSQL

- **NestJS (TypeORM):**
  ```typescript
  TypeOrmModule.forRoot({
    type: 'postgres',
    host: process.env.NODE_ENV === 'production' ? 'postgres' : 'localhost',
    port: 5432,
    username: 'dev',
    password: 'devpass',
    database: 'devdb',
    // ...otras opciones
  })
  ```
- **Python (SQLAlchemy):**
  ```python
  DATABASE_URL = "postgresql://dev:devpass@localhost:5432/devdb"
  # O dentro de Docker: "postgresql://dev:devpass@postgres:5432/devdb"
  ```

### MySQL

- **NestJS (TypeORM):**
  ```typescript
  TypeOrmModule.forRoot({
    type: 'mysql',
    host: process.env.NODE_ENV === 'production' ? 'mysql' : 'localhost',
    port: 3306,
    username: 'root',
    password: 'devpass',
    database: 'devdb',
    // ...otras opciones
  })
  ```
- **Python (SQLAlchemy):**
  ```python
  DATABASE_URL = "mysql+pymysql://root:devpass@localhost:3306/devdb"
  # O dentro de Docker: "mysql+pymysql://root:devpass@mysql:3306/devdb"
  ```

### MongoDB

- **NestJS (Mongoose):**
  ```typescript
  MongooseModule.forRoot(
    process.env.NODE_ENV === 'production'
      ? 'mongodb://dev:devpass@mongo:27017/devdb?authSource=admin'
      : 'mongodb://dev:devpass@localhost:27017/devdb?authSource=admin'
  )
  ```
- **Python (PyMongo):**
  ```python
  MONGO_URI = "mongodb://dev:devpass@localhost:27017/devdb?authSource=admin"
  # O dentro de Docker: "mongodb://dev:devpass@mongo:27017/devdb?authSource=admin"
  ```

### Redis

- **NestJS (ioredis):**
  ```typescript
  const redis = new Redis({
    host: process.env.NODE_ENV === 'production' ? 'redis' : 'localhost',
    port: 6379,
    // password: 'devpass', // Si configuras autenticación
  });
  ```
- **Python (redis-py):**
  ```python
  import redis
  r = redis.Redis(host='localhost', port=6379)
  # O dentro de Docker: redis.Redis(host='redis', port=6379)
  ```

---

## ⚠️ Nota importante sobre conexiones desde herramientas web (Adminer, Mongo Express, Redis Commander)

Cuando accedas a las herramientas de administración web incluidas (Adminer, Mongo Express, Redis Commander), **NO uses `localhost` ni `127.0.0.1` como servidor**.  
Debes usar el **nombre del servicio** definido en el archivo `docker-compose.yml` para conectarte a cada base de datos, ya que las herramientas corren en contenedores separados y se comunican por la red interna de Docker.

| Base de datos | Servidor a usar en la herramienta | Usuario | Contraseña | Puerto |
|---------------|-----------------------------------|---------|------------|--------|
| PostgreSQL    | `postgres`                        | dev     | devpass    | 5432   |
| MySQL         | `mysql`                           | root    | devpass    | 3306   |
| MongoDB       | `mongo`                           | dev     | devpass    | 27017  |
| Redis         | `redis`                           | —       | —          | 6379   |

**Ejemplo para Adminer:**
- Motor: PostgreSQL
- Servidor: `postgres`
- Usuario: `dev`
- Contraseña: `devpass`
- Base de datos: `devdb`

**Ejemplo para Mongo Express:**
- Servidor: `mongo`
- Usuario: `dev`
- Contraseña: `devpass`

**Ejemplo para Redis Commander:**
- Servidor: `redis`
- Puerto: `6379`

> Si usas `localhost` o `127.0.0.1` desde Adminer, Mongo Express o Redis Commander, la conexión fallará porque buscan la base de datos dentro del mismo contenedor, no en la red de Docker.

---

## Uso

1. Levanta los servicios:
   ```sh
   docker compose up -d
   ```

---
