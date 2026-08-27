# 🔎 Verification Tools — checking your own work from outside

> ⭐⭐ **Mobile has a gap the web does not: most scanners cannot see your app at all.** They can only
> check the API it talks to. **So the external tools cover less here, and the manual checks matter
> more.**

---

# 1 · ⭐⭐ Point these at your API domain

```
⭐⭐ THESE DO NOT TEST YOUR APP. THEY TEST THE SERVER IT CALLS — AND
   THAT IS WHERE YOUR CUSTOMERS' DATA LIVES.
   ⭐ People skip them because "it's a mobile app, there's no website".
     The API is a website.
```

| Tool | Checks | ⭐ Target | ⭐⭐ Does not catch |
|---|---|---|---|
| **[SSL Labs](https://www.ssllabs.com/ssltest/)** | TLS on your API domain — protocols, ciphers, chain | ⭐ **A or A+** | ⭐⭐ Anything in your app. And ⭐ if you use certificate pinning, **check the expiry here before it bricks your app.** |
| **[Security Headers](https://securityheaders.com/)** | Headers on your API and any web pages (privacy policy, marketing site) | ⭐ **A** | ⭐ Most matter less for a native client — but your privacy-policy page is a website and is required to be live |
| **[mail-tester.com](https://www.mail-tester.com/)** | Email deliverability if you send any | ⭐ **9/10+** | ⭐⭐ Whether Gmail specifically filters you |
| **`npx license-checker --summary`** | Dependency licences | ⭐ **No GPL/AGPL** | ⭐ Native-side licences in pods/gradle — check those separately |
| **`npm audit --omit=dev`** | Known vulnerable dependencies | ⭐ 0 high | A package malicious today and undiscovered |

---

# 2 · ⭐⭐ The mobile-only checks — all manual

```
⭐⭐ NO SCANNER DOES ANY OF THESE. THEY ARE THE ONES THAT MATTER.

 ① ⭐⭐ UNZIP YOUR OWN BUILD AND GREP IT
    unzip -p app.apk | strings | grep -iE "sk_live|secret|api[_-]?key"
    ⭐ Anything found is public forever, and old versions keep using it.

 ② ⭐⭐ THE ID-SWAP TEST, THROUGH A PROXY
    ⭐ Run the app through Charles / Proxyman / mitmproxy, capture a
      request, replay it with another user's id. Must be 403/404.
    ⭐⭐ THIS IS THE #1 REAL DATA LEAK AND NO TOOL FINDS IT.
    ⇒ ⭐ AND PROXYING YOUR OWN APP ONCE IS THE FASTEST WAY TO SEE WHAT
      AN ATTACKER SEES.

 ③ ⭐⭐ RUN IT ON A CHEAP ANDROID. Jank shows there first.
 ④ ⭐ AIRPLANE MODE — offline state, or an infinite spinner?
 ⑤ ⭐⭐ A THROTTLED, LOSSY CONNECTION — worse than offline, because
    requests HANG instead of failing.
 ⑥ ⭐ EVERY FORM WITH THE KEYBOARD OPEN, on a small device
 ⑦ ⭐ LARGEST SYSTEM FONT SIZE — does text clip?
 ⑧ ⭐⭐ FORCE-KILL MID-ACTION AND REOPEN — what state?
 ⑨ ⭐ DENY EVERY PERMISSION — does it still work?
 ⑩ ⭐⭐ THE CODEX CROSS-CHECK (→ [09-Testing.md §3](../09-Testing.md))
```

---

# 3 · Built-in tooling that earns its place

| Tool | For |
|---|---|
| **Flipper / React Native DevTools** | ⭐ Network inspection, layout, performance on device |
| **Xcode Instruments / Android Studio Profiler** | ⭐⭐ Memory leaks — navigate 50 screens and watch it climb |
| **`npx react-native-bundle-visualizer`** | ⭐ What is actually in your bundle |
| **EAS build size output** | ⭐ Every MB costs installs on slow connections |
| **Sentry crash-free rate** | ⭐⭐ Target > 99.5%. **And trigger a real crash once to confirm symbolication works** — a trace without symbols is useless, and you find out during the incident. |
| **Store pre-launch report** (Google Play) | ⭐ Free automated run on real devices — crashes, a11y, performance. **Genuinely useful and often ignored.** |

---

# ⭐ Reading a grade honestly

```
⭐⭐ EVERY AUTOMATED CHECK TESTS CONFIGURATION. NONE TEST YOUR LOGIC.

  "A+ on SSL Labs" ⇒ ⭐ TLS is configured well on the API.
                   ⇒ ⭐⭐ IT SAYS NOTHING ABOUT WHETHER USER A CAN READ
                     USER B'S DATA THROUGH IT.
  "Crash-free 99.8%" ⇒ ⭐ it does not crash.
                   ⇒ ⭐⭐ IT CAN STILL BE WRONG, SLOW, OR LEAKING.
                     A silent wrong answer never crashes.
  "Pre-launch report clean" ⇒ ⭐ it launched and clicked around.
                   ⇒ ⭐ It did not test your workflow, your offline
                     path, or your permissions.

⇒ ⭐ USE THEM TO CATCH WHAT YOU FORGOT, NEVER TO CONCLUDE YOU ARE DONE.
```

---

**Back:** [folder index](../README.md) · **Security:** [05-Security.md](../05-Security.md) ·
**Ship:** [10-Ship-Checklist.md](../10-Ship-Checklist.md)
