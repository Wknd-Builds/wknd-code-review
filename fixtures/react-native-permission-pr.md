# Fixture: React Native Permission PR

## Title
Enable background location for nearby event reminders

## Intent
Let mobile users receive reminders when they are near saved event venues.

## Changed Files
- `app.config.ts`
- `src/features/events/NearbyReminder.tsx`
- `eas.json`

## Pseudo-Diff

```diff
diff --git a/app.config.ts b/app.config.ts
@@ line 19 @@ export default {
   ios: {
     bundleIdentifier: "com.wknd.app",
+    infoPlist: {
+      NSLocationAlwaysAndWhenInUseUsageDescription: "",
+      NSLocationWhenInUseUsageDescription: "Show nearby events."
+    }
   },
   android: {
     package: "com.wknd.app",
+    permissions: ["ACCESS_FINE_LOCATION"]
+    // Missing ACCESS_BACKGROUND_LOCATION while JS requests background updates.
   },
```

```diff
diff --git a/src/features/events/NearbyReminder.tsx b/src/features/events/NearbyReminder.tsx
@@ line 41 @@ export async function enableNearbyReminders() {
-  const status = await Location.requestForegroundPermissionsAsync();
+  const status = await Location.requestBackgroundPermissionsAsync();
   if (status.granted) {
+    await Location.startLocationUpdatesAsync("nearby-reminders", {
+      accuracy: Location.Accuracy.Highest,
+      timeInterval: 60_000,
+      showsBackgroundLocationIndicator: false,
+    });
   }
 }
```

```diff
diff --git a/eas.json b/eas.json
@@ line 8 @@
   "production": {
-    "channel": "production"
+    "channel": "production",
+    "autoIncrement": false
   }
```

## Expected Validation
- React Native/Expo specialist is routed.
- AppSec/auth specialist is routed because background location is privacy-sensitive permission data.
- Final output should request changes for permission copy, platform divergence, and native capability/OTA release risk.
