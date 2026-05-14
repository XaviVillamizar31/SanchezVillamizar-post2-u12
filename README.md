# Post-Contenido 2 — Logging con SLF4J/Logback y Documentación con Swagger/OpenAPI

**Programación Web — Unidad 11: Buenas Prácticas y Patrones de Diseño**  
Ingeniería de Sistemas — UDES 2026

---

## Descripción

Integración de SLF4J y Logback en la aplicación Spring Boot del catálogo de productos para registrar eventos con niveles apropiados y rotación de archivos, y documentación completa de la API REST con springdoc-openapi generando una Swagger UI interactiva que refleja todos los endpoints, parámetros y respuestas posibles.

---

## Requisitos previos

- Java 17+
- Maven 3.9.x
- Spring Boot 4.0.6
- Proyecto base del Post-Contenido 1 (catálogo de productos con SOLID, DAO/DTO y @ControllerAdvice)

---

## Estructura del Proyecto

```
apellido-post2-u11/
├── src/
│   └── main/
│       ├── java/com/empresa/catalogo/
│       │   ├── controller/
│       │   │   └── ProductoController.java       ← @Tag, @Operation, @ApiResponse
│       │   ├── service/
│       │   │   ├── ProductoService.java
│       │   │   └── ProductoServiceImpl.java      ← Logger SLF4J
│       │   ├── repository/
│       │   │   └── ProductoRepository.java
│       │   ├── dto/
│       │   │   ├── ProductoRequestDTO.java        ← @Schema
│       │   │   └── ProductoResponseDTO.java
│       │   ├── entity/
│       │   │   └── Producto.java
│       │   ├── factory/
│       │   │   └── ProductoFactory.java
│       │   └── exception/
│       │       ├── ApiError.java
│       │       ├── GlobalExceptionHandler.java
│       │       └── RecursoNoEncontradoException.java
│       └── resources/
│           ├── application.properties
│           └── logback-spring.xml                ← Configuración de Logback
├── logs/                                         ← Generada en tiempo de ejecución (en .gitignore)
│   └── catalogo.log
└── pom.xml
```

---

## Cómo ejecutar el proyecto

```bash
# Clonar el repositorio
git clone https://github.com/tu-usuario/apellido-post2-u11.git
cd apellido-post2-u11

# Compilar
mvn compile

# Ejecutar
mvn spring-boot:run
```

La aplicación queda disponible en `http://localhost:8081`.

---

## Checkpoint 1 — Logging con SLF4J

Se integró el logger SLF4J en `ProductoServiceImpl` usando un logger estático con niveles apropiados:

| Nivel | Cuándo se usa |
|-------|---------------|
| `INFO` | Operaciones exitosas (crear, listar, eliminar) |
| `DEBUG` | Búsquedas por ID |
| `WARN` | Recurso no encontrado |
| `ERROR` | Excepciones inesperadas |

Ejemplo de mensajes en consola al hacer un POST:
```
17:18:57 INFO  c.e.c.service.ProductoServiceImpl - Creando producto: nombre=Laptop, categoria=ELECTRONICA
17:18:57 INFO  c.e.c.service.ProductoServiceImpl - Producto creado exitosamente con id=1
```

---

## Checkpoint 2 — Configuración de Logback

El archivo `src/main/resources/logback-spring.xml` configura dos appenders:

- **CONSOLA** — `ConsoleAppender` con formato `HH:mm:ss LEVEL logger - mensaje`
- **ARCHIVO** — `RollingFileAppender` con rotación diaria y 30 días de historial

Los logs se guardan en la carpeta `logs/catalogo.log` (excluida del repositorio vía `.gitignore`).

Para ver el contenido del archivo de log en Windows:
```bash
type logs\catalogo.log
```

El nivel global es `INFO`. El paquete `com.empresa.catalogo` registra desde nivel `DEBUG`.

---

## Checkpoint 3 — Documentación con Swagger UI

### Acceder a Swagger UI

Con la aplicación en ejecución, abre en el navegador:

```
http://localhost:8081/swagger-ui.html
```

### Endpoints documentados

| Método | Endpoint | Descripción | Respuestas |
|--------|----------|-------------|------------|
| POST | `/api/productos` | Crear un nuevo producto | 201, 400 |
| GET | `/api/productos` | Listar todos los productos activos | 200 |
| GET | `/api/productos/{id}` | Obtener producto por ID | 200, 404 |
| DELETE | `/api/productos/{id}` | Eliminar producto por ID | 204, 404 |

### Anotaciones utilizadas

| Anotación | Dónde se aplica |
|-----------|-----------------|
| `@OpenAPIDefinition` | `CatalogoApplication` — título, versión y descripción de la API |
| `@Tag` | `ProductoController` — agrupa los endpoints bajo "Productos" |
| `@Operation` | Cada método del controlador — descripción del endpoint |
| `@ApiResponse` | Cada método del controlador — códigos de respuesta posibles |
| `@Parameter` | Parámetros de path (`@PathVariable`) |
| `@Schema` | Campos de `ProductoRequestDTO` — descripción y ejemplos |

---

## Archivos de log

Los archivos de log se generan automáticamente en la carpeta `logs/` al iniciar la aplicación. Esta carpeta está en `.gitignore` y no se sube al repositorio.

```
logs/
├── catalogo.log              ← Log del día actual
└── catalogo.2026-05-12.log   ← Logs anteriores rotados automáticamente
```

---

## Evidencias

| Evidencia | Descripción |
|-----------|-------------|
| `evidencia/slf4j-consola.png` | Consola mostrando mensajes SLF4J con formato correcto |
<img width="1920" height="1032" alt="idea64_LlFcuSsd0x" src="https://github.com/user-attachments/assets/1c51f2ec-14e1-4e9a-b1d3-0ee02cbdd86e" />

| `evidencia/catalogo-log.png` | Archivo `logs/catalogo.log` con registros de operaciones |
<img width="1920" height="1032" alt="idea64_3LAWw3uJna" src="https://github.com/user-attachments/assets/a40c1622-5426-472c-8218-c401c403b6b6" />

| `evidencia/swagger-ui.png` | Swagger UI mostrando endpoints documentados con respuestas |
<img width="1912" height="914" alt="msedge_0YXQYfGzSx" src="https://github.com/user-attachments/assets/a882c12c-abce-484b-b93e-6eb25a5ca299" />


---

## Tecnologías usadas

| Tecnología | Versión | Uso |
|------------|---------|-----|
| Spring Boot | 4.0.6 | Framework base |
| SLF4J | incluido en Spring Boot | Fachada de logging |
| Logback | incluido en Spring Boot | Implementación de logging con rotación de archivos |
| springdoc-openapi | 2.8.8 | Generación de Swagger UI y documentación OpenAPI |
