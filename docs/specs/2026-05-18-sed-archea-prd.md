# PRD — SED_ARCHEA: Plataforma de Arquitectura Empresarial

> Estado: draft  
> Fecha: 2026-05-18  
> Sistema: `SED_ARCHEA`  
> Metodología: SDD Spec-Anchored (incrementos iterables por MVP)  
> Etiqueta: `ready-for-agent`

---

## Problem Statement

La Secretaría de Educación del Distrito (SED) de Bogotá no cuenta con un repositorio centralizado y actualizado de sus activos tecnológicos. Los líderes técnicos y de servicio no tienen visibilidad unificada de qué sistemas de información existen, en qué servidores residen, a qué bases de datos conectan, ni cómo se relacionan entre sí a través de los distintos ambientes (desarrollo, pruebas, producción).

Ante incidentes, cambios de infraestructura o auditorías, el equipo de la Oficina de Tecnologías de la Información (OTI) debe recopilar esta información manualmente desde múltiples fuentes dispersas, lo que genera demoras, inconsistencias y riesgos operativos. No existe una fuente de verdad que permita responder preguntas de correlación como: "¿qué aplicaciones corren sobre el servidor X?", "¿qué sistemas dependen de la base de datos Y?" o "¿cuál es el historial de despliegues del sistema Z?".

---

## Solution

Construir la **Plataforma SED_ARCHEA**, un repositorio centralizado on-premise de arquitectura empresarial que actúa como fuente de verdad de los activos TI de la SED. La plataforma combina una base de datos relacional (Oracle 19c) para datos operacionales con una base de datos de grafos (Neo4j 4.4 Community) para modelar y visualizar las relaciones entre activos.

El MVP se compone de cuatro módulos entregados en incrementos:

- **M0** — Plantilla Excel de recolección de información base (entregada en paralelo al desarrollo).
- **M1** — Catálogo de Activos con visualización de grafo.
- **M2** — Módulo de Ingesta / Encuesta digital con importación desde M0.
- **M3** — Bitácora de Despliegues (hoja de vida del activo).

A futuro, la plataforma operará como hub de referencia: otros sistemas de la SED podrán consultarla para resolver dependencias de infraestructura. La incorporación de Inteligencia Artificial Agéntica queda explícitamente fuera del MVP.

---

## User Stories

### Acceso y autenticación

1. Como usuario interno de la SED, quiero autenticarme con mi cuenta institucional de Office 365, para no tener que gestionar credenciales adicionales.
2. Como administrador de la OTI, quiero que los roles de acceso se gestionen desde Azure Active Directory, para centralizar la administración de permisos.
3. Como usuario con rol de solo lectura, quiero poder navegar el catálogo y el grafo sin poder modificar datos, para consultar información sin riesgo de alterarla.

### Catálogo de Activos — Vista de Negocio

4. Como líder de servicio, quiero ver un listado de todos los sistemas de información de mi dependencia, para conocer el portafolio tecnológico a mi cargo.
5. Como líder de servicio, quiero filtrar los sistemas por dependencia, líder técnico o estado, para encontrar rápidamente el sistema que necesito.
6. Como líder de servicio, quiero ver el nombre del sistema, la dependencia responsable, el líder de servicio y el líder técnico asignado, para tener la vista de negocio sin entrar al detalle técnico.
7. Como director de la OTI, quiero exportar el catálogo en formato Excel, para incluirlo en informes de gestión o presentaciones.

### Catálogo de Activos — Vista Técnica

8. Como arquitecto TI, quiero ver para cada sistema los datos técnicos por ambiente (DEV, QA, PROD) incluyendo DNS, servidor de aplicaciones, versión del servidor y datos de base de datos, para tener el detalle técnico completo en un solo lugar.
9. Como líder técnico, quiero crear un nuevo activo en el catálogo ingresando sus datos de identificación y detalle técnico por ambiente, para registrar un sistema nuevo en la plataforma.
10. Como líder técnico, quiero editar los datos técnicos de un activo existente, para mantener el inventario actualizado ante cambios de infraestructura.
11. Como líder técnico, quiero registrar la App Registration de Office 365 asociada a un sistema, para documentar la integración con el tenant institucional de Azure AD.
12. Como arquitecto TI, quiero filtrar activos por nombre de servidor, para saber qué sistemas comparten una misma máquina.
13. Como arquitecto TI, quiero filtrar activos por nombre de base de datos, para identificar todos los sistemas que consumen una misma BD.
14. Como arquitecto TI, quiero filtrar activos por ambiente (DEV / QA / PROD), para ver el estado de cada entorno de manera independiente.
15. Como líder técnico, quiero buscar un sistema por nombre o por DNS, para encontrarlo rápidamente entre todos los activos registrados.

### Visualización de Grafo

16. Como arquitecto TI, quiero ver una representación visual en grafo de los activos y sus relaciones (Sistema → Servidor → Base de Datos → Dependencia), para entender el mapa de dependencias de la infraestructura.
17. Como arquitecto TI, quiero hacer clic en un nodo del grafo para ver el detalle del activo, para navegar desde la vista global al detalle sin cambiar de pantalla.
18. Como arquitecto TI, quiero expandir o colapsar nodos del grafo para explorar relaciones específicas, para no saturarme de información cuando solo necesito ver una parte del grafo.
19. Como arquitecto TI, quiero filtrar el grafo por tipo de nodo (solo servidores, solo sistemas, solo BDs), para enfocarme en una capa de la arquitectura.
20. Como arquitecto TI, quiero que al seleccionar un servidor en el grafo se resalten todos los sistemas que corren sobre él, para responder de inmediato la pregunta "¿qué hay en este servidor?".
21. Como arquitecto TI, quiero que el grafo refleje el ambiente que estoy consultando (DEV / QA / PROD), para no mezclar relaciones de distintos entornos.

### Módulo de Ingesta / Encuesta (M2)

22. Como líder técnico, quiero diligenciar un formulario digital dentro de la plataforma para registrar los datos de un sistema nuevo, para no depender del archivo Excel después de la fase inicial.
23. Como administrador de la OTI, quiero importar el archivo Excel M0 diligenciado por los líderes técnicos, para poblar el catálogo inicial de activos sin ingresarlos uno a uno.
24. Como administrador de la OTI, quiero que la importación del Excel me muestre un resumen de registros procesados, duplicados omitidos y errores encontrados, para validar la calidad de los datos importados.
25. Como administrador de la OTI, quiero que los registros importados del Excel sean revisables antes de confirmarse en el catálogo, para evitar poblar el grafo con datos incorrectos.

### Bitácora de Despliegues (M3)

26. Como líder técnico, quiero registrar un nuevo evento en la bitácora de un activo (despliegue, cambio de versión, incidente, mantenimiento), para mantener la hoja de vida del sistema actualizada.
27. Como líder técnico, quiero ver el historial cronológico de eventos de un activo ordenado de más reciente a más antiguo, para revisar qué cambios se han hecho sobre un sistema.
28. Como auditor TI, quiero filtrar la bitácora por tipo de evento, rango de fechas o responsable, para encontrar eventos específicos en el historial.
29. Como arquitecto TI, quiero ver en el nodo del grafo un indicador del último evento de despliegue registrado, para saber cuándo fue la última actividad sobre un activo sin entrar al detalle.
30. Como líder técnico, quiero adjuntar notas o referencias a cada evento de bitácora, para dejar contexto del cambio realizado.

### Administración

31. Como administrador de la OTI, quiero gestionar el catálogo de dependencias disponibles para asignar a los sistemas, para mantener una lista controlada de áreas.
32. Como administrador de la OTI, quiero ver un log de auditoría de quién creó o modificó cada activo y cuándo, para tener trazabilidad de los cambios en el inventario.
33. Como administrador de la OTI, quiero gestionar los tipos de eventos disponibles en la bitácora (despliegue, rollback, mantenimiento, incidente, etc.), para controlar el vocabulario de eventos.

---

## Implementation Decisions

### Stack tecnológico (heredado de referencia SED_ESAL)

- **Frontend:** Angular 20, TypeScript strict, PrimeNG 20, Tailwind CSS 3.4, MSAL Angular 3.x
- **Backend:** Java 8, Spring Boot 2.7.18, Spring Security 5.7, Spring Data JPA, WAR desplegado en Oracle WebLogic 12.2.1.4
- **BD Operacional:** Oracle Database 19c+ — auditoría, eventos de bitácora, respuestas de encuesta, configuración de listas
- **BD Grafo:** Neo4j 4.4 Community Edition — nodos de activos y relaciones entre ellos; driver Bolt 4.x compatible con Java 8
- **Auth:** Azure AD / Office 365, mismo patrón MSAL + OAuth2 Resource Server de SED_ESAL

### Coordenadas canónicas

| Coordenada | Valor |
|---|---|
| Sistema | `SED_ARCHEA` |
| Backend | `sed-archea-backend` |
| Frontend | `sed-archea-angular` |
| WAR | `sed-archea-backend.war` |
| Contexto WebLogic | `/sed-archea` |
| Paquete Java base | `co.gov.bogota.sed.archea` |
| Esquema Oracle | `SED_ARCHEA` |
| Prefijo Oracle | `ARCHEA_` |
| Labels Neo4j | prefijo `EA_` (ej: `EA_Sistema`, `EA_Servidor`) |

### Modelo de dominio del grafo (Neo4j)

Tipos de nodo:
- `EA_Sistema` — sistema de información (nombre, dependencia, ambiente)
- `EA_Servidor` — servidor físico o virtual (nombre, versión, SO)
- `EA_BaseDatos` — instancia de BD (nombre servidor, nombre BD, tipo auth)
- `EA_AppRegistration` — registro en Azure AD (nombre, client ID)
- `EA_Dependencia` — área/dependencia de la SED
- `EA_Ambiente` — enum: DEV, QA, PROD

Tipos de relación:
- `CORRE_EN` — Sistema → Servidor (por Ambiente)
- `USA_BD` — Sistema → BaseDatos (por Ambiente)
- `TIENE_APP_REG` — Sistema → AppRegistration
- `PERTENECE_A` — Sistema → Dependencia
- `HOSPEDA_BD` — Servidor → BaseDatos

### Modelo operacional (Oracle)

Tablas principales:
- `ARCHEA_ACTIVO` — datos maestros del sistema (espeja nodo EA_Sistema)
- `ARCHEA_ACTIVO_AMBIENTE` — detalle técnico por ambiente (capa app + capa BD + app registration)
- `ARCHEA_EVENTO_BITACORA` — eventos de hoja de vida del activo
- `ARCHEA_TIPO_EVENTO` — catálogo de tipos de evento
- `ARCHEA_DEPENDENCIA` — catálogo de dependencias
- `ARCHEA_AUDITORIA` — trazas operativas (quién, cuándo, acción, activo)

### Sincronización Oracle ↔ Neo4j

- Escritura dual: toda operación de creación/actualización de activos escribe primero en Oracle (fuente de verdad operacional) y luego actualiza el grafo Neo4j en la misma transacción de servicio.
- En caso de falla de Neo4j, la operación Oracle se completa y se encola la sincronización al grafo para reintento.
- El grafo es siempre derivado de Oracle; Oracle nunca es derivado del grafo.

### Arquitectura por capas (Backend)

```
web/controller/       → DTOs, endpoints REST, roles
application/service/  → lógica de negocio, escritura dual Oracle+Neo4j
domain/entity/        → JPA entities Oracle
domain/graph/         → Neo4j node/relationship entities (Spring Data Neo4j)
infrastructure/neo4j/ → Neo4jConfig, GraphRepository
config/               → SecurityConfig, DevSecurityConfig, AuditConfig
```

### Roles de acceso

| Rol Azure AD | Rol aplicativo | Capacidades |
|---|---|---|
| `ARCHEA_ADMINISTRADOR` | ADMINISTRADOR | CRUD completo, importación Excel, gestión catálogos, auditoría |
| `ARCHEA_LIDER_TECNICO` | LIDER_TECNICO | Crear/editar activos a su cargo, registrar eventos bitácora |
| `ARCHEA_CONSULTOR` | CONSULTOR | Solo lectura: catálogo, grafo, bitácora |

### Módulo M0 (entregado)

Plantilla Excel generada con 5 hojas: PORTADA, CATÁLOGO (vista negocio), DESARROLLO, PRUEBAS, PRODUCCIÓN. Archivo: `M0_Inventario_Sistemas_SED.xlsx`. Sirve como insumo para la importación masiva del módulo M2.

### Visualización de grafo (Frontend)

Usar **Cytoscape.js** (licencia MIT, compatible con Angular) para renderizar el grafo en el frontend. Los datos del grafo se sirven desde el backend como una respuesta JSON con lista de nodos y aristas; el backend ejecuta Cypher queries en Neo4j y transforma el resultado al formato que consume Cytoscape.

---

## Testing Decisions

**Principio general:** Los tests validan comportamiento observable desde fuera del módulo, no detalles de implementación. No se prueban métodos privados ni estructuras internas de repositorios.

**Módulos a testear:**

| Módulo | Tipo de test | Qué probar |
|---|---|---|
| `ActivoService` | Unitario (Mockito) | Creación de activo, escritura dual Oracle+Neo4j, validaciones de negocio |
| `ImportacionExcelService` | Unitario | Parsing de Excel M0, detección de duplicados, mapeo a entidades |
| `GraphQueryService` | Integración (Neo4j embebido) | Consultas de correlación: activos por servidor, activos por BD |
| `BitacoraService` | Unitario | Registro de eventos, filtros de historial |
| `ActivoController` | Integración (MockMvc) | Endpoints REST, códigos HTTP, validación de roles |

**Prior art:** seguir el mismo patrón de tests de `SED_ESAL` (MockMvc + Mockito + H2 para Oracle en tests, Neo4j Test Harness para grafo).

---

## Out of Scope

- **Inteligencia Artificial / IA Agéntica** — explícitamente diferida a fases posteriores.
- **Integración automática con otros sistemas SED** — el MVP es ingesta manual y vía Excel; la lectura desde sistemas externos (p.ej. Active Directory, CMDB) queda fuera.
- **Clustering Neo4j** — Neo4j Community Edition es single-instance; alta disponibilidad del grafo queda fuera del MVP.
- **API pública de consulta para otros sistemas** — el hub de referencia (otros sistemas consultando SED_ARCHEA) es una capacidad futura, no del MVP.
- **QR / código de verificación en reportes** — fuera del alcance inicial.
- **Gestión de usuarios en la plataforma** — los usuarios se gestionan exclusivamente desde Azure AD.
- **Despliegue en nube** — la plataforma es estrictamente on-premise sobre WebLogic SED.
- **Módulo de reportes avanzados / dashboards de métricas** — el MVP ofrece filtros y export básico; dashboards analíticos son una fase posterior.

---

## Further Notes

- El proyecto sigue **SDD Spec-Anchored**: las especificaciones son la fuente primaria de verdad. Cambios técnicos deben reflejarse primero en ARCHITECTURE.md y TECNOLOGIAS.md antes de tocar código.
- La restricción **Java 8** (impuesta por WebLogic 12.2.1.4) limita el driver de Neo4j a la versión Bolt 4.x. No usar Spring Data Neo4j 7+ (requiere Java 11+); usar Spring Data Neo4j 6.3.x que soporta Java 8.
- El campo **DNS** en los ambientes puede apuntar a una URL interna de la SED o a un hostname de red privada; no debe exponerse en logs ni en mensajes de error al usuario final.
- La **App Registration de Office 365** capturada en el catálogo es solo el nombre de referencia para documentación; no se almacenan client secrets ni tokens en la plataforma.
- La plantilla **M0** ya fue generada y está disponible en `M0_Inventario_Sistemas_SED.xlsx` para distribución inmediata al equipo de líderes técnicos.
