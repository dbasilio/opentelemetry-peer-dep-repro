# opentelemetry-peer-dep-repro
This repo reproduces an issue with the opentelemetry js package

## Repro Steps
- nvm install
- corepack enable
- yarn install

The yarn install output produces:
```
➤ YN0000: ┌ Resolution step
➤ YN0002: │ @opentelemetry/auto-instrumentations-web@npm:0.33.2 [83915] doesn't provide zone.js (pb09f8), requested by @opentelemetry/instrumentation-user-interaction
➤ YN0000: │ Some peer dependencies are incorrectly met; run yarn explain peer-requirements <hash> for details, where <hash> is the six-letter p-prefixed code
➤ YN0000: └ Completed
➤ YN0000: ┌ Fetch step
➤ YN0000: └ Completed
➤ YN0000: ┌ Link step
➤ YN0000: └ Completed
➤ YN0000: Failed with errors in 0s 68ms
```

## Explanation
This error happens because `@opentelemetry/auto-instrumentations-web` depends on `@opentelemetry/instrumentation-user-interaction` which declares a mandatory peerDependency on `zone.js` which `@opentelemetry/auto-instrumentations-web` doesn't provide.

All peerDependencies of a package must be resolved by any package that depends on it.

## Possible Solutions
There's a few ways to solve this problem:
- add `zone.js` as a `peerDependency` on `@opentelemetry/auto-instrumentations-web`
- add `zone.js` as a `dependency` on `@opentelemetry/auto-instrumentations-web`
- list `zone.js` as an optional peer of `@opentelemetry/instrumentation-user-interaction` if it's not mandatory
- add `zone.js` as both a dependency and peerDependency of `@opentelemetry/instrumentation-user-interaction` so that if parents do not supply it, the dependency version is used. (or do this on `@opentelemetry/auto-instrumentations-web`)
