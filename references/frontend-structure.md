# Frontend Structure Review Reference

Source snapshot: 2026-08-11.

Use this reference when reviewing frontend routes, settings screens, editable forms, tabbed navigation, reusable UI elements, or route-level component organization.

## Review Checklist

- Prefer one aggregate form for related editable fields on a screen. Avoid splitting each editable field into its own independent form unless the product flow truly commits each field separately.
- Use one primary update action for the aggregate form. Submitting it should open a confirmation modal or dialog before persistence when the change affects user-visible state, permissions, billing, configuration, data access, or other consequential settings.
- Confirmation dialogs should preview the pending changes clearly and provide explicit confirm and cancel actions. The preview should distinguish changed fields from unchanged fields.
- Extract shared UI primitives into reusable components under `components/ui`, such as buttons, form fields, selects/dropdowns, tables, modals/dialogs, alerts, tabs, badges, and layout primitives.
- Extract route-specific product experiences into feature components under `components/features`. Route modules should compose loaders/actions/meta/error boundaries with feature components rather than owning large UI trees directly.
- Use route structure for tab navigation when tabs represent addressable product states. Prefer nested routes, child routes, or sibling route segments over URL string parameters for primary tab state.
- Keep URL search params for filters, sorts, pagination, transient view options, and shareable query state, not as a substitute for route-modeled tabs.
- Preserve existing local conventions when they are already clear and consistent, but flag new code that deepens ad hoc route UI, one-off primitives, or string-param tab patterns.

## Common Review Smells

- A settings page renders many small forms with separate save buttons for fields that users understand as one edit session.
- A mutation persists settings immediately without a confirmation step or preview of the exact changes.
- Button, field, dropdown, table, or modal markup is repeated inside route files instead of using `components/ui`.
- Route modules contain large product-specific component trees that should live in `components/features`.
- Tab selection is driven by `?tab=...`, `?section=...`, or similar string parameters when each tab should be a route.
- New feature code mixes primitive UI components, product feature components, route loaders/actions, and mutation state in one large route file.

## Finding Calibration

- Treat these as findings when the change adds or reinforces structure that will materially affect maintainability, reviewability, user safety, accessibility, or navigation correctness.
- Treat them as suggestions when the change is small, isolated, follows an existing local pattern, or would require a broad migration outside the PR scope.
- Do not request changes solely to rename directories if the codebase already has an equivalent established convention; focus on the architectural intent.
