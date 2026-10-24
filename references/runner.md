# RUN.js, Compose, imágenes y entrypoints

## Responsabilidades

RUN.js ofrece una interfaz común para humanos y automatización; Compose declara servicios, redes, montajes, healthchecks y dependencias; Dockerfiles construyen imágenes; entrypoints preparan estado de ejecución y terminan con `exec "$@"` para propagar señales. Evitar duplicar lógica de negocio en el runner.

## Contrato de RUN.js

Generar un runner funcional adaptado al stack elegido, sin dependencias de SIIA. Si se usa import ESM en RUN.js, declarar `type: module` en su package.json. Usar APIs nativas de Node y documentar versión compatible. Compatibilidad Bun/Deno solo si se eligió y verificó; Deno necesita permisos efectivos de lectura, escritura y procesos además de los necesarios para el caso.

Resolver rutas desde import.meta.url, no desde el cwd del usuario. Ejecutar procesos con spawn y arrays de argumentos, sin shell para valores del usuario. Resolver de forma explícita .env y proyecto Compose. Usar un nombre de proyecto único y puertos configurables para coexistir con otros proyectos; evitar container_name global fijo salvo necesidad documentada.

Ofrecer una sola acción por invocación. Acciones sugeridas, según alcance: --help, --start-development, --stop-development, --remove-development, --start-testing, --stop-testing, --test-unit, --test-integration, --test-e2e, --test-all, --start-production, --logs y --status. No exponer flags sin implementación. Añadir --filter solo donde tenga sentido; traducirlo al ejecutor correspondiente. Menú TTY opcional llama a las mismas funciones que CLI. Sin TTY y sin acción: error claro, nunca lectura bloqueante. CLI no espera Enter ni abre prompts.

Validar argumentos desconocidos, acciones múltiples y valores faltantes; exit 2 para uso inválido, 0 para éxito y no cero para fallo de operación. Propagar fallos de procesos. --yes confirma operaciones destructivas acordadas; no elimina restricciones de permisos del agente. Diferenciar stop, down y borrado de datos: down normal conserva volúmenes. Reinicialización de datos requiere alcance explícito; preferir respaldo y no borrar directorios por defecto.

Reutilizar servicios saludables; construir cuando falte imagen o se solicite rebuild. Esperar disponibilidad con límites finitos; mostrar logs útiles al fallar. Para agregados elegir fail-fast como valor inicial o comportamiento acordado; si continúa tras fallos, devolver el primer fallo y documentarlo. No prometer un comportamiento distinto al ejecutado.

## Contenedores y datos

Para Docker local aplicar [docker-session.md](docker-session.md): `restart: "no"`, arranque manual y limpieza de build cache al iniciar el host. RUN.js no instala servicios de autoarranque de proyectos, no habilita políticas `always`/`unless-stopped` y no ejecuta podas globales al arrancar o parar un proyecto.

- Desarrollo: fuentes montadas en vivo, HMR cuando aplique, dependencias Linux y caché separadas del host. Si se elige fuente read-only, prever directorios de montaje existentes y todos los paths de escritura. El host/agente sigue editando sus archivos autorizados.
- Pruebas: proyecto/red/datos diferentes de desarrollo y producción. Unitarias, integración HTTP y E2E son niveles opcionales según stack. Navegador en contenedor separado si corresponde; reportes, capturas y trazas en ruta documentada.
- Producción: builds por etapas, assets compilados, fuentes y dependencias incluidas; sin HMR. Variables/secretos externos, usuario y permisos adecuados al stack. No confundir ejecutar este perfil local con desplegarlo.
- Modular: servicios individuales con dependencias claramente documentadas; no prometer stack funcional al usar --no-deps.
- Persistencia: separar por entorno y documentar restauración/reset. No compartir automáticamente la BD de desarrollo con producción.
- Base: usar versión elegida y compatible. SIIA precargaba un dump grande en build y copiaba archivos físicos al datadir vacío al primer runtime. Es una optimización específica, sensible a versión del motor y contenido; para proyectos nuevos ofrecer migraciones/seed ficticio o mecanismo elegido, sin copiar datos reales.

Healthchecks deben reflejar disponibilidad útil; depends_on por salud donde el motor lo soporte. Ajustar start_period y timeouts al arranque inicial real. Evitar ejecutar migraciones destructivas automáticamente. Entry points idempotentes, errores visibles, finales LF y chmod correcto. Instalar dependencias con lockfiles y fingerprint que incluya modo/versión relevantes; no reinstalar siempre ni detectar solo por existencia de un binario. Excluir secretos, .git, datos persistentes y dependencias locales de build contexts.

## Worktrees opcionales

Si se acordó aislamiento por rama: mapa grupo→rama→worktree, rutas validadas dentro del workspace, validación del repo común y rama, montajes read-only y comprobación mediante docker inspect antes de ejecutar. No copiar cambios pendientes del checkout humano ni cambiarle la rama. Bloqueo por proyecto para evitar remounts concurrentes; en multiusuario/multihost definir alcance real del lock. Actualización de main con fast-forward solo con worktree limpio y alcance acordado. Conservar ramas/worktrees retirados para revisión. Aplicar por repositorio cuando back y front tienen historiales distintos.

## Verificación y documentación

Comprobar Node --check, --help, error por flag desconocida/acción múltiple y docker compose config con archivos/variables del setup. Ejecutar smoke del entorno si está acordado y Docker disponible. Documentar cwd o comandos absolutos y rutas desde cada repo. Verificar todos los enlaces Markdown y que el agente en back/front llega a docs y vuelve a la fuente correcta. Distinguir configuración validada de servicios arrancados y tests pasados.
