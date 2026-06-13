# Primer despliegue con AWS y Cloudflare

Este laboratorio utiliza una arquitectura fácil de comprender: una instancia de AWS ejecuta Nginx y una aplicación web, mientras Cloudflare administra el dominio, HTTPS, proxy y reglas básicas de seguridad.

## Arquitectura del laboratorio

```text
Internet
   │
   ▼
Cloudflare DNS + Proxy + TLS + WAF
   │
   ▼
AWS EC2
Nginx :80/:443
   │
   ▼
Aplicación :3000
```

## 1. Preparar AWS

1. Crea una VPC o utiliza una VPC de laboratorio.
2. Lanza una instancia EC2 pequeña con una distribución Linux compatible.
3. Asigna una dirección estable si el proyecto lo necesita.
4. Configura un grupo de seguridad.
5. Restringe SSH a tu dirección IP o utiliza un mecanismo administrado de acceso.
6. Permite HTTP y HTTPS solamente cuando el servicio esté listo.

| Puerto | Protocolo | Origen recomendado | Propósito |
| --- | --- | --- | --- |
| 22 | TCP | Tu IP administrativa | Acceso SSH temporal |
| 80 | TCP | Internet o rangos autorizados | Redirección y validaciones HTTP |
| 443 | TCP | Internet o rangos de Cloudflare | Tráfico HTTPS |

!!! warning "No expongas la base de datos"
    Una base de datos no debe aceptar conexiones desde todo Internet. Ubícala en una red privada y permite acceso únicamente desde la aplicación.

## 2. Instalar la aplicación y Nginx

Ejemplo para Ubuntu:

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable --now nginx
```

Ejemplo de proxy inverso para una aplicación en el puerto `3000`:

```nginx
server {
    listen 80;
    server_name app.ejemplo.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Valida y recarga Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## 3. Configurar Cloudflare

1. Agrega el dominio a Cloudflare.
2. Cambia los servidores de nombre en el registrador.
3. Crea un registro `A` para `app` apuntando a la dirección pública del origen.
4. Activa el estado **Proxied**.
5. Configura SSL/TLS en **Full (strict)**.
6. Fuerza HTTPS cuando el certificado del origen esté listo.
7. Crea reglas de seguridad para rutas sensibles.

Ejemplo de registros:

| Tipo | Nombre | Destino | Proxy |
| --- | --- | --- | --- |
| A | `app` | IP pública del origen | Activado |
| CNAME | `www` | `app.ejemplo.com` | Activado |
| CNAME | `api` | `app.ejemplo.com` | Activado |

## 4. Definir la caché

=== "Puede almacenarse"

    ```text
    /assets/*
    /css/*
    /js/*
    /img/*
    ```

=== "Debe excluirse inicialmente"

    ```text
    /api/*
    /login
    /admin/*
    /cuenta/*
    ```

!!! tip "Empieza conservador"
    Primero verifica que la aplicación funciona sin caché agresiva. Después agrega reglas para archivos estáticos y mide el resultado.

## 5. Probar el flujo completo

```bash
curl -I https://app.ejemplo.com
curl -I https://app.ejemplo.com/assets/app.css
```

Comprueba:

- Resolución DNS correcta.
- Certificado HTTPS válido.
- Redirección de HTTP a HTTPS.
- Respuesta de la aplicación.
- Encabezados de Cloudflare.
- Exclusión de caché en sesiones y API.
- Registros de Nginx y de la aplicación.

## 6. Preparar un despliegue repetible

Un flujo DevOps básico puede ser:

```text
Commit → Pruebas → Build → Artefacto → Despliegue → Health check → Rollback si falla
```

Ejemplo de comandos ejecutados por un pipeline:

```bash
npm ci
npm test
npm run build
rsync -avz dist/ deploy@app-server:/srv/app/
ssh deploy@app-server "sudo systemctl restart app"
curl --fail https://app.ejemplo.com/health
```

!!! warning "Los secretos no pertenecen al repositorio"
    Claves privadas, tokens, contraseñas y variables sensibles deben almacenarse en un gestor de secretos o en el sistema seguro del pipeline.

## Criterios de finalización

- La aplicación responde por HTTPS.
- Cloudflare está delante del origen.
- El origen no expone servicios innecesarios.
- Existe un health check.
- Los logs pueden consultarse.
- Hay un procedimiento de rollback.
- El gasto del laboratorio está controlado.

Continúa con [operación y seguridad](operacion-seguridad.md).
