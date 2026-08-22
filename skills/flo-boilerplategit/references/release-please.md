# Release Please Reference Template

## `release-please-config.json`

Adapt the type of release with the current repo needs

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "packages": {
    ".": {
      "release-type": "simple",
    }
  }
}
```

## `.release-please-manifest.json`

```json
{
  ".": "1.0.0"
}
```
