# Fixture: Frontend Race PR

## Title
Add optimistic invite acceptance in account settings

## Intent
Make the invite accept button feel instant by updating local state before the mutation finishes.

## Changed Files
- `app/routes/settings.invites.tsx`
- `app/routes/settings.invites.test.tsx`

## Pseudo-Diff

```diff
diff --git a/app/routes/settings.invites.tsx b/app/routes/settings.invites.tsx
@@ line 38 @@ export default function Invites() {
   const fetcher = useFetcher<typeof action>();
   const [invites, setInvites] = useState(loaderData.invites);
+
+  function acceptInvite(inviteId: string) {
+    setInvites((current) => current.filter((invite) => invite.id !== inviteId));
+    fetcher.submit({ inviteId }, { method: "post" });
+  }
+
   return (
     <ul>
       {invites.map((invite) => (
         <li key={invite.id}>
-          <fetcher.Form method="post">
-            <input type="hidden" name="inviteId" value={invite.id} />
-            <button type="submit">Accept</button>
-          </fetcher.Form>
+          <button onClick={() => acceptInvite(invite.id)}>
+            Accept
+          </button>
         </li>
       ))}
     </ul>
@@ line 72 @@ export async function action({ request }: ActionFunctionArgs) {
   await acceptInviteForCurrentUser(request, inviteId);
+  return json({ ok: true }, { headers: { "X-Skip-Revalidation": "1" } });
 }
```

```diff
diff --git a/app/routes/settings.invites.test.tsx b/app/routes/settings.invites.test.tsx
@@ line 17 @@ it("accepts an invite", async () => {
-  await user.click(screen.getByRole("button", { name: "Accept" }));
-  await waitForElementToBeRemoved(() => screen.queryByText("Design workspace"));
+  await user.dblClick(screen.getByRole("button", { name: "Accept" }));
+  expect(screen.queryByText("Design workspace")).not.toBeInTheDocument();
 });
```

## Expected Validation
- Frontend runtime specialist is routed.
- Test strategy specialist checks duplicate-submit, rollback, and revalidation user-flow coverage.
- Performance specialist is optional only if reviewers identify payload or render churn risk.
