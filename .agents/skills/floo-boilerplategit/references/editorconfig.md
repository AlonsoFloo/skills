# EditorConfig Reference Template (`.editorconfig`)

```ini
# ------------------------------------------------------------------------------
# | EditorConfig
# ------------------------------------------------------------------------------

# Top-most EditorConfig file
root = true

# ---
# | General Settings
# ---
[*]
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
charset = utf-8
indent_style = space
indent_size = 4

# ---
# | EXTENSION Settings
# ---
# YAML Files
[*.{yml,yaml}]
indent_size = 2

[*.{md,mdx}]
indent_size = 2

[*.{json,json5}]
indent_size = 2

[*.{xml,config}]
indent_size = 2

[*.{cmd,bat}]
end_of_line = crlf

[*.sh]
end_of_line = lf

[Makefile]
indent_style = tab
```
