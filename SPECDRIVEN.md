# Guía de Lineamientos y Buenas Prácticas para Creación de Proyectos

**Versión:** 7.0

Este documento establece los principios, estándares y procesos que deben seguirse al crear y mantener un proyecto de software. Las recomendaciones aquí descritas aplican a cualquier lenguaje, framework o plataforma. El enfoque está en **cómo pensar y planificar**, no en qué herramientas específicas usar.

---

## Tabla de Contenidos

1. [Filosofía General](#1-filosofía-general)
2. [Metodología Spec-Driven](#2-metodología-spec-driven)
3. [Especificación y Plan](#3-especificación-y-plan)
4. [Stack y Dependencias](#4-stack-y-dependencias)
5. [Arquitectura y Diseño](#5-arquitectura-y-diseño)
6. [Estándares de Código](#6-estándares-de-código)
7. [Componentes y Librerías Externas](#7-componentes-y-librerías-externas)
8. [Manejo de Errores y Resiliencia](#8-manejo-de-errores-y-resiliencia)
9. [Seguridad](#9-seguridad)
10. [Entorno de Desarrollo](#10-entorno-de-desarrollo)
11. [Build y Despliegue](#11-build-y-despliegue)
12. [CI/CD y Automatización](#12-cicd-y-automatización)
13. [Monitoreo y Observabilidad](#13-monitoreo-y-observabilidad)
14. [Rendimiento](#14-rendimiento)
15. [Verificación y Pruebas](#15-verificación-y-pruebas)
16. [Checklist de Auditoría y Consultas](#16-checklist-de-auditoría-y-consultas)
17. [Accesibilidad](#17-accesibilidad)
18. [Adopción y Migración](#18-adopción-y-migración)
19. [Glosario](#19-glosario)
20. [Guía para AGENTS.md](#20-guía-para-agentsmd)

---

## 1. Filosofía General

### 1.1. Primero el Plan, Después el Código

Nunca escribir código sin haber definido antes las especificaciones y el plan de implementación. El plan debe tener checkboxes y fases claras. Esto aplica al proyecto completo y a cada feature individual.

> **Ver también:** La sección [2. Metodología Spec-Driven](#2-metodología-spec-driven) detalla el ciclo completo, los roles de `/spec`, `/plan` y `/.skills`, y el flujo de trabajo paso a paso.

> **Ejemplo práctico:** Antes de implementar un sistema de autenticación, documenta: qué método se usará (tokens, sesiones, OAuth), qué endpoints existirán, qué datos se almacenan, y qué flujos de error se manejan.

### 1.2. Decisiones por Escrito

Toda decisión técnica importante debe quedar documentada. Si no está escrita, no existe. Esto incluye:

- Por qué se eligió la tecnología X sobre Y
- Qué alternativas se descartaron y por qué
- Cuáles son las restricciones y trade-offs aceptados

### 1.3. Fuente Única de Verdad (Source of Truth)

Designar archivos específicos como la fuente de verdad del proyecto:

- **Especificaciones técnicas** → define el *qué* y el *por qué*
- **Plan de implementación** → define el *cómo* y el *cuándo*
- **Documentación de referencia** → detalla decisiones y guías

Cualquier desviación del código respecto a estos archivos es un error que debe corregirse.

### 1.4. Menos es Más

Cada dependencia, cada abstracción, cada archivo tiene un costo. Preguntar siempre: *"¿Realmente necesitamos esto?"* antes de agregar algo. Preferir cero dependencias sobre una dependencia innecesaria.

> **Regla práctica:** Si una dependencia se usa en muy pocos lugares, evalúa si puedes resolverlo con las capacidades nativas del lenguaje.

### 1.5. Reproducibilidad

El proyecto debe poder construirse desde cero siguiendo solo los archivos de documentación y el plan. Si alguien nuevo llega al equipo, debe poder entender todo sin preguntar.

---

## 2. Metodología Spec-Driven

Spec-Driven (Desarrollo Guiado por Especificaciones) es una metodología donde las especificaciones son el centro del desarrollo. En lugar de empezar con código, se comienza con especificaciones claras y verificables. Los tres pilares son **spec** (qué), **plan** (cómo) y **skills** (quién ejecuta).

### 2.1. ¿Para qué sirve?

a) **Definir antes de implementar** — Reduce ambigüedad antes de escribir código
b) **Verificación objetiva** — Las specs se convierten en criterios de aceptación
c) **Comunicación clara** — Todo el equipo comparte la misma interpretación
d) **Reducir retrabajo** — Menos cambios costosos después de implementar

> **Para equipos que migran proyectos existentes:** Ver la sección [18. Adopción y Migración](#18-adopción-y-migración) para estrategias de adopción incremental.

### 2.2. Ciclo Spec-Driven

El ciclo se repite para cada feature o componente:

1. **Escribir especificaciones** — Describir *qué* debe hacer el sistema (el "qué" vive en `/spec`)
2. **Crear el plan** — Describir *cómo* se implementará (el "cómo" vive en `/plan`)
3. **Validar** — Revisar que las specs sean correctas, completas y consistentes (ver criterios en [2.2.1](#221-criterios-de-validación-de-especificaciones))
4. **Implementar** — Escribir código que cumpla las especificaciones
5. **Verificar** — Confirmar que la implementación cumple las specs (tests, auditoría, revisión)
6. **Iterar** — Si la verificación falla, corregir la implementación o ajustar las specs

#### 2.2.1. Criterios de Validación de Especificaciones

Antes de pasar a la fase de implementación, cada especificación debe cumplir estos criterios:

- [ ] **Completitud:** Cubre todos los flujos principales, incluyendo casos de error y edge cases
- [ ] **Consistencia:** No hay contradicciones entre las secciones de la spec
- [ ] **Verificabilidad:** Cada requisito puede ser confirmado con un test, auditoría o inspección manual
- [ ] **Alcance definido:** La spec describe un componente o feature con límites claros
- [ ] **Dependencias explícitas:** Las interacciones con otros componentes están documentadas
- [ ] **Revisión cruzada:** Al menos una persona distinta del autor revisó la spec

> **Regla práctica:** Si no puedes escribir un test de aceptación para un requisito de la spec, el requisito no está lo suficientemente claro.

### 2.3. Regla de Oro: el "qué", el "cómo" y el "quién"

- **`/spec`** define el *qué*: qué hace el sistema, qué datos maneja, qué restricciones tiene
- **`/plan`** define el *cómo*: cómo se implementa, qué pasos seguir, qué orden respetar
- **`/.skills`** define el *quién*: qué expertos ejecutan, verifican y auditan basándose en spec y plan
- El código es la consecuencia de los tres, nunca la fuente de verdad

> **Importante:** Los skills siempre deben hacer referencia a `/spec` y `/plan`. Si un skill contradice una especificación o el plan, el skill está mal — corregir el skill, no la especificación.

### 2.4. Flujo de Trabajo Paso a Paso

Para iniciar un proyecto nuevo o una feature importante, crear los directorios en este orden exacto:

```
1. /spec/        → Definir el QUÉ
2. /plan/        → Definir el CÓMO
3. /.skills/     → Configurar los EXPERTOS que ejecutan basándose en spec y plan
```

> **Sobre la convención `/.skills`:** El punto inicial (`.`) sigue la convención de directorios ocultos/sistémicos (como `.git`, `.github`). Esto indica que `/.skills` es un directorio de configuración interna del proyecto, no una carpeta de código fuente. Los skills no se distribuyen como artefactos de la aplicación — son herramientas de desarrollo que viven junto al proyecto pero no forman parte del build de producción.

#### Paso 1: Crear `/spec/` — Las Especificaciones

Definir qué debe hacer el sistema antes de cualquier otra cosa. Este directorio contiene:

- El stack tecnológico elegido y por qué
- Las restricciones del proyecto
- La arquitectura general
- Los flujos de datos y procesos
- Las variables de entorno y su configuración

#### Paso 2: Crear `/plan/` — El Plan de Implementación

Con las especificaciones definidas, crear el plan paso a paso. Este directorio contiene:

- Fases ordenadas por dependencia con checkboxes
- Comandos exactos de instalación y verificación
- Notas técnicas sobre decisiones de implementación
- Criterios de aceptación para cada fase

#### Paso 3: Crear `/.skills/` — Los Expertos

Una vez que `/spec/` y `/plan/` están definidos, configurar los expertos (agents, skills, herramientas de análisis) que trabajarán basándose en lo documentado. Cada skill/experto debe:

- Referenciar explícitamente las secciones relevantes de `/spec/` y `/plan/` que le competen
- Definir su alcance: qué aspecto del proyecto analiza o implementa
- Especificar qué debe verificar, qué debe ignorar, y cuándo debe actuar
- Estar alineado con las especificaciones y el plan, nunca contradecirlos

> **Regla fundamental:** Los skills se crean DESPUÉS de spec y plan porque necesitan saber QUÉ construir y CÓMO hacerlo para poder actuar como expertos. Un skill sin spec y plan es un experto sin contexto.

### 2.5. Formato de Archivos y Convenciones de Nombre

Los directorios `/spec` y `/plan` deben seguir convenciones claras para que sean mantenibles y navegables:

**Formato recomendado:**
- Usar **Markdown** (`.md`) como formato estándar para specs y planes — es legible, versionable y universal
- Un archivo principal de especificación (por ejemplo `SPEC.md`) que contenga la visión general
- Archivos adicionales por feature o componente cuando la spec crezca (por ejemplo `auth-spec.md`, `payments-spec.md`)

**Convenciones de nombre:**
- Nombres descriptivos en minúsculas con guiones: `user-management-spec.md`
- Prefijo del directorio al que pertenecen: `/spec/auth-spec.md`, `/plan/auth-plan.md`
- Evitar abreviaciones ambiguas: `payments-spec.md` en lugar de `pay-s.md`

**Estructura mínima de una spec:**
```markdown
# [Nombre del Componente/Feature]
## Objetivo
## Restricciones
## Flujos
## Variables de Entorno
## Criterios de Aceptación
```

**Estructura mínima de un plan:**
```markdown
# [Nombre del Componente/Feature] — Plan
## Fase 1: [Nombre]
- [ ] Paso 1.1
- [ ] Paso 1.2
## Fase 2: [Nombre]
- [ ] Paso 2.1
## Comandos de Verificación
## Notas Técnicas
```

> **Nota:** Las convenciones de nombre son una guía, no una restricción rígida. Lo importante es la consistencia dentro del proyecto. Definir la convención en la spec inicial y respetarla.

---

## 3. Especificación y Plan

### 3.1. Crear Especificaciones Primero

Antes de escribir una línea de código:

a) Definir el stack tecnológico (runtime, framework, base de datos, etc.)
b) Definir restricciones (qué NO se permite)
c) Definir la arquitectura general
d) Documentar flujos clave (request/response, datos, autenticación, etc.)
e) Incluir tabla de variables de entorno con valores por defecto

### 3.2. Crear el Plan Después

a) Dividir la implementación en fases ordenadas por dependencia
b) Cada fase debe tener checkboxes accionables
c) Las fases deben ir de lo fundamental a lo específico
d) Incluir comandos exactos de instalación y verificación
e) Incluir notas técnicas sobre decisiones importantes

### 3.3. Mantener Ambos Actualizados

Cada vez que se implementa un cambio significativo:

a) Marcar los checkboxes del plan como completados
b) Actualizar especificaciones si el cambio afecta decisiones previas
c) Si se descubre algo que contradice el plan, corregir el plan (no el código)
d) Actualizar los skills que referencien las secciones modificadas de spec y plan

> **Nota:** Para el flujo completo de creación de directorios (spec → plan → skills), consultar la [sección 2.4](#24-flujo-de-trabajo-paso-a-paso).

---

## 4. Stack y Dependencias

### 4.1. Versiones Explícitas

Registrar en las especificaciones las versiones exactas o rangos de cada tecnología. NO dejar versiones "latest" sin control. Cada ecosistema tiene su mecanismo de lock de versiones — usarlo desde el día 1.

### 4.2. Clasificar Dependencias

- **Producción:** Necesarias para que la aplicación funcione en runtime
- **Desarrollo:** Herramientas, bundlers, CLIs, testing frameworks
- **Peer/Solo:** Dependencias opcionales o que el consumidor debe proporcionar

### 4.3. Mínimo Necesario

Cada dependencia debe justificarse respondiendo tres preguntas:

1. ¿Qué problema resuelve?
2. ¿Podemos resolverlo con las capacidades nativas del lenguaje?
3. ¿El beneficio justifica el costo (tamaño, complejidad, superficie de ataque, mantenimiento)?

### 4.4. Fuentes de Verdad para Versiones

No asumir. Investigar en:

- Sitio web oficial del proyecto
- Repositorio de código fuente y sus releases
- Registry del ecosistema
- Documentación oficial

### 4.5. Actualizar Registro

Cuando se agrega una dependencia, actualizar el archivo de especificaciones. Si una herramienta instala dependencias automáticamente, revisar el archivo de manifest/lock después para registrar los cambios.

---

## 5. Arquitectura y Diseño

### 5.1. Entrypoint Único

La aplicación debe tener un solo punto de entrada claro, documentado en las especificaciones.

### 5.2. Separación de Responsabilidades

Cada capa tiene una función única:

- **Transporte/Routing:** Solo encaminar peticiones
- **Handlers/Controllers:** Solo recibir y responder
- **Servicios:** Solo orquestar lógica de negocio
- **Datos:** Solo acceso a datos

NO mezclar responsabilidades en un mismo archivo. Si un archivo hace dos cosas, divídelo.

### 5.3. Flujos Documentados

Cada flujo importante (request/response, build, deploy, autenticación) debe documentarse paso a paso en las especificaciones.

### 5.4. Configuración por Entorno

a) Variables de entorno para configuración (puerto, modo, URL de base de datos)
b) Valores por defecto sensatos que permitan desarrollo sin configuración manual
c) Archivo de configuración local con valores de desarrollo (no versionado)
d) Documentar cada variable en una tabla con: nombre, valor por defecto, descripción, si es requerida

### 5.5. Nombres de Archivos

- Seguir la convención del lenguaje/framework elegido (kebab-case, snake_case, PascalCase, etc.)
- Ser consistente en todo el proyecto: no mezclar estilos de nomenclatura
- Los nombres deben ser descriptivos y predecibles

---

## 6. Estándares de Código

### 6.1. Tipado y Validación

- Usar el sistema de tipos del lenguaje de forma estricta cuando esté disponible
- Tipar todo: parámetros, retornos, estados
- Evitar tipos débiles o genéricos a menos que sea unavoidable y esté documentado

### 6.2. Comentarios en Código

El código debe ser autoexplicativo. Los comentarios deben explicar **por qué**, no **qué**:

- ✅ **Correcto:** `// Usamos retry exponencial porque la API de pagos tiene rate limits agresivos` → explica el por qué de una decisión no obvia
- ❌ **Incorrecto:** `// Llama a process_payment con la orden` → describe lo que el código ya dice por sí solo

Los comentarios se desactualizan; el código compilado no miente.

### 6.3. Convenciones

a) Seguir las convenciones del lenguaje/framework elegido
b) Ser consistente: una vez que se elige un patrón, usarlo en todo el proyecto
c) Preferir inmutabilidad cuando el lenguaje lo permita
d) Nombres descriptivos sobre abreviaciones

### 6.4. Organización de Módulos

a) Ordenar imports: estándar del lenguaje primero, luego terceros, luego internos
b) Usar aliases o módulos organizados si el ecosistema lo soporta
c) No importar lo que no se usa

---

## 7. Componentes y Librerías Externas

### 7.1. Instalar Tal Cual, No Modificar

Los componentes de librerías externas que se copian al proyecto deben quedar **exactamente** como los genera la herramienta de instalación. Si se necesita modificar, crear un wrapper propio encima.

### 7.2. CLI vs. Guías de Composición

Algunos componentes son instalables vía CLI y otros solo documentados como guías de composición. Identificar cuál es cuál en la documentación oficial antes de asumir.

### 7.3. Providers y Configuración Inicial

Identificar qué componentes requieren configuración a nivel de aplicación (providers, inicialización, plugins) y agregarlos en el entrypoint correspondiente **inmediatamente después** de instalar el componente.

### 7.4. Wrappers de Terceros

Si un componente wrapper depende de un contexto que no existe en el proyecto, usar la librería base directamente en lugar de instalar dependencias pesadas innecesarias.

---

## 8. Manejo de Errores y Resiliencia

### 8.1. Nunca Crashear el Proceso

a) Cada handler/punto de entrada debe tener manejo de errores
b) Un handler global debe capturar errores no manejados
c) Nunca dejar que una excepción crashee el proceso sin intentar recuperación
d) Responder al usuario/cliente con mensajes útiles (no stack traces)

### 8.2. Logging Estructurado

a) Loggear el error completo con contexto suficiente para diagnosticarlo
b) Incluir: timestamp, nivel, módulo, acción que falló, datos relevantes (sin secretos)
c) En producción, usar formato estructurado (JSON) para facilitar la búsqueda

### 8.3. Errores Específicos vs. Genéricos

a) Capturar errores específicos cuando se pueda hacer algo al respecto (reintentar, fallback, notificar)
b) Captura genérica como safety net, no como estrategia principal
c) Definir tipos de error propios del dominio

---

## 9. Seguridad

### 9.1. Principios Fundamentales

a) **Nunca confiar en la entrada del usuario:** Validar y sanitizar toda entrada externa
b) **Principio de mínimo privilegio:** Cada componente solo tiene acceso a lo que necesita
c) **Defensa en profundidad:** Múltiples capas de seguridad, no una sola

### 9.2. Gestión de Secretos

a) **Nunca** hardcodear secretos en el código fuente
b) Usar variables de entorno o un gestor de secretos dedicado
c) Rotar secretos periódicamente
d) Tener un plan para revocar secretos comprometidos

### 9.3. Autenticación y Autorización

a) Usar frameworks/librerías probadas para autenticación (no implementar desde cero)
b) Hashear contraseñas con algoritmos seguros (bcrypt, Argon2) — nunca MD5 ni SHA1
c) Implementar rate limiting en endpoints de autenticación
d) Usar HTTPS obligatoriamente en producción

### 9.4. Protección de Datos

a) Sanitizar outputs para prevenir XSS
b) Usar consultas parametrizadas para prevenir inyección SQL
c) Validar tipos y formatos en cada capa de la aplicación
d) Implementar CORS de forma restrictiva

---

## 10. Entorno de Desarrollo

### 10.1. Scripts Claros

a) **`dev`:** Inicia todo lo necesario para desarrollo (servidor, watchers, hot-reload)
b) **`build`:** Compila todo para producción
c) **`start`:** Ejecuta el build de producción
d) Scripts auxiliares con prefijo descriptivo

### 10.2. Hot Reload

a) Usar watch mode del runtime/bundler para reinicio automático al guardar
b) Hot-reload de estilos en paralelo al servidor
c) Asegurar que los procesos hijos (watchers) no queden huérfanos al hacer Ctrl+C

### 10.3. Variables de Entorno

a) Valores por defecto para desarrollo que permitan arrancar sin configuración
b) Archivo de configuración local en `.gitignore`
c) Archivo de ejemplo **versionado** como referencia (`.env.example` o equivalente)
d) Valores sensibles **NUNCA** en el repositorio

### 10.4. Puerto Dedicado

a) Puerto fijo para el proyecto (no usar el default del framework)
b) Documentar el puerto en la configuración de ejemplo y en las especificaciones
c) Configurable vía variable de entorno

---

## 11. Build y Despliegue

### 11.1. Build Reproducible

a) El build debe ser determinístico: misma entrada → misma salida
b) Documentar los comandos exactos de build
c) El build debe funcionar en un entorno limpio (sin dependencias implícitas del sistema)

### 11.2. Artefactos no Versionados

a) Directorios de build en `.gitignore`
b) Los artefactos compilados se regeneran siempre, no se versionan

### 11.3. Entorno de Producción

a) Distinguir claramente entre desarrollo y producción mediante variables de entorno
b) El build de producción debe ser diferente del de desarrollo:
   - Minificado y optimizado
   - Sin source maps
   - Con headers de caché apropiados
   - Sin herramientas de desarrollo

### 11.4. Verificación Pre-Deploy

Antes de cada despliegue, verificar:

a) El build completa sin errores
b) El servidor arranca correctamente
c) Los endpoints principales responden correctamente
d) Los assets estáticos se sirven correctamente

---

## 12. CI/CD y Automatización

### 12.1. Pipeline Mínimo

Todo proyecto debe tener un pipeline automatizado que ejecute, como mínimo:

1. **Lint:** Verificación de estilo y formato
2. **Type Check:** Verificación de tipos (si aplica)
3. **Tests:** Suite de tests automatizados
4. **Build:** Compilación del proyecto

### 12.2. Ramas y Despliegue

a) Definir una estrategia de ramas clara (trunk-based, gitflow, etc.)
b) Los merges a la rama principal solo deben ocurrir si el pipeline pasa
c) El despliegue a producción debe ser automático o de un solo comando

### 12.3. Hooks de Control de Calidad

a) Usar hooks pre-commit para formateo automático y linting
b) Usar hooks pre-push para ejecutar tests rápidos
c) Automatizar todo lo que sea repetible; no confiar en la disciplina humana

---

## 13. Monitoreo y Observabilidad

### 13.1. Logging en Producción

a) Logs estructurados (JSON) con campos consistentes
b) Niveles claros: DEBUG, INFO, WARN, ERROR, FATAL
c) No loggear información sensible (contraseñas, tokens, datos personales)

### 13.2. Health Checks

a) Exponer un endpoint de salud que verifique dependencias críticas
b) Incluir verificación de conectividad a base de datos y servicios externos
c) Usar para alertas automatizadas y balanceo de carga

### 13.3. Métricas y Alertas

a) Monitorear: latencia de respuesta, tasa de error, uso de recursos
b) Configurar alertas para umbrales críticos
c) Tener un dashboard accesible para el equipo

---

## 14. Rendimiento

### 14.1. Principios Generales

a) Medir antes de optimizar — no optimizar prematuramente
b) Establecer benchmarks y métricas de rendimiento desde el inicio
c) Documentar los límites de rendimiento aceptables en las especificaciones

### 14.2. Estrategias Comunes

a) **Caché:** Implementar en capas apropiadas (HTTP, aplicación, base de datos)
b) **Paginación:** Nunca retornar colecciones completas sin límite
c) **Índices:** Asegurar que las consultas frecuentes estén indexadas
d) **Compresión:** Habilitar compresión para respuestas HTTP
e) **Lazy loading:** Cargar recursos solo cuando se necesitan

### 14.3. Prevención de Degradación

a) Rate limiting para proteger contra abuso
b) Timeouts en llamadas a servicios externos
c) Circuit breakers para dependencias externas
d) Connection pooling para bases de datos

---

## 15. Verificación y Pruebas

### 15.1. Estrategia de Testing

Definir qué se prueba y con qué nivel de detalle:

| Nivel | Qué cubre | Velocidad | Cantidad |
|-------|-----------|-----------|----------|
| **Unit** | Funciones/clases aisladas | Rápido | Muchos |
| **Integration** | Interacción entre módulos | Medio | Moderados |
| **E2E** | Flujos completos de usuario | Lento | Pocos |
| **Security** | Vulnerabilidades y superficie de ataque | Variable | Moderados |
| **Performance** | Latencia, throughput, uso de recursos | Variable | Pocos |

### 15.2. Verificar Después de Cada Cambio

a) Compilar sin errores
b) Ejecutar suite de tests
c) Probar endpoints/rutas principales
d) Verificar status codes correctos

### 15.3. Comandos de Verificación

Incluir en la documentación comandos exactos para verificar:

- Compilación
- Tests
- Respuesta del servidor
- Manejo de errores (rutas inexistentes, errores 500)

### 15.4. Lo que No se Compila, No Existe

Si no compila, no funciona. Si no se puede verificar, no está completo. El proyecto debe poder verificarse con comandos simples ejecutables por cualquier desarrollador.

### 15.5. Testing de Seguridad

Incluir en la estrategia de testing:

a) **Análisis estático:** Herramientas SAST para detectar vulnerabilidades en el código fuente
b) **Dependencias:** Escaneo automático de dependencias con vulnerabilidades conocidas (e.g., `npm audit`, `pip audit`)
c) **Autenticación:** Verificar que endpoints protegidos rechazan accesos no autorizados
d) **Inyección:** Probar inputs maliciosos en formularios y parámetros de URL
e) **Secretos:** Verificar que no hay secretos hardcodeados en el código o en el historial de git

> **Nota:** Referenciar la sección [9. Seguridad](#9-seguridad) para los principios que estas pruebas deben validar.

### 15.6. Testing de Rendimiento

Incluir en la estrategia de testing:

a) **Benchmarks base:** Establecer métricas de referencia (latencia, throughput) desde las primeras versiones
b) **Carga:** Probar con volúmenes de datos y tráfico esperados en producción
c) **Degradación:** Verificar comportamiento bajo estrés (timeouts, memory leaks, connection pooling)
d) **Regresión:** Comparar métricas contra benchmarks base después de cada cambio significativo

> **Nota:** Referenciar la sección [14. Rendimiento](#14-rendimiento) para las estrategias que estas pruebas deben cubrir.

---

## 16. Checklist de Auditoría y Consultas

Antes de dar por completada una fase, un feature o el proyecto completo, ejecutar este checklist. Cada punto es una pregunta que debe tener una respuesta documentada.

### 16.1. Coherencia y Calidad del Código

- [ ] ¿El código es coherente con las convenciones establecidas en las especificaciones?
- [ ] ¿Se realizó una auditoría del código antes de merge?
- [ ] ¿Se aprovechan los beneficios nativos de cada tecnología instalada?
- [ ] ¿Se aplicaron las correcciones necesarias identificadas en la auditoría?

### 16.2. Flujo de Procesos y Funcionalidad

- [ ] ¿El flujo de procesos está documentado y es verificable?
- [ ] ¿Todos los CRUDs son funcionales y completos (Create, Read, Update, Delete)?
- [ ] ¿Se verificó el aislamiento entre módulos/componentes?
- [ ] ¿Se verificó que no existen problemas de N+1 en las consultas a datos?
- [ ] ¿Se escribieron tests completos para cada flujo?

### 16.3. Base de Datos y Persistencia

- [ ] ¿Las tablas de la base de datos están alineadas con el modelo de datos de las especificaciones?
- [ ] ¿En sistemas multi-tenant, las tablas necesarias se crean en cada tenant?
- [ ] ¿Las migraciones son reversibles y están versionadas?

### 16.4. Seguridad y Red

- [ ] ¿Se revisaron los puertos abiertos del servidor?
- [ ] ¿Se verificó el nivel de seguridad del sistema?
- [ ] ¿Los endpoints públicos tienen autenticación y autorización?
- [ ] ¿Los datos sensibles están protegidos en tránsito y en reposo?

### 16.5. Interfaz y Experiencia de Usuario

- [ ] ¿Los formularios están distribuidos de manera coherente, responsive y adaptada a cada dispositivo?
- [ ] ¿Se realizó simulación de navegador para verificar la experiencia?
- [ ] ¿Se ejecutaron pruebas de navegador en múltiples dispositivos y resoluciones?

### 16.6. Documentación, Proceso y Skills

- [ ] ¿Se aplicó la metodología spec-driven (el "qué" en `/spec` y el "cómo" en `/plan`)?
- [ ] ¿Se documentaron las especificaciones del plan de creación?
- [ ] ¿Se documentaron las especificaciones del plan de integración?
- [ ] ¿Se configuraron los skills/expertos en `/.skills` referenciando `/spec` y `/plan`?
- [ ] ¿Los skills están alineados con las especificaciones y no las contradicen?
- [ ] ¿Se generó un resumen de lo realizado?

### 16.7. Verificación Final

- [ ] ¿La aplicación inicia en segundo plano sin errores?
- [ ] ¿Los logs no muestran errores ni advertencias críticas?
- [ ] ¿Todos los tests pasan?
- [ ] ¿El build de producción funciona correctamente?

---

## 17. Accesibilidad

> **Nota:** Esta sección aplica principalmente a proyectos con interfaz de usuario (web, móvil, desktop).

### 17.1. Estándar Mínimo

a) Cumplir al menos WCAG 2.1 nivel AA
b) Toda interacción debe ser accesible por teclado
c) Todo contenido visual debe tener alternativa textual apropiada
d) El contraste de colores debe cumplir ratios mínimos (4.5:1 para texto normal)

### 17.2. Prácticas de Desarrollo

a) Usar semántica correcta en los elementos de interfaz
b) Probar con lectores de pantalla periódicamente
c) No depender exclusivamente del color para transmitir información
d) Asegurar que los formularios tengan labels asociados

### 17.3. Testing de Accesibilidad

a) Incluir tests automatizados de accesibilidad en el pipeline
b) Realizar pruebas manuales con lectores de pantalla al menos una vez por sprint
c) Documentar y trackear issues de accesibilidad como bugs prioritarios

---

## 18. Adopción y Migración

> **Nota:** Esta sección aplica a equipos que adoptan Spec-Driven en proyectos existentes.

### 18.1. Adopción Gradual

Spec-Driven no requiere un cambio radical. Adoptarlo de forma incremental:

1. **Empezar por una feature nueva o en desarrollo:** Aplicar el ciclo completo (spec → plan → skills) en un scope pequeño
2. **Documentar lo que ya existe:** Crear specs retrospectivas para componentes críticos del proyecto — no necesitan ser perfectas, solo útiles
3. **Evitar el Big Bang:** No intentar documentar todo el proyecto de golpe. Priorizar por riesgo y actividad reciente

### 18.2. Proyectos sin `/spec` ni `/plan`

Si el proyecto ya tiene código pero no sigue Spec-Driven:

a) Crear `/spec/` con al menos: stack, arquitectura, variables de entorno, flujos principales
b) Crear `/plan/` con al menos: fases pendientes, deuda técnica conocida, próximos features
c) A partir de ahí, todo trabajo nuevo sigue el ciclo completo
d) No refactorizar solo para "alinear con la spec" — hacerlo cuando se toque el componente por otras razones

### 18.3. Métricas de Adopción

Para medir si la metodología está funcionando:

a) **Reducción de retrabajo:** ¿Se están haciendo menos cambios después de implementar?
b) **Claridad de requisitos:** ¿Se reducen las preguntas durante la implementación?
c) **Velocidad de onboarding:** ¿Los nuevos miembros del equipo entienden el proyecto más rápido?
d) **Cobertura de specs:** ¿Qué porcentaje del proyecto tiene specs actualizadas?

---

## 19. Glosario

| Término | Definición |
|---------|------------|
| **Spec (especificación)** | Documento que describe *qué* debe hacer el sistema, sus restricciones, flujos y datos. Vive en `/spec`. |
| **Plan** | Documento que describe *cómo* se implementará el sistema, con fases, pasos y comandos. Vive en `/plan`. |
| **Skill** | Definición de un experto o agente que ejecuta, verifica o audita basándose en spec y plan. Vive en `/.skills`. |
| **Spec-Driven** | Metodología de desarrollo donde las especificaciones son el centro del proceso. Se escribe *qué* antes de *cómo*. |
| **Source of Truth** | Archivos designados como la fuente de verdad del proyecto (specs, plan, documentación). El código debe reflejarlos. |
| **Fase** | Un conjunto de pasos relacionados en el plan de implementación, ordenados por dependencia. |
| **Criterio de aceptación** | Condición verificable que debe cumplirse para considerar completa una feature o fase. |
| **Checklist de auditoría** | Lista de verificación que se ejecuta antes de dar por completada una fase o el proyecto, cubriendo calidad de código, funcionalidad, seguridad, documentación y verificación final. |
| **Reproducibilidad** | Capacidad de construir el proyecto desde cero siguiendo solo la documentación. |
| **Wrapper** | Capa de abstracción propia que envuelve una librería externa para adaptarla a las necesidades del proyecto. |

---

## 20. Guía para AGENTS.md

`AGENTS.md` es un archivo Markdown que actúa como **manual de instrucciones para asistentes de IA** (Cursor, Copilot, Claude, Codebuff, Windsurf, etc.). Cuando un agente de IA inicia trabajo en un proyecto, este archivo es su primera fuente de contexto.

> **Analogía:** Si `README.md` es el manual del usuario, `AGENTS.md` es el manual del operador.

### 20.1. Relación con Spec-Driven

`AGENTS.md` actúa como **puente operativo** entre los tres pilares y el agente de IA:

```
/spec/  →  qué hacer
         ↓
/plan/  →  cómo hacerlo
         ↓
AGENTS.md  →  instrucciones concretas para el agente de IA
         ↓
/.skills/ →  especializaciones por dominio
```

**Regla fundamental:** `AGENTS.md` nunca contradice `/spec` o `/plan`. Si hay conflicto, spec y plan tienen prioridad.

### 20.2. Ubicación en el Proyecto

```
/proyecto/
├── AGENTS.md              ← Archivo principal (raíz)
├── /spec/                 ← Especificaciones
│   ├── SPEC.md
│   └── auth-spec.md
├── /plan/                 ← Plan de implementación
│   ├── PLAN.md
│   └── auth-plan.md
├── /.skills/              ← Agentes especializados
│   ├── security-agent.md
│   └── testing-agent.md
├── README.md
└── ...
```

**Reglas de ubicación:**
- `AGENTS.md` siempre va en la **raíz** del proyecto
- En monorepos, se pueden agregar `AGENTS.md` adicionales en subdirectorios
- El `AGENTS.md` de raíz es el que tienen prioridad los agentes

### 20.3. Estructura Recomendada

La estructura de `AGENTS.md` debe seguir este orden:

```markdown
# AGENTS.md

## 1. Contexto del Proyecto
## 2. Fuente de Verdad
## 3. Comandos de Verificación
## 4. Convenciones de Código
## 5. Lo que NO se debe hacer
## 6. Flujo de Trabajo
## 7. Agentes por Fase
## 8. Checklists de Calidad
```

> **Por qué este orden:** Los agentes de IA leen de arriba a abajo. Lo más importante (contexto y restricciones) va primero.

### 20.4. Secciones Obligatorias

#### 20.4.1. Contexto del Proyecto

Descripción breve del proyecto, su stack tecnológico, y su arquitectura general.

```markdown
## Contexto del Proyecto

- **Nombre:** [Nombre del proyecto]
- **Stack:** [Tecnologías principales]
- **Arquitectura:** [Estilo arquitectónico — MVC, Clean Architecture, etc.]
- **Propósito:** [Qué resuelve el proyecto en una línea]
```

#### 20.4.2. Fuente de Verdad

Indicar explícitamente qué archivos son la fuente de verdad.

```markdown
## Fuente de Verdad

- `/spec/SPEC.md` → Especificación general del sistema
- `/plan/PLAN.md` → Plan de implementación
- `/.skills/` → Agentes especializados

**Nunca modificar** estos archivos a menos que se pida explícitamente.
Si el código difiere de la spec, corregir el código.
```

#### 20.4.3. Comandos de Verificación

Lista de comandos exactos que el agente debe ejecutar para validar cambios.

```markdown
## Comandos de Verificación

### Compilación
[comando de build]

### Tests
[comando de tests]

### Lint
[comando de lint]

### Type Check
[comando de typecheck]

### Iniciar servidor
[comando de dev]

**Siempre ejecutar estos comandos antes de declarar una tarea completa.**
```

#### 20.4.4. Convenciones de Código

```markdown
## Convenciones de Código

- **Lenguaje:** [Nombre del lenguaje]
- **Estilo:** [Formato — Prettier, Black, etc.]
- **Nomenclatura de archivos:** [kebab-case, snake_case, PascalCase]
- **Imports:** [Orden — estándar, terceros, internos]
- **Tipado:** [Estricto, nominal, structural]
- **Comentarios:** [Solo el por qué, nunca el qué]
```

#### 20.4.5. Lo que NO se debe hacer

Lista explícita de restricciones. Los agentes de IA responden mejor a restricciones claras que a sugerencias vagas.

```markdown
## Lo que NO se debe hacer

- ❌ No instalar dependencias nuevas sin justificación documentada en `/spec`
- ❌ No modificar archivos en `/spec/`, `/plan/` o `/.skills/` a menos que se pida
- ❌ No usar tipos `any` o equivalentes
- ❌ No hardcodear valores que debieran ser variables de entorno
- ❌ No cambiar la arquitectura sin actualizar la spec primero
- ❌ No ejecutar `git push` sin confirmación explícita del usuario
- ❌ No eliminar código existente sin verificar que no tiene dependencias
```

### 20.5. Secciones Opcionales

#### 20.5.1. Variables de Entorno

```markdown
## Variables de Entorno

| Variable | Requerida | Default | Descripción |
|----------|-----------|---------|-------------|
| `PORT` | No | `3000` | Puerto del servidor |
| `DATABASE_URL` | Sí | — | URL de conexión a la DB |
| `NODE_ENV` | No | `development` | Modo de ejecución |
```

#### 20.5.2. Estructura de Directorios

```markdown
## Estructura del Proyecto

/src
├── /handlers      → Controllers/Rutas
├── /services      → Lógica de negocio
├── /models        → Modelos de datos
├── /middleware     → Middlewares
├── /utils         → Utilidades
└── app.ts         → Entrypoint
```

#### 20.5.3. Patrones de Error

```markdown
## Manejo de Errores

- Todos los handlers deben tener try/catch
- Usar el tipo de error definido en `src/errors/`
- Responder con formato: `{ error: string, code: number, details?: any }`
- Loggear errores con `logger.error()` antes de responder
```

#### 20.5.4. Estrategia de Testing

```markdown
## Testing

- **Framework:** [Nombre]
- **Ubicación:** Los tests van junto a los archivos que testean (co-location)
- **Convención de nombre:** `[nombre].test.[ext]` o `[nombre].spec.[ext]`
- **Cobertura mínima:** [Porcentaje o criterio]
```

### 20.6. Plantilla Base

Copia esta plantilla y personalízala para tu proyecto:

```markdown
# AGENTS.md

> Este archivo contiene instrucciones para asistentes de IA que trabajan en
> este proyecto. Siempre consultar `/spec/` y `/plan/` para decisiones de
> diseño e implementación.

## Contexto del Proyecto

- **Nombre:** [Tu proyecto]
- **Stack:** [Tecnologías]
- **Arquitectura:** [Estilo]
- **Propósito:** [Qué resuelve]

## Fuente de Verdad

- `/spec/SPEC.md` → Qué hace el sistema
- `/plan/PLAN.md` → Cómo se implementa
- `/.skills/` → Agentes especializados
- `AGENTS.md` → Este archivo (instrucciones para el agente)

**Regla:** Si el código difiere de la spec, corregir el código.

## Comandos de Verificación

[comandos de build, test, lint, dev]

## Convenciones de Código

- **Lenguaje:** [Lenguaje]
- **Estilo:** [Formatter]
- **Archivos:** [Convención de nombre]
- **Imports:** [Orden]
- **Tipado:** [Estricto/No]

## Lo que NO se debe hacer

- ❌ No modificar `/spec/`, `/plan/` o `/.skills/` sin pedido explícito
- ❌ No instalar dependencias sin justificación
- ❌ No usar tipos débiles
- ❌ No hardcodear secretos
- ❌ No cambiar arquitectura sin actualizar spec
- ❌ No hacer push sin confirmación

## Variables de Entorno

[tabla de variables]

## Estructura del Proyecto

[mapa de directorios]

## Agentes por Fase

Cada agente en esta tabla corresponde a un archivo en `/.skills/`.

| Fase | Agente | Archivo en /.skills/ | Responsabilidad | Paralelizable |
|------|--------|---------------------|-----------------|---------------|
| Análisis | Analista | `analyst-agent.md` | Revisar spec | No |
| Planificación | Planificador | `planner-agent.md` | Crear plan | No |
| Implementación | Desarrollador | `dev-agent.md` | Escribir código | Sí (con Instalador) |
| Verificación | Tester | `tester-agent.md` | Ejecutar tests | Sí (con Linter) |
| Verificación | Linter | `linter-agent.md` | Verificar estilo | Sí (con Tester) |
| Auditoría | Auditor Seguridad | `security-agent.md` | Revisar vulnerabilidades | Sí (con Rendimiento) |
| Auditoría | Auditor Rendimiento | `perf-agent.md` | Revisar performance | Sí (con Seguridad) |
| Despliegue | Deployer | `deploy-agent.md` | Desplegar | No |
```

### 20.7. Cómo Referenciar /spec y /plan

El `AGENTS.md` debe indicar al agente **dónde buscar** las especificaciones y el plan, pero **no duplicar** su contenido.

**Reglas de Referencia:**

1. **Nunca copiar contenido** de `/spec` o `/plan` dentro de `AGENTS.md`
2. **Siempre usar rutas relativas** para referenciar archivos
3. **Indicar al agente** que lea los archivos relevantes antes de implementar

**Referencia Correcta:**
```markdown
1. Leer la especificación correspondiente en `/spec/[feature]-spec.md`
2. Revisar el plan en `/plan/[feature]-plan.md`
3. Implementar siguiendo el plan fase por fase
```

**Referencia Incorrecta:**
```markdown
## ❌ NO hacer esto:
## El sistema debe usar JWT con tokens de 24h de duración,
## refresh tokens de 7 días, y rate limiting de 100 req/min.
## (Esto debería estar en /spec, no en AGENTS.md)
```

### 20.8. Agentes por Fase del Ciclo

El `AGENTS.md` debe mapear qué agente ejecuta en cada fase del ciclo Spec-Driven. Cada agente tiene un **alcance definido**, **herramientas disponibles**, y **criterios de éxito**.

> **Paralelismo:** Algunos agentes pueden ejecutarse en paralelo cuando no tienen dependencias entre sí (ej: Linter y Tester en Fase 4).

#### Fase 1: Análisis y Especificación

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Analista** | Revisar que la spec sea completa | Lector de archivos, web search | Requisitos del usuario | Spec revisada | Todos los items de 2.2.1 cumplidos |
| **Validador** | Verificar criterios de validación | Lector de archivos | Spec | Checklist 2.2.1 completado | 6/6 criterios aprobados |

> **Escalamiento:** Si el Analista detecta requisitos ambiguos, escalar al usuario antes de continuar.

#### Fase 2: Planificación

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Planificador** | Crear/modificar el plan | Lector de archivos, editor | Spec validada | Plan con fases y checkboxes | Cada fase tiene dependencias claras |
| **Arquitecto** | Verificar coherencia técnica | Lector de archivos, code search | Spec + Plan | Plan revisado | Sin contradicciones con la spec |

> **Escalamiento:** Si el Planificador detecta que la spec es insuficiente, pedir revisión al Analista.

#### Fase 3: Implementación

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Desarrollador** | Escribir código según spec/plan | Editor, terminal, code search | Spec + Plan | Código funcional | Compila sin errores, cumple spec |
| **Instalador** | Gestionar dependencias | Terminal, package manager | Plan | Packages instalados | Dependencias justificadas en spec |

> **Escalamiento:** Si el Desarrollador detecta que algo de la spec no es implementable, escalar al Planificador.

#### Fase 4: Verificación

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Tester** | Ejecutar suite de tests | Terminal | Código + Spec | Tests pasando | 100% tests pasan |
| **Linter** | Verificar estilo y convenciones | Terminal | Código | Sin warnings | 0 warnings |
| **Tipador** | Verificar tipos | Terminal | Código | Sin errores de tipo | 0 errores |

> **Escalamiento:** Si el Tester encuentra bugs, escalar al Desarrollador.

#### Fase 5: Auditoría

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Auditor de seguridad** | Revisar vulnerabilidades | Lector de archivos, code search, terminal | Código | Reporte de seguridad | Sin vulnerabilidades críticas |
| **Auditor de rendimiento** | Revisar performance | Lector de archivos, terminal | Código | Métricas | Dentro de benchmarks aceptables |
| **Revisor final** | Checklist 16 completo | Lector de archivos | Todo | Checklist aprobado | Todos los items aprobados |

> **Escalamiento:** Si el Auditor detecta vulnerabilidades críticas, bloquear el despliegue hasta resolución.

#### Fase 6: Despliegue

| Agente | Rol | Herramientas | Inputs | Outputs | Criterio de Éxito |
|--------|-----|-------------|--------|---------|-------------------|
| **Build** | Ejecutar build de producción | Terminal | Código verificado | Artefacto | Build determinístico |
| **Deployer** | Desplegar | Terminal, git | Build verificado | Deploy exitoso | Health check pasa |

> **Escalamiento:** Si el Build falla, escalar al Desarrollador. Si el Deploy falla, escalar al equipo.

### 20.9. Reglas de Mantenimiento

| Evento | Acción |
|--------|--------|
| Cambio de stack tecnológico | Actualizar sección "Contexto del Proyecto" |
| Nuevo comando de verificación | Agregar a "Comandos de Verificación" |
| Nueva restricción | Agregar a "Lo que NO se debe hacer" |
| Nueva variable de entorno | Agregar a la tabla de variables |
| Cambio de arquitectura | Actualizar "Estructura del Proyecto" |
| Nuevo agente especializado | Actualizar "Agentes por Fase" |

**Quién Actualiza:**
- **Desarrolladores humanos:** Actualizan cuando hay cambios estructurales
- **Agentes de IA:** Pueden sugerir actualizaciones, pero no aplicarlas sin confirmación
- **Al actualizar:** Verificar que no contradiga `/spec` ni `/plan`

### 20.10. Checklist de Revisión de AGENTS.md

Antes de dar por completado un `AGENTS.md`, verificar:

**Contenido:**
- [ ] El contexto del proyecto es claro y actualizado
- [ ] La fuente de verdad está definida explícitamente
- [ ] Los comandos de verificación funcionan (probar cada uno)
- [ ] Las convenciones de código son consistentes con el proyecto
- [ ] Las restricciones son claras y sin ambigüedades

**Referencias:**
- [ ] `/spec/` se referencia correctamente (sin duplicar contenido)
- [ ] `/plan/` se referencia correctamente
- [ ] `/.skills/` se referencia correctamente
- [ ] No hay contradicciones entre AGENTS.md y los archivos de spec/plan

**Mantenimiento:**
- [ ] La versión está documentada
- [ ] Las variables de entorno están actualizadas
- [ ] La estructura de directorios refleja el estado actual
- [ ] Los agentes por fase cubren todo el ciclo Spec-Driven

**Usabilidad para Agentes:**
- [ ] Las instrucciones son accionables (comandos, no descripciones)
- [ ] Las restricciones son explícitas (❌ NO hacer X)
- [ ] El orden prioriza lo más importante primero
- [ ] No hay información redundante

---

*FIN DE LA GUÍA — v7.0 — Principios universales para crear proyectos mantenibles, seguros y robustos*
