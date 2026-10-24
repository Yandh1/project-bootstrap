# Docker local: arranque manual y limpieza al iniciar el host

Preferencia de Rafael (2026-10-24). Aplicar a todos los servicios ejecutados en su ordenador; una base de datos sigue persistiendo aunque su contenedor no arranque solo.

## Arranque por sesión

Declarar `restart: "no"` (con comillas) en cada servicio Compose local. No usar `always` ni `unless-stopped`; comprobar también que `deploy.restart_policy` no lo contradiga y que no haya unidades systemd, cron o autostart que ejecuten `compose up`. Si existe `deploy.restart_policy`, usar `condition: none` para ejecución local. Comprobar la configuración fusionada de todos los overrides con `docker compose config`.

RUN.js arranca explícitamente con Compose y conserva volúmenes al parar; no usar `down -v` ni `--rm` para simular una sesión. El contenedor puede seguir ejecutándose al cerrar la terminal; al apagar/reiniciar el ordenador se detiene y requiere arranque manual. No prometer parada al cerrar sesión gráfica salvo que se implemente expresamente.

Para contenedores existentes dentro del alcance autorizado: guardar políticas anteriores y aplicar `docker update --restart=no <ids>` sin detenerlos. La recreación desde un Compose antiguo puede restaurar la política anterior: al migrar un proyecto, corregir también sus fuentes y overrides, respetando AGENTS.md y cambios pendientes. No modificar otros proyectos automáticamente durante un setup nuevo.

## Limpieza de caché al arrancar

La limpieza del build cache es global al builder, no a un proyecto. En Linux con Docker Engine y systemd, instalar una sola unidad del host tras Docker; reutilizar la existente si coincide. En el equipo de Rafael existe `docker-build-cache-clean.service` desde 2026-10-24, para el daemon local y builder predeterminado. Verificar antes de instalar duplicados. No reiniciar Docker para configurar esto.

```ini
[Unit]
Description=Clear unused Docker build cache at boot
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
Environment=DOCKER_HOST=unix:///var/run/docker.sock
ExecStart=/usr/bin/docker builder prune --all --force
TimeoutStartSec=15min
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Validar con `systemd-analyze verify`, instalar como root en `/etc/systemd/system/`, ejecutar `systemctl daemon-reload` y `systemctl enable docker-build-cache-clean.service`. Probar con `systemctl start` y consultar resultado/journal. Se ejecuta al arrancar el sistema, no al cerrar un proyecto. La caché puede crecer durante la sesión; la siguiente compilación tras la poda puede tardar más y descargar dependencias.

El comando solo elimina caché de compilación no utilizada. No sustituirlo por `docker system prune`, `image prune`, `container prune` ni `volume prune`. No limpiar volúmenes de dependencias, bases de datos ni cachés de servicios como Redis. Las imágenes y capas todavía referenciadas permanecen. No sumar categorías de `docker system df` como si no compartieran capas.

Comprobar contexto/endpoint y `docker buildx ls`: no podar builders remotos; si el usuario usa builders locales adicionales, cubrirlos explícitamente tras identificarlos. Para Docker rootless, Docker Desktop u otro SO, adaptar al gestor real; no fingir que esta unidad funciona universalmente. La instalación global requiere alcance autorizado; un setup no autoriza por sí solo modificar el host fuera de lo acordado.

Verificar `docker system df`, `df -h`, políticas de los contenedores, unidad habilitada y último resultado. No reiniciar el ordenador para verificar sin petición expresa. Documentar cómo volver a arrancar el proyecto con RUN.js o Compose y cómo deshabilitar la limpieza: `sudo systemctl disable docker-build-cache-clean.service`.

Fuentes: [políticas de reinicio](https://docs.docker.com/engine/containers/start-containers-automatically/), [Compose services](https://docs.docker.com/reference/compose-file/services/), [poda de build cache](https://docs.docker.com/reference/cli/docker/buildx/prune/).
