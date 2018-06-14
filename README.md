# Example CRA project with multiple local TypeScript packages in a monorepo

## Goals

1. To upgrade internal modules (in app source tree) to independent local private packages.

2. To share code between CRA-ts apps in a monorepo

3. While still be able to use IDE 'go to definition' to go to .ts source, instead of .d.ts

## Tools

1. create-react-app (will eject to config webpack/jest to achive #3 goal)

1. react-scripts-ts

1. yarn workspace

## Steps

1. Initial boilerplate from create-react-app-typescript in root/webapp [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/5bd4cdca6167b85068454b4b80602f31385dfd24).

1. Create yarn workspace [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/cc9862ea09c5173b8c64e856befb5a13d26254eb).

1. Move CRA tsconfig.json to root to be shared with other local packages [commit 1](https://github.com/untsamphan/cra-ts-monorepo-example/commit/00003e837044845dd34e031479ac2810269c552a), [commit 2](https://github.com/untsamphan/cra-ts-monorepo-example/commit/5cbc685ac6d44bdf0150306503f1e78ef8417226).

1. Implement a trivial local packages `root/packages/mymain` [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/1a3ea1fb5763bf4f3ac4cfb2413afe6b589b0050). Run yarn now so it'll create symlink to mymain in `root/node_modules`. Then we can `import from 'mymain'` from anywhere.

1. Make the CRA app use the new local packages [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/20ecb7ab2add90030d69701a7a62d4f58e7db7c1). Now the CRA app will tsc compile correctly (because we have index.ts at mymain root). But when `yarn start` it'll fail in browser because mymain isn't built. To fix this we can tsc in mymain to build the package and the app will run successfully. However, when we `go to definition` to a symbol in mymain, it'll goto a .d.ts.

1. To achive goal #3, we configure tsconfig.json `baseUrl` to directly reference local packages. Since webpack won't bundle code outside `webapp/src`, and jest won't find the packages, we have to eject to configure them. [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/00980f8b5dc6e8dc116ba600cfc7f61d996a049c)

1. Simple webpack config hack to allow it to bundle code outside `webpack/src`. This to achieve goal #3. [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/284fa7eff600a7a2b1bd287db848bddff7de346a). Don't forget to delete `build` in local packages, because otherwise everyone will use `build/index.*` (per NPM spec) instead of `index.ts` at the local package root (a TS-specific behavior).

1. Simple jest config hack to make jest inside webapp also run all tests in local packages. [commit](https://github.com/untsamphan/cra-ts-monorepo-example/commit/ef0b50673e1ee40c0b515f1dcc6c870bb2990d24)
