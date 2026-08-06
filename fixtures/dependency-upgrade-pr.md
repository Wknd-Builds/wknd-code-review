# Fixture: Dependency Upgrade PR

## Title
Upgrade Next.js and add image processing helper

## Intent
Adopt the latest app router fixes and use a new package for user-uploaded profile thumbnails.

## Changed Files
- `package.json`
- `package-lock.json`
- `app/lib/avatar-image.server.ts`

## Pseudo-Diff

```diff
diff --git a/package.json b/package.json
@@ line 12 @@
   "dependencies": {
-    "next": "14.2.9",
+    "next": "15.0.0",
+    "sharp-resize-helper": "1.0.0",
     "react": "18.3.1",
     "react-dom": "18.3.1"
   },
@@ line 28 @@
-  "engines": { "node": ">=20" }
+  "engines": { "node": ">=22" }
 }
```

```diff
diff --git a/package-lock.json b/package-lock.json
@@ line 4021 @@
+    "node_modules/sharp-resize-helper": {
+      "version": "1.0.0",
+      "hasInstallScript": true,
+      "bin": { "sharp-resize-helper": "postinstall.js" }
+    }
```

```diff
diff --git a/app/lib/avatar-image.server.ts b/app/lib/avatar-image.server.ts
@@ line 9 @@ export async function resizeAvatar(input: Buffer) {
+  return resizeWithSharpHelper(input, { width: 512, height: 512 });
}
```

## Expected Validation
- Dependency/supply-chain specialist is routed.
- CI/deployment specialist is routed for Node engine and framework rollout compatibility.
- Test strategy specialist is routed for framework and image-processing coverage.
- Primary-source refresh trigger is required before judging the Next.js major upgrade and new package risk.
