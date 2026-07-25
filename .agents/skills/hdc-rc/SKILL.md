---
name: hdc-rc
description: Run the configured HDCharts binary API compatibility check and generate concise per-PR migration fragments in charts. Use when the user asks to inspect breaking API changes, check snapshot or release compatibility, or update release migration notes.
---

# Check HDCharts release compatibility

Generate incremental migration guidance from the library's configured API
compatibility baseline and reports. Migration fragments accumulate for the
target release even when the compatibility baseline advances. The docs sync
mirrors these fragments unchanged and the docs app assembles them at build
time; do not generate a combined snapshot migration file.

## Guardrails

- Do not commit, push, or open pull requests unless the user explicitly asks.
- Never rewrite or remove migration fragments belonging to another pull
  request.
- Stop before writing when the intended fragment has uncommitted changes.
- Inspect generated reports even when the compatibility command exits nonzero;
  distinguish reported incompatibilities from infrastructure failures.

## Workspace

Work from the `charts-workspace` workspace root. Require `workspace/charts`.

## Workflow

1. Read `workspace/charts/.version`, require
   `<major>.<minor>.<patch>-SNAPSHOT`, remove the suffix to obtain
   `release_version`, and use:

   ```text
   workspace/charts/release-notes/<release_version>/migrations/
   ```

2. Resolve the current `HDCharts/charts` pull request number. Search the target
   migration directory for `<pr-number>-*.md`:
   - when exactly one matching fragment exists, reuse that path;
   - when multiple matching fragments exist, report the ambiguity and stop
     before writing;
   - when no matching fragment exists, resolve a concise kebab-case summary and
     create:

   ```text
   <pr-number>-<short-kebab>.md
   ```

   An explicit user value overrides an inferred value when creating the first
   fragment. When no stable fragment identity can be resolved, complete the
   compatibility inspection but stop before writing.
3. Read the configured baseline ref from
   `workspace/charts/.github/api-compatibility-baseline.txt` for reporting.
4. Remove the generated
   `workspace/charts/build/reports/api-compatibility/` directory so only reports
   from the current run can be considered.
5. Run from `workspace/charts`:

   ```text
   ./gradlew apiCompatibilityCheck --no-daemon --continue
   ```

6. Capture the command's exit status and output. When it exits nonzero,
   distinguish API incompatibilities from build, dependency, tool, and other
   infrastructure failures. If any infrastructure failure occurred, report it
   and stop without changing release notes, even when some reports exist.
7. Read every Markdown report generated under
   `build/reports/api-compatibility/`. Verify that every configured library
   module expected to run produced a current report or was explicitly skipped
   because its baseline artifact does not exist. If the report set is missing,
   stale, or incomplete, report the failure and stop without changing
   release notes.
8. For each module with a breaking change, determine:
   - whether source call sites require edits;
   - the user-visible API change;
   - a minimal Kotlin before/after migration when edits are required.
9. Update only the current pull request's fragment using
   [assets/migration-fragment.md](assets/migration-fragment.md):
   - when breaking modules exist, write one section per module and omit
     inapplicable migration content;
   - when no breaking modules exist and the current pull request already has a
     fragment, remove only that fragment because it is now stale;
   - when no breaking modules exist and no current fragment exists, do not
     modify release notes.
10. Validate that no placeholders remain and that examples match the reports.
11. Report the release version, baseline ref, command result, breaking modules,
    and fragment path or no-write reason.
