# Gitconfig & Git Attributes Reference (`.gitattributes` & `.gitconfig`)

## `.gitattributes`

```gitattributes
# Auto-detect text files and normalize line endings to LF
* text=auto eol=lf

# Explicitly declare binary files
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
```

## `.gitconfig` Reference

```ini
[core]
    autocrlf = input
    whitespace = error-trailing-space,space-before-tab
[init]
    defaultBranch = main
```
