# Flutter App Template

A [Copier](https://copier.readthedocs.io) template for per-app files
shared by solidui Flutter apps (radiopod, innerpod, billipod, ...). It
replaces the previous approach of hard links into
`~/projects/scripts`. Each app now keeps ordinary copies of the files,
rendered with its own values, and pulls template changes in when it
chooses.

For both apps and packages (`kind`) the template covers the files
common to every project, copied unchanged:

+ `.github/ISSUE_TEMPLATE/*.md` and `.github/pull_request_template.md`
+ `.githooks/` `pre-commit` and `pre-push` (enabled with `git config
  core.hooksPath .githooks`)
+ `.gitignore`, `.lycheeignore`, `dart_dependency_validator.yaml`
+ `support/` `flutter.mk`, `git.mk`, `modules.mk`, `loc.sh`,
  `meld_zip_from_claude.sh`
+ `Makefile`, with the installer host, folder and URL from `install`,
  obfuscation when `build.secrets` is set, and the web install
  (`install.prod`) as part of `make ginstall`
+ `android/` gradle files, when the answers set `android`

+ `.github/workflows/ci.yaml`, a short caller of this repository's
  reusable `ci.yaml`

For an app (`kind: app`) it also covers, with the app's values:

+ `.github/workflows/installers.yaml`, a short caller of this
  repository's reusable `installers.yaml`, passing the signing secrets
  and the app's `build.secrets` by name
+ `snap/snapcraft.yaml`
+ `snap/gui/<app>.desktop`
+ `flatpak/com.togaware.<app>.yml`, `.desktop` and `.metainfo.xml`, only
  when the answers set `flatpak` (`{}` for a plain Flatpak)
+ `installers/deb.sh`, for `make deb` and, with `--bundle`, for the
  installers workflow
+ `installers/update.sh`, uploading the workflow's installers to
  `install.host`, and `installers/Makefile`
+ `support/install.mk`, the web install
+ `installers/README.md`, the download page, with sections only for the
  installers the app has
+ `INSTALL.md`, the install guide the CHANGELOG links to on GitHub
  Pages, listing the installers the app has and the stores set in
  `stores` (`play`, `snap`)
+ `license.dart`, GPL for an app and MIT for a package, with the
  `copyright` year and owner
+ `installers/app-windows.iss`, the Inno Setup script, only when the
  answers set `windows.appid`

## Adopting a file the app already has

The first update that brings in a file the app already holds, such as an
existing `flatpak/` manifest, marks the whole file as a conflict, since
the template has no earlier version to merge from. Take the template's
version and add back what is particular to the app as local edits; from
then on updates merge normally.

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
make update
```

which runs `copier update --defaults`. It replaces `support/update.sh`
and its hard links into `~/projects/scripts`.

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

## Reusable workflows

The jobs of the lint checks and the installer builds live once, in this
repository's `.github/workflows/`, triggered only by `workflow_call`. Each
app's rendered caller pins them to the template version the app was last
updated to, so `make update` moves the app's files and its workflows
together. A fix to a workflow reaches an app with its next `make update`.

