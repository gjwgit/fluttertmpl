# Flutter App Template

A [Copier](https://copier.readthedocs.io) template for the per-app files
shared by Graham's Flutter apps (radiopod, innerpod, billipod, ...). It
replaces the hard links into `~/projects/scripts`: each app keeps
ordinary copies of the files, rendered with its own values, and pulls
template changes in when it chooses.

So far the template covers:

+ `snap/snapcraft.yaml`
+ `snap/gui/<app>.desktop`

## Install Copier

```bash
sudo apt install pipx
pipx install copier
```

## Add an app

From the app's root folder, with its values in a YAML data file, and the
destination given as an absolute path, since the app name defaults to
the destination folder's name:

```bash
copier copy --data-file app.yaml gh:gjwgit/fluttertmpl "$(pwd)"
```

The values are recorded in `.copier-answers.yml`, which is committed
with the app. The data file is no longer needed.

## Update an app

With a clean git tree:

```bash
copier update --defaults
```

Template changes since the app's recorded version are merged into the
app's files. Changes made locally in the app are kept, and a clash is
marked as a git style conflict.

To change a value, edit `.copier-answers.yml` and run `copier update`,
or pass it directly:

```bash
copier update --defaults --data 'comment=New desktop comment'
```

## Release a template change

Commit, then tag the release. Apps update to the latest tag.

```bash
git tag v0.2.0
git push --tags
```
