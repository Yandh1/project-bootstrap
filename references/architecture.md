# Arquitectura exportable y ruteo Markdown

## Patrón de repositorios separados

Ejemplo con nombres elegidos por el usuario:

```text
workspace/
  proyecto-back/
    AGENTS.md
    <código backend>
  proyecto-front/
    AGENTS.md
    <código frontend>
  proyecto-docs/
    AGENTS.md
    README.md
    RUN.js
    package.json
    .env.example
    agents/
      init.md
      ROUTER.md
      STACK.md
    documentacion/
      arquitectura.md
      entorno.md
      decisiones.md
    infra/
      compose.development.yml
      compose.testing.yml
      compose.production.yml
      Dockerfile.<servicio>
      entrypoint.<servicio>.sh
```

Es un ejemplo ajustable. No crear frontend, perfiles, worktrees o archivos de entornos que el usuario no necesite. Los Dockerfiles y orquestación se mantienen en docs; los build contexts y montajes resuelven explícitamente repositorios hermanos. Compose resuelve rutas respecto al archivo base/proyecto, y COPY respecto al contexto de build: diseñarlos juntos y comprobarlos. Si hay que compilar fuentes hermanas, seleccionar contexto padre con Dockerfile en docs o contextos adicionales compatibles con el motor acordado. No usar COPY de rutas fuera del contexto ni incluir indiscriminadamente otros proyectos o secretos.

Clonar solo back o front no garantiza que docs exista. Sus entradas indican cómo ubicar/clonar el hermano requerido; si falta, informar la ruta esperada y evitar inventar instrucciones. Documentar manifiesto de repositorios y revisiones cuando se necesite reproducibilidad. Submódulos son una elección explícita, no una condición del patrón.

## Contrato y navegación

Cada AGENTS.md local contiene propósito del repo, áreas editables, verificación mínima y enlaces reales a:
- `../proyecto-docs/agents/init.md`: contrato común.
- `../proyecto-docs/agents/ROUTER.md`: tabla tarea → repo → rutas → documentación → checks.
- `../proyecto-docs/agents/STACK.md`: stack elegido y evidencia en manifests/locks.

En docs, ajustar los enlaces a `agents/...`. La entrada local hace explícito leer el contrato común y la fila pertinente antes de editar. Las instrucciones de un repositorio hermano no se cargan automáticamente por estar enlazadas: el agente debe abrirlas. Al actuar en otro repo, leer también su AGENTS.md, revisar su rama/estado y verificar autorización y acceso del workspace; un enlace no amplía permisos.

El contrato común registra rutas relativas, fuente de verdad, límites de cambio, manejo de secretos, política de Git elegida y cómo usar RUN.js sin TTY. El router puede incluir backend/API, frontend/UI, contrato API, datos/migraciones, infraestructura y pruebas. Cargar únicamente referencias pertinentes. Evitar instrucciones globales dependientes de identidades o rutas absolutas del equipo original.

Mantener contexto duradero en decisiones y notas; tareas activas y terminadas pueden usar TODO/ y COMPLETED/ si el usuario elige esa convención. No declarar trabajo verificado por la sola existencia de código.

## Procedencia SIIA y diferencias relevantes

El estudio original encontró un repositorio documental con `src/src-original/` como submódulo Laravel de propiedad humana; Compose y Dockerfiles en src; tests externos en `src-siia2mocker-tests`; y contrato en `agents/init.md`. Perfiles Kristan y Rafael dirigían a routers propios según identidad Git; el de Kristan cargaba skills/referencias por tema y comandos, y el de Rafael documentación y evidencia técnica. ALWAYS/NEVER añadían restricciones sobre main, checkout humano y operaciones Git.

El runner original mantiene el checkout humano y ejecuta pruebas sobre worktrees hermanos. `test-branches.json` relaciona grupos, ramas y rutas; `test-worktrees.mjs` valida ruta canónica, pertenencia al repositorio y rama esperada. Crea ramas locales `siia-test/<rama>`, comprueba montajes Docker y usa un lock exclusivo por proyecto. Retirar una rama remapea grupos a main y conserva el worktree anterior; no demuestra que la integración ocurrió, ni elimina ramas. Incorporar este mecanismo solo si se solicita trabajo con ramas aisladas; adaptar nombres y validar independencia de back/front.

Hallazgos del estudio original (septiembre de 2026, no describen necesariamente el checkout actual): había enlaces a index.md y README.AI.md ausentes; comentarios prometían detener agregados al primer fallo, pero allResident/allTests pasaban continueOnError=true. El frontend invalidaba dependencias solo al faltar Vite, mientras PHP usaba fingerprint de manifests. No copiar esas inconsistencias: sincronizar CLI/documentación, elegir política de fallo explícita e invalidar dependencias al cambiar lockfile. Los entrypoints copiaban public/config sin purgar archivos retirados: definir sincronización coherente al cambiar ramas. El conocimiento del patrón queda en esta skill aunque SIIA no esté disponible.
