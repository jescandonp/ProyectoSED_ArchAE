# Arquitectura de Referencia — SED_ARCHEA

> Estado: base arquitectónica aprobada para iniciar I1.
> Sistema: `SED_ARCHEA` — Plataforma de Arquitectura Empresarial SED Bogotá.
> Fecha base: 2026-05-18. Referencia: arquitectura SED/ESAL adaptada.

## 1. Visión General

`SED_ARCHEA` es una plataforma web empresarial interna para la OTI de la Secretaría de
Educación del Distrito. Su objetivo es proveer un catálogo centralizado de activos TI,
visualización de relaciones en grafo y bitácora de despliegues.

Construida bajo SDD Spec-Anchored: las especificaciones vivas son la fuente primaria de
verdad, y el código es un artefacto derivado de CONSTITUTION, ARCHITECTURE, TECNOLOGIAS,
specs técnicas y planes aprobados.

## 2. Coordenadas Canónicas

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
| Labels Neo4j | prefijo `EA_` |

## 3. Stack Tecnológico

- Frontend: Angular 20, TypeScript strict, PrimeNG 20, Tailwind CSS 3.4, MSAL Angular 3.x.
- Backend: Java 8, Spring Boot 2.7.18, Spring Security 5.7, Spring Data JPA 2.7, WAR en WebLogic 12.2.1.4.
- BD Operacional: Oracle Database 19c+.
- BD Grafo: Neo4j 4.4 Community Edition.
- Auth: Azure AD / Office 365 en perfil weblogic. HTTP Basic en local-dev.

## 4. Arquitectura por Capas

```text
Usuario interno SED
   |
   | HTTPS
   v
Angular 20 SPA
   |
   | REST / Authorization: Bearer JWT / Basic solo local-dev
   v
Spring Boot API — WAR en WebLogic
   |
   |-- Spring Data JPA --> Oracle 19c (datos operacionales)
   |-- Spring Data Neo4j --> Neo4j 4.4 (grafo de activos)
```

## 5. Backend — Estructura de Paquetes

```text
co.gov.bogota.sed.archea/
├── config/         SecurityConfig, DevSecurityConfig, Neo4jConfig, AuditConfig, OpenApiConfig
├── domain/
│   ├── entity/     JPA entities (Oracle)
│   ├── graph/      Neo4j node/relationship entities
│   ├── enums/      Ambiente, TipoEvento
│   └── repository/ Spring Data JPA + Neo4j repositories
├── application/
│   ├── service/    Lógica de negocio, escritura dual Oracle+Neo4j
│   ├── dto/        DTOs de entrada y salida
│   └── mapper/     MapStruct mappers
└── web/
    ├── controller/ REST controllers
    └── exception/  GlobalExceptionHandler
```

## 6. Frontend — Estructura de Módulos

```text
src/app/
├── core/           auth/, services/, interceptors/
├── shared/         layout/, components/, pipes/
└── features/
    ├── dashboard/
    ├── catalogo/   M1 — Catálogo de Activos
    ├── grafo/      M1 — Visualización de Grafo (Cytoscape.js)
    ├── ingesta/    M2 — Encuesta + Importación Excel
    ├── bitacora/   M3 — Bitácora de Despliegues
    └── administracion/
```

## 7. Sincronización Oracle ↔ Neo4j

Oracle es la fuente de verdad. El grafo Neo4j es derivado.

Escritura: Oracle primero, luego Neo4j en el mismo método de servicio.
En caso de falla Neo4j: Oracle se confirma y la sincronización se reintenta.
El grafo nunca escribe en Oracle.

## 8. Seguridad

Mismo patrón que SED_ESAL:
- `local-dev`: HTTP Basic, usuarios de prueba, Swagger activo.
- `weblogic`: Bearer JWT Azure AD, OAuth2 Resource Server, HTTPS obligatorio.

Roles: `ARCHEA_ADMINISTRADOR`, `ARCHEA_LIDER_TECNICO`, `ARCHEA_CONSULTOR`.

## 9. Regla de Evolución

Cambios en esta arquitectura deben reflejarse primero aquí, luego en TECNOLOGIAS.md,
luego en specs y planes afectados, antes de tocar código.
