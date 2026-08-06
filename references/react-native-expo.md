# React Native And Expo Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- React Native documentation: https://reactnative.dev/docs/getting-started
- Expo documentation: https://docs.expo.dev/
- Expo Application Services documentation: https://docs.expo.dev/eas/
- Expo Updates documentation: https://docs.expo.dev/versions/latest/sdk/updates/
- React Navigation documentation: https://reactnavigation.org/docs/getting-started

Use this reference when reviewing React Native or Expo apps, Expo config, native permissions, lifecycle behavior, OTA updates, platform divergence, navigation, storage, offline behavior, deep links, native modules, or mobile performance.

## Review Checklist

- Review Expo config and native manifests for permission, capability, bundle identifier, scheme, plugin, background mode, URL scheme, and platform-specific changes.
- Ensure permission requests have user-facing value, platform-appropriate copy, denied-state handling, and privacy implications reviewed.
- Check iOS and Android divergence explicitly: permissions, background behavior, file paths, notifications, deep links, webviews, storage, and native module support.
- Review app lifecycle paths: cold start, foreground/background, resume, network changes, auth expiration, push notification entry, and interrupted flows.
- Treat OTA/update behavior carefully. Confirm native/runtime compatibility, update channels, rollback, critical bug fixes, and migrations for persisted state.
- Check navigation for deep-link routing, auth-gated screens, back behavior, modal stacks, tab state, and invalid route params.
- Validate all data from deep links, push payloads, local storage, secure storage, clipboard, camera/files, and native modules.
- Review storage choice for sensitivity and durability: memory, AsyncStorage, SecureStore/keychain, SQLite, filesystem, and cache.
- Consider offline behavior: queued mutations, retry/idempotency, conflict handling, stale data, sync indicators, and failure recovery.
- Treat new native modules, config plugins, prebuild changes, and EAS build changes as supply-chain and deploy risks.
- Check mobile performance: excessive renders, expensive lists, image sizes, JS thread blocking, startup cost, bundle size, bridge/native-module work, and battery/network usage.
- Ensure tests or manual validation cover both platforms when behavior can diverge.

## Common Review Smells

- Permission added to config without runtime request flow, denied-state UI, or privacy explanation.
- OTA update changes code that assumes a native module or permission not present in installed binaries.
- Deep link navigates to authenticated or tenant-scoped data without server validation.
- AsyncStorage stores tokens, secrets, or sensitive PII.
- Android works but iOS permission string, background mode, or URL scheme is missing.
- Large list renders without virtualization or stable keys.
- Native module or config plugin added without EAS/build and rollback consideration.

## Primary-Source Refresh Triggers

- Expo SDK, React Native, EAS, React Navigation, native module, or platform OS version upgrades.
- Changes to permissions, background modes, push notifications, deep links, secure storage, OTA updates, config plugins, or prebuild output.
- Privacy-sensitive data collection, camera/microphone/location/photos/contacts/files usage, or app store policy implications.
- Offline sync, persisted migrations, native runtime compatibility, or rollback behavior.
- Mobile performance regressions, startup changes, JS engine changes, or bundle-size changes.
