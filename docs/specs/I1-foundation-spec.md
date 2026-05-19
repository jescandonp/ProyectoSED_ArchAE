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
