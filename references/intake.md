# Entrevista para cada setup nuevo

Empezar con 4–6 preguntas claras; esperar y continuar con las dependientes. Las opciones orientan, no preseleccionan decisiones críticas. El usuario puede delegar decisiones concretas. Preguntar solo lo aplicable y conservar las respuestas como especificación del setup.

## Primera ronda: define el proyecto

- ¿Nombre, propósito, tipo de producto y alcance inicial? ¿Aplicación nueva o código existente?
- ¿Qué stack quieres para backend y frontend, lenguajes, frameworks, runtimes y gestores de paquetes? ¿Versiones concretas o quieres propuesta? Puede no existir frontend o backend independiente.
- ¿Repositorios separados —uno back, uno front y uno docs con infraestructura y agentes—, monorepo u otra distribución?
- ¿En qué directorio padre y con qué nombres? ¿Se crean repositorios Git locales o se conectan a remotos existentes? ¿Host, organización y visibilidad si pide remotos?
- ¿Todo en Docker, esquema híbrido o ejecución nativa? ¿Qué sistemas deben soportarse: Linux, Windows/WSL, macOS?

## Segunda ronda: servicios y desarrollo

- Base de datos y versión, ORM/migraciones, datos iniciales ficticios o snapshot autorizado, persistencia y política de reinicialización.
- Servicios adicionales: cache, colas, workers, correo de prueba, almacenamiento, búsqueda, websockets. No añadirlos por costumbre.
- API REST/GraphQL/RPC, contrato compartido, autenticación, cookies o tokens, CORS/CSRF, URL frontend/backend, reverse proxy y puertos disponibles.
- HMR, acceso desde LAN si se necesita, dominios locales/TLS y conflictos de puertos.
- Docker local ya tiene preferencia persistente: arranque manual, sin reinicio automático de contenedores, limpieza de caché de compilación al iniciar el host y conservación de datos. No volver a preguntar salvo que Rafael solicite una excepción.
- Entornos requeridos: desarrollo, pruebas, producción local o despliegue real. ¿Cuál es el destino de despliegue?

## Tercera ronda: agentes y operación

- ¿Qué agentes o herramientas consumirán las instrucciones? ¿Contrato común solamente o perfiles adicionales? Crear adaptadores de entrada solo para herramientas elegidas.
- ¿Agentes editan checkout normal, ramas o worktrees separados? ¿Ramas protegidas, acceso entre repositorios y límites de Git?
- ¿Qué pruebas: unitarias, integración HTTP, E2E navegador? ¿CI y proveedor? ¿Lint/formato y checks requeridos?
- ¿Qué acciones necesita RUN.js: arranque, parada, logs, estado, rebuild, pruebas, servicios individuales, inicialización de datos? ¿Menú además de CLI?
- ¿Convenciones de documentación, seguimiento de tareas, idioma y responsables?

Al cerrar la entrevista, resumir decisiones y asuntos pendientes antes de construir. Si todo está resuelto, continuar sin pedir una confirmación redundante. No interpretar silencio como respuesta a decisiones necesarias. No preguntar por valores de secretos en el chat; definir sus nombres y forma de provisión local.
