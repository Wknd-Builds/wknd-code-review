# Accessibility Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- WCAG 2.2: https://www.w3.org/TR/WCAG22/
- WAI-ARIA Authoring Practices Guide: https://www.w3.org/WAI/ARIA/apg/
- MDN accessibility documentation: https://developer.mozilla.org/en-US/docs/Web/Accessibility
- React accessibility documentation: https://react.dev/reference/react-dom/components/common#applying-aria-attributes

Use this reference when reviewing semantic HTML, labels, keyboard interaction, focus management, forms, dialogs, dynamic content, errors, loading/disabled states, or mobile accessibility basics.

## Review Checklist

- Prefer semantic HTML controls and landmarks before ARIA. Use ARIA to fill gaps, not to replace native behavior.
- Ensure every form control has an accessible name and that helper/error text is associated programmatically.
- Check keyboard access for all interactive controls, including menus, dialogs, popovers, tabs, comboboxes, custom buttons, and drag/drop alternatives.
- Preserve visible focus indicators and logical focus order.
- Manage focus on dialogs, drawers, route transitions, validation errors, destructive confirmations, and content that appears after async work.
- Confirm disabled and loading states communicate status without trapping keyboard users or hiding required context.
- Ensure errors are visible, specific, and announced when appropriate.
- Review dynamic content, toasts, optimistic updates, and background saves for screen reader announcements when the change matters.
- Check icons, icon buttons, images, charts, and media for accessible names, alt text, captions, or hidden decorative status.
- Avoid clickable non-controls such as `div` or `span` without keyboard semantics and role behavior.
- Validate color-dependent states have non-color cues. When color contrast is changed, verify against current WCAG requirements.
- For mobile, check touch target size, screen reader labels, orientation/zoom assumptions, reduced motion, and platform-specific accessibility APIs.
- Include accessibility assertions in tests for critical flows when the implementation changes semantics, labels, focus, or validation.

## Common Review Smells

- Placeholder text is the only label.
- Custom button lacks `button` semantics, keyboard activation, or disabled behavior.
- Dialog opens without focus trap, initial focus, escape/close behavior, or focus restoration.
- Error summary is visually present but not connected to fields.
- Loading spinners replace button text without accessible status.
- Icon-only controls have no accessible name.
- `aria-hidden` hides focusable or important content.
- Mobile permission, navigation, or form UI lacks screen-reader-specific labels.

## Primary-Source Refresh Triggers

- WCAG, ARIA, browser, React, component library, or mobile accessibility guidance changes.
- New custom widgets, dialogs, popovers, drag/drop interactions, charts, media, or virtualized lists.
- Changes to forms, validation, focus management, route transitions, dynamic content announcements, or disabled/loading states.
- Mobile platform changes involving permissions, gestures, screen reader labels, reduced motion, or orientation.
- Accessibility bug reports, audit findings, or legal/compliance requirements.
