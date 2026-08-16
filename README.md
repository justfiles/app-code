# Code

Build and run apps directly in JustFiles.

Code is an editor and a toolchain in one app. It holds a gallery of projects, edits their
source over a JustFiles volume, type-checks and compiles a draft, runs it as a live preview,
and installs the finished app.

## The devkit sysroot

Code owns the fixed SDK that a draft compiles and type-checks against. `devkit-sysroot.ts`
writes it to `dist/devkit/` at build time, and `include: ['devkit/**']` carries it into the
app bundle — so a host needs no `node_modules` and no package resolver to build a draft.

The bytes come from `@justfiles/app`, which bakes them into its npm package. Bumping that
dependency is what moves the SDK a draft sees.

## Develop

```sh
pnpm install
pnpm dev
```

`pnpm dev` starts a local host that emulates the JustFiles runtime. The app does not need a
JustFiles installation for local development.

## Check

```sh
pnpm format
pnpm typecheck
pnpm lint:fix
pnpm test
```

## Build

```sh
pnpm build
```

The build writes the app bundle to `dist/`:

| File | Purpose |
| --- | --- |
| `manifest.json` | App identity, version, icon, and capabilities |
| `app.js` | Headless reducer that runs in a Worker |
| `gui.js` | Self-contained browser GUI with React and CSS |
| `icon.png` | Launcher icon |
| `devkit/**` | The SDK sysroot a draft compiles and type-checks against |

The manifest version comes from `package.json`.

## Release

Increase `version` in `package.json` and merge the change to `main`.

The release workflow runs the checks, builds the app, creates `code.zip` from the contents of
`dist/`, attaches build provenance, and creates the matching `v<version>` GitHub release.
