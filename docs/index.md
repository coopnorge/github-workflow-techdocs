# TechDocs GitHub Actions Workflow

Publish your TechDocs to Inventory.

Expect breaking changes until version 1.

## Usage

In your repository you will need the following:

### Content and TechDocs configuration

#### Documentation

A `docs` directory containing at least an `index.md` file. The `docs` directory
must be in the same directory as the `mkdocs.yml` file.

#### Component descriptor

```yaml title="catalog-info.yaml"
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: mycomponent
  title: My Component
  description: What my component does
  annotations:
    github.com/project-slug: coopnorge/mycomponent-repository
    backstage.io/techdocs-ref: dir:. # This is where TechDocs looks for mkdocs.yml
spec:
  type: library
  lifecycle: experimental
  owner: platform-guild
```

For more information about see [Descriptor Format of Catalog Entities].

#### MkDocs configuration

The centrally managed
[`mkdocs.yml`](https://github.com/coopnorge/engineering-docker-images/blob/main/images/techdocs/context/mkdocs.yml)
provides a default configuration and a set up of bundled plugins.

For more information see: [Creating and publishing your docs].

#### markdownlint configuration

```yaml title=".markdownlint.yaml"
---
default: true

# MD007/ul-indent - Unordered list indentation
MD007:
  indent: 4  # This is what works in mkdocs

MD033: false

# MD046/code-block-style - Code block style
MD046: false


MD013:
  code_blocks: false
  tables: false

MD024:
  allow_different_nesting: true

# MD029- ignored to prevent errors when using code blocks within ordered lists
MD029: false
```

#### Vale configuration

```ini title=".vale.ini"
[*.md]
TokenIgnores = [\w][\w.]*@[\w*][\w.]*[\w]
BasedOnStyles = Vale
```

### Workflow configuration

```yaml title=".github/workflows/techdocs.yaml"
name: TechDocs
on:
  push: {}
jobs:
  techdocs:
    permissions:
      contents: read
      id-token: write
      packages: read
    name: TechDocs
    uses: coopnorge/github-workflow-techdocs/.github/workflows/techdocs.yaml@v0
```

### Inputs

<!-- markdownlint-disable MD013 -->
| Name                                | Type     | Required | Description                                                                                    | Default Value                                                                                             |
|-------------------------------------|----------|----------|------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| `docs_dir`                          | `string` | `false`  | The directory where the documentation files are stored, relative to the root of the repository | `docs/`                                                                                                   |
| `docker-compose-service`            | `string` | `false`  | The `docker compose` service hosting the [TechDocs Engineering Image]                          | `techdocs`                                                                                                |
| `techdocs_bucket`                   | `string` | `false`  | The name of the TechDocs Cloud Storage Bucket                                                  | `coop-techdocs-backstage-production-44f7`                                                                 |
| `workload_identity_provider`        | `string` | `false`  | Workload Identity Federation Provider, used for debugging                                      | `projects/1063410054216/locations/global/workloadIdentityPools/techdocs-pool/providers/techdocs-provider` |
| `workload_identity_service_account` | `string` | `false`  | Service Account that can managed the Cloud Storage Bucket, used for debugging                  | `techdocs-publisher@backstage-production-44f7.iam.gserviceaccount.com`                                    |
<!-- markdownlint-enable MD013 -->

[Creating and publishing your docs]: https://backstage.io/docs/features/techdocs/creating-and-publishing
[Descriptor Format of Catalog Entities]: https://backstage.io/docs/features/software-catalog/descriptor-format
[TechDocs Engineering Image]: https://github.com/coopnorge/engineering-docker-images/tree/main/images/techdocs
