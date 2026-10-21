# Actividad S05-S06-A0: Ejercicio Introductorio de Backend con Flask y MySQL

## Objetivos específicos de aprendizaje

- **6.1** Examinar diferentes tipos de almacenamiento persistente, incluyendo bases de datos relacionales como MySQL.
- **6.2** Experimentar con almacenamiento persistente ejecutando operaciones CRUD (Create, Read, Update, Delete) sobre una base de datos real.
- **7.1** Examinar las tecnologías y herramientas utilizadas en la implementación de aplicaciones backend: Docker, MySQL, Python, Flask.
- **7.2** Consumir y exponer servicios web REST usando herramientas de línea de comandos (`curl`) y clientes gráficos (Postman).

---

## Instrucciones

Esta actividad es un ejercicio introductorio guiado que te familiariza con los componentes fundamentales de un backend antes de que construyas el tuyo. Seguirás paso a paso el README del repositorio de ejemplo, levantarás una base de datos MySQL en Docker, ejecutarás código Python que realiza operaciones CRUD y expondrás esos datos como servicios web REST con Flask. Al final, probarás los servicios tanto desde la terminal como desde Postman.

No usarás un agente de IA para generar código en esta actividad: el objetivo es que leas, entiendas y ejecutes el código existente. La comprensión de estos componentes es el prerrequisito para que puedas supervisar y validar el código que el agente genere en las actividades posteriores.

**Importante:** Recuerda revisar los criterios de evaluación (rúbrica) de la actividad para asegurarte cumplas todos los puntos requeridos.

### Parte 0 – Crear el repositorio y abrir el Codespace

1. A partir este repositorio, en la organización de GitHub del curso, crea un repositorio nuevo con el nombre exacto `A0XXXXXXX-ejemplo-backend`, donde `XXXXXXX` es tu matrícula (ejemplo: `A01234567-ejemplo-backend`).
2. El repositorio debe ser **privado**.
3. Abre un **GitHub Codespace** en tu repositorio (`Code → Codespaces → Create codespace on main`).
4. Espera a que el entorno termine de inicializarse, esta operación puede durar varios minutos.


### Parte 1 – Configurar MySQL en Docker 

Revisa con detenimiento el archivo README, ahí está explicado todo lo que se va a realizar, en particular varios conceptos necesarios para las siguientes actividades. Sigue las instrucciones al pie de la letra.

1. **Levanta el contenedor de MySQL** ejecutando en la terminal del Codespace:
    ```bash
   docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=contrasena -e MYSQL_DATABASE=testdb -p 3306:3306 -d mysql:8.4
   ```

   Espera unos segundos mientras MySQL inicializa.

2. **Crea la tabla `jedi`** ejecutando el script SQL:
   ```bash
   docker exec -i mysql-container mysql -u root -pcontrasena testdb < jedi.sql
   ```

3. **Verifica que la tabla existe** conectándote al contenedor:
   ```bash
   docker exec -it mysql-container mysql -u root -pcontrasena
   ```
   Dentro del cliente MySQL ejecuta:
   ```sql
   USE testdb;
   SHOW TABLES;
   DESCRIBE jedi;
   ```
   Escribe `quit` para salir.


### Parte 2 – CRUD con Python directo a MySQL 

Sigue las instrucciones de las secciones 4.2–4.5 del README.

1. **Instala las dependencias** necesarias:
   ```bash
   pip install -r requirements.txt
   ```

2. **Examina el archivo `crudMySQL.py`** antes de ejecutarlo. Identifica:
   - Dónde se establece la conexión a MySQL.
   - Cuál es la función que inserta registros (`CREATE`).
   - Por qué se usa `%s` en lugar de concatenar strings en las consultas SQL.

3. **Ejecuta el script**:
   ```bash
   python crudMySQL.py
   ```

4. **Verifica que los datos se insertaron** conectándote al contenedor:
   ```bash
   docker exec -it mysql-container mysql -u root -pcontrasena
   ```
   Dentro del cliente MySQL ejecuta:
   ```sql
   USE testdb;
   SELECT * FROM jedi;
   ```
   Escribe `quit` para salir.


> **Captura requerida:** `captura_01_crud_python.png` — terminal mostrando el output de `python crudMySQL.py` y el resultado de `SELECT * FROM jedi;` con los registros insertados.

### Parte 3 – Servicios web REST con Flask y pruebas con `curl`

Sigue las instrucciones de las secciones 5.1–5.3 del README.

1. **Levanta el servidor Flask** en la terminal:
   ```bash
   python ws_jedi.py
   ```
   Deberías ver un mensaje indicando que Flask está corriendo en `http://127.0.0.1:5000`.

2. **Abre una segunda terminal** (sin cerrar la primera) y prueba los siguientes endpoints con `curl`:

   - **Obtener todos los jedis (GET):**
     ```bash
     curl -X GET http://localhost:5000/jedi
     ```

   - **Obtener un jedi por ID (GET):**
     ```bash
     curl -X GET http://localhost:5000/jedi/1
     ```

   - **Crear un nuevo jedi (POST):**
     ```bash
     curl -X POST http://localhost:5000/jedi \
          -H "Content-Type: application/json" \
          -d '{"nombre_jedi": "Ahsoka Tano", "email_jedi": "ahsoka@jedi.com"}'
     ```

   - **Eliminar un jedi (DELETE):**
     ```bash
     curl -X DELETE http://localhost:5000/jedi/2
     ```

3. Después de cada operación, verifica el efecto haciendo un `GET /jedi` para confirmar el estado actual de los datos.

> **Captura requerida:** `captura_02_curl.png` — terminal mostrando Flask corriendo en un panel y al menos dos resultados de `curl` (GET y POST) con sus respuestas JSON en otro panel o en la misma terminal.

### Parte 4 – Pruebas con Postman

Sigue las instrucciones de la sección 5.4 del README.

1. **Haz público el puerto 5000** en el Codespace:
   - En VS Code, abre la pestaña **Ports** (junto a la terminal).
   - Localiza el puerto `5000` y haz clic derecho → **Port Visibility → Public**.
   - Copia la URL pública generada (tiene la forma `https://<nombre-codespace>-5000.app.github.dev`).

2. **Abre Postman** en tu computadora y ejecuta las siguientes peticiones usando la URL pública:

> Para las peticiones POST y PUT, ve a la pestaña **Body → raw → JSON** en Postman antes de enviar.

   - **GET** `https://<tu-codespace>-5000.app.github.dev/jedi` — lista todos los jedis.
   - **GET** `https://<tu-codespace>-5000.app.github.dev/jedi/1` — consulta un jedi por ID.
   - **POST** `https://<tu-codespace>-5000.app.github.dev/jedi` con body JSON:
     ```json
     {"nombre_jedi": "Mace Windu", "email_jedi": "mace@jedi.com"}
     ```
   - **PUT** `https://<tu-codespace>-5000.app.github.dev/jedi/1` con body JSON actualizando el nombre o email.
   - **DELETE** `https://<tu-codespace>-5000.app.github.dev/jedi/3`

> **Captura requerida:** `captura_03_postman_get.png` — Postman ejecutando GET a `/jedi` con la URL pública del Codespace, mostrando el código de respuesta y la lista de jedis en JSON.

**Importante:** Recuerda revisar los criterios de evaluación (rúbrica) de la actividad para asegurarte cumplas todos los puntos requeridos.

---

## Especificaciones de entrega

Entrega los archivos:

1. **Canvas:** Sube las capturas de pantalla.

Las capturas de pantalla son:

| Archivo | Descripción |
|---|---|
| `captura_01_crud_python.png` | Output de `SELECT * FROM jedi;` con los registros insertados |
| `captura_02_curl.png` | Flask corriendo y resultados de `curl` GET y POST con sus respuestas JSON |
| `captura_03_postman_get.png` | Postman ejecutando GET a `/jedi` con URL pública del Codespace y respuesta JSON |

**Fecha límite:** Antes del inicio de la actividad S05-S06-A1

---

## Recursos de apoyo
**Entorno de desarrollo:**
- [GitHub Codespaces](https://docs.github.com/en/codespaces) — entorno de desarrollo en la nube basado en VS Code
- [GitHub Codespaces — Port Forwarding](https://docs.github.com/en/codespaces/developing-in-a-codespace/forwarding-ports-in-your-codespace) — cómo hacer público un puerto para acceso externo

**Tecnologías del ejercicio:**
- [Docker — `docker run`](https://docs.docker.com/engine/reference/commandline/run/) — referencia del comando para levantar contenedores
- [MySQL Official Image (Docker Hub)](https://hub.docker.com/_/mysql) — imagen oficial de MySQL
- [mysql-connector-python](https://dev.mysql.com/doc/connector-python/en/) — documentación del driver Python para MySQL
- [Flask](https://flask.palletsprojects.com) — microframework web para Python
- [flask-cors](https://flask-cors.readthedocs.io) — extensión para habilitar CORS en Flask

**Prueba de APIs:**
- [Postman](https://www.postman.com) — cliente gráfico para probar APIs REST; disponible como aplicación de escritorio y como extensión de VS Code
- [curl Documentation](https://curl.se/docs/) — referencia completa del cliente HTTP de línea de comandos

---

## Bibliografía

- Docker Inc. (2024). *Docker documentation: docker run*. https://docs.docker.com/engine/reference/commandline/run/
- GitHub. (2024). *GitHub Codespaces documentation*. https://docs.github.com/en/codespaces
- IEEE Computer Society. (2024). *Guide to the Software Engineering Body of Knowledge (SWEBOK) v4.0a*, Chapter 4: Software Construction. IEEE. https://www.computer.org/education/bodies-of-knowledge/software-engineering
- Oracle. (2024). *MySQL Connector/Python developer guide*. https://dev.mysql.com/doc/connector-python/en/
- Pallets Projects. (2024). *Flask documentation (3.x)*. https://flask.palletsprojects.com
