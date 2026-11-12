# OMERO ARM64 Docker

ARM64-native Docker images and stack for [OMERO](https://www.openmicroscopy.org/omero/) on Apple Silicon (M1/M2/M3/M4) and other aarch64 hosts.

Official OMERO images are x86_64-only. This project builds native `linux/arm64` images by:
- compiling ZeroC Ice 3.6.5 from source (no pre-built aarch64 tarball exists)
- patching upstream OME Ansible roles for aarch64
- consolidating `dnf` layers for minimal image size

Pre-built images are published to GHCR:

| Image | Content |
|---|---|
| `ghcr.io/simonhard/omero-server-arm64` | OMERO.server, incl. the OMERO.figure export script (PDF/TIFF) |
| `ghcr.io/simonhard/omero-web-arm64` | OMERO.web |
| `ghcr.io/simonhard/omero-web-standalone-arm64` | OMERO.web plus the OME web apps of the upstream `omero-web-standalone` image: OMERO.iviewer (default viewer), OMERO.figure, mapr, parade, fpbioimage, autotag, tagsearch |

## Versions

| Release | OMERO.server | OMERO.web | ZeroC Ice | Web apps (standalone) |
|---|---|---|---|---|
| v1.2.0 | 5.6.18 | 5.33.3 | 3.6.5 | figure 8.0.1, iviewer 0.20.0, mapr 0.5.3, parade 0.2.4, fpbioimage 0.4.1, autotag 4.3.0, tagsearch 4.3.0 |
| v1.1.0 | 5.6.18 | 5.33.3 | 3.6.5 | – |
| v1.0.x | 5.6.17 | newest on PyPI at build time | 3.6.5 | – |

Official OME images for these versions are still `linux/amd64` only (checked 2026-11-11).

## Architecture

Four containers orchestrated via Docker Compose:

```
nginx (:8094 / :8443)
  └── omero-web (:4080)
        └── omero-server (:4063/:4064)
              └── postgres (:5432)
```

## Quick start (pre-built images)

Requires: Docker Desktop (arm64 mode), ~5 GB disk.

You must create a `.env` file before starting the stack: Docker Compose will not start without the required variables. Copy `.env.example` to `.env`, then set the passwords as in the steps below.

```bash
git clone https://github.com/simonhard/omero-arm64-docker.git
cd omero-arm64-docker

cp .env.example .env
# Edit .env: set OMERO_DB_PASS and OMERO_ROOT_PASS

docker compose up -d
```

OMERO.web: **http://localhost:8094**

To use the web apps (OMERO.iviewer, OMERO.figure, ...), set the standalone image in `.env`:

```
OMERO_WEB_IMAGE=ghcr.io/simonhard/omero-web-standalone-arm64:latest
```

OMERO.figure export to PDF/TIFF works out of the box: the export script is part of the server image.

First startup takes a few minutes while OMERO initialises the database.

## Build images locally

Requires additionally: Python 3, Git, ~20 GB disk, ~20–40 min.

```bash
# Build all images (linux/arm64 by default)
python3 build.py

# Options
python3 build.py --omero-server-version 5.6.18
python3 build.py --omero-web-version 5.33.3
python3 build.py --platform linux/amd64
python3 build.py --server-only
python3 build.py --no-standalone   # skip omero-web-standalone
python3 build.py --help
```

After building, use the locally tagged images by adding to `.env`:

```
OMERO_SERVER_IMAGE=ghcr.io/simonhard/omero-server-arm64:latest
OMERO_WEB_IMAGE=ghcr.io/simonhard/omero-web-arm64:latest
# or: OMERO_WEB_IMAGE=ghcr.io/simonhard/omero-web-standalone-arm64:latest
```

## Optional TLS

Uncomment the HTTPS server block in `nginx.conf`, place your certificates in `./certs/`, and add the volume mount to the nginx service in `docker-compose.yml`:

```yaml
volumes:
  - ./nginx.conf:/etc/nginx/nginx.conf:ro
  - ./certs:/etc/nginx/certs:ro
```

## Key files

| File | Purpose |
|------|---------|
| `build.py` | Clone upstream repos, build ARM64 images |
| `dockerfiles/omero-server.Dockerfile` | Two-stage build: Ice from source + OMERO.server + OMERO.figure export script |
| `dockerfiles/omero-web.Dockerfile` | Targets `omero-web` (OMERO.web with WhiteNoise static files) and `omero-web-standalone` (plus web apps) |
| `docker-compose.yml` | Stack orchestration |
| `nginx.conf` | Reverse proxy (10G upload, 600s timeout) |
| `.env.example` | Credentials template |
| `.env` | Local credentials (not in git); copy from `.env.example` |

## Releasing a new version

1. Bump the versions: `OMERO_VERSION` (server) and `OMERO_WEB_VERSION` (web) in `dockerfiles/`, `build.py`, both workflows in `.github/workflows/`, and the table under *Versions*. Web app versions are the `OMERO_*_VERSION` build args of the `omero-web-standalone` stage; keep `OMERO_FIGURE_VERSION` in the server Dockerfile in sync (export script).
2. Merge via pull request once CI is green.
3. Push a version tag to trigger the release workflow and push new images to GHCR:

```bash
git tag v1.2.0
git push origin v1.2.0
```

## License

This project (scripts, Dockerfiles, configuration) is licensed under **Apache-2.0**.

The Docker images built from this project incorporate third-party software under different licenses (GPL-2.0 for OMERO and ZeroC Ice, BSD-2-Clause for upstream Docker wrappers). See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for details.

## Acknowledgements

- [Open Microscopy Environment](https://www.openmicroscopy.org/) for OMERO and the upstream Docker repos
- [ZeroC](https://zeroc.com/) for Ice 3.6.5
