---
title: Project setup
description: Information on moonlight's project structure and build system
sidebar:
  order: 1
---


You will need [pnpm](https://pnpm.io), as moonlight uses it for dependency and workspace management.

- Clone the repository.
- Install dependencies with `pnpm i`.
- Build the project with `pnpm run build`.
  - For working on moonlight, a watch mode is available with `pnpm run dev`.

## Contribution guidelines

- Ensure your commits pass Prettier/ESLint. This is a requirement for merge.
  - moonlight uses [husky](https://typicode.github.io/husky/) to check lints on commit.
- Please make PRs to the `develop` branch instead of `main`.
  - `develop` is merged into `main` when moonlight updates happen. Our in-progress work resides on `develop`.
- Don't break the existing API surface.
  - Library extensions and moonlight globals cannot change in a way that breaks existing extensions (unless there is an upcoming API bump).

## Project structure

moonlight is split into a [pnpm workspace](https://pnpm.io/workspaces). The project contains multiple packages:

- `core`: core code and utilities responsible for loading extensions
- `injector`: the entrypoint for moonlight loaded by the patched Discord client
  - loads the config and detected extensions from disk and forwards them to the other stages
  - loads host modules
  - sets up `node-preload` to run when the browser window is created
- `node-preload`: ran before Discord's code on the Node side
  - receives the config/detected extensions from `injector` and forwards it to `web-preload`
  - loads node modules
  - loads `web-preload` and forwards some information to it
  - starts Discord once everything is ready
- `web-preload`: ran before Discord's code on the web side
  - receives the loaded config and detected extensions from `node-preload`
  - loads extensions and installs their Webpack modules and patches
- `core-extensions`: built-in extensions that come with every moonlight install, mostly libraries
- `types`: types for moonlight's core, core extensions, and extension manifests/exports
- `browser`: the entrypoint for the [browser extension](/using/install#browser)

## Build system

moonlight uses [esbuild](https://esbuild.github.io) as its build system (`build.mjs`). This script is responsible for outputting `injector`, `node-preload`, and `web-preload` into `dist`, along with all core extensions.

## Other dependencies

moonlight uses some other packages that are not in the monorepo, like [LunAST](https://github.com/moonlight-mod/lunast), [moonmap](https://github.com/moonlight-mod/moonmap), and [mappings](https://github.com/moonlight-mod/mappings). When testing local forks, try [pnpm link](https://pnpm.io/cli/link).
