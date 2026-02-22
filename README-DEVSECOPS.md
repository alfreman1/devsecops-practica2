# DevSecOps CI/CD - Documentacion

Este documento describe el pipeline DevSecOps implementado para frontend, backend y contenedores, con quality gates y evidencia.

## Fases y herramientas

1. Install (reproducible)
- Herramienta: `npm ci` (frontend + servicios backend)
- Fase DevSecOps: Build
- Riesgo mitigado: builds no reproducibles o drift de dependencias
- Por que es necesaria: asegura instalaciones deterministas y evita sorpresas entre entornos

2. Code quality
- Herramienta: `eslint` (frontend + servicios backend)
- Fase DevSecOps: Shift-left quality
- Riesgo mitigado: errores comunes, malas practicas, deuda tecnica temprana
- Por que es necesaria: detecta fallos antes de ejecutar pruebas o desplegar

3. Tests
- Herramienta: `jest` (frontend + servicios backend)
- Fase DevSecOps: Verification
- Riesgo mitigado: regresiones funcionales basicas
- Por que es necesaria: confirma que el comportamiento esperado sigue intacto

4. SCA (dependencias)
- Herramienta: `npm audit --audit-level=critical` (frontend + servicios backend)
- Fase DevSecOps: Dependency risk
- Riesgo mitigado: dependencias vulnerables conocidas
- Por que es necesaria: evita introducir CVEs criticos en el artefacto

5. SAST
- Herramienta: `semgrep --config=auto --severity=ERROR` (frontend + servicios backend)
- Fase DevSecOps: Secure coding
- Riesgo mitigado: patrones de vulnerabilidad en codigo fuente
- Por que es necesaria: encuentra fallos antes de empaquetar o desplegar

6. Docker build + versionado
- Herramienta: `docker compose build` + tags con `github.sha` y `github.run_number`
- Fase DevSecOps: Release
- Riesgo mitigado: imagenes sin trazabilidad o sin control de versiones
- Por que es necesaria: permite identificar exactamente que codigo se desplego

7. Container scanning
- Herramienta: Trivy (HIGH,CRITICAL) con `exit-code=1`
- Fase DevSecOps: Container security
- Riesgo mitigado: vulnerabilidades en sistema base y librerias del contenedor
- Por que es necesaria: evita publicar imagenes con riesgos severos

8. Run stack + smoke tests
- Herramienta: Docker Compose + `curl`
- Fase DevSecOps: Deploy verification
- Riesgo mitigado: despliegues que "compilan" pero no responden
- Por que es necesaria: valida health y endpoints criticos

9. DAST
- Herramienta: OWASP ZAP baseline (repositorio `zap/`)
- Fase DevSecOps: Runtime security
- Riesgo mitigado: vulnerabilidades dinamicas en endpoints expuestos
- Por que es necesaria: complementa SAST/SCA con pruebas reales en ejecucion

## Evidencia

- GitHub Actions run (pegar enlace): `https://github.com/<org>/<repo>/actions/runs/<id>`
- Captura o log (pegar referencia en el repo): `docs/evidence/<archivo>`

Ejemplo de extracto de log (placeholder):
```
Lint OK
Tests OK
Trivy scan OK (HIGH,CRITICAL)
Smoke test OK
```

## Como ejecutar en local

### Docker Compose (stack completo)
```bash
docker compose -f backend/docker-compose.yml up --build
```

```bash
docker compose -f backend/docker-compose.yml down
```

### Lint, tests y SCA

Frontend:
```bash
cd frontend
npm ci
npm run lint
npm test
npm audit --audit-level=critical
```

Backend (cada servicio):
```bash
cd backend/users-service
npm ci
npm run lint
npm test
npm audit --audit-level=critical
```

```bash
cd backend/academic-service
npm ci
npm run lint
npm test
npm audit --audit-level=critical
```

```bash
cd backend/api-gateway
npm ci
npm run lint
npm test
npm audit --audit-level=critical
```

### Smoke tests (manual)
```bash
curl http://localhost:3000/health
curl -i http://localhost:3000/courses
```
