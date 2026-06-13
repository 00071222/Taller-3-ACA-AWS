# Fundamentos cloud para DevOps

Antes de desplegar una aplicación es necesario comprender qué problema resuelve cada capa. Una arquitectura sencilla y bien delimitada suele ser más segura y fácil de operar que una arquitectura con demasiados servicios.

## Conceptos esenciales de AWS

| Concepto | Qué debes entender | Aplicación práctica |
| --- | --- | --- |
| Región | Zona geográfica que contiene infraestructura de AWS. | Seleccionar una región cercana a los usuarios y compatible con los servicios requeridos. |
| Zona de disponibilidad | Centro de datos aislado dentro de una región. | Distribuir componentes críticos para reducir puntos únicos de falla. |
| IAM | Identidades, roles y políticas de acceso. | Evitar claves compartidas y aplicar mínimo privilegio. |
| VPC | Red privada virtual en AWS. | Separar recursos públicos, privados y bases de datos. |
| Grupo de seguridad | Firewall asociado a recursos. | Permitir únicamente puertos y orígenes necesarios. |
| CloudWatch | Métricas, registros y alarmas. | Detectar fallos, consumo alto y degradación del servicio. |

!!! warning "Cuenta raíz"
    La cuenta raíz de AWS no debe utilizarse para las tareas diarias. Protege esa cuenta con autenticación multifactor y crea identidades administrativas separadas para la operación normal.

## Conceptos esenciales de Cloudflare

### DNS

DNS traduce un nombre como `app.ejemplo.com` al destino técnico del servicio. Cloudflare puede administrar los registros y decidir si una solicitud pasa directamente al origen o atraviesa su proxy.

| Estado | Significado | Uso habitual |
| --- | --- | --- |
| Proxied | Cloudflare recibe primero la solicitud. | Sitios y APIs públicas que necesitan CDN, TLS y protección. |
| DNS only | Cloudflare solamente responde el DNS. | Servicios no compatibles con el proxy o arquitecturas donde otra CDN es la capa principal. |

### TLS

TLS protege los datos en tránsito. En una arquitectura con proxy existen dos conexiones:

```text
Usuario ── HTTPS ── Cloudflare ── HTTPS ── Origen en AWS
```

!!! tip "Modo recomendado"
    Utiliza **Full (strict)** cuando el origen tiene un certificado válido. Evita configuraciones que cifran únicamente el tramo entre el usuario y Cloudflare.

### CDN, caché y WAF

- La CDN acerca archivos estáticos al usuario.
- La caché reduce solicitudes repetidas al origen.
- El WAF inspecciona solicitudes y bloquea patrones maliciosos.
- El rate limiting ayuda a controlar abuso en rutas sensibles.

## Modelo de responsabilidad compartida

AWS protege la infraestructura física y los servicios administrados. El equipo que utiliza AWS sigue siendo responsable de la configuración, las identidades, los datos, las aplicaciones y los permisos.

Cloudflare protege y procesa el tráfico que atraviesa su red, pero el equipo DevOps debe configurar correctamente DNS, reglas, certificados, caché y acceso al origen.

## Servicios para comenzar

=== "Aplicación dinámica"

    ```text
    IAM
    VPC
    EC2 o Lightsail
    RDS
    S3
    CloudWatch
    Cloudflare DNS + Proxy + TLS + WAF
    ```

=== "Sitio estático"

    ```text
    S3 privado
    CloudFront
    Certificado administrado
    Cloudflare DNS only
    ```

!!! note "Evita una doble CDN sin necesidad"
    Para un sitio estático, CloudFront puede ser la CDN principal delante de S3 y Cloudflare puede quedarse como proveedor DNS. Colocar dos capas de caché requiere una justificación clara y complica invalidaciones y diagnósticos.

## Preparación antes del laboratorio

1. Activa MFA en AWS y Cloudflare.
2. Crea un presupuesto y alertas de costos en AWS.
3. Define una convención de nombres y etiquetas.
4. Decide qué recursos serán públicos y cuáles privados.
5. Guarda el código en Git.
6. Documenta cómo desplegar y cómo revertir cambios.

Continúa con el [primer despliegue](primer-despliegue.md).
