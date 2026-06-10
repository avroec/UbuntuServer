# Guía de Lineamientos y Buenas Prácticas para Creación de Proyectos

**Versión:** 2.0
**Enfoque:** Independiente del stack tecnológico

Este documento establece los principios, estándares y procesos que deben seguirse al crear y mantener un proyecto de software. Las recomendaciones aquí descritas aplican a cualquier lenguaje, framework o plataforma. Cuando una sección usa un ejemplo de un ecosistema específico (por ejemplo, TypeScript), se indica explícitamente y se proporciona la contraparte para otros stacks.

---

## Tabla de Contenidos

1. [Filosofía General](#1-filosofía-general)
2. [Fase 0: Especificación y Plan](#2-fase-0-especificación-y-plan)
3. [Estructura del Proyecto](#3-estructura-del-proyecto)
4. [Stack y Dependencias](#4-stack-y-dependencias)
5. [Documentación para Agentes de IA](#5-documentación-para-agentes-de-ia)
6. [Arquitectura y Diseño](#6-arquitectura-y-diseño)
7. [Estándares de Código](#7-estándares-de-código)
8. [Componentes y Librerías Externas](#8-componentes-y-librerías-externas)
9. [Manejo de Errores y Resiliencia](#9-manejo-de-errores-y-resiliencia)
10. [Seguridad](#10-seguridad)
11. [Entorno de Desarrollo](#11-entorno-de-desarrollo)
12. [Build y Despliegue](#12-build-y-despliegue)
13. [CI/CD y Automatización](#13-cicd-y-automatización)
14. [Monitoreo y Observabilidad](#14-monitoreo-y-observabilidad)
15. [Rendimiento](#15-rendimiento)
16. [Verificación y Pruebas](#16-verificación-y-pruebas)
17. [Accesibilidad](#17-accesibilidad)

---

## 1. Filosofía General

### 1.1. Primero el Plan, Después el Código

Nunca escribir código sin haber definido antes las especificaciones y el plan de implementación. El plan debe tener checkboxes y fases claras. Esto aplica al proyecto completo y a cada feature individual.

> **Ejemplo práctico:** Antes de implementar un sistema de autenticación, documenta: qué método se usará (JWT, sesiones, OAuth), qué endpoints existirán, qué datos se almacenan, y qué flujos de error se manejan.

### 1.2. Decisiones por Escrito

Toda decisión técnica importante debe quedar documentada. Si no está escrita, no existe. Esto incluye:

- Por qué se eligió la tecnología X sobre Y
- Qué alternativas se descartaron y por qué
- Cuáles son las restricciones y trade-offs aceptados

### 1.3. Canonical Source of Truth

Designar archivos específicos como la fuente de verdad del proyecto:

- **`/spec/`** para especificaciones técnicas
- **`/plan/`** para planes de implementación
- **`/docs/`** para documentación de referencia

Cualquier desviación del código respecto a estos archivos es un error que debe corregirse.

### 1.4. Menos es Más

Cada dependencia, cada abstracción, cada archivo tiene un costo. Preguntar siempre: *"¿Realmente necesitamos esto?"* antes de agregar algo. Preferir cero dependencias sobre una dependencia innecesaria.

> **Regla práctica:** Si una dependencia se usa en menos de 3 lugares, evalúa si puedes resolverlo con código nativo del lenguaje.

### 1.5. Reproducibilidad

El proyecto debe poder construirse desde cero siguiendo solo los archivos de documentación y el plan. Si alguien nuevo llega al equipo, debe poder entender todo sin preguntar.

---

## 2. Fase 0: Especificación y Plan

### 2.1. Crear `/spec/` Primero

Antes de escribir una línea de código:

a) Definir el stack tecnológico (runtime, framework, base de datos, etc.)
b) Definir restricciones (qué NO se permite)
c) Definir la arquitectura general
d) Definir la estructura de directorios
e) Documentar flujos clave (request/response, datos, autenticación, etc.)
f) Incluir tabla de variables de entorno con valores por defecto

### 2.2. Crear `/plan/` Después

a) Dividir la implementación en fases ordenadas por dependencia
b) Cada fase debe tener checkboxes accionables
c) Las fases deben ir de lo fundamental a lo específico
d) Incluir comandos exactos de instalación y verificación
e) Incluir notas técnicas sobre decisiones importantes

### 2.3. Mantener Ambos Actualizados

Cada vez que se implementa un cambio significativo:

a) Marcar los checkboxes del plan como completados
b) Actualizar especificaciones si el cambio afecta decisiones previas
c) Si se descubre algo que contradice el plan, corregir el plan (no el código)

---

## 3. Estructura del Proyecto

### 3.1. Separación por Propósito

La estructura debe reflejar la arquitectura del proyecto. A continuación se muestra un esquema genérico adaptable:

```
src/
  core/        - Lógica de negocio central (independiente del framework)
  api/         - Capa de exposición (endpoints, controladores, resolvers)
  data/        - Acceso a datos (repositorios, modelos, migraciones)
  services/    - Servicios de aplicación (orquestación, lógica transversal)
  config/      - Configuración y constantes
  utils/       - Helpers y utilidades reutilizables
  middleware/  - Middleware de la capa de transporte
```

> **Adaptación por stack:**
> - **Frontend (React/Vue/Svelte):** `components/`, `pages/`, `hooks/`, `stores/`, `styles/`
> - **Backend (Node/Python/Go):** `handlers/`, `services/`, `repositories/`, `models/`
> - **Monorepo:** `packages/` con subpaquetes independientes

### 3.2. Archivos Raíz Mínimos

En la raíz del proyecto solo debe haber:

- Archivos de configuración (`.json`, `.toml`, `.yaml`, `.env`)
- Documentación (`.md`)
- Directorios principales: `spec/`, `plan/`, `src/`, `tests/`, `docs/`

### 3.3. Nombres de Archivos

- **kebab-case** para archivos de código: `user-service.ts`, `auth_handler.go`
- **PascalCase** para componentes de UI (si aplica): `Button.tsx`, `CardHeader.vue`
- **snake_case** si el lenguaje lo convenciona (Python, Ruby): `user_service.py`
- **Regla de oro:** Seguir la convención del lenguaje/framework elegido y ser consistente en todo el proyecto

### 3.4. .gitignore

Todo lo que se genera (build, `node_modules/`, `__pycache__/`, `target/`, `.env` con secretos) debe estar en `.gitignore` desde el día 1.

---

## 4. Stack y Dependencias

### 4.1. Versiones Explícitas

Registrar en las especificaciones las versiones exactas o rangos de cada tecnología. NO dejar versiones "latest" sin control.

> **Ejemplos por ecosistema:**
> - **Node.js:** Usar `package-lock.json` con versiones exactas
> - **Python:** Usar `requirements.txt` con pines (`requests==2.31.0`) o `pyproject.toml`
> - **Go:** Usar `go.sum` y verificar hashes
> - **Rust:** Usar `Cargo.lock`

### 4.2. Clasificar Dependencias

- **Producción:** Necesarias para que la aplicación funcione en runtime
- **Desarrollo:** Herramientas, bundlers, CLIs, type definitions, testing frameworks
- **Peer/Solo:** Dependencias opcionales o que el consumidor debe proporcionar

### 4.3. Mínimo Necesario

Cada dependencia debe justificarse respondiendo tres preguntas:

1. ¿Qué problema resuelve?
2. ¿Podemos resolverlo con APIs nativas del lenguaje?
3. ¿El beneficio justifica el costo (tamaño, complejidad, superficie de ataque, mantenimiento)?

### 4.4. Sources of Truth para Versiones

No asumir. Investigar en:

- Sitio web oficial del proyecto
- GitHub releases / Git tags
- Registry del ecosistema (npm, PyPI, crates.io, Maven Central)
- Documentación oficial

### 4.5. Actualizar Registro

Cuando se agrega una dependencia, actualizar el archivo de especificaciones. Si un gestor de paquetes o CLI instala dependencias automáticamente, revisar el archivo de lock/manifest después para registrar los cambios.

---

## 5. Documentación para Agentes de IA

Esta sección aplica si utilizas herramientas de IA (Copilot, Cursor, Codebuff, Claude, etc.) como parte de tu flujo de desarrollo.

### 5.1. Archivo de Instrucciones del Proyecto

Crear un archivo compacto que responda: *"¿Qué se equivocaría un agente si no tuviera esto?"*

El nombre varía según la herramienta (`AGENTS.md`, `CLAUDE.md`, `.cursorrules`, `COPILOT.md`). Incluir:

- Stack exacto con versiones
- Comandos importantes (especialmente los no obvios)
- Patrones de arquitectura (cómo se añade una página, un componente, un endpoint)
- Quirks y advertencias del proyecto
- Pasos de verificación

### 5.2. Skills y Prompts Especializados

Si la herramienta lo permite, crear instrucciones especializadas para tareas específicas:

- Skill de arquitectura general del proyecto
- Skill para la biblioteca de componentes (si aplica)
- Skill para features específicos

Cada skill debe tener: nombre, versión, descripción, y cuándo usarlo.

### 5.3. Jerarquía de Referencias

El archivo principal debe referenciar `/spec` y `/plan` como fuente de verdad. Los skills pueden referenciar el archivo principal.

### 5.4. Lo que NO Debe Ir en la Documentación

- Tutoriales largos (van en `/docs/tutorials/`)
- Árboles de archivos exhaustivos (se generan automáticamente)
- Consejos genéricos de programación
- Afirmaciones especulativas o no verificables

---

## 6. Arquitectura y Diseño

### 6.1. Entrypoint Único

El servidor/aplicación debe tener un solo punto de entrada claro.

> **Ejemplos por stack:**
> - **Node.js:** `src/server.ts`, `src/index.js`
> - **Python:** `main.py`, `app/__init__.py`
> - **Go:** `cmd/server/main.go`
> - **Rust:** `src/main.rs`

### 6.2. Separación de Responsabilidades

Cada capa tiene una función única:

- **Routing/Transport:** Solo encaminar peticiones
- **Handlers/Controllers:** Solo recibir y responder
- **Services:** Solo orquestar lógica de negocio
- **Repositories/Data:** Solo acceso a datos

NO mezclar responsabilidades en un mismo archivo. Si un archivo hace dos cosas, divídelo.

### 6.3. Flujos Documentados

Cada flujo importante (request/response, build, deploy, autenticación) debe documentarse paso a paso en las especificaciones.

### 6.4. Configuración por Entorno

a) Variables de entorno para configuración (puerto, modo, URL de base de datos)
b) Valores por defecto sensatos que permitan desarrollo sin configuración manual
c) Archivo `.env` (o equivalente) con valores de desarrollo
d) Documentar cada variable en una tabla con: nombre, valor por defecto, descripción, si es requerida

---

## 7. Estándares de Código

### 7.1. Tipado y Validación

Usar el sistema de tipos del lenguaje de forma estricta cuando esté disponible. Tipar todo: parámetros, retornos, estados. Evitar `any`, `object` genéricos, o tipos débiles a menos que sea unavoidable y esté documentado.

> **Por lenguaje:**
> - **TypeScript:** Activar `strict: true` en `tsconfig.json`
> - **Python:** Usar type hints + `mypy` o `pyright`
> - **Go:** Aprovechar el tipado estático nativo
> - **Rust:** El compilador ya es estricto por diseño

### 7.2. Comentarios en Código

El código debe ser autoexplicativo. Los comentarios deben explicar **por qué**, no **qué**:

```python
# ✅ Correcto: explica el por qué
# Usamos retry exponencial porque la API de pagos tiene rate limits agresivos
def process_payment(order):
    ...

# ❌ Incorrecto: describe lo que el código ya dice
# Llama a process_payment con la orden
process_payment(order)
```

Los comentarios se desactualizan; el código compilado no miente.

### 7.3. Convenciones

a) Seguir las convenciones del lenguaje/framework elegido
b) Ser consistente: una vez que se elige un patrón, usarlo en todo el proyecto
c) Preferir inmutabilidad cuando el lenguaje lo permita
d) Nombres descriptivos sobre abreviaciones (`user_repository` > `usr_repo`)

### 7.4. Imports y Módulos

a) Orden: estándar del lenguaje primero, luego terceros, luego internos
b) Usar path aliases o módulos organizados si el lenguaje lo soporta
c) No importar lo que no se usa

---

## 8. Componentes y Librerías Externas

### 8.1. Instalar Tal Cual, No Modificar

Los componentes de librerías externas que se copian al proyecto deben quedar **exactamente** como los genera la herramienta de instalación. Si se necesita modificar, crear un wrapper propio encima.

> **Ejemplo:** En un proyecto React con shadcn/ui, no edites directamente `components/ui/button.tsx`. En su lugar, crea `components/custom-button.tsx` que importe y extienda el componente original.

### 8.2. CLI vs. Guías de Composición

Algunos componentes son instalables vía CLI y otros solo documentados como guías de composición. Identificar cuál es cuál en la documentación oficial antes de asumir.

### 8.3. Providers y Configuración Inicial

Identificar qué componentes requieren configuración a nivel de aplicación (providers, inicialización, plugins) y agregarlos en el entrypoint correspondiente **inmediatamente después** de instalar el componente.

### 8.4. Wrappers de Terceros

Si un componente wrapper depende de un contexto que no existe en el proyecto, usar la librería base directamente en lugar de instalar dependencias pesadas innecesarias.

---

## 9. Manejo de Errores y Resiliencia

### 9.1. Nunca Crashear el Proceso

a) Cada handler/punto de entrada debe tener manejo de errores (try/except, Result type, error handling según el lenguaje)
b) Un handler global debe capturar errores no manejados
c) Nunca dejar que una excepción crashee el proceso sin intentar recuperación
d) Responder al usuario/cliente con mensajes útiles (no stack traces)

> **Por lenguaje:**
> - **Python:** `try/except` con logging
> - **Go:** Manejo explícito de `error` returns
> - **Rust:** `Result<T, E>` con `?` operator
> - **Node.js:** `try/catch` + `process.on('uncaughtException')`

### 9.2. Logging Estructurado

a) Loggear el error completo con contexto suficiente para diagnosticarlo
b) Incluir: timestamp, nivel, módulo, acción que falló, datos relevantes (sin secretos)
c) En producción, usar formato estructurado (JSON) para facilitar la búsqueda

### 9.3. Errores Específicos vs. Genéricos

a) Capturar errores específicos cuando se pueda hacer algo al respecto (reintentar, fallback, notificar)
b) Captura genérica como safety net, no como estrategia principal
c) Definir tipos de error propios del dominio

---

## 10. Seguridad

### 10.1. Principios Fundamentales

a) **Nunca confiar en la entrada del usuario:** Validar y sanitizar toda entrada externa
b) **Principio de mínimo privilegio:** Cada componente solo tiene acceso a lo que necesita
c) **Defensa en profundidad:** Múltiples capas de seguridad, no una sola

### 10.2. Gestión de Secretos

a) **Nunca** hardcodear secretos en el código fuente
b) Usar variables de entorno o un gestor de secretos (Vault, AWS Secrets Manager, etc.)
c) Rotar secretos periódicamente
d) Tener un plan para revocar secretos comprometidos

### 10.3. Autenticación y Autorización

a) Usar frameworks/librerías probadas para autenticación (no implementar desde cero)
b) Hashear contraseñas con algoritmos seguros (bcrypt, Argon2) — nunca MD5 ni SHA1
c) Implementar rate limiting en endpoints de autenticación
d) Usar HTTPS obligatoriamente en producción

### 10.4. Protección de Datos

a) Sanitizar outputs para prevenir XSS
b) Usar consultas parametrizadas para prevenir SQL injection
c) Validar tipos y formatos en cada capa de la aplicación
d) Implementar CORS de forma restrictiva

---

## 11. Entorno de Desarrollo

### 11.1. Scripts Claros

a) **`dev`:** Inicia todo lo necesario para desarrollo (servidor, watchers, hot-reload)
b) **`build`:** Compila todo para producción
c) **`start`:** Ejecuta el build de producción
d) Scripts auxiliares con prefijo descriptivo (`dev:db`, `build:assets`, `test:unit`)

### 11.2. Hot Reload

a) Usar watch mode del runtime/bundler para reinicio automático al guardar
b) Hot-reload de CSS/estilos en paralelo al servidor
c) Asegurar que los procesos hijos (watchers) no queden huérfanos al hacer Ctrl+C

### 11.3. Variables de Entorno

a) Valores por defecto para desarrollo que permitan arrancar sin configuración
b) Archivo `.env` en `.gitignore`
c) Archivo `.env.example` (o `.env.template`) **versionado** como referencia
d) Valores sensibles **NUNCA** en el repositorio

### 11.4. Puerto Dedicado

a) Puerto fijo para el proyecto (no usar el default del framework)
b) Documentar el puerto en `.env.example` y en las especificaciones
c) Configurable vía variable de entorno

---

## 12. Build y Despliegue

### 12.1. Build Reproducible

a) El build debe ser determinístico: misma entrada → misma salida
b) Documentar los comandos exactos de build
c) El build debe funcionar en un entorno limpio (sin dependencias implícitas del sistema)

### 12.2. Artefactos en .gitignore

a) Directorios de build (`build/`, `dist/`, `out/`, `target/`) en `.gitignore`
b) Los artefactos compilados se regeneran siempre, no se versionan

### 12.3. Entorno de Producción

a) Distinguir claramente entre desarrollo y producción (`NODE_ENV`, `APP_ENV`, `RUST_ENV`, etc.)
b) El build de producción debe ser diferente del de desarrollo:
   - Minificado y optimizado
   - Sin source maps
   - Con headers de caché apropiados
   - Sin herramientas de desarrollo

### 12.4. Verificación Pre-Deploy

Antes de cada despliegue, verificar:

a) El build completa sin errores
b) El servidor arranca correctamente
c) Los endpoints principales responden correctamente
d) Los assets estáticos se sirven correctamente

---

## 13. CI/CD y Automatización

### 13.1. Pipeline Mínimo

Todo proyecto debe tener un pipeline automatizado que ejecute, como mínimo:

1. **Lint:** Verificación de estilo y formato
2. **Type Check:** Verificación de tipos (si aplica)
3. **Tests:** Suite de tests automatizados
4. **Build:** Compilación del proyecto

### 13.2. Ramas y Despliegue

a) Definir una estrategia de ramas clara (trunk-based, gitflow, etc.)
b) Los merges a `main` solo deben ocurrir si el pipeline pasa
c) El despliegue a producción debe ser automático o de un solo comando

### 13.3. Hooks de Git

a) Usar pre-commit hooks para formateo automático y linting
b) Usar pre-push hooks para ejecutar tests rápidos
c) Herramientas recomendadas: `husky`, `pre-commit`, `lefthook`

---

## 14. Monitoreo y Observabilidad

### 14.1. Logging en Producción

a) Logs estructurados (JSON) con campos consistentes
b) Niveles claros: DEBUG, INFO, WARN, ERROR, FATAL
c) No loggear información sensible (contraseñas, tokens, datos personales)

### 14.2. Health Checks

a) Exponer un endpoint `/health` o equivalente que verifique dependencias críticas
b) Incluir verificación de conectividad a base de datos y servicios externos
c) Usar para alertas automatizadas y balanceo de carga

### 14.3. Métricas y Alertas

a) Monitorear: latencia de respuesta, tasa de error, uso de recursos
b) Configurar alertas para umbrales críticos
c) Tener un dashboard accesible para el equipo

---

## 15. Rendimiento

### 15.1. Principios Generales

a) Medir antes de optimizar — no optimizar prematuramente
b) Establecer benchmarks y métricas de rendimiento desde el inicio
c) Documentar los límites de rendimiento aceptables en las especificaciones

### 15.2. Estrategias Comunes

a) **Caché:** Implementar en capas apropiadas (HTTP, aplicación, base de datos)
b) **Paginación:** Nunca retornar colecciones completas sin límite
c) **Índices:** Asegurar que las consultas frecuentes estén indexadas
d) **Compresión:** Habilitar gzip/brotli para respuestas HTTP
e) **Lazy loading:** Cargar recursos solo cuando se necesitan

### 15.3. Prevención de Degradación

a) Rate limiting para proteger contra abuso
b) Timeouts en llamadas a servicios externos
c) Circuit breakers para dependencias externas
d) Connection pooling para bases de datos

---

## 16. Verificación y Pruebas

### 16.1. Estrategia de Testing

Definir qué se prueba y con qué nivel de detalle:

| Nivel | Qué cubre | Velocidad | Cantidad |
|-------|-----------|-----------|----------|
| **Unit** | Funciones/clases aisladas | Rápido | Muchos |
| **Integration** | Interacción entre módulos | Medio | Moderados |
| **E2E** | Flujos completos de usuario | Lento | Pocos |

### 16.2. Verificar Después de Cada Cambio

a) Compilar sin errores
b) Ejecutar suite de tests
c) Probar endpoints/rutas principales con curl o herramienta similar
d) Verificar status codes correctos

### 16.3. Comandos de Verificación

Incluir en la documentación comandos exactos para verificar:

```bash
# Ejemplos genéricos (adaptar al stack del proyecto)

# Compilación
npm run build          # Node.js
python -m build        # Python
go build ./...         # Go
cargo build            # Rust

# Tests
npm test               # Node.js
pytest                 # Python
go test ./...          # Go
cargo test             # Rust

# Verificación de servidor
curl http://localhost:3000/
curl http://localhost:3000/api/health
curl -o /dev/null -s -w "%{http_code}" http://localhost:3000/ruta-inexistente
```

### 16.4. Lo que No se Compila, No Existe

Si no compila, no funciona. Si no se puede verificar, no está completo. El proyecto debe poder verificarse con comandos simples ejecutables por cualquier desarrollador.

---

## 17. Accesibilidad

> **Nota:** Esta sección aplica principalmente a proyectos con interfaz de usuario (web, móvil, desktop).

### 17.1. Estándar Mínimo

a) Cumplir al menos WCAG 2.1 nivel AA
b) Toda interacción debe ser accesible por teclado
c) Todo contenido visual debe tener alternativa textual apropiada
d) El contraste de colores debe cumplir ratios mínimos (4.5:1 para texto normal)

### 17.2. Prácticas de Desarrollo

a) Usar semántica HTML correcta (o equivalente nativo del framework)
b) Probar con lectores de pantalla periódicamente
c) No depender exclusivamente del color para transmitir información
d) Asegurar que los formularios tengan labels asociados

### 17.3. Testing de Accesibilidad

a) Incluir tests automatizados de accesibilidad en el pipeline (axe, Lighthouse)
b) Realizar pruebas manuales con lectores de pantalla al menos una vez por sprint
c) Documentar y trackear issues de accesibilidad como bugs prioritarios

---

*FIN DE LA GUÍA — v2.0 — Principios universales para crear proyectos mantenibles, seguros y robustos*
