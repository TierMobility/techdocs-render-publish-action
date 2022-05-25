# TechDocs Render & Publish Action

A GitHub Action that extracts entity information from a [Backstage](https://backstage.io/) catalog file, renders the associated [TechDocs](https://backstage.io/docs/features/techdocs/techdocs-overview) files and publishes the rendered files to a cloud storage (AWS S3 / GCS bucket).

Built on top of [tier-mkdocs-techdocs-core](https://github.com/TierMobility/tier-mkdocs-techdocs-core) plugin, an oppinionated fork of the [mkdocs-techdocs-core](https://github.com/backstage/mkdocs-techdocs-core) plugin. Makes use of [yuzutech/kroki](https://hub.docker.com/r/yuzutech/kroki) and [yuzutech/kroki-mermaid](https://hub.docker.com/r/yuzutech/kroki-mermaid) docker containers to render textual diagram descriptions into image files.

The `metadata.name` and `metadata.namespace` information of the `techdocs-ref` annotated entity is automatically parsed from the YAML file provided via the `path` parameter. **NOTE**: This limits the number of allowed `techdocs-ref` annotated entities matching the kind provided via the `entity_kind` parameter to **one** (in this particular file only, of course).

## Inputs

| Name                             | Description                                                       | Required | Default             |
| -------------------------------- | ----------------------------------------------------------------- | -------- | ------------------- |
| `path`                           | Path to catalog file containing the techdocs-ref annotated entity | ❌       | `catalog-info.yaml` |
| `entity_kind`                    | Entity kind that contains the techdocs-ref annotation             | ❌       | `Component`         |
| `bucket_name`                    | Name of the AWS S3/GCS bucket to publish to                       | ✅       | -                   |
| `aws_access_key_id`              | AWS S3 bucket access key ID                                       | ❌       | -                   |
| `aws_secret_access_key`          | AWS S3 bucket secret access key                                   | ❌       | -                   |
| `aws_region`                     | AWS S3 bucket secret access key                                   | ❌       | -                   |
| `google_application_credentials` | Google Cloud authentication key                                   | ❌       | -                   |

## Usage

The following examples demonstrate how to use this action in a GitHub Actions workflow. We provide support for publishing to two separate backends, AWS S3 and GCS. Make sure your Backstage is [configured](https://backstage.io/docs/features/techdocs/using-cloud-storage) to access the buckets your are publishing to.

### Publishing to AWS S3

To configure access to your S3 bucket, you will need to provide the `bucket_name`, `aws_access_key_id`, `aws_secret_access_key` and `aws_region` input parameters. We recommend using [Action secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) to store those variables.

```yaml
name: Render & Publish TechDocs (AWS S3 Example)

on:
  push: # example trigger, detects changes to TechDocs
    branches: [main]
    paths:
      - "docs/**"
      - "mkdocs.yml"

jobs:
  techdocs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: TierMobility/techdocs-render-publish-action@main
        with:
          path: catalog-info.yaml
          entity_kind: Component
          bucket_name: ${{ secrets.TECHDOCS_S3_BUCKET_NAME }}
          aws_access_key_id: ${{ secrets.TECHDOCS_AWS_ACCESS_KEY_ID }}
          aws_secret_access_key: ${{ secrets.TECHDOCS_AWS_SECRET_ACCESS_KEY }}
          aws_access_key_id: ${{ secrets.TECHDOCS_AWS_ACCESS_KEY_ID }}
```

### Publishing to GCS

To configure access to your Google Cloud Storage bucket, you will need to provide the `bucket_name` and the `google_application_credentials` input parameters. We recommend using [Action secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) to store those variables.

```yaml
name: Render & Publish TechDocs (GCS Example)

on:
  push: # example trigger, detects changes to TechDocs
    branches: [main]
    paths:
      - "docs/**"
      - "mkdocs.yml"

jobs:
  techdocs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: TierMobility/techdocs-render-publish-action@main
        with:
          path: catalog-info.yaml
          entity_kind: Component
          bucket_name: ${{ secrets.TECHDOCS_S3_BUCKET_NAME }}
          google_application_credentials: ${{ secrets.TECHDOCS_GOOGLE_APPLICATION_CREDENTIALS }}
```
