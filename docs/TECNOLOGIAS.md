# TECNOLOGIAS — SED_ARCHEA

> Estado: base técnica aprobada para iniciar I1.
> Fuente rectora: CONSTITUTION.md y ARCHITECTURE.md.

## Backend

| Tecnología | Versión canónica | Regla |
|---|---|---|
| Java runtime | Oracle JDK 8 | Obligatorio por WebLogic 12.2.1.4 |
| Spring Boot | 2.7.18 | No subir a 3.x |
| Spring Security | 5.7.x | Compatible con Boot 2.7.x |
| Spring OAuth2 Resource Server | 2.7.x | JWT Azure AD en perfil weblogic |
| Spring Data JPA | 2.7.x | Persistencia Oracle |
| Spring Data Neo4j | 6.3.x | Compatible con Java 8 y Spring Boot 2.7.x |
| Neo4j Java Driver | 4.4.x | Bolt driver compatible Java 8 |
| Hibernate | 5.6.x | ORM Oracle |
| SpringDoc OpenAPI | 1.7.0 | Swagger siempre activo |
| Oracle JDBC | ojdbc8 19.x | Compatible JDK 8 + Oracle 19c |
| Lombok | 1.18.x | Reducir boilerplate |
| Maven | 3.9.x | Build |
| Packaging | WAR | WebLogic |

## Frontend

| Tecnología | Versión canónica | Regla |
|---|---|---|
| Angular | 20.x | Standalone components, strict mode |
| TypeScript | 5.8.x | Strict mode obligatorio |
| PrimeNG | 20.x | Librería primaria de componentes |
| Tailwind CSS | 3.4.x | Utilidades de layout |
| RxJS | 7.8.x | Observables Angular |
| Cytoscape.js | 3.x | Visualización de grafo (MIT) |
| MSAL Angular | 3.x | Auth Azure AD |
| MSAL Browser | 3.x | OAuth2/OIDC con PKCE |
| Node.js | 20 LTS | Solo build/desarrollo |

## Base de Datos

| Tecnología | Versión |
|---|---|
| Oracle Database | 19c+ |
| Dialecto Hibernate | Oracle12cDialect |
| Esquema | SED_ARCHEA |
| Prefijo tablas | ARCHEA_ |
| Neo4j | 4.4 Community |
| Labels Neo4j | prefijo EA_ |

## Infraestructura

| Área | Decisión |
|---|---|
| Servidor objetivo | Oracle WebLogic 12.2.1.4 |
| Auth local | HTTP Basic perfil local-dev |
| Auth servidor | Azure AD JWT + MSAL |
| Despliegue | On-premise SED |
| Docker/nube | Fuera de arquitectura |
