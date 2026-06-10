# Guía de Lineamientos y Buenas Prácticas para Creación de Proyectos

**Versión:** 5.0

Este documento establece los principios, estándares y procesos que deben seguirse al crear y mantener un proyecto de software. Las recomendaciones aquí descritas aplican a cualquier lenguaje, framework o plataforma. El enfoque está en **cómo pensar y planificar**, no en qué herramientas específicas usar.

---

## Tabla de Contenidos

1. [Filosofía General](#1-filosofía-general)
2. [Metodología Spec-Driven](#2-metodología-spec-driven)
3. [Fase 0: Especificación y Plan](#3-fase-0-especificación-y-plan)
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

---

## 1. Filosofía General

### 1.1. Primero el Plan, Después el Código

Nunca escribir código sin haber definido antes las especificaciones y el plan de implementación. El plan debe tener checkboxes y fases claras. Esto aplica al proyecto completo y a cada feature individual.

> **Ejemplo práctico:** Antes de implementar un sistema de autenticación, documenta: qué método se usará (tokens, sesiones, OAuth), qué endpoints existirán, qué datos se almacenan, y qué flujos de error se manejan.

### 1.2. Decisiones por Escrito

Toda decisión técnica importante debe quedar documentada. Si no está escrita, no existe. Esto incluye:

- Por qué se eligió la tecnología X sobre Y
- Qué alternativas se descartaron y por qué
- Cuáles son las restricciones y trade-offs aceptados

### 1.3. Canonical Source of Truth

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

Spec-Driven (Desarrollo Guiado por Especificaciones) es una metodología donde las especificaciones son el centro del desarrollo. En lugar de empezar con código, se comienza con especificaciones claras y verificables.

### 2.1. ¿Para qué sirve?

a) **Definir antes de implementar** — Reduce ambigüedad antes de escribir código
b) **Verificación objetiva** — Las specs se convierten en criterios de aceptación
c) **Comunicación clara** — Todo el equipo comparte la misma interpretación
d) **Reducir retrabajo** — Menos cambios costosos después de implementar

### 2.2. Ciclo Spec-Driven

El ciclo se repite para cada feature o componente:

1. **Escribir especificaciones** — Describir *qué* debe hacer el sistema (el "qué" vive en `/spec`)
2. **Crear el plan** — Describir *cómo* se implementará (el "cómo" vive en `/plan`)
3. **Validar** — Revisar que las specs sean correctas, completas y consistentes
4. **Implementar** — Escribir código que cumpla las especificaciones
5. **Verificar** — Confirmar que la implementación cumple las specs (tests, auditoría, revisión)
6. **Iterar** — Si la verificación falla, corregir la implementación o ajustar las specs

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

---

## 3. Fase 0: Especificación y Plan

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

### 4.4. Sources of Truth para Versiones

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

### 16.6. Documentación y Proceso

- [ ] ¿Se aplicó la metodología spec-driven (el "qué" en `/spec` y el "cómo" en `/plan`)?
- [ ] ¿Se documentaron las especificaciones del plan de creación?
- [ ] ¿Se documentaron las especificaciones del plan de integración?
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

*FIN DE LA GUÍA — v5.0 — Principios universales para crear proyectos mantenibles, seguros y robustos*
