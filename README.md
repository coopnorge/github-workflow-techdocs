# TechDocs GitHub Actions Workflow

Publish your TechDocs to Inventory.

Expect breaking changes until version 1.

## Usage

In your repository add these 3 files.

`.github/workflow/techdocs.yaml`
```yaml
---
name: TechDocs
on:
  push:
    banches:
      - main
  pull_request: {}
jobs:
  techdocs:
    permissions:
      contents: read
      id-token: write
      packages: read
    name: TechDocs
    uses: coopnorge/github-workflow-techdocs/.github/workflows/techdocs.yaml@v0
```

`docker-compose.yaml`
```yaml
services:
  techdocs:
    build:
      context: docker-compose
      dockerfile: Dockerfile
      target: techdocs
    working_dir: /content
    environment:
      GOOGLE_APPLICATION_CREDENTIALS: ${GOOGLE_APPLICATION_CREDENTIALS:-}
      GCLOUD_PROJECT: ${GCLOUD_PROJECT:-}
    volumes:
      - .:/content
      - ${XDG_CACHE_HOME:-xdg-cache-home}:/root/.cache
      - $HOME/.config/gcloud:/root/.config/gcloud
      - ${GOOGLE_APPLICATION_CREDENTIALS:-nothing}:${GOOGLE_APPLICATION_CREDENTIALS:-/tmp/empty-GOOGLE_APPLICATION_CREDENTIALS}
    ports:
      - "127.0.0.1:3000:3000/tcp"
      - "127.0.0.1:8000:8000/tcp"
volumes:
  xdg-cache-home: { }
  nothing: { }
```

`docker-compose/Dockerfile`
```Dockerfile
FROM ghcr.io/coopnorge/engineering-docker-images/e0/techdocs:latest@sha256:709cbdadb158616ea681e042d9f48f4eeafe574e7265c1765e3de38de7109dec as techdocs
```

### Inputs

| Name                                | Type     | Required | Description                                                                                    | Default Value                                                                                             |
|-------------------------------------|----------|----------|------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| `docs_dir`                          | `string` | `false`  | The directory where the documentation files are stored, relative to the root of the repository | `docs/`                                                                                                   |
| `docker-compose-service`            | `string` | `false`  | The `docker compose` service hosting the [TechDocs Engineering Image]                          | `techdocs`                                                                                                |
| `techdocs_bucket`                   | `string` | `false`  | The name of the TechDocs Cloud Storage Bucket                                                  | `coop-techdocs-backstage-production-44f7`                                                                 |
| `workload_identity_provider`        | `string` | `false`  | Workload Identity Federation Provider, used for debugging                                      | `projects/1063410054216/locations/global/workloadIdentityPools/techdocs-pool/providers/techdocs-provider` |
| `workload_identity_service_account` | `string` | `false`  | Service Account that can managed the Cloud Storage Bucket, used for debugging                  | `techdocs-publisher@backstage-production-44f7.iam.gserviceaccount.com`                                    |

[TechDocs Engineering Image]: https://github.com/coopnorge/engineering-docker-images/tree/main/images/techdocs
