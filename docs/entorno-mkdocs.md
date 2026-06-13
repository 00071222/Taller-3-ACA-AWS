# Entorno MkDocs y advertencia de la terminal

Esta página explica cómo ejecutar la documentación y cómo interpretar la advertencia mostrada por Material for MkDocs.

## La advertencia no es un error

El bloque rojo sobre **MkDocs 2.0** es un aviso informativo emitido por versiones recientes de Material for MkDocs. No significa que la documentación haya fallado.

Las líneas siguientes confirman que el sitio fue construido y publicado localmente:

```text
Documentation built
Watching paths for changes
Serving on http://127.0.0.1:8000/
Browser connected
```

!!! note "Resultado de la terminal"
    El servidor local funciona correctamente. El mensaje importante es `Serving on http://127.0.0.1:8000/`.

## Por qué aparece

Material for MkDocs está construido sobre MkDocs 1.x. El equipo del tema advierte que MkDocs 2.0 introduce cambios incompatibles con el sistema actual de temas y plugins.

Este proyecto fija versiones compatibles:

```text
mkdocs==1.6.1
mkdocs-material==9.7.6
pymdown-extensions==10.21.3
```

La versión de Material incluida también limita MkDocs a una versión menor que 2 para evitar una actualización accidental incompatible.

## Reinstalación limpia en Windows

Desde la carpeta del proyecto:

```powershell
Deactivate 2>$null
Remove-Item -Recurse -Force .venv -ErrorAction SilentlyContinue
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Comprueba las versiones:

```powershell
python -m mkdocs --version
pip show mkdocs-material
```

## Ejecutar sin mostrar la advertencia

La advertencia puede ocultarse mediante la variable oficial indicada por Material for MkDocs.

=== "Script incluido"

    ```powershell
    .\serve.ps1
    ```

=== "PowerShell manual"

    ```powershell
    $env:NO_MKDOCS_2_WARNING = "1"
    python -m mkdocs serve
    ```

=== "macOS o Linux"

    ```bash
    export NO_MKDOCS_2_WARNING=1
    python -m mkdocs serve
    ```

!!! warning "Ocultarla no cambia la compatibilidad"
    La variable solamente silencia el aviso. La protección real es mantener `mkdocs<2` y utilizar versiones controladas en `requirements.txt`.

## Ejecutar y validar

```powershell
.\serve.ps1
```

Abre:

```text
http://127.0.0.1:8000
```

Para validar enlaces, navegación y configuración:

```powershell
$env:NO_MKDOCS_2_WARNING = "1"
python -m mkdocs build --strict
```

## Solución de problemas

| Mensaje | Significado | Acción |
| --- | --- | --- |
| `Serving on 127.0.0.1:8000` | Servidor iniciado. | Abrir la dirección en el navegador. |
| `Browser connected` | El navegador usa recarga automática. | Ninguna acción necesaria. |
| `Address already in use` | El puerto 8000 está ocupado. | Ejecutar `python -m mkdocs serve -a 127.0.0.1:8001`. |
| `Config value 'theme': Unrecognised theme name 'material'` | El tema no está instalado en el entorno activo. | Activar `.venv` y reinstalar `requirements.txt`. |
| `ModuleNotFoundError` | Falta una dependencia. | Reinstalar el entorno virtual. |
| Enlaces rotos durante `--strict` | Archivo o ruta incorrecta. | Corregir el enlace Markdown o la navegación. |

## Referencias oficiales

- [Material for MkDocs: instalación](https://squidfunk.github.io/mkdocs-material/getting-started/)
- [Material for MkDocs: aviso sobre MkDocs 2.0](https://squidfunk.github.io/mkdocs-material/blog/2026/02/18/mkdocs-2.0/)
- [Material for MkDocs: historial de versiones](https://squidfunk.github.io/mkdocs-material/changelog/)
- [MkDocs: instalación](https://www.mkdocs.org/user-guide/installation/)

Regresa al [inicio](index.md).
