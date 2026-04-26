# products-launcher

Guia operativa completa en [docs/README.md](docs/README.md).

Arquitectura de microservicios con NestJS, NATS y PostgreSQL, preparada con Docker Compose para dos entornos:

- Desarrollo local con hot reload y sync de archivos
- Produccion con imagenes optimizadas y politicas de reinicio

## Estructura Docker Compose

- `docker-compose.yml`: base compartido (servicios y dependencias)
- `docker-compose.override.yml`: solo desarrollo (bind mounts, target development)
- `docker-compose.prod.yml`: solo produccion (target production, restart, logging y limits)

Este patron esta alineado con la recomendacion oficial de Docker para multiples archivos Compose.

## Variables de entorno

1. Copia la plantilla de entorno para desarrollo:

```bash
cp .env.example .env.local
```

2. Ajusta valores segun tu entorno local.

3. Para produccion, usa `.env.prod` con credenciales reales.

## Levantar en desarrollo

Compose cargara automaticamente `docker-compose.override.yml`.

```bash
docker compose --env-file .env.local up --build
```

Para correr en segundo plano:

```bash
docker compose --env-file .env.local up -d --build
```

## Levantar en produccion

```bash
docker compose \
	-f docker-compose.yml \
	-f docker-compose.prod.yml \
	--env-file .env.prod \
	up -d --build
```

## Validar configuracion final

Desarrollo:

```bash
docker compose --env-file .env.local config
```

Produccion:

```bash
docker compose \
	-f docker-compose.yml \
	-f docker-compose.prod.yml \
	--env-file .env.prod \
	config
```

## Apagar servicios

Desarrollo:

```bash
docker compose --env-file .env.local down
```

Produccion:

```bash
docker compose \
	-f docker-compose.yml \
	-f docker-compose.prod.yml \
	--env-file .env.prod \
	down
```

## Scripts de ayuda

```bash
./scripts/dev-up.sh
./scripts/dev-down.sh
./scripts/prod-up.sh
./scripts/prod-down.sh
```

## Seed de datos

Con los servicios arriba, puedes poblar datos de ejemplo con:

```bash
docker compose --env-file .env.local exec products-service pnpm db:seed
docker compose --env-file .env.local exec orders-service pnpm db:seed
```

Nota: el seed de orders crea una orden de ejemplo con `productId` 1 y 2.
