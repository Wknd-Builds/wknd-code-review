# Fixture: Clean PR

## Title
Clarify empty-state copy for saved searches

## Intent
Make the saved-searches empty state clearer without changing data loading or mutation behavior.

## Changed Files
- `app/routes/search.saved.tsx`
- `app/routes/search.saved.test.tsx`

## Pseudo-Diff

```diff
diff --git a/app/routes/search.saved.tsx b/app/routes/search.saved.tsx
@@ line 28 @@ export default function SavedSearches() {
   if (savedSearches.length === 0) {
     return (
       <EmptyState
-        title="Nothing here yet"
-        description="Create a search to see it here."
+        title="No saved searches"
+        description="Save a search to reuse filters and alerts from this page."
       />
     );
   }
```

```diff
diff --git a/app/routes/search.saved.test.tsx b/app/routes/search.saved.test.tsx
@@ line 14 @@ it("shows the empty state", () => {
-  expect(screen.getByText("Nothing here yet")).toBeInTheDocument();
+  expect(screen.getByText("No saved searches")).toBeInTheDocument();
+  expect(screen.getByText("Save a search to reuse filters and alerts from this page.")).toBeInTheDocument();
 });
```

## Expected Validation
- Plugin can approve without inventing findings.
- Only locally relevant specialists should be used.
- Residual risk stays honest and short.
