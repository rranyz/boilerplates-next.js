# boilerplates-next.js

A collection of customized Next.js (JavaScript/TypeScript) applications and shared packages, managed in a single Git repository.

This repository currently contains only root-level Git and editor configuration. No applications have been scaffolded yet.

## Directory structure

The planned layout (these directories are created as needed):

```text
.
├── .editorconfig     # editor conventions (UTF-8, LF, 2-space indent)
├── .gitattributes    # LF-only line endings, binary file handling
├── .gitignore        # ignore rules for the whole repository
├── README.md
├── apps/             # Next.js applications, e.g. website/, admin/, dashboard/
├── packages/         # shared code, e.g. ui/, config/, shared/
└── docs/             # documentation
```

## Git configuration files

| File | Purpose |
|------|---------|
| `.gitignore` | Ignores dependencies, Next.js/build output, caches, coverage, logs, env secrets, and IDE/OS files at **any depth**. Lockfiles are tracked. |
| `.gitattributes` | Forces LF line endings for all text files and marks images, fonts, archives, and compiled files as binary. |
| `.editorconfig` | Tells editors to use UTF-8, LF, 2-space indentation, a final newline, and no trailing whitespace. |

All three live **only at the repository root**. Nested apps and packages inherit them automatically.

## LF-only policy

Every text file in this repository uses LF (`\n`) line endings, on every OS.

- `.gitattributes` sets `* text=auto eol=lf` plus explicit `text eol=lf` rules for known file types. Git stores text as LF and checks it out as LF, regardless of each developer's `core.autocrlf`.
- Binary files (`*.png`, `*.woff2`, `*.zip`, ...) are marked `binary`, so they are never converted.
- `.editorconfig` sets `end_of_line = lf`, so editors write LF to begin with.

Recommended on Windows (keeps Git from introducing CRLF in files that aren't covered by attributes):

```bash
git config --global core.autocrlf input
```

If a file was committed with CRLF before these rules existed, renormalize it:

```bash
git add --renormalize .
git status   # review, then commit
```

## Adding a new Next.js application

1. Create the app inside `apps/` (or a shared package inside `packages/`), e.g.:

   ```bash
   npx create-next-app@latest apps/<name> --disable-git
   ```

2. **Delete the app's generated `.gitignore`** (`rm apps/<name>/.gitignore`). The root `.gitignore` already covers everything it contains.
3. Make sure there's **no nested `.git` directory** in the app (`ls -a apps/<name>`). Every app belongs to this single repository.
4. Commit the app's lockfile (`package-lock.json`, `pnpm-lock.yaml`, or `yarn.lock`).
5. Never commit real secrets. Commit a `.env.example` template instead.

## How nested applications inherit root rules

Git applies a `.gitignore` to its own directory **and all subdirectories**. Patterns without a leading `/` (e.g. `node_modules/`, `.next/`, `.env`) match at any depth, so `apps/admin/node_modules/` and `packages/ui/dist/` are ignored by the single root file. `.gitattributes` and `.editorconfig` work the same way.

### Extending `.gitignore` for one app

Add path-scoped rules to the **"App-specific additions"** section at the bottom of the root `.gitignore` rather than creating a nested file:

```gitignore
apps/admin/generated/
packages/ui/storybook-static/
```

To track something a general rule ignores, re-include it with `!` and a scoped path. This only works if the parent directory isn't itself ignored:

```gitignore
!packages/config/build/
!apps/website/.env.production
```

### Known trade-offs

- `build/`, `out/`, `dist/`, `logs/`, `tmp/`, and `temp/` are ignored at every depth. A source folder with one of those names needs a scoped `!` exception.
- All `.env.*` files are ignored, including `.env.development` and `.env.production`, which Next.js allows committing. Add an explicit exception if you really want to commit one.
- LF-only also applies to any future Windows `.bat`/`.cmd` scripts. If CRLF is ever required for them, add `*.{bat,cmd} text eol=crlf` to `.gitattributes`.

## Checking ignore rules and line endings

```bash
# Is a path ignored, and by which rule? (no output + exit 1 = not ignored)
git check-ignore -v apps/admin/node_modules/example
git check-ignore -v apps/admin/.env.local

# Which line-ending attributes apply to a path?
git check-attr text eol -- apps/admin/src/example.ts

# Line endings of tracked and new files (i/ = index, w/ = working tree)
git ls-files --eol -co --exclude-standard

# Your local autocrlf setting
git config --get core.autocrlf
```
