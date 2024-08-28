# `vitest-pool-workers` extension issue reproduction

This repository shows that the `vitest-pool-workers` package seems not to apply the correct module resolution for `require` calls not specifying file's extension.

## Steps to reproduce the issue

Install the dependencies

```sh
$ npm i
```

Run the test:

```sh
$ npm test
```

See it fail because of the following issue:

```
Error: No such module "Users/dario/Desktop/vitest-pool-workers-ext-repro/node_modules/discord-api-types/v10.js".
  imported from "Users/dario/Desktop/vitest-pool-workers-ext-repro/node_modules/discord-api-types/v10.mjs"
```

Comment out or remove the `import "discord-api-types/v10";` line in `test/index.spec.ts` to see the test pass.

This failure happens because of `vitest-pool-workers` not correctly resolving some modules.

## Details

You can see what's causing the issue if you go to `node_modules/discord-api-types/v10.js` and check the `require` calls at the bottom.

Those get incorrectly resolved to the files' `mjs` versions instead of the correct `js` ones and you can see that everything works fine if you manually add the extensions in the `require` calls:

```diff
-__exportStar(require("./gateway/v10"), exports);
-__exportStar(require("./globals"), exports);
-__exportStar(require("./payloads/v10/index"), exports);
-__exportStar(require("./rest/v10/index"), exports);
-__exportStar(require("./rpc/v10"), exports);
+__exportStar(require("./gateway/v10.js"), exports);
+__exportStar(require("./globals.js"), exports);
+__exportStar(require("./payloads/v10/index.js"), exports);
+__exportStar(require("./rest/v10/index.js"), exports);
+__exportStar(require("./rpc/v10.js"), exports);
```
