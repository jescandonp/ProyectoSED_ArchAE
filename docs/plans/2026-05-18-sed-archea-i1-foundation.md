# SED_ARCHEA I1 — Fundación: Estructura SDD + Scaffolding Dual-DB

> **Para agentes:** USA superpowers:subagent-driven-development o superpowers:executing-plans para ejecutar tarea a tarea. Pasos marcados con checkbox (`- [ ]`).

**Goal:** Establecer la estructura SDD completa del proyecto y el scaffolding funcional del backend (Spring Boot 2.7 + Oracle + Neo4j) y frontend (Angular 20), con seguridad configurada en ambos perfiles (`local-dev` / `weblogic`), lista para recibir las features de M1, M2 y M3.

**Architecture:** Dual-DB: Oracle 19c (datos operacionales vía Spring Data JPA) + Neo4j 4.4 Community (grafo de activos vía Spring Data Neo4j 6.3). Un solo WAR desplegado en WebLogic 12.2.1.4. Frontend Angular 20 SPA con MSAL para auth institucional.

**Tech Stack:** Java 8 · Spring Boot 2.7.18 · Spring Data Neo4j 6.3.x · Neo4j Java Driver 4.4.x · Oracle JDBC ojdbc8 19.x · Angular 20 · PrimeNG 20 · Tailwind 3.4 · MSAL Angular 3.x · WebLogic 12.2.1.4

**SDD Level:** Spec-Anchored — las specs son la fuente de verdad. Cambiar spec antes de cambiar código. Jerarquía: `CONSTITUTION.md` → `ARCHITECTURE.md` → `TECNOLOGIAS.md` → spec por incremento → plan → código.

---

## Mapa de archivos del incremento I1

### Raíz del proyecto
```
ProyectoSEDArch/
├── docs/
│   ├── CONSTITUTION.md
│   ├── ARCHITECTURE.md
│   ├── TECNOLOGIAS.md
│   └── specs/
│       └── I1-foundation-spec.md
├── sed-archea-backend/
│   ├── pom.xml
│   └── src/main/
│       ├── java/co/gov/bogota/sed/archea/
│       │   ├── SedArcheaApplication.java
│       │   ├── config/
│       │   │   ├── Neo4jConfig.java
│       │   │   ├── SecurityConfig.java
│       │   │   ├── DevSecurityConfig.java
│       │   │   ├── AuditConfig.java
│       │   │   └── OpenApiConfig.java
│       │   ├── domain/
│       │   │   ├── entity/       (tablas Oracle - vacío en I1)
│       │   │   ├── graph/        (nodos Neo4j - vacío en I1)
│       │   │   ├── enums/
│       │   │   │   └── Ambiente.java
│       │   │   └── repository/   (vacío en I1)
│       │   ├── application/
│       │   │   └── service/      (vacío en I1)
│       │   └── web/
│       │       ├── controller/
│       │       │   └── HealthController.java
│       │       └── exception/
│       │           └── GlobalExceptionHandler.java
│       └── resources/
│           ├── application.yml
│           ├── application-local-dev.yml
│           └── application-weblogic.yml
└── sed-archea-angular/
    ├── package.json
    ├── angular.json
    ├── tailwind.config.js
    └── src/
        ├── main.ts
        ├── app/
        │   ├── app.config.ts
        │   ├── app.routes.ts
        │   ├── core/
        │   │   └── auth/
        │   │       ├── msal.config.ts
        │   │       └── auth.guard.ts
        │   ├── shared/
        │   │   └── layout/
        │   │       └── shell.component.ts
        │   └── features/
        │       ├── dashboard/
        │       │   └── dashboard.component.ts
        │       ├── catalogo/     (placeholder - M1)
        │       ├── grafo/        (placeholder - M1)
        │       ├── ingesta/      (placeholder - M2)
        │       └── bitacora/     (placeholder - M3)
        └── styles.scss
```

---

## Tarea 1: Estructura SDD — docs base

**Files:**
- Crear: `docs/CONSTITUTION.md`
- Crear: `docs/ARCHITECTURE.md`
- Crear: `docs/TECNOLOGIAS.md`
- Crear: `docs/specs/I1-foundation-spec.md`

- [ ] **Paso 1.1: Crear CONSTITUTION.md**

```markdown
# CONSTITUTION — SED_ARCHEA

> Reglas no negociables. Nada en el proyecto puede violarlas.
> Fecha base: 2026-05-18. Aprobada para iniciar I1.

## 1. SDD Spec-Anchored

Las especificaciones son la fuente primaria de verdad. El código es un artefacto derivado.
Orden obligatorio de cambio: CONSTITUTION → ARCHITECTURE → TECNOLOGIAS → spec afectada → plan → código.
Nunca modificar código sin especificación aprobada que lo respalde.

## 2. Stack canónico inamovible en I1

- Java 8 (impuesto por WebLogic 12.2.1.4). No subir a Java 11+.
- Spring Boot 2.7.18. No subir a Spring Boot 3.x.
- Oracle WebLogic 12.2.1.4 como servidor objetivo. Empaquetado WAR.
- Oracle Database 19c+ como BD operacional.
- Neo4j 4.4 Community como BD de grafo.
- Angular 20 strict mode como frontend.

## 3. Separación de responsabilidades Oracle / Neo4j

Oracle es la fuente de verdad operacional. Neo4j es siempre derivado de Oracle.
En caso de inconsistencia, Oracle prevalece. El grafo se sincroniza desde Oracle.

## 4. Seguridad

El frontend nunca es fuente de autorización. Toda decisión efectiva de acceso se valida en backend.
Perfil `local-dev`: HTTP Basic para desarrollo local. Perfil `weblogic`: Azure AD JWT.
No desplegar usuarios local-dev en ambiente institucional.

## 5. Evolucion documental

Cambios de version actualizan en orden:
1. CONSTITUTION.md (si cambia regla no negociable).
2. ARCHITECTURE.md (si cambia decision tecnica).
3. TECNOLOGIAS.md (si cambia version o herramienta).
4. Spec del incremento afectado.
5. Plan de implementacion.
6. Codigo.
```

- [ ] **Paso 1.2: Crear ARCHITECTURE.md**

```markdown
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
```

- [ ] **Paso 1.3: Crear TECNOLOGIAS.md**

```markdown
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
```

- [ ] **Paso 1.4: Crear docs/specs/I1-foundation-spec.md**

```markdown
# Spec I1 — Fundación SED_ARCHEA

> Estado: aprobada.
> Incremento: I1. Tipo: infraestructura / scaffolding.

## Objetivo

Establecer la base técnica del proyecto: estructura SDD, proyectos backend y frontend,
configuración dual-DB (Oracle + Neo4j), seguridad por perfil y endpoint de salud funcional.

## Criterios de aceptación

- [ ] El backend compila como WAR sin errores con `mvn clean package -DskipTests`.
- [ ] `GET /sed-archea/api/health` responde 200 con `{"status":"UP"}` en perfil local-dev.
- [ ] El contexto Spring arranca con Neo4j desconectado sin fallar (conexión opcional en local-dev).
- [ ] El frontend compila sin errores con `ng build`.
- [ ] La ruta `/` redirige al dashboard en el frontend.
- [ ] Los docs CONSTITUTION, ARCHITECTURE y TECNOLOGIAS existen y están completos.

## Fuera de alcance en I1

- Cualquier feature de M1, M2 o M3.
- Conexión real a Neo4j o Oracle en CI (se simula con H2 y Neo4j Test Harness).
- Despliegue en WebLogic real.
```

- [ ] **Paso 1.5: Commit inicial de docs SDD**

```bash
git init
git add docs/
git commit -m "docs: estructura SDD base — CONSTITUTION, ARCHITECTURE, TECNOLOGIAS, spec I1"
```

---

## Tarea 2: Backend — pom.xml y configuración Maven

**Files:**
- Crear: `sed-archea-backend/pom.xml`

- [ ] **Paso 2.1: Crear pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.18</version>
        <relativePath/>
    </parent>

    <groupId>co.gov.bogota.sed</groupId>
    <artifactId>sed-archea-backend</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>SED_ARCHEA Backend</name>

    <properties>
        <java.version>8</java.version>
        <spring-data-neo4j.version>6.3.7</spring-data-neo4j.version>
        <neo4j-java-driver.version>4.4.12</neo4j-java-driver.version>
        <springdoc.version>1.7.0</springdoc.version>
        <msal.version>1.13.10</msal.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Data JPA (Oracle) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Spring Data Neo4j -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-neo4j</artifactId>
            <version>${spring-data-neo4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.neo4j.driver</groupId>
            <artifactId>neo4j-java-driver</artifactId>
            <version>${neo4j-java-driver.version}</version>
        </dependency>

        <!-- Spring Security + OAuth2 Resource Server -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
        </dependency>

        <!-- Oracle JDBC (debe proveerse localmente o en repo interno SED) -->
        <dependency>
            <groupId>com.oracle.database.jdbc</groupId>
            <artifactId>ojdbc8</artifactId>
            <version>19.21.0.0</version>
            <scope>runtime</scope>
        </dependency>

        <!-- SpringDoc OpenAPI / Swagger -->
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-ui</artifactId>
            <version>${springdoc.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-security</artifactId>
            <version>${springdoc.version}</version>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- Actuator -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- WAR — excluir Tomcat embebido para WebLogic -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>

        <!-- Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- H2 para tests Oracle -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Neo4j Test Harness -->
        <dependency>
            <groupId>org.neo4j.test</groupId>
            <artifactId>neo4j-harness</artifactId>
            <version>4.4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>sed-archea-backend</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

- [ ] **Paso 2.2: Verificar compilación vacía**

```bash
cd sed-archea-backend
mvn clean compile -q
```
Esperado: BUILD SUCCESS sin errores.

- [ ] **Paso 2.3: Commit**

```bash
git add sed-archea-backend/pom.xml
git commit -m "build: pom.xml Spring Boot 2.7.18 con Spring Data Neo4j 6.3, Oracle, seguridad y WAR"
```

---

## Tarea 3: Backend — application.yml (perfiles)

**Files:**
- Crear: `sed-archea-backend/src/main/resources/application.yml`
- Crear: `sed-archea-backend/src/main/resources/application-local-dev.yml`
- Crear: `sed-archea-backend/src/main/resources/application-weblogic.yml`

- [ ] **Paso 3.1: Crear application.yml (base)**

```yaml
spring:
  application:
    name: sed-archea-backend
  jpa:
    hibernate:
      ddl-auto: validate
    properties:
      hibernate:
        dialect: org.hibernate.dialect.Oracle12cDialect
        default_schema: SED_ARCHEA
    open-in-view: false

management:
  endpoints:
    web:
      exposure:
        include: health
  endpoint:
    health:
      show-details: never

springdoc:
  api-docs:
    path: /api/api-docs
  swagger-ui:
    path: /api/swagger-ui.html
```

- [ ] **Paso 3.2: Crear application-local-dev.yml**

```yaml
spring:
  profiles:
    active: local-dev
  datasource:
    url: jdbc:oracle:thin:@localhost:1521/ARCHEA_DEV
    username: SED_ARCHEA
    password: archea_local
    driver-class-name: oracle.jdbc.OracleDriver
  jpa:
    hibernate:
      ddl-auto: create-drop
  neo4j:
    uri: bolt://localhost:7687
    authentication:
      username: neo4j
      password: archea_local

server:
  port: 8080
  servlet:
    context-path: /sed-archea

logging:
  level:
    co.gov.bogota.sed.archea: DEBUG
    org.springframework.security: DEBUG
    org.springframework.data.neo4j: DEBUG
```

- [ ] **Paso 3.3: Crear application-weblogic.yml**

```yaml
spring:
  datasource:
    jndi-name: jdbc/archea-oracle
  neo4j:
    uri: ${NEO4J_URI}
    authentication:
      username: ${NEO4J_USER}
      password: ${NEO4J_PASSWORD}
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: ${AZURE_ISSUER_URI}
          jwk-set-uri: ${AZURE_JWKS_URI}

server:
  servlet:
    context-path: /sed-archea

logging:
  level:
    co.gov.bogota.sed.archea: INFO
```

- [ ] **Paso 3.4: Commit**

```bash
git add sed-archea-backend/src/main/resources/
git commit -m "config: application.yml — perfiles local-dev y weblogic con dual-DB Oracle+Neo4j"
```

---

## Tarea 4: Backend — clase principal y enum Ambiente

**Files:**
- Crear: `sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/SedArcheaApplication.java`
- Crear: `sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/domain/enums/Ambiente.java`

- [ ] **Paso 4.1: Crear SedArcheaApplication.java**

```java
package co.gov.bogota.sed.archea;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

@SpringBootApplication
public class SedArcheaApplication extends SpringBootServletInitializer {

    public static void main(String[] args) {
        SpringApplication.run(SedArcheaApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(SedArcheaApplication.class);
    }
}
```

- [ ] **Paso 4.2: Crear Ambiente.java**

```java
package co.gov.bogota.sed.archea.domain.enums;

public enum Ambiente {
    DESARROLLO,
    PRUEBAS,
    PRODUCCION
}
```

- [ ] **Paso 4.3: Commit**

```bash
git add sed-archea-backend/src/main/java/
git commit -m "feat: clase principal WAR y enum Ambiente (DESARROLLO, PRUEBAS, PRODUCCION)"
```

---

## Tarea 5: Backend — Neo4jConfig

**Files:**
- Crear: `sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/Neo4jConfig.java`

**Test:**
- Crear: `sed-archea-backend/src/test/java/co/gov/bogota/sed/archea/config/Neo4jConfigTest.java`

- [ ] **Paso 5.1: Escribir test fallido**

```java
package co.gov.bogota.sed.archea.config;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.neo4j.driver.Driver;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
@ActiveProfiles("local-dev")
class Neo4jConfigTest {

    @Autowired
    private Driver neo4jDriver;

    @Test
    void contextLoadsWithNeo4jDriver() {
        assertThat(neo4jDriver).isNotNull();
    }
}
```

- [ ] **Paso 5.2: Ejecutar test — verificar falla**

```bash
cd sed-archea-backend
mvn test -Dtest=Neo4jConfigTest -pl . 2>&1 | tail -20
```
Esperado: FAIL — `Neo4jConfig` no existe aún.

- [ ] **Paso 5.3: Crear Neo4jConfig.java**

```java
package co.gov.bogota.sed.archea.config;

import org.neo4j.driver.AuthTokens;
import org.neo4j.driver.Driver;
import org.neo4j.driver.GraphDatabase;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.neo4j.config.AbstractNeo4jConfig;
import org.springframework.data.neo4j.repository.config.EnableNeo4jRepositories;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@EnableNeo4jRepositories(basePackages = "co.gov.bogota.sed.archea.domain.repository")
@EnableTransactionManagement
public class Neo4jConfig extends AbstractNeo4jConfig {

    @Value("${spring.neo4j.uri}")
    private String uri;

    @Value("${spring.neo4j.authentication.username}")
    private String username;

    @Value("${spring.neo4j.authentication.password}")
    private String password;

    @Bean
    @Override
    public Driver driver() {
        return GraphDatabase.driver(uri, AuthTokens.basic(username, password));
    }
}
```

- [ ] **Paso 5.4: Ejecutar test — verificar éxito**

```bash
mvn test -Dtest=Neo4jConfigTest 2>&1 | tail -10
```
Esperado: BUILD SUCCESS — 1 test pasado.

- [ ] **Paso 5.5: Commit**

```bash
git add sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/Neo4jConfig.java
git add sed-archea-backend/src/test/
git commit -m "feat: Neo4jConfig — driver Neo4j 4.4 con repositorios habilitados"
```

---

## Tarea 6: Backend — SecurityConfig y DevSecurityConfig

**Files:**
- Crear: `sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/SecurityConfig.java`
- Crear: `sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/DevSecurityConfig.java`

**Test:**
- Crear: `sed-archea-backend/src/test/java/co/gov/bogota/sed/archea/config/SecurityConfigTest.java`

- [ ] **Paso 6.1: Escribir test fallido**

```java
package co.gov.bogota.sed.archea.config;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles("local-dev")
class SecurityConfigTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void healthEndpointIsPublic() throws Exception {
        mockMvc.perform(get("/api/health"))
               .andExpect(status().isOk());
    }

    @Test
    void protectedEndpointRequiresAuth() throws Exception {
        mockMvc.perform(get("/api/activos"))
               .andExpect(status().isUnauthorized());
    }
}
```

- [ ] **Paso 6.2: Ejecutar test — verificar falla**

```bash
mvn test -Dtest=SecurityConfigTest 2>&1 | tail -20
```
Esperado: FAIL.

- [ ] **Paso 6.3: Crear DevSecurityConfig.java**

```java
package co.gov.bogota.sed.archea.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
@Profile("local-dev")
public class DevSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeHttpRequests(auth -> auth
                .antMatchers(
                    "/api/health",
                    "/api/api-docs/**",
                    "/api/swagger-ui/**",
                    "/api/swagger-ui.html"
                ).permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic();
        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService(PasswordEncoder encoder) {
        return new InMemoryUserDetailsManager(
            User.withUsername("admin-dev")
                .password(encoder.encode("admin123"))
                .roles("ARCHEA_ADMINISTRADOR")
                .build(),
            User.withUsername("tecnico-dev")
                .password(encoder.encode("tecnico123"))
                .roles("ARCHEA_LIDER_TECNICO")
                .build(),
            User.withUsername("consultor-dev")
                .password(encoder.encode("consultor123"))
                .roles("ARCHEA_CONSULTOR")
                .build()
        );
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

- [ ] **Paso 6.4: Crear SecurityConfig.java (perfil weblogic)**

```java
package co.gov.bogota.sed.archea.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.oauth2.server.resource.authentication.JwtAuthenticationConverter;
import org.springframework.security.oauth2.server.resource.authentication.JwtGrantedAuthoritiesConverter;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
@Profile("weblogic")
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeHttpRequests(auth -> auth
                .antMatchers("/api/health").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt.jwtAuthenticationConverter(jwtAuthConverter()))
            )
            .headers(headers -> headers
                .frameOptions().deny()
                .contentTypeOptions()
                .and()
                .httpStrictTransportSecurity().maxAgeInSeconds(31536000).includeSubDomains(true)
            );
        return http.build();
    }

    @Bean
    public JwtAuthenticationConverter jwtAuthConverter() {
        JwtGrantedAuthoritiesConverter rolesConverter = new JwtGrantedAuthoritiesConverter();
        rolesConverter.setAuthoritiesClaimName("roles");
        rolesConverter.setAuthorityPrefix("ROLE_");

        JwtAuthenticationConverter converter = new JwtAuthenticationConverter();
        converter.setJwtGrantedAuthoritiesConverter(rolesConverter);
        return converter;
    }
}
```

- [ ] **Paso 6.5: Ejecutar tests — verificar éxito**

```bash
mvn test -Dtest=SecurityConfigTest 2>&1 | tail -10
```
Esperado: BUILD SUCCESS — 2 tests pasados.

- [ ] **Paso 6.6: Commit**

```bash
git add sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/
git commit -m "feat: SecurityConfig (weblogic JWT) y DevSecurityConfig (local-dev Basic) con 3 usuarios de prueba"
```

---

## Tarea 7: Backend — AuditConfig y OpenApiConfig

**Files:**
- Crear: `...config/AuditConfig.java`
- Crear: `...config/OpenApiConfig.java`

- [ ] **Paso 7.1: Crear AuditConfig.java**

```java
package co.gov.bogota.sed.archea.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.oauth2.jwt.Jwt;

import java.util.Optional;

@Configuration
@EnableJpaAuditing(auditorAwareRef = "auditorProvider")
public class AuditConfig {

    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> {
            Authentication auth = SecurityContextHolder.getContext().getAuthentication();
            if (auth == null || !auth.isAuthenticated()) {
                return Optional.of("SISTEMA");
            }
            Object principal = auth.getPrincipal();
            if (principal instanceof Jwt) {
                String username = ((Jwt) principal).getClaimAsString("preferred_username");
                return Optional.ofNullable(username).or(() -> Optional.of("JWT_SIN_USERNAME"));
            }
            return Optional.of(auth.getName());
        };
    }
}
```

- [ ] **Paso 7.2: Crear OpenApiConfig.java**

```java
package co.gov.bogota.sed.archea.config;

import io.swagger.v3.oas.models.Components;
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.security.SecurityRequirement;
import io.swagger.v3.oas.models.security.SecurityScheme;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("SED_ARCHEA API")
                .description("Plataforma de Arquitectura Empresarial — Secretaría de Educación del Distrito")
                .version("1.0.0"))
            .addSecurityItem(new SecurityRequirement().addList("BearerAuth"))
            .components(new Components()
                .addSecuritySchemes("BearerAuth", new SecurityScheme()
                    .type(SecurityScheme.Type.HTTP)
                    .scheme("bearer")
                    .bearerFormat("JWT")));
    }
}
```

- [ ] **Paso 7.3: Commit**

```bash
git add sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/AuditConfig.java
git add sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/config/OpenApiConfig.java
git commit -m "feat: AuditConfig (preferred_username como auditor) y OpenApiConfig (BearerAuth)"
```

---

## Tarea 8: Backend — HealthController y GlobalExceptionHandler

**Files:**
- Crear: `...web/controller/HealthController.java`
- Crear: `...web/exception/GlobalExceptionHandler.java`

**Test:**
- Crear: `...test/.../web/controller/HealthControllerTest.java`

- [ ] **Paso 8.1: Escribir test fallido**

```java
package co.gov.bogota.sed.archea.web.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles("local-dev")
class HealthControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void healthReturnsUp() throws Exception {
        mockMvc.perform(get("/api/health"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$.status").value("UP"))
               .andExpect(jsonPath("$.sistema").value("SED_ARCHEA"));
    }
}
```

- [ ] **Paso 8.2: Ejecutar test — verificar falla**

```bash
mvn test -Dtest=HealthControllerTest 2>&1 | tail -20
```
Esperado: FAIL — controller no existe.

- [ ] **Paso 8.3: Crear HealthController.java**

```java
package co.gov.bogota.sed.archea.web.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.LinkedHashMap;
import java.util.Map;

@RestController
@RequestMapping("/api")
public class HealthController {

    @GetMapping("/health")
    public ResponseEntity<Map<String, String>> health() {
        Map<String, String> response = new LinkedHashMap<>();
        response.put("status", "UP");
        response.put("sistema", "SED_ARCHEA");
        response.put("version", "1.0.0");
        return ResponseEntity.ok(response);
    }
}
```

- [ ] **Paso 8.4: Crear GlobalExceptionHandler.java**

```java
package co.gov.bogota.sed.archea.web.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.LocalDateTime;
import java.util.LinkedHashMap;
import java.util.Map;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<Map<String, Object>> handleAccessDenied(AccessDeniedException ex) {
        return errorResponse(HttpStatus.FORBIDDEN, "Acceso denegado");
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<Map<String, Object>> handleGeneral(Exception ex) {
        return errorResponse(HttpStatus.INTERNAL_SERVER_ERROR, "Error interno del servidor");
    }

    private ResponseEntity<Map<String, Object>> errorResponse(HttpStatus status, String mensaje) {
        Map<String, Object> body = new LinkedHashMap<>();
        body.put("timestamp", LocalDateTime.now().toString());
        body.put("status", status.value());
        body.put("error", mensaje);
        return ResponseEntity.status(status).body(body);
    }
}
```

- [ ] **Paso 8.5: Ejecutar test — verificar éxito**

```bash
mvn test -Dtest=HealthControllerTest 2>&1 | tail -10
```
Esperado: BUILD SUCCESS — 1 test pasado.

- [ ] **Paso 8.6: Verificar que todos los tests pasan**

```bash
mvn test 2>&1 | tail -15
```
Esperado: BUILD SUCCESS — todos los tests pasados.

- [ ] **Paso 8.7: Verificar compilación como WAR**

```bash
mvn clean package -DskipTests 2>&1 | tail -10
```
Esperado: BUILD SUCCESS. Archivo `target/sed-archea-backend.war` creado.

- [ ] **Paso 8.8: Commit**

```bash
git add sed-archea-backend/src/main/java/co/gov/bogota/sed/archea/web/
git add sed-archea-backend/src/test/
git commit -m "feat: HealthController GET /api/health + GlobalExceptionHandler"
```

---

## Tarea 9: Frontend — scaffolding Angular 20

**Files:**
- Crear: `sed-archea-angular/package.json`
- Crear: `sed-archea-angular/tailwind.config.js`
- Crear: `sed-archea-angular/src/styles.scss`
- Crear: `sed-archea-angular/src/app/app.config.ts`
- Crear: `sed-archea-angular/src/app/app.routes.ts`

- [ ] **Paso 9.1: Generar proyecto Angular 20**

```bash
cd ..
npx @angular/cli@20 new sed-archea-angular \
  --style=scss \
  --routing=true \
  --ssr=false \
  --standalone=true \
  --strict=true \
  --skip-git=true
cd sed-archea-angular
```

- [ ] **Paso 9.2: Instalar dependencias**

```bash
npm install primeng@20 @primeng/themes@20 primeicons
npm install tailwindcss@3.4 postcss autoprefixer
npm install @azure/msal-angular@3 @azure/msal-browser@3
npm install cytoscape@3
npm install --save-dev @types/cytoscape
npx tailwindcss init
```

- [ ] **Paso 9.3: Configurar tailwind.config.js**

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,ts}"],
  theme: {
    extend: {
      fontFamily: {
        sans: ["Public Sans", "sans-serif"],
      },
      colors: {
        sed: {
          azul: "#1F3864",
          medio: "#2E75B6",
          claro: "#BDD7EE",
        },
      },
    },
  },
  plugins: [],
};
```

- [ ] **Paso 9.4: Actualizar styles.scss**

```scss
@import url("https://fonts.googleapis.com/css2?family=Public+Sans:wght@300;400;500;600;700&display=swap");
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --border-radius: 4px;
  --font-family: "Public Sans", sans-serif;
}

body {
  font-family: var(--font-family);
  background-color: #f8f9fa;
  margin: 0;
}
```

- [ ] **Paso 9.5: Verificar compilación frontend**

```bash
ng build --configuration=development 2>&1 | tail -15
```
Esperado: compilación exitosa sin errores.

- [ ] **Paso 9.6: Commit**

```bash
cd ..
git add sed-archea-angular/
git commit -m "feat: scaffolding Angular 20 con PrimeNG 20, Tailwind 3.4, MSAL, Cytoscape.js"
```

---

## Tarea 10: Frontend — rutas, layout y módulos placeholder

**Files:**
- Crear: `sed-archea-angular/src/app/app.routes.ts`
- Crear: `sed-archea-angular/src/app/core/auth/msal.config.ts`
- Crear: `sed-archea-angular/src/app/shared/layout/shell.component.ts`
- Crear: `sed-archea-angular/src/app/features/dashboard/dashboard.component.ts`

- [ ] **Paso 10.1: Crear app.routes.ts**

```typescript
import { Routes } from "@angular/router";

export const routes: Routes = [
  {
    path: "",
    redirectTo: "dashboard",
    pathMatch: "full",
  },
  {
    path: "dashboard",
    loadComponent: () =>
      import("./features/dashboard/dashboard.component").then(
        (m) => m.DashboardComponent
      ),
  },
  {
    path: "catalogo",
    loadComponent: () =>
      import("./features/catalogo/catalogo.component").then(
        (m) => m.CatalogoComponent
      ),
  },
  {
    path: "grafo",
    loadComponent: () =>
      import("./features/grafo/grafo.component").then(
        (m) => m.GrafoComponent
      ),
  },
  {
    path: "ingesta",
    loadComponent: () =>
      import("./features/ingesta/ingesta.component").then(
        (m) => m.IngestaComponent
      ),
  },
  {
    path: "bitacora",
    loadComponent: () =>
      import("./features/bitacora/bitacora.component").then(
        (m) => m.BitacoraComponent
      ),
  },
  {
    path: "**",
    redirectTo: "dashboard",
  },
];
```

- [ ] **Paso 10.2: Crear msal.config.ts**

```typescript
import { MsalGuardConfiguration, MsalInterceptorConfiguration } from "@azure/msal-angular";
import { InteractionType, PublicClientApplication } from "@azure/msal-browser";
import { environment } from "../../environments/environment";

export function msalInstanceFactory(): PublicClientApplication {
  return new PublicClientApplication({
    auth: {
      clientId: environment.msalClientId,
      authority: `https://login.microsoftonline.com/${environment.msalTenantId}`,
      redirectUri: environment.msalRedirectUri,
    },
    cache: {
      cacheLocation: "sessionStorage",
    },
  });
}

export const msalGuardConfig: MsalGuardConfiguration = {
  interactionType: InteractionType.Redirect,
  authRequest: {
    scopes: [environment.msalScope],
  },
};

export const msalInterceptorConfig: MsalInterceptorConfiguration = {
  interactionType: InteractionType.Redirect,
  protectedResourceMap: new Map([
    [environment.apiBaseUrl, [environment.msalScope]],
  ]),
};
```

- [ ] **Paso 10.3: Crear environments/environment.ts**

```typescript
export const environment = {
  production: false,
  apiBaseUrl: "http://localhost:8080/sed-archea/api",
  msalClientId: "LOCAL-DEV-CLIENT-ID",
  msalTenantId: "LOCAL-DEV-TENANT-ID",
  msalRedirectUri: "http://localhost:4200",
  msalScope: "api://LOCAL-DEV-CLIENT-ID/.default",
  useMsal: false,
};
```

- [ ] **Paso 10.4: Crear shell.component.ts (layout principal)**

```typescript
import { Component } from "@angular/core";
import { RouterOutlet, RouterLink, RouterLinkActive } from "@angular/router";

@Component({
  selector: "app-shell",
  standalone: true,
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <div class="flex h-screen bg-gray-50">
      <!-- Sidebar -->
      <aside class="w-64 bg-[#1F3864] text-white flex flex-col">
        <div class="p-4 border-b border-blue-800">
          <h1 class="text-sm font-bold uppercase tracking-wide text-blue-200">SED Bogotá</h1>
          <h2 class="text-lg font-bold mt-1">SED_ARCHEA</h2>
          <p class="text-xs text-blue-300 mt-1">Arquitectura Empresarial</p>
        </div>
        <nav class="flex-1 p-4 space-y-1">
          <a routerLink="/dashboard" routerLinkActive="bg-blue-700"
             class="flex items-center gap-2 px-3 py-2 rounded text-sm hover:bg-blue-800 transition">
            Dashboard
          </a>
          <a routerLink="/catalogo" routerLinkActive="bg-blue-700"
             class="flex items-center gap-2 px-3 py-2 rounded text-sm hover:bg-blue-800 transition">
            Catálogo de Activos
          </a>
          <a routerLink="/grafo" routerLinkActive="bg-blue-700"
             class="flex items-center gap-2 px-3 py-2 rounded text-sm hover:bg-blue-800 transition">
            Grafo de Relaciones
          </a>
          <a routerLink="/ingesta" routerLinkActive="bg-blue-700"
             class="flex items-center gap-2 px-3 py-2 rounded text-sm hover:bg-blue-800 transition">
            Ingesta / Encuesta
          </a>
          <a routerLink="/bitacora" routerLinkActive="bg-blue-700"
             class="flex items-center gap-2 px-3 py-2 rounded text-sm hover:bg-blue-800 transition">
            Bitácora
          </a>
        </nav>
      </aside>
      <!-- Main -->
      <main class="flex-1 overflow-auto">
        <router-outlet />
      </main>
    </div>
  `,
})
export class ShellComponent {}
```

- [ ] **Paso 10.5: Crear dashboard.component.ts**

```typescript
import { Component } from "@angular/core";

@Component({
  selector: "app-dashboard",
  standalone: true,
  template: `
    <div class="p-6">
      <h2 class="text-2xl font-bold text-[#1F3864] mb-2">
        Plataforma de Arquitectura Empresarial
      </h2>
      <p class="text-gray-500 text-sm">Secretaría de Educación del Distrito — SED_ARCHEA v1.0</p>
      <div class="mt-6 grid grid-cols-3 gap-4">
        <div class="bg-white rounded border border-gray-200 p-4 shadow-sm">
          <p class="text-xs text-gray-400 uppercase tracking-wide">Activos registrados</p>
          <p class="text-2xl font-bold text-[#1F3864] mt-1">—</p>
        </div>
        <div class="bg-white rounded border border-gray-200 p-4 shadow-sm">
          <p class="text-xs text-gray-400 uppercase tracking-wide">Relaciones en grafo</p>
          <p class="text-2xl font-bold text-[#2E75B6] mt-1">—</p>
        </div>
        <div class="bg-white rounded border border-gray-200 p-4 shadow-sm">
          <p class="text-xs text-gray-400 uppercase tracking-wide">Eventos en bitácora</p>
          <p class="text-2xl font-bold text-gray-600 mt-1">—</p>
        </div>
      </div>
    </div>
  `,
})
export class DashboardComponent {}
```

- [ ] **Paso 10.6: Crear componentes placeholder para M1, M2, M3**

Crear `src/app/features/catalogo/catalogo.component.ts`:
```typescript
import { Component } from "@angular/core";
@Component({ selector: "app-catalogo", standalone: true,
  template: `<div class="p-6"><h2 class="text-xl font-bold text-[#1F3864]">Catálogo de Activos</h2><p class="text-gray-400 mt-2">Módulo M1 — disponible en I2.</p></div>` })
export class CatalogoComponent {}
```

Crear `src/app/features/grafo/grafo.component.ts`:
```typescript
import { Component } from "@angular/core";
@Component({ selector: "app-grafo", standalone: true,
  template: `<div class="p-6"><h2 class="text-xl font-bold text-[#1F3864]">Grafo de Relaciones</h2><p class="text-gray-400 mt-2">Módulo M1 — visualización disponible en I3.</p></div>` })
export class GrafoComponent {}
```

Crear `src/app/features/ingesta/ingesta.component.ts`:
```typescript
import { Component } from "@angular/core";
@Component({ selector: "app-ingesta", standalone: true,
  template: `<div class="p-6"><h2 class="text-xl font-bold text-[#1F3864]">Ingesta / Encuesta</h2><p class="text-gray-400 mt-2">Módulo M2 — disponible en I4.</p></div>` })
export class IngestaComponent {}
```

Crear `src/app/features/bitacora/bitacora.component.ts`:
```typescript
import { Component } from "@angular/core";
@Component({ selector: "app-bitacora", standalone: true,
  template: `<div class="p-6"><h2 class="text-xl font-bold text-[#1F3864]">Bitácora de Despliegues</h2><p class="text-gray-400 mt-2">Módulo M3 — disponible en I5.</p></div>` })
export class BitacoraComponent {}
```

- [ ] **Paso 10.7: Verificar compilación final del frontend**

```bash
cd sed-archea-angular
ng build --configuration=development 2>&1 | tail -10
```
Esperado: Build at `dist/sed-archea-angular`. 0 errores.

- [ ] **Paso 10.8: Verificar que la app levanta localmente**

```bash
ng serve --port 4200 &
sleep 5
curl -s http://localhost:4200 | head -5
kill %1
```
Esperado: respuesta HTML con `SED_ARCHEA` en el título.

- [ ] **Paso 10.9: Commit final de I1**

```bash
cd ..
git add sed-archea-angular/src/
git commit -m "feat: Angular 20 — layout shell, rutas lazy, dashboard y placeholders M1/M2/M3"
```

---

## Verificación de criterios de aceptación de I1

- [ ] **CA1:** `mvn clean package -DskipTests` → BUILD SUCCESS, WAR generado.

```bash
cd sed-archea-backend && mvn clean package -DskipTests -q && ls target/*.war
```
Esperado: `target/sed-archea-backend.war`

- [ ] **CA2:** `GET /api/health` → 200 `{"status":"UP","sistema":"SED_ARCHEA"}`

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=local-dev &
sleep 15
curl -s http://localhost:8080/sed-archea/api/health | python -m json.tool
kill %1
```

- [ ] **CA3:** `ng build` → 0 errores.

```bash
cd ../sed-archea-angular && ng build --configuration=development -q
```

- [ ] **CA4:** Todos los tests del backend pasan.

```bash
cd ../sed-archea-backend && mvn test 2>&1 | grep -E "Tests run:|BUILD"
```
Esperado: BUILD SUCCESS, 0 failures, 0 errors.

- [ ] **Commit final de verificación**

```bash
git add .
git commit -m "chore: I1 completado — backend WAR, dual-DB config, seguridad por perfil, Angular 20 con shell y rutas"
```

---

## Siguientes incrementos (sub-planes independientes)

| Incremento | Descripción | Prerequisito |
|---|---|---|
| **I2** | M1 — Catálogo de Activos CRUD (Oracle + Neo4j sync) | I1 |
| **I3** | M1 — Visualización de Grafo (Cytoscape.js + Cypher queries) | I2 |
| **I4** | M2 — Formulario digital de ingesta + importación Excel M0 | I2 |
| **I5** | M3 — Bitácora de Despliegues (hoja de vida del activo) | I2 |

Cada incremento tiene su propia spec en `docs/specs/` y su plan en `docs/plans/`.
