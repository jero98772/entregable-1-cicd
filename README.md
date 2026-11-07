# entregable-1-cicd

## Integrantes

**Daniel Arango Sohm**

| Nombre completo | Usuario de GitHub | Rol |
|---|---|---|
| Daniel Arango Sohm | [jero98772](https://github.com/jero98772) | Propietario del repositorio |

## Ejecución exitosa del workflow

Ejecución exitosa de `mi_workflow.yml`: https://github.com/jero98772/entregable-1-cicd/actions/runs/42387863145

## Respuestas

### 1. ¿Qué eventos activan el workflow y por qué?

El workflow se activa con dos eventos, definidos en la clave `on`:

- **`push` a la rama `main`**: el workflow corre solo cada vez que se suben commits a `main`. Así cada cambio que entra a la rama principal se verifica automáticamente, sin que nadie tenga que acordarse de lanzarlo.
- **`workflow_dispatch`**: permite ejecutarlo manualmente desde la pestaña Actions (botón *Run workflow*) o con `gh workflow run mi_workflow.yml`. Sirve para probar el workflow o repetir una ejecución sin tener que hacer un commit nuevo.

### 2. ¿Qué es el runner `ubuntu-latest` y por qué se fija `shell: bash`?

`ubuntu-latest` es una máquina virtual con Ubuntu Linux que GitHub aloja y gestiona. Los pasos `run` se ejecutan en esa máquina y no en mi computadora; por eso el workflow funciona igual sin importar si trabajo desde Windows, macOS o Linux, y por eso el script reporta `Sistema Operativo: Linux`.

El shell por defecto depende del runner: Bash en Linux y macOS, PowerShell en Windows. Con `shell: bash` el paso usa siempre Bash, así que comandos como `echo` y la sustitución `$(date)` se comportan igual aunque el runner cambie.

### 3. Tres acciones del GitHub Marketplace

1. **[`actions/cache`](https://github.com/marketplace/actions/cache)**: guarda y restaura dependencias y salidas de compilación entre ejecuciones (por ejemplo `~/.cache/pip` o `node_modules`) usando una clave. Sirve para que los workflows tarden menos al no descargar o recompilar lo mismo cada vez.
2. **[`actions/setup-node`](https://github.com/marketplace/actions/setup-node-js-environment)**: instala una versión concreta de Node.js en el runner, la agrega al `PATH` y puede cachear las dependencias de npm, yarn o pnpm. Sirve para compilar y probar proyectos de JavaScript/TypeScript con una versión de Node fija.
3. **[`docker/build-push-action`](https://github.com/marketplace/actions/build-and-push-docker-images)**: construye imágenes Docker con Buildx y puede subirlas a un registro (Docker Hub, GHCR, etc.). Sirve para automatizar la construcción y publicación de imágenes de contenedores en un pipeline de CI/CD.

### 4. ¿Cómo se manejan los secretos en GitHub Actions?

Los secretos son valores sensibles (tokens, contraseñas, claves de API) que se guardan fuera del código, en **Settings > Secrets and variables > Actions** del repositorio. En el workflow se usan con la expresión `${{ secrets.NOMBRE }}`, por ejemplo como variable de entorno de un paso. GitHub los enmascara en los logs (aparecen como `***`); aun así no deben imprimirse a propósito ni escribirse en archivos del repositorio, porque el repo y sus logs son públicos.

### 5. Reflexión

1. **Automatizar las pruebas en cada `push` y en cada pull request.** En mis proyectos suelo correr las pruebas a mano y a veces se me olvida antes de subir cambios. Con un workflow que ejecute `pytest` en cada `push` y en cada pull request, los errores se detectarían en minutos y en un entorno limpio (el runner), no en mi computadora, donde puede haber dependencias instaladas que ocultan problemas.

2. **Revisar el estilo y la seguridad del código antes de integrarlo.** Agregaría pasos que ejecuten un linter (por ejemplo `ruff`) y un análisis de seguridad como `github/codeql-action`. Así el código sigue un estilo consistente y se detectan vulnerabilidades o secretos expuestos antes de que lleguen a `main`, lo que ahorra revisiones manuales y reduce riesgos en un repositorio público.
