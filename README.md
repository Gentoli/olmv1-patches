# OLMv1 Compatibility Catalog Patches

This repository automates the extraction, modification, and delivery of OLMv0 catalogs to make them compatible with OLMv1.

## How it works

1. **Extraction**: The GitHub Action performs a single-pass `opm render` on the base OLMv0 catalog.
2. **Filtering**: It automatically extracts only the packages that have a corresponding `.yaml` file in the `packages/` directory.
3. **Patching**: It applies JSON patches to the extracted objects to resolve OLMv1 compatibility issues. Rules for `pkgA.yaml` are strictly scoped to resources belonging to `pkgA`.
4. **Delivery**: It builds a single, unified catalog image and pushes it to GHCR. The image path is dynamically generated based on the repository owner.

## Adding a New Package

Create a file named `<package-name>.yaml` in the `packages/` directory.

### Patch Format

Each file is an array of rules. Each rule consists of a `target` (selector) and a `patch` (operations).

```yaml
- target:
    schema: olm.bundle
  patch:
    - op: replace
      path: /image
      value: "quay.io/my-org/my-bundle:v1.2.3"
    # Custom 'remove-items' extension for OLM properties
    - op: remove-items
      path: /properties
      match:
        type: olm.gvk.required
```

## Operations

- **Standard RFC 6902**: Supports `add`, `remove`, `replace`, `move`, `copy`, and `test`.
- **`remove-items` (Extension)**: Specifically designed for OLM arrays (like `properties` or `relatedImages`). It removes elements from an array that match the key/value pairs in `match`.
