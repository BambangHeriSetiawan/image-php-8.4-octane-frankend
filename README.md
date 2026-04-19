# Laravel Octane + FrankenPHP Base Image

This folder contains a production-oriented base image for Laravel Octane running on FrankenPHP.

## Build the base image

From this folder, run:

```bash
docker build -t setiawanheribambang/frankenphp-base:8.4 -f Dockerfile.base .
```

## Publish to Docker Hub

1. Log in to Docker Hub:

```bash
docker login
```

2. Build the image:

```bash
docker build -t setiawanheribambang/frankenphp-base:8.4 -f Dockerfile.base .
```

3. Push the image:

```bash
docker push setiawanheribambang/frankenphp-base:8.4
```

Optional: also tag it as `latest` and push that tag too:

```bash
docker tag setiawanheribambang/frankenphp-base:8.4 setiawanheribambang/frankenphp-base:latest
docker push setiawanheribambang/frankenphp-base:latest
```

## How to use

Use this base image as the `FROM` image in your Laravel app Dockerfile.

Example `Dockerfile` in your Laravel project:

```Dockerfile
FROM setiawanheribambang/frankenphp-base:8.4

WORKDIR /app

COPY . .

RUN composer install --no-dev --optimize-autoloader --no-interaction --prefer-dist \
	&& php artisan config:cache \
	&& php artisan route:cache \
	&& php artisan view:cache

EXPOSE 8000

CMD ["php", "artisan", "octane:start", "--server=frankenphp", "--host=0.0.0.0", "--port=8000"]
```

## Run container

```bash
docker run --rm -it -p 8000:8000 your-laravel-app-image
```

Then open:

`http://localhost:8000`

## Notes

- This base image is for runtime. Build frontend assets in a separate build stage (or CI) and copy only compiled assets into the final image.
- Use environment variables and secrets from your orchestrator (`docker compose`, Kubernetes, etc.), not hardcoded values in the image.
- HTTPS is supported by FrankenPHP. For production, it is usually best to terminate TLS with a reverse proxy or load balancer in front of the container. For local testing, you can also run it directly with certificates.