# Third-Party Notices

This repository (`omero-arm64-docker`) is licensed under Apache-2.0. It contains only build scripts, configuration files, and Dockerfiles. It does **not** redistribute any third-party source code or binaries.

The Docker images **built** from this repository incorporate third-party software. Their licenses and source locations are listed below.

---

## omero-server-docker

- **License**: BSD 2-Clause
- **Copyright**: Copyright (c) 2015, Open Microscopy Environment
- **Source**: https://github.com/ome/omero-server-docker

The Dockerfiles in this repository are derived from the upstream Dockerfiles in that project and modified for ARM64 (aarch64) compatibility.

---

## omero-web-docker

- **License**: BSD 2-Clause
- **Copyright**: Copyright (c) 2015, Open Microscopy Environment
- **Source**: https://github.com/ome/omero-web-docker

The Dockerfiles in this repository are derived from the upstream Dockerfiles in that project and modified for ARM64 (aarch64) compatibility.

---

## OMERO.server / OMERO.web

- **License**: GNU General Public License v2.0 (GPL-2.0)
- **Copyright**: Copyright (C) 2006-present, Open Microscopy Environment
- **Source**: https://github.com/ome/openmicroscopy

OMERO server and web software are downloaded at image build time via the `omego` installer and Ansible roles. They are not included in this repository.

---

## ZeroC Ice 3.6.5

- **License**: GNU General Public License v2.0 with exceptions (see ICE_LICENSE in the source)
- **Source**: https://github.com/zeroc-ice/ice/tree/v3.6.5

The `omero-server.Dockerfile` compiles Ice 3.6.5 C++ from source during the build stage. The Ice source code is not included in this repository; it is cloned at build time. The compiled binaries are present only in the resulting Docker image.

---

## OME web apps (omero-web-standalone image)

- **License**: GNU Affero General Public License v3.0 (AGPL-3.0)
- **Copyright**: Open Microscopy Environment; German BioImaging (autotag, tagsearch)
- **Sources**:
  - OMERO.figure: https://github.com/ome/omero-figure
  - OMERO.iviewer: https://github.com/ome/omero-iviewer
  - omero-mapr: https://github.com/ome/omero-mapr
  - omero-parade: https://github.com/ome/omero-parade
  - omero-fpbioimage: https://github.com/ome/omero-fpbioimage
  - omero-autotag: https://github.com/German-BioImaging/omero-autotag
  - omero-tagsearch: https://github.com/German-BioImaging/omero-tagsearch

Installed from PyPI at image build time into the `omero-web-standalone` image. The OMERO.figure export script (`Figure_To_Pdf.py`, with its fonts) is also copied from the OMERO.figure wheel into the `omero-server` image. None of these are included in this repository.

---

## dumb-init

- **License**: MIT
- **Source**: https://github.com/Yelp/dumb-init

Downloaded at image build time as a pre-compiled binary from GitHub Releases.
