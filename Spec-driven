================================================================================
  GUÍA DE LINEAMIENTOS Y BUENAS PRÁCTICAS PARA CREACIÓN DE PROYECTOS
  (Independiente del stack tecnológico)
================================================================================

ÍNDICE
  1.  Filosofía general
  2.  Fase 0: Especificación y plan
  3.  Estructura del proyecto
  4.  Stack y dependencias
  5.  Documentación para agentes de IA
  6.  Arquitectura y diseño
  7.  Estándares de código
  8.  Componentes y librerías externas
  9.  Manejo de errores
  10. Entorno de desarrollo
  11. Build y despliegue
  12. Verificación y pruebas


1. FILOSOFÍA GENERAL
--------------------------------------------------------------------------------

  1.1. PRIMERO EL PLAN, DESPUÉS EL CÓDIGO
       Nunca escribir código sin haber definido antes las especificaciones
       y el plan de implementación. El plan debe tener checkboxes y fases
       claras. Esto aplica al proyecto completo y a cada feature individual.

  1.2. DECISIONES POR ESCRITO
       Toda decisión técnica importante debe quedar documentada. Si no está
       escrita, no existe. Esto incluye: por qué se eligió X sobre Y, qué
       alternativas se descartaron, y cuáles son las restricciones.

  1.3. CANONICAL SOURCE OF TRUTH
       Designar archivos específicos como la fuente de verdad:
       - /spec/ para especificaciones técnicas
       - /plan/ para planes de implementación
       - Cualquier desviación del código respecto a estos archivos es un error.

  1.4. MENOS ES MÁS
       Cada dependencia, cada abstracción, cada archivo tiene un costo.
       Preguntar siempre: "¿Realmente necesitamos esto?" antes de agregar
       algo. Preferir cero dependencias sobre una dependencia innecesaria.

  1.5. REPRODUCIBILIDAD
       El proyecto debe poder construirse desde cero siguiendo solo los
       archivos de documentación y el plan. Si alguien nuevo llega, debe
       poder entender todo sin preguntar.


2. FASE 0: ESPECIFICACIÓN Y PLAN
--------------------------------------------------------------------------------

  2.1. CREAR /spec/ PRIMERO
       Antes de escribir una línea de código:
       a) Definir el stack tecnológico (runtime, framework, CSS, DB, etc.)
       b) Definir restricciones (qué NO se permite)
       c) Definir la arquitectura general
       d) Definir la estructura de directorios
       e) Documentar flujos clave (request/response, datos, etc.)
       f) Incluir tabla de variables de entorno con defaults

  2.2. CREAR /plan/ DESPUÉS
       a) Dividir la implementación en fases ordenadas por dependencia
       b) Cada fase debe tener checkboxes accionables
       c) Las fases deben ir de lo fundamental a lo específico
       d) Incluir comandos exactos de instalación y verificación
       e) Incluir notas técnicas sobre decisiones importantes

  2.3. MANTENER AMBOS ACTUALIZADOS
       Cada vez que se implementa un cambio significativo:
       a) Marcar los checkboxes del plan como completados
       b) Actualizar especificaciones si el cambio afecta decisiones previas
       c) Si se descubre algo que contradice el plan, corregir el plan


3. ESTRUCTURA DEL PROYECTO
--------------------------------------------------------------------------------

  3.1. SEPARACIÓN POR PROPÓSITO
       src/
         lib/       - Lógica reutilizable (helpers, utils, config)
         components/- Componentes de UI (atómicos y compuestos)
         pages/     - Páginas/rutas del servidor
         client/    - Entrypoints de hidratación cliente
         styles/    - Estilos globales y temas
         hooks/     - Custom hooks (si aplica)

  3.2. ARCHIVOS RAÍZ MÍNIMOS
       En la raíz del proyecto solo debe haber:
       - Archivos de configuración (.json, .toml, .env)
       - Documentación (.md)
       - Directorios: spec/, plan/, src/, public/ (y build/ si aplica)

  3.3. NOMBRES DE ARCHIVOS
       - kebab-case para archivos: server.tsx, globals.css
       - PascalCase para componentes: Button.tsx, CardHeader.tsx
       - Consistencia: NO mezclar estilos

  3.4. GITIGNORE
       Todo lo que se genera (build, node_modules, .env con secretos)
       debe estar en .gitignore desde el día 1.


4. STACK Y DEPENDENCIAS
--------------------------------------------------------------------------------

  4.1. VERSIONES EXPLÍCITAS
       Registrar en las especificaciones las versiones exactas o rangos
       de cada tecnología. NO dejar versiones "latest" sin control.

  4.2. CLASIFICAR DEPENDENCIAS
       - dependencies: runtime, necesita el usuario final
       - devDependencies: herramientas, bundlers, CLIs, type definitions
       - peerDependencies: si aplica

  4.3. MÍNIMO NECESARIO
       Cada dependencia debe justificarse:
       - ¿Qué problema resuelve?
       - ¿Podemos resolverlo con APIs nativas?
       - ¿El beneficio justifica el costo (tamaño, complejidad, mantenimiento)?

  4.4. SOURCES OF TRUTH PARA VERSIONES
       No asumir. Investigar en:
       - Sitio web oficial del proyecto
       - GitHub releases
       - npm registry
       - Documentación oficial

  4.5. ACTUALIZAR REGISTRO
       Cuando se agrega una dependencia, actualizar el archivo de
       especificaciones. Si el CLI instala deps automáticamente (como
       shadcn/ui), revisar package.json después para registrar los cambios.


5. DOCUMENTACIÓN PARA AGENTES DE IA
--------------------------------------------------------------------------------

  5.1. AGENTS.md / CLAUDE.md
       Archivo compacto que responde: "¿Qué se equivocaría un agente
       si no tuviera esto?" Incluir:
       - Stack exacto
       - Comandos importantes (especialmente los no obvios)
       - Patrones de arquitectura (cómo se añade una página, un componente)
       - Quirks y advertencias
       - Pasos de verificación

  5.2. SKILLS (.opencode/skills/)
       Skills especializados para tareas específicas:
       - Skill de arquitectura general del proyecto
       - Skill para la biblioteca de componentes
       - Skill para features específicos si es necesario
       Cada skill debe tener: nombre, versión, descripción, cuándo usarlo.

  5.3. REFERENCIAS
       AGENTS.md debe referenciar /spec y /plan como fuente de verdad.
       Skills pueden referenciar AGENTS.md.

  5.4. LO QUE NO DEBE IR EN LA DOCUMENTACIÓN
       - Tutoriales largos
       - Árboles de archivos exhaustivos
       - Consejos genéricos de programación
       - Afirmaciones especulativas o no verificables


6. ARQUITECTURA Y DISEÑO
--------------------------------------------------------------------------------

  6.1. ENTRYPOINT ÚNICO
       El servidor/librería debe tener un solo punto de entrada
       claro (src/server.tsx, src/index.ts, main.tsx, etc.).

  6.2. SEPARACIÓN DE RESPONSABILIDADES
       - Router: solo enrutamiento
       - Render: solo renderizado
       - Páginas: solo contenido
       - Cliente: solo hidratación
       NO mezclar responsabilidades en un mismo archivo.

  6.3. ERROR HANDLING ESTRUCTURADO
       a) Cada handler debe tener try/catch
       b) Un catch global debe capturar errores no manejados
       c) Nunca dejar que una excepción crashee el proceso
       d) Loggear errores con suficiente contexto

  6.4. FLUJOS DOCUMENTADOS
       Cada flujo importante (request, build, deploy) debe documentarse
       paso a paso en las especificaciones.

  6.5. CONFIGURACIÓN POR ENTORNO
       a) Variables de entorno para configuración (puerto, modo, DB URL)
       b) Valores default sensatos
       c) .env con valores de desarrollo
       d) Documentar variables en tabla con nombre, default, descripción


7. ESTÁNDARES DE CÓDIGO
--------------------------------------------------------------------------------

  7.1. TIPADO ESTRICTO
       - TypeScript estricto (strict: true)
       - Tipar todo: props, estados, retornos
       - Sin "any" a menos que sea unavoidable y documentado

  7.2. SIN COMENTARIOS EN CÓDIGO
       El código debe ser autoexplicativo. NO agregar comentarios
       a menos que expliquen un "por qué" que no es obvio del código.
       Los comentarios se desactualizan; el código compilado no miente.

  7.3. CONVENCIONES
       a) Seguir las convenciones del framework/lenguaje
       b) Ser consistente: una vez que se elige un patrón, usarlo en todo
       c) Preferir inmutabilidad
       d) Nombres descriptivos > abreviaciones

  7.4. IMPORTS
       a) Orden: externos primero, internos después
       b) Usar path aliases (si el proyecto los define)
       c) No importar lo que no se usa


8. COMPONENTES Y LIBRERÍAS EXTERNAS
--------------------------------------------------------------------------------

  8.1. INSTALAR TAL CUAL, NO MODIFICAR
       Los componentes de librerías externas que se copian al proyecto
       (como shadcn/ui) deben quedar EXACTAMENTE como los genera el CLI.
       Si se necesita modificar, crear un wrapper propio encima.

  8.2. COMPONENTES DEL CLI vs. GUÍAS
       Algunos componentes pueden ser instalables vía CLI y otros solo
       documentados como guías de composición. Identificar cuál es cuál
       en la documentación oficial antes de asumir.

  8.3. PROVIDERS
       Identificar qué componentes requieren providers (TooltipProvider,
       Toaster, ThemeProvider, etc.) y agregarlos en el entrypoint
       correspondiente inmediatamente después de instalar el componente.

  8.4. WRAPPERS DE TERCEROS
       Si un componente wrapper (como el Toaster de shadcn) depende de
       un contexto que no existe en el proyecto (como next-themes),
       usar la librería base directamente en lugar de instalar
       dependencias pesadas innecesarias.


9. MANEJO DE ERRORES
--------------------------------------------------------------------------------

  9.1. NUNCA CRASHEAR EL PROCESO
       a) try/catch en cada handler
       b) catch global en el entrypoint
       c) Respuestas amigables al usuario (500, 404) en lugar de stack traces

  9.2. LOGGING ESTRUCTURADO
       a) Console.error con el error completo
       b) Mensajes descriptivos que permitan identificar el contexto
       c) En producción, considerar un sistema de logging más robusto

  9.3. ERRORES ESPECÍFICOS vs. GENÉRICOS
       a) Capturar errores específicos cuando se pueda hacer algo al respecto
       b) Captura genérica como safety net, no como strategy principal


10. ENTORNO DE DESARROLLO
--------------------------------------------------------------------------------

  10.1. SCRIPTS CLAROS
        a) dev: inicia todo lo necesario para desarrollo
        b) build: compila todo para producción
        c) start: build + servidor producción
        d) Scripts auxiliares con prefijo (dev:css, build:client)

  10.2. HOT RELOAD
        a) Usar watch mode del runtime/bundler
        b) Hot-reload de CSS en paralelo al servidor
        c) Asegurar que child processes (watchers) no queden huérfanos al
           hacer Ctrl+C (unref en Node/Bun, traps en scripts)

  10.3. .env
        a) Valores por defecto para desarrollo
        b) .env en .gitignore
        c) Valores sensibles NUNCA en el repositorio

  10.4. PUERTO DEDICADO
        a) Puerto fijo para el proyecto (no usar el default del framework)
        b) Documentar el puerto en .env y en las especificaciones
        c) Configurable vía variable de entorno


11. BUILD Y DESPLIEGUE
--------------------------------------------------------------------------------

  11.1. BUILD EN DOS PASOS
        a) Compilar assets estáticos (CSS, JS cliente, imágenes)
        b) El servidor usa los assets compilados

  11.2. ARTEFACTOS GITIGNOBADOS
        a) build/, dist/, out/ en .gitignore
        b) Los assets compilados se regeneran siempre, no se versionan

  11.3. ENTORNO DE PRODUCCIÓN
        a) NODE_ENV=production cambia: caching, minificación, logs
        b) El build de producción debe ser diferente del de desarrollo
           (minificado, sin sourcemaps, con cache headers)

  11.4. VERIFICACIÓN PRE-DEPLOY
        a) Compilar sin errores
        b) Iniciar servidor
        c) Curl a rutas principales (200, 404, 500)
        d) Verificar assets estáticos servidos correctamente


12. VERIFICACIÓN Y PRUEBAS
--------------------------------------------------------------------------------

  12.1. VERIFICAR DESPUÉS DE CADA CAMBIO
        a) Compilar cliente y servidor
        b) Probar rutas con curl o herramienta similar
        c) Status codes correctos (200, 404, 500)

  12.2. COMANDOS DE VERIFICACIÓN
        Incluir en la documentación comandos exactos para verificar:
        - Compilación: bun run build
        - Servidor: curl localhost:XXXX/
        - Estáticos: curl localhost:XXXX/static/...
        - Errores: curl localhost:XXXX/ruta-inexistente

  12.3. LO QUE NO SE COMPILA, NO EXISTE
        Si no compila, no funciona. Si no se puede verificar, no está
        completo. El proyecto debe poder verificarse con comandos simples.

================================================================================
  FIN DE LA GUÍA
  v1.0 — Principios universales para crear proyectos mantenibles y robustos
================================================================================
