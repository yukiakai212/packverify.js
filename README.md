# packverify

Validate your npm package before publishing.

`packverify` simulates how consumers install and load your published package. It packs your project, extracts the published archive into a temporary workspace, creates a temporary consumer project that depends on that tarball, installs it with npm, and validates that your package can be loaded successfully.

## Features

* Validates the actual files that will be published.
* Installs the packed tarball in a temporary consumer project.
* Loads runtime entry points (`main`, `exports`, and optionally `bin`).
* Verifies TypeScript declaration files exist.
* Detects forbidden published files using glob patterns.
* Supports packages with `exports`, `types`, `typings`, and `bin`.
* Resolves glob-based entry declarations before validation.
* Controls lifecycle scripts independently for the pack and install phases.
* Automatically cleans up temporary files after validation.

## Installation

```bash
npm install -D packverify
```

or

```bash
pnpm add -D packverify
```

or

```bash
yarn add -D packverify
```

## Usage

Validate the current project:

```bash
packverify validate
```

Validate another project:

```bash
packverify validate --project ./packages/core
```

Skip runtime validation for executable `bin` entries:

```bash
packverify validate --no-validate-bins
```

Skip lifecycle scripts triggered while packing:

```bash
packverify validate --ignore-pack-scripts
```

Skip lifecycle scripts while installing the packed package:

```bash
packverify validate --ignore-install-scripts
```

Disallow published files:

```bash
packverify validate \
  --forbidden-files "**/*.map" \
  --forbidden-files ".env*"
```

## How it works

`packverify` validates the published package instead of your working directory.

```text
Project
  |
  v
npm pack
  |
  v
Extract published package
  |
  v
Create temporary consumer project
  |
  v
Install the packed tarball with npm
  |
  v
Validate package artifacts and installed runtime entries
  |
  v
Cleanup
```

This closely matches how consumers install packages from the npm registry.

## Runtime validation

Runtime validation attempts to load published runtime entry points.

By default, the following entries are validated:

* `main`
* `exports`

Executable `bin` entries are also validated by default and can be disabled with:

```bash
packverify validate --no-validate-bins
```

## Lifecycle scripts

By default, `packverify` allows npm lifecycle scripts in both phases:

* `npm pack` runs its normal pack-related lifecycle scripts.
* `npm install` runs lifecycle scripts while the packed tarball is installed into the temporary consumer project.

The controls are independent.

Skip scripts only during the pack phase:

```bash
packverify validate --ignore-pack-scripts
```

Skip scripts only during the install phase:

```bash
packverify validate --ignore-install-scripts
```

Skip scripts in both phases:

```bash
packverify validate \
  --ignore-pack-scripts \
  --ignore-install-scripts
```

## Exit codes

* `0` Validation succeeded.
* Non-zero Validation failed.
