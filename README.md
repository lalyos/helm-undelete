helm plugin to recover from `helm uninstall --keep-history`

## Usage

```
helm undelete
```

## tl;dr

helm release info is stored in secrets. Th secret is a huge gzipped json with:
- user supplied values
- the original chart templates
- the generated manifests

if you uninstall with `--keep-history` than resources are removed but the release secret is left behind. `helm ls` wont show uninstalled releases bu you can still list them

```
k get secrets -l owner -L status
```

uninstall does 3 things:
- deletes all resources contained in a release
- labels the secrete as `status=uninstalled`
- changes the secret json data:
  - .info.status = uninstalled
  - .info.description = Uninstallation complete
  - .info.deleted = timestamp

to recover from an uninstall you need to revert those 3 things.
