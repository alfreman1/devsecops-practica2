# DevSecOps CI/CD - Documentación

Este documento describe el pipeline DevSecOps implementado para frontend + backend + contenedores, con quality gates y evidencias listas para entrega.

## Fases y herramientas

1. Install (reproducible)

- Herramienta: `npm ci` (frontend + servicios backend)
- Fase DevSecOps: Build
- Riesgo mitigado: builds no reproducibles o drift de dependencias
- Por qué es necesaria: asegura instalaciones deterministas y evita sorpresas entre entornos

2. Code quality

- Herramienta: `eslint` (frontend + servicios backend)
- Fase DevSecOps: Shift-left quality
- Riesgo mitigado: errores comunes, malas prácticas, deuda técnica temprana
- Por qué es necesaria: detecta fallos antes de ejecutar pruebas o desplegar

3. Tests

- Herramienta: `jest` (frontend + servicios backend)
- Fase DevSecOps: Verification
- Riesgo mitigado: regresiones funcionales básicas
- Por qué es necesaria: confirma que el comportamiento esperado sigue intacto

4. SAST

- Herramienta: `semgrep --config=auto`
- Fase DevSecOps: Secure coding
- Riesgo mitigado: patrones de vulnerabilidad en código fuente
- Por qué es necesaria: encuentra fallos antes de empaquetar o desplegar

5. SCA

- Herramienta: `npm audit --audit-level=critical` (frontend)
- Fase DevSecOps: Dependency risk
- Riesgo mitigado: dependencias vulnerables conocidas
- Por qué es necesaria: evita introducir CVEs críticos en el artefacto

6. Docker build + versionado

- Herramienta: `docker compose build` + tag con `github.sha` y `github.run_number`
- Fase DevSecOps: Release
- Riesgo mitigado: imágenes sin trazabilidad o sin control de versiones
- Por qué es necesaria: permite identificar exactamente qué código se desplegó

7. Container scanning

- Herramienta: Trivy (HIGH/CRITICAL)
- Fase DevSecOps: Container security
- Riesgo mitigado: vulnerabilidades en sistema base y librerías del contenedor
- Por qué es necesaria: evita publicar imágenes con riesgos severos

8. Run stack + smoke tests

- Herramienta: Docker Compose + `curl`
- Fase DevSecOps: Deploy verification
- Riesgo mitigado: despliegues que "compilan" pero no responden
- Por qué es necesaria: valida health y seguridad básica (401/403 sin token)

9. DAST

- Herramienta: OWASP ZAP baseline
- Fase DevSecOps: Runtime security
- Riesgo mitigado: vulnerabilidades dinámicas en endpoints expuestos
- Por qué es necesaria: complementa SAST/SCA con pruebas reales en ejecución

## Cómo ejecutar en local

### Docker Compose (stack completo)

```bash
docker compose -f backend/docker-compose.yml up --build
```

```bash
docker compose -f backend/docker-compose.yml down
```

### Lint y tests f

Frontend:

```bash
cd frontend
npm ci
npm run lint
npm test
```

Backend (cada servicio):

```bash
cd backend/users-service
npm ci
npm run lint
npm test
```

```bash
cd backend/academic-service
npm ci
npm run lint
npm test
```

```bash
cd backend/api-gateway
npm ci
npm run lint
npm test
```

### Smoke tests (manual)

```bash
curl http://localhost:3000/health
curl -i http://localhost:3000/courses
```
