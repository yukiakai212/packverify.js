# packverify

Validate your npm package before publishing.

`packverify` simulates how consumers install and load your published package. It packs your project, extracts the published archive into a temporary workspace, installs production dependencies, and validates that your package can be loaded successfully.

## Features

* Validates the actual files that will be published.
* Loads runtime entry points (`main`, `exports`, and optionally `bin`).
* Verifies TypeScript declaration files exist.
* Detects forbidden published files using glob patterns.
* Supports packages with `exports`, `types`, `typings`, and `bin`.
* Resolves glob-based entry declarations before validation.
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

Skip npm lifecycle scripts while installing dependencies:

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
    │
    ▼
npm pack
    │
    ▼
Extract published package
    │
    ▼
Install production dependencies
    │
    ▼
Validate runtime and package artifacts
    │
    ▼
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

## Install scripts

By default, dependency installation behaves like a normal npm installation, including lifecycle scripts.

If your package executes expensive or interactive install scripts, you can skip them:

```bash
packverify validate --ignore-install-scripts
```

## Exit codes

* `0` Validation succeeded.
* Non-zero Validation failed.
