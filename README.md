# API Equipo 00 — Pipeline CD con GitHub Actions y Cloudflare Workers

API REST serverless desplegada mediante un flujo de **Despliegue Continuo (CD)** automatizado en GitHub Actions (`.github/workflows/cd.yml`) hacia los entornos de **Staging** y **Producción** en Cloudflare Workers, con validación automática (*smoke tests*), aprobación manual y reversión instantánea (*rollback*).

## Enlaces públicos

* **Repositorio:** https://github.com/YonaHD5/api-equipo-00
* **Entorno de Staging (`/health`):** https://api-equipo-00-staging.syntax-error-ags.workers.dev/health
* **Entorno de Producción (`/health`):** https://api-equipo-00.syntax-error-ags.workers.dev/health

## Arquitectura del Pipeline CD

Cada `push` hacia la rama `main` dispara tres etapas secuenciales:

1. **Pruebas (`pruebas`):** Instala dependencias (`npm ci`) y ejecuta la suite de pruebas automatizadas (`npm test`).
2. **Staging (`staging`):** Despliega automáticamente el Worker en el ambiente de pruebas (`wrangler deploy --env staging`) y verifica disponibilidad mediante un *smoke test* con `curl` contra `/health`.
3. **Producción (`produccion`):** Requiere aprobación manual (`Required reviewers` en el environment `production`) antes de ejecutar `wrangler deploy` y validar el endpoint productivo.

## Endpoints disponibles

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `GET` | `/health` | Devuelve el estado operativo, ambiente y versión activa de la API. |

## Ejecución local y Rollback

```bash
# Instalar dependencias
npm install

# Ejecutar pruebas unitarias
npm test

# Ejecutar en desarrollo local
npx wrangler dev

# Revertir producción a la versión estable anterior
npx wrangler rollback
