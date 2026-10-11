# Gitignore Reference Template (`.gitignore`)

Gitignore file must be fetch based on the API [https://www.toptal.com/developers/gitignore/api/](https://www.toptal.com/developers/gitignore/api/)
A basic example is [https://www.toptal.com/developers/gitignore/api/visualstudiocode,macos,linux,windows](https://www.toptal.com/developers/gitignore/api/visualstudiocode,macos,linux,windows)

## Rule 1: Scan the current repository

Scan the current repository for software, tools, language and pattern.
Load the Toptal api with the base key + the local repo needs :

Base keys :

- visualstudiocode
- macos
- linux
- windows
- zsh

## Rule 2: Merge with existing

Check the exisitng gitignore, and merge it with the provided result of Toptal API

## Rule 3: Formatting

The local and Toptal config must be explicit the final format must be like this example

```gitignore
################
## REPO NEED
################
# --------------------------------
# Some configs
# --------------------------------
[....]


# --------------------------------
# APM
# --------------------------------
[....]


################
## GENERATED
################
{Toptal output}
```
