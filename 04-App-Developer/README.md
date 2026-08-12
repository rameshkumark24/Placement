# 04 — App Developer

| File | Contents |
|---|---|
| [App-Development-Cheatsheet.md](App-Development-Cheatsheet.md) | **Main guide.** Stack, screen inventory, build order, mobile API safety, security, offline, permissions, push, performance, device testing |
| [UI-Component-Libraries.md](UI-Component-Libraries.md) | RN/Expo + Flutter component kits, animation libraries, design assets |
| [Store-Release-Checklist.md](Store-Release-Checklist.md) | Play Store + App Store submission, assets, rejection triggers |

> Universal rules live in [`00-Vibe-Coding-Core`](../00-Vibe-Coding-Core/) and are not repeated here.

## Default stack

React Native + Expo · TypeScript · Expo Router · NativeWind · Supabase ·
TanStack Query · EAS Build + EAS Update · Sentry

## What makes mobile different from web

1. **You cannot hotfix.** Store review takes 1–3 days. Set up OTA updates *before* you need them.
2. **The network is hostile.** Offline behaviour is a feature, not an edge case.
3. **Nothing in the bundle is secret.** Anyone can unzip an APK.
4. **Battery and data are the user's.** A wasteful poll gets you uninstalled.
5. **The store is a gatekeeper.** Rejections are usually about policy, not code.

## The three that cause the most damage

1. **An API call inside `build()` (Flutter) or the render body (RN)** → infinite request loop.
   → [Mobile API safety](App-Development-Cheatsheet.md#5-mobile-specific-api-safety)
2. **Tokens in AsyncStorage instead of SecureStore** → plain text on a rooted device.
   → [Security](App-Development-Cheatsheet.md#6-mobile-specific-security)
3. **No in-app account deletion** → rejected by both stores.
   → [Store checklist](Store-Release-Checklist.md#legal--privacy--where-most-rejections-happen)
