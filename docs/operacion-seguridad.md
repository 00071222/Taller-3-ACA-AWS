# Operación y seguridad

Un despliegue no termina cuando la página responde. DevOps también debe mantener la disponibilidad, reducir riesgos, detectar fallos y controlar costos.

## Lista mínima de seguridad

| Área | Acción mínima | Evidencia esperada |
| --- | --- | --- |
| Identidades | MFA y mínimo privilegio. | Usuarios y roles revisados. |
| Red | Puertos limitados y recursos privados. | Reglas de seguridad documentadas. |
| TLS | HTTPS de extremo a extremo. | Cloudflare en Full (strict). |
| Secretos | Fuera del código fuente. | Variables seguras o gestor de secretos. |
| Parches | Sistema y dependencias actualizados. | Registro de mantenimiento. |
| Respaldo | Copias verificadas. | Prueba de restauración. |
| Protección web | WAF y rate limiting. | Reglas activas y monitoreadas. |

## Observabilidad

### Logs

Centraliza al menos:

- Accesos y errores de Nginx.
- Logs de la aplicación.
- Eventos de autenticación.
- Cambios relevantes en infraestructura.
- Eventos de seguridad de Cloudflare.

### Métricas

Monitorea:

```text
Disponibilidad
Latencia
Tasa de errores
CPU y memoria
Espacio en disco
Conexiones a base de datos
Solicitudes bloqueadas
Costo diario o mensual
```

### Alertas

Una alerta debe indicar qué sucede y qué acción tomar. Evita crear alertas para cada fluctuación pequeña.

!!! note "Ejemplo de alerta útil"
    **La tasa de respuestas 5xx supera el 5 % durante 5 minutos.** Revisar logs de la aplicación, estado de la base de datos y despliegues recientes.

## Copias de seguridad

1. Define qué datos son críticos.
2. Establece frecuencia y retención.
3. Cifra las copias.
4. Almacena copias separadas del recurso principal.
5. Prueba restauraciones periódicamente.

!!! warning "Un backup no probado es solo una suposición"
    La existencia de un archivo de respaldo no demuestra que el servicio pueda recuperarse. Documenta y ensaya el procedimiento de restauración.

## Protección del origen

Cloudflare reduce la exposición pública, pero la dirección del origen todavía puede descubrirse por historial DNS, registros antiguos o servicios adicionales.

Opciones de endurecimiento:

- Permitir tráfico web únicamente desde rangos autorizados de Cloudflare.
- Utilizar Authenticated Origin Pulls.
- Implementar Cloudflare Tunnel cuando la arquitectura lo permita.
- Separar los accesos administrativos del tráfico público.
- Evitar reutilizar la misma dirección pública para otros servicios.

## Gestión de incidentes

=== "Detección"

    ```text
    Confirmar el impacto
    Identificar servicios afectados
    Revisar alertas y cambios recientes
    ```

=== "Contención"

    ```text
    Bloquear tráfico malicioso
    Revertir el despliegue
    Aislar recursos comprometidos
    Preservar evidencias
    ```

=== "Recuperación"

    ```text
    Restaurar el servicio
    Validar integridad
    Monitorear recurrencia
    Comunicar el estado
    ```

=== "Aprendizaje"

    ```text
    Crear postmortem
    Identificar causa raíz
    Asignar acciones correctivas
    Actualizar runbooks
    ```

## Control de costos

- Activa AWS Budgets y notificaciones.
- Etiqueta recursos por proyecto, ambiente y responsable.
- Detén o elimina laboratorios que no se utilizan.
- Revisa almacenamiento, snapshots y direcciones reservadas.
- Ajusta el tamaño de instancias según métricas reales.
- Comprueba límites y condiciones del plan de Cloudflare utilizado.

## Runbook de verificación diaria

```text
[ ] La página principal responde.
[ ] El endpoint de salud responde.
[ ] No existe un aumento anormal de errores.
[ ] CPU, memoria y disco están dentro de límites.
[ ] Las copias de seguridad recientes finalizaron.
[ ] No hay alertas críticas de seguridad.
[ ] El costo acumulado sigue dentro del presupuesto.
```

Regresa al [inicio](index.md) o revisa el [entorno MkDocs](entorno-mkdocs.md).
