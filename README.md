# Guía DevOps Cloud con AWS y Cloudflare

Sitio de documentación creado con MkDocs Material para introducir a un profesional DevOps en el uso de AWS y Cloudflare para desplegar, proteger y operar aplicaciones web.

## Contenido

- Fundamentos de cloud, redes, IAM, DNS, CDN y TLS.
- Arquitecturas recomendadas para sitios estáticos y aplicaciones dinámicas.
- Primer despliegue con AWS, Nginx y Cloudflare.
- Seguridad, observabilidad, copias de respaldo y control de costos.
- Instalación de MkDocs y explicación de la advertencia sobre MkDocs 2.0.

## Ejecutar en Windows

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
.\serve.ps1
```

Abre `http://127.0.0.1:8000`.

También puedes ejecutar directamente:

```powershell
$env:NO_MKDOCS_2_WARNING = "1"
python -m mkdocs serve
```

## Ejecutar en macOS o Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
./serve.sh
```

## Validar el proyecto

```bash
python -m mkdocs build --strict
```

La carpeta `site/` es generada automáticamente y no debe versionarse.
