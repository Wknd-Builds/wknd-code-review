# Fixture: Migration Risk PR

## Title
Collapse page access fields into a single visibility column

## Intent
Simplify authorization checks by replacing `is_public` and `shared_with_team` with a `visibility` enum.

## Changed Files
- `prisma/migrations/20260806081000_collapse_page_visibility/migration.sql`
- `app/models/page.server.ts`
- `.github/workflows/deploy.yml`

## Pseudo-Diff

```diff
diff --git a/prisma/migrations/20260806081000_collapse_page_visibility/migration.sql b/prisma/migrations/20260806081000_collapse_page_visibility/migration.sql
@@ line 1 @@
+ALTER TABLE "Page" DROP COLUMN "is_public";
+ALTER TABLE "Page" DROP COLUMN "shared_with_team";
+ALTER TABLE "Page" ADD COLUMN "visibility" TEXT NOT NULL DEFAULT 'private';
+
+-- No backfill for previously public or team-shared pages.
+-- No CHECK constraint limiting visibility values.
+-- No index for existing queries filtering by workspace_id + visibility.
```

```diff
diff --git a/app/models/page.server.ts b/app/models/page.server.ts
@@ line 67 @@ export async function listVisiblePages(workspaceId: string) {
-  return db.page.findMany({ where: { workspaceId, OR: [{ isPublic: true }, { sharedWithTeam: true }] } });
+  return db.page.findMany({ where: { workspaceId, visibility: { in: ["public", "team"] } } });
}
```

```diff
diff --git a/.github/workflows/deploy.yml b/.github/workflows/deploy.yml
@@ line 32 @@ jobs:
       - run: npm run build
-      - run: npm run prisma:migrate
       - run: npm run deploy
+      - run: npm run prisma:migrate
+      # Migration now runs after deploy; new code can query visibility before it exists.
```

## Expected Validation
- Data/migrations specialist is routed.
- CI/deployment specialist is routed.
- Observability specialist is routed because deploy-order failure visibility is part of the risk.
- Final output cites destructive migration and rollout evidence with file/line-style references.
