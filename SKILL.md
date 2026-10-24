---
name: project-bootstrap
description: Iniciar proyectos y repositorios con entrevista técnica, separación opcional backend/frontend/docs, Docker, RUN.js y ruteo agéntico Markdown. Activar ante «un setup nuevo», «comienza un repositorio para proyecto», «inicia un proyecto» o solicitudes equivalentes de setup inicial. No aplicar a cambios rutinarios de proyectos existentes.
---

# Setup de proyectos de Rafael

Preferencia explícita del 2026-10-20. Cada setup nuevo comienza con varias preguntas para configurar el proyecto correctamente. Adaptar el patrón estudiado de SIIA al stack y las decisiones del usuario.

## Entrevista obligatoria

Leer [references/intake.md](references/intake.md). Preguntar en español, en bloques manejables, esperando las respuestas antes de generar el scaffold. Aprovechar lo ya indicado; no volver a preguntar decisiones contestadas. Primero resolver propósito, stack, topología y destino local. Después preguntar lo pertinente sobre Docker, datos, API, pruebas, agentes y despliegue. Permitir «recomiéndame» y proponer una opción concreta con motivos. No elegir silenciosamente el stack ni monorepo frente a repositorios separados. Registrar lo acordado y lo pendiente en docs; resolver las decisiones que impidan generar una configuración coherente. Si el usuario solicita explícitamente omitir la entrevista o da todas las respuestas, respetarlo.

## Docker local: sesión y caché

Preferencia explícita de Rafael del 2026-10-24: los contenedores locales se arrancan manualmente y no vuelven a arrancar al reiniciar el ordenador. Generar `restart: "no"` para todos los servicios locales, incluidos bases de datos y perfiles de producción ejecutados localmente. Mantener volúmenes persistentes. La caché de compilación debe limpiarse automáticamente tras cada arranque del ordenador mediante una única tarea del host, sin borrar imágenes, contenedores ni volúmenes. Leer [references/docker-session.md](references/docker-session.md) al configurar Docker; no volver a preguntar estas preferencias. Un despliegue remoto real tiene política independiente según los requisitos del servicio.

## Construcción

Leer [references/architecture.md](references/architecture.md) para ruteo entre repositorios y [references/runner.md](references/runner.md) para RUN.js, Compose y entrypoints. Las referencias son autosuficientes: no requieren acceder al proyecto SIIA original.

1. Inspeccionar destino, instrucciones aplicables, repositorios y cambios existentes. Determinar si se crean repositorios nuevos o se integra un setup existente; conservar trabajo ajeno.
2. En modo separado, ofrecer `<proyecto>-back`, `<proyecto>-front`, `<proyecto>-docs` como hermanos; el repositorio docs aloja Dockerfiles, Compose, scripts, RUN.js, contrato común y documentación. Adaptar nombres a las respuestas.
3. En monorepo, usar áreas equivalentes y un único contrato común. Añadir solo componentes pertinentes al stack.
4. Generar ruteo Markdown real desde cada repositorio hacia el contrato y las rutas de los demás; usar instrucciones locales mínimas y enlaces relativos comprobados.
5. Crear RUN.js, configuración de entorno de ejemplo sin secretos reales, ignores, Dockerfiles, Compose y entrypoints necesarios. Documentar requisitos del host y comandos para personas y agentes.
6. Verificar sintaxis del runner, resolución de rutas/enlaces y Compose usando el motor disponible. Comprobar una ruta CLI sin efectos y rechazos de argumentos. Arrancar servicios o descargar dependencias pesadas según el alcance acordado; informar con precisión qué se ejecutó y qué queda pendiente.
7. Entregar rutas, decisiones, comandos de arranque y limitaciones reales. Inicializar Git local si forma parte de la petición; crear remotos, publicar, hacer commit o push solo con autorización correspondiente.

No trasladar credenciales, SQL, cuentas, nombres, versiones o reglas institucionales de SIIA a proyectos nuevos. Mantener la personalidad conversacional fuera de código y documentación salvo petición expresa.
