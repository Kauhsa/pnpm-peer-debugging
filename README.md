# Debugging pnpm peer dependency resolution

Make sure corepack is enabled and run pnpm install (`pnpm@9.0.0-alpha.8` is set as package manager in `package.json`). 

```bash
corepack enable
rm -rf node_modules && rm -f pnpm-lock.yaml && pnpm i && pnpm why commander
```

Correct peer dependency resolution:

```
devDependencies:
@snowplow/snowtype 0.12.2
├─┬ @commander-js/extra-typings 11.1.0
│ └── commander 11.1.0 peer
└─┬ terser 5.39.0
  └── commander 2.20.3
```

Looks great! Now, change package manager in `package.json` to newer version:

```diff
diff --git a/package.json b/package.json
index aeae631..f4a979c 100644
--- a/package.json
+++ b/package.json
@@ -2,7 +2,7 @@
     "name": "sample-project",
     "version": "1.0.0",
     "private": true,
-    "packageManager": "pnpm@9.0.0-alpha.8",
+    "packageManager": "pnpm@9.0.0-alpha.9",
     "scripts": {},
     "dependencies": {},
     "devDependencies": {
```

Try to install everything from scratch again:

```bash
rm -rf node_modules && rm -f pnpm-lock.yaml && pnpm i && pnpm why commander
```

...and now there is an incompatible version of `commander` dependency:

```
devDependencies:
@snowplow/snowtype 0.12.2
├─┬ @commander-js/extra-typings 11.1.0
│ └── commander 2.20.3 peer
└─┬ terser 5.39.0
  └── commander 2.20.3
```

...so presumably things have changed somewhere around here? https://github.com/pnpm/pnpm/compare/v9.0.0-alpha.8...v9.0.0-alpha.9
