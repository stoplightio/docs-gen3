# Analyzer and file formats

The Analyzer CLI tool makes some assumptions about your project structure and file names for your specification files.

## Filenames

You can include the following in your specification filename, in this order:

```
$name.$version.$spec.$format
```

- Name: required
- Version: optional. Starts with a lowercase `v`. Separate major and minor version numbers with a hyphen `-`. For example, `v1` or `v1-1-0`.
<!-- TODO: update info on version if it becomes compulsory, e.g. if we decide to ignore version numbers from the info block in the spec -->
- Spec: optional, but recommended. Stoplight supports `oas` and `schema`.
- Format: required. Stoplight supports `md`, `json`, `yaml` and `yml`.

### Examples

#### OpenAPI spec (v2 or v3):

```
# Without versioning
my-api.oas.json
my-api.oas.yaml

# With versioning
my-api.1-0.oas.json
my-api.1-0.oas.yaml
```

#### JSON schema spec:

```
# Without versioning
my-api.schema.json

# With versioning
my-api.1-0.schema.json
```

#### Markdown files

Markdown files are source files for documentation. Do not use markdown to write your API specification.
<!-- QUERY: is this ^ correct? -->

```
# Without versioning
intro.md

# With versioning
intro.v1-1.md
```


## Project structure

<!-- TODO -->







## Project structure