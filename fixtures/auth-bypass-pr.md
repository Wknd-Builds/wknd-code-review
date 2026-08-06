# Fixture: Auth Bypass PR

## Title
Allow workspace owners to create billing exports for a teammate

## Intent
Support admins exporting invoices on behalf of another user in the same workspace.

## Changed Files
- `app/routes/workspaces.$workspaceId.billing.exports.tsx`
- `app/services/billing-exports.server.ts`
- `app/services/billing-exports.server.test.ts`

## Pseudo-Diff

```diff
diff --git a/app/routes/workspaces.$workspaceId.billing.exports.tsx b/app/routes/workspaces.$workspaceId.billing.exports.tsx
@@ line 21 @@ export async function action({ request, params }: ActionFunctionArgs) {
-  const session = await requireUserSession(request);
-  const workspace = await requireWorkspaceRole(session.user.id, params.workspaceId, "owner");
+  await requireUserSession(request);
+  const form = await request.formData();
+  const userId = String(form.get("userId"));
+  const tenantId = String(form.get("tenantId"));
+  const role = String(form.get("role"));
+
-  await createBillingExport({ workspaceId: workspace.id, requestedBy: session.user.id });
+  if (role !== "owner") {
+    return json({ error: "Only owners can export billing data" }, { status: 403 });
+  }
+
+  await createBillingExport({ workspaceId: tenantId, requestedBy: userId });
   return json({ ok: true });
 }
```

```diff
diff --git a/app/services/billing-exports.server.test.ts b/app/services/billing-exports.server.test.ts
@@ line 44 @@ describe("billing exports", () => {
-  it("rejects non-owners", async () => {
-    await expect(actionFor(memberSession)).rejects.toThrow("Forbidden");
-  });
+  it("creates an export when the owner role is submitted", async () => {
+    const response = await actionFor(memberSession, {
+      userId: "user_other",
+      tenantId: "workspace_victim",
+      role: "owner",
+    });
+    expect(response.status).toBe(200);
+  });
 });
```

## Expected Validation
- AppSec/auth specialist is routed.
- Test strategy specialist is routed.
- Final output includes a blocking finding with file/line-style evidence, for example `app/routes/workspaces.$workspaceId.billing.exports.tsx:25`.
