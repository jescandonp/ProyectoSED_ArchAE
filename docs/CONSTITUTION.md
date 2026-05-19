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

## 5. Evolución documental

Cambios de versión actualizan en orden:
1. CONSTITUTION.md (si cambia regla no negociable).
2. ARCHITECTURE.md (si cambia decisión técnica).
3. TECNOLOGIAS.md (si cambia versión o herramienta).
4. Spec del incremento afectado.
5. Plan de implementación.
6. Código.
