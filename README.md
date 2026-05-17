# SanchezVillamizar-post2-u12 — Pipeline CI/CD con GitHub Actions y Docker Hub

![CI/CD Status](https://github.com/XaviVillamizar31/SanchezVillamizar-post2-u12/actions/workflows/ci.yml/badge.svg)

**Programación Web — Unidad 12: Despliegue y CI/CD**  
Ingeniería de Sistemas — UDES 2026

---

## Descripción

Implementación de un pipeline de integración y entrega continua con GitHub Actions que automatiza la compilación, ejecución de pruebas con reporte de cobertura JaCoCo, construcción de la imagen Docker con multi-stage build, y publicación en Docker Hub, activándose automáticamente en cada push a la rama `main`.

---

## Imagen Docker

```bash
docker pull xavivillamizar31/catalogo-app:latest
```

Ejecutar localmente:

```bash
docker run -p 8081:8081 \
  -e SPRING_PROFILES_ACTIVE=dev \
  xavivillamizar31/catalogo-app:latest
```

---

## Requisitos previos

- Java 21+
- Maven 3.9.x
- Docker Desktop
- Cuenta en Docker Hub
- Cuenta en GitHub con Actions habilitado

---

## Estructura del Proyecto

```
SanchezVillamizar-post2-u12/
├── .github/
│   └── workflows/
│       └── ci.yml              ← Pipeline CI/CD
├── src/
│   └── main/java/com/empresa/catalogo/
├── Dockerfile                  ← Multi-stage build
├── .dockerignore
├── docker-compose.yml
└── pom.xml
```

---

## Pipeline CI/CD

El pipeline se activa automáticamente en cada `push` o `pull_request` a `main` y realiza:

1. **Compilación** con Maven y ejecución de pruebas unitarias
2. **Generación de reporte de cobertura JaCoCo** disponible como artefacto descargable
3. **Construcción de imagen Docker** con multi-stage build
4. **Publicación en Docker Hub** con tags `latest` y `sha-<commit>`

### Jobs del workflow

| Job | Descripción | Se ejecuta en |
|-----|-------------|---------------|
| `build-and-test` | Compila, ejecuta pruebas y genera reporte JaCoCo | Push y Pull Request a `main` |
| `docker-publish` | Construye y publica imagen en Docker Hub | Solo Push a `main` |

El job `docker-publish` depende de `build-and-test` mediante `needs:` — si las pruebas fallan, la imagen no se publica.

---

## GitHub Secrets requeridos

Para que el pipeline funcione correctamente, deben estar configurados los siguientes secrets en **Settings → Secrets and variables → Actions**:

| Secret | Descripción | Cómo obtenerlo |
|--------|-------------|----------------|
| `DOCKERHUB_USERNAME` | Nombre de usuario de Docker Hub | Tu usuario en hub.docker.com |
| `DOCKERHUB_TOKEN` | Access Token de Docker Hub | hub.docker.com → Account Settings → Security → New Access Token (permisos Read & Write) |

> **Importante:** Nunca incluir credenciales directamente en el archivo YAML. Siempre usar `${{ secrets.NOMBRE_SECRET }}`.

---

## Cómo configurar los Secrets

1. Ve a tu repositorio en GitHub
2. Clic en **Settings** → **Secrets and variables** → **Actions**
3. Clic en **New repository secret**
4. Agrega `DOCKERHUB_USERNAME` con tu usuario de Docker Hub
5. Agrega `DOCKERHUB_TOKEN` con el Access Token generado en Docker Hub

---

## Verificar el pipeline

Después de hacer push a `main`:

1. Ve a la pestaña **Actions** del repositorio
2. Verifica que ambos jobs terminen con check verde ✅
3. El reporte JaCoCo estará disponible como artefacto descargable en la ejecución
4. Ve a **hub.docker.com/r/xavivillamizar31/catalogo-app** para ver la imagen publicada

---

## Evidencias

| Evidencia | Descripción |
|-----------|-------------|
| `evidencia/actions-verde.png` | Historial de GitHub Actions con ejecuciones exitosas |
| `evidencia/jacoco-artefacto.png` | Reporte JaCoCo descargado como artefacto |
| `evidencia/dockerhub-tags.png` | Repositorio Docker Hub con tags `latest` y `sha-XXXXXXX` |

---

## Commits del repositorio

| # | Commit | Descripción |
|---|--------|-------------|
| 1 | `ci: configurar GitHub Secrets para Docker Hub` | Configuración de credenciales como Secrets |
| 2 | `ci: agregar pipeline GitHub Actions con build, test JaCoCo y Docker Hub publish` | Workflow ci.yml con jobs build-and-test y docker-publish |
| 3 | `ci: verificar pipeline completo y publicacion en Docker Hub` | Pipeline en verde con imagen publicada |

---

## Tecnologías usadas

| Tecnología | Versión | Uso |
|------------|---------|-----|
| Spring Boot | 4.0.6 | Framework base |
| JaCoCo | 0.8.11 | Reporte de cobertura de pruebas |
| GitHub Actions | — | Pipeline de CI/CD |
| Docker | — | Contenedorización |
| Docker Hub | — | Registro de imágenes |

