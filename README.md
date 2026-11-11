# charts-workspace

Shared local workspace for the HDCharts projects.

This repository is the workspace entrypoint. Clone it first, then use the setup
script to clone the projects you want to work on.

## Clone Repositories

From this repository:

```bash
scripts/clone-repos.sh
```

The script asks which projects to clone and places them as siblings
alongside this repository in the parent directory:

```text
..
├── charts-workspace/
├── charts/
├── charts-docs/
├── ...
```

To clone everything without a prompt:

```bash
scripts/clone-repos.sh --all
```

To use SSH clone URLs:

```bash
scripts/clone-repos.sh --ssh
```

To preview what would be cloned:

```bash
scripts/clone-repos.sh --dry-run
```

## VS Code Workspace

After cloning the projects, open the multi-root workspace:

```bash
code charts-workspace.code-workspace
```

The workspace includes this repository and every project configured by the
clone script. VS Code then shows each repository separately in the Source
Control panel, including the repository name and its changed files.

If a project has not been cloned yet, run the clone script and reload the
workspace.

The workspace also provides a terminal profile for each repository on macOS
and Linux. Use the dropdown next to the new-terminal button and choose a
repository. Each terminal starts in that repository's directory, and you can
open as many repository terminals as needed. VS Code restores those terminals
the next time the workspace is opened. Open the desired terminals once; close
a terminal when it should no longer be restored.

## Repositories

The setup script currently knows about:

| Repository | Purpose | Link |
| --- | --- | --- |
| `charts` | HDCharts core library | [Maven Central](https://central.sonatype.com/artifact/io.github.dautovicharis/charts/overview), [Demo](https://charts.hdcode.dev/demo) |
| `charts-docs` | Documentation site | [Documentation](https://charts.hdcode.dev/) |
| `charts-gif-recorder` | GIF recorder | [Repository](https://github.com/hdcodedev/compose-gif-recorder) |
| `charts-github-profile` | GitHub organization profile and defaults | [Repository](https://github.com/HDCharts/.github) |
