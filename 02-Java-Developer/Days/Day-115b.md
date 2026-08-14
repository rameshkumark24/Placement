# Day 115b

**L-115b** · API keys — issuing, hashed storage, scoping, rotation · HMAC signing · mTLS overview

**Time:** 2 hrs · **Mode:** NEW

> Authenticating **machines**, not people. Every assumption from the last three days changes: there
> is no user to prompt, no browser to protect, no MFA, no login page — and credentials live for
> months in configuration rather than minutes in memory.
>
> This is what you build when another company integrates with your API, and what you consume when you
> integrate with theirs.

---

# Part 1 — API keys

## What they are, and what they are not

An API key is a **bearer credential**: possession is proof. There is no proof of *identity* beyond
holding the string.

```http
Authorization: Bearer sk_live_8f14e45fceea167a5a36dedd4bea2543
X-API-Key: sk_live_8f14e45fceea167a5a36dedd4bea2543
```

> **Use the `Authorization` header, not a custom one or a query parameter.**
>
> **Never a query parameter** — URLs are logged by every proxy, load balancer and access log, stored
> in browser history, and leaked in `Referer` headers. A key in a URL is a key in a dozen log files.

That single rule prevents the most common real-world API-key leak.

## Designing the key format

```
sk_live_8f14e45fceea167a5a36dedd4bea2543
│  │    └── 32 bytes of SecureRandom, hex or base62
│  └─────── environment: live | test
└────────── type: sk (secret) | pk (publishable)
```

Every part earns its place:

| Element | Why |
|---|---|
| **A prefix** (`sk_live_`) | Identifiable in logs and support tickets; **and scannable** |
| **Environment marker** | Makes "we used the test key in production" visible immediately |
| **≥256 bits of `SecureRandom`** | Unguessable |
| **A checksum** (optional) | Detect typos before a database lookup |
| **A short lookup id** | See below — avoids a full table scan |

**The prefix has a security purpose people miss:** GitHub, GitLab and others run **secret scanning**
against public repositories, and providers register their key prefixes so a leaked key is detected
and auto-revoked within minutes. A key with no recognisable prefix cannot be scanned for. Stripe's
`sk_live_`, GitHub's `ghp_`, AWS's `AKIA` all exist for this.

## Storing keys — hash them

> **An API key is a password for a machine. Store it hashed.** (Day 112)

```java
record ApiKey(String id, String userId, String keyHash, Set<String> scopes,
              Instant createdAt, Instant expiresAt, Instant lastUsedAt, String label) { }

String raw = "sk_live_" + randomHex(32);
String hash = sha256(raw);                    // SHA-256 is correct — 256 bits of entropy already
store(new ApiKey(id, userId, hash, scopes, …));
return raw;                                    // ⚠️ shown ONCE, never retrievable again
```

**Show it once.** If your dashboard can display an existing key, it is stored recoverably — the same
disqualifying finding as emailing a password (Day 112). Every good API provider shows the key once
and offers regeneration instead.

SHA-256, not argon2, for the same reason as refresh tokens (Day 115): the input is already
high-entropy random, so there is nothing to brute-force and no reason to pay for a slow hash.

**The lookup problem:** you cannot query by hash without hashing the presented key first — which is
fine — but you also cannot use a prefix index if the whole key is hashed. The standard solution is a
**short non-secret lookup id embedded in the key**:

```
sk_live_<8-char keyId><32-byte secret>
        └─ indexed, non-secret ─┘└─ hashed ─┘
```

Look up by `keyId` (an index seek), then verify the secret with a **constant-time comparison**:

```java
MessageDigest.isEqual(expectedHash, actualHash);       // ← constant-time; not String.equals
```

`String.equals` short-circuits on the first differing byte, which is a timing oracle. It matters less
here than for passwords, but it costs nothing to do correctly.

## Scoping

**A key should carry the minimum permission needed** — the same principle as OAuth scopes (Day 116).

```java
Set<String> scopes = Set.of("orders:read", "orders:write");     // not "admin"
```

```
orders:read     orders:write     customers:read     webhooks:manage
```

Plus, where it applies: **IP allow-listing**, **rate limits per key** (Day 122), and **expiry**. A
key with no expiry is a permanent credential; 90 or 365 days with a renewal reminder is the norm.

The publishable/secret split is worth copying from Stripe: a `pk_` key is safe in client-side code
and can only do harmless things (create a payment intent), while `sk_` never leaves a server.

## Rotation without downtime

**Support at least two active keys per account.** Rotation is then a sequence with no outage:

```
1. Create key B    (A and B both valid)
2. Deploy B to every consumer
3. Watch B's usage rise and A's fall to zero      ← requires per-key usage tracking
4. Revoke A
```

Step 3 requires tracking `lastUsedAt` per key — without it, revoking A is a guess. That is the same
principle as Day 109's deprecation instrumentation.

**When you must rotate immediately** (a key was leaked), revoke first and accept the outage; a live
leaked key is worse than a broken integration.

---

# Part 2 — HMAC request signing

API keys are bearer credentials: anyone who obtains one can use it. **Request signing proves the
caller holds the secret without ever transmitting it**, and additionally protects the request's
integrity.

```http
POST /v1/payments
X-Api-Key:   pk_8f14e45f
X-Timestamp: 1755172800
X-Nonce:     0f6b3a1c9e2d
X-Signature: 4a8f2c1e9b7d3f5a6c8e0b2d4f6a8c0e2b4d6f8a0c2e4b6d8f0a2c4e6b8d0f2a
```

```java
String canonical = String.join("\n",
        "POST",                        // method
        "/v1/payments",                // path
        canonicalQueryString,          // sorted parameters
        timestamp,
        nonce,
        sha256Hex(body));              // ← the BODY is covered

String signature = hmacSha256(canonical, secretKey);
```

**Verification, and every step is load-bearing:**

```java
// 1. Reject stale requests — bounds the replay window
if (abs(now() - timestamp) > 300) reject("timestamp outside tolerance");

// 2. Reject replays within that window
if (!nonceStore.putIfAbsent(nonce, ttl(300))) reject("nonce reused");   // Day 053

// 3. Recompute and compare in CONSTANT TIME
String expected = hmacSha256(canonicalise(request), lookupSecret(apiKeyId));
if (!MessageDigest.isEqual(expected.getBytes(), provided.getBytes())) reject("bad signature");
```

What signing gives you over a bearer key:

| Property | Bearer key | HMAC signature |
|---|---|---|
| Secret transmitted | ✅ every request | ❌ **never** |
| Integrity of the body | ❌ | ✅ |
| Replay protection | ❌ | ✅ with timestamp + nonce |
| Works if TLS is terminated by a proxy | ⚠️ the key is visible there | ✅ still valid |
| Implementation cost | trivial | moderate, both ends |

**The timestamp-plus-nonce pair is what stops replay**, and both are needed: the timestamp bounds how
long a captured request is useful, and the nonce store prevents reuse inside that window. A nonce
store with no timestamp bound would have to grow forever.

**This is how webhooks are verified** (Day 129) — Stripe, GitHub and Slack all sign their webhook
payloads exactly this way, and verifying the signature is the only thing that distinguishes a real
webhook from anyone on the internet POSTing to your endpoint.

⚠️ **Canonicalisation is where implementations break.** Both sides must build the signed string
identically — the same header casing, the same query-parameter ordering, the same treatment of an
empty body. Specify it precisely and provide test vectors, or integrators will spend days on it.

---

# Part 3 — mTLS

**Mutual TLS**: both sides present certificates. The client proves its identity at the transport
layer, before any application code runs.

```
Standard TLS:  server proves identity → client verifies
mTLS:          server proves identity → client verifies
               client proves identity → server verifies      ← the addition
```

```nginx
ssl_client_certificate /etc/nginx/ca.crt;    # the CA that signed permitted client certs
ssl_verify_client on;                        # require and verify
```

The application then reads the verified identity from a header the proxy sets:

```java
String clientCn = request.getHeader("X-SSL-Client-CN");   // ← set by the terminating proxy
```

⚠️ **That header must be stripped from incoming requests at the edge**, or a client can simply send
it themselves. This is Day 105's trust-boundary rule at its sharpest: a header set by *your* proxy is
trustworthy; the same header arriving from outside is not, and they are indistinguishable once
inside.

| | mTLS |
|---|---|
| ✅ | Strongest machine authentication; no shared secret in application config; rejected at the transport layer before any request processing |
| ❌ | Certificate lifecycle is real operational work — issuance, distribution, expiry, revocation, and an outage when a cert expires unnoticed |

**Where it is used:** service meshes (Istio, Linkerd do it automatically), banking and payment
networks, IoT fleets, and zero-trust internal architectures. **Not** for public API consumers — the
onboarding friction is prohibitive.

The dominant failure mode is mundane and worth stating: **an expired certificate takes the
integration down**, and it is always at 2 AM. Automated renewal and expiry alerting are mandatory,
not optional.

---

# Part 4 — Choosing

```
Public API, third-party developers        → API key (scoped, prefixed, rotatable)
Webhooks you SEND                          → HMAC signature (Day 129)
High-value operations (payments)           → HMAC signing + API key
Internal service-to-service                → mTLS, or short-lived JWTs from a central issuer
Machine-to-machine over OAuth              → client credentials grant (Day 116)
Cloud provider access                      → workload identity / IAM roles, not static keys
```

**The last line matters and is increasingly the right answer.** A static long-lived key in
configuration is a credential waiting to leak. Workload identity — AWS IAM roles, GCP workload
identity federation, Kubernetes service accounts — issues **short-lived credentials automatically**
from the workload's verified identity, with no secret to store, rotate or leak.

> **If you are on a cloud platform, the correct answer to "where do I put this API key" is often
> "you do not need one".**

## Secret management, wherever the credential lives

| ❌ Never | ✅ Instead |
|---|---|
| Hard-coded in source | Environment variables at minimum |
| Committed `.env` | `.gitignore` + `.env.example` (Day 085) |
| In a Docker image layer | Injected at runtime |
| In CI logs | Masked variables |
| In a URL | Header |
| Long-lived static keys | Short-lived, auto-rotated (Vault, AWS Secrets Manager, KMS) |

And the response to a leak is Day 085's, unchanged: **rotate first.** Removing it from history is
cleanup, not remediation.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| API key in a query parameter | Logged everywhere, in history, leaked via `Referer` |
| Storing keys in plaintext | The key table is a set of live credentials |
| A dashboard that can re-display a key | Proof of recoverable storage |
| No prefix | Cannot be caught by secret scanning |
| Hashing the whole key with no lookup id | A table scan per request |
| `String.equals` for comparison | Timing oracle |
| Unscoped keys | One leaked key grants everything |
| No expiry | A permanent credential |
| One key per account | Rotation requires downtime |
| No per-key usage tracking | Revocation is a guess |
| HMAC without a timestamp | Unlimited replay |
| HMAC without a nonce | Replay within the window |
| Under-specified canonicalisation | Integrators cannot produce a matching signature |
| Trusting `X-SSL-Client-CN` from outside | Trivial identity spoofing |
| Static cloud keys instead of workload identity | A leakable secret that need not exist |
| No certificate expiry alerting | An outage nobody predicted |

---

## Interview questions

**Q: How do you store and issue API keys?**
Generate at least 256 bits from `SecureRandom` with an identifiable prefix, store only the hash
(SHA-256 is sufficient — the input is already random), show the key exactly once, scope it to the
minimum permissions, and give it an expiry. Support two active keys so rotation needs no downtime.

**Q: Why must an API key never be in a URL?**
URLs are recorded in access logs, proxy logs, browser history and `Referer` headers, so the key ends
up in many systems you do not control. Use the `Authorization` header.

**Q: Why does an API key have a prefix?**
Identification in logs, immediate visibility of test-versus-live, and — most importantly — so
secret-scanning services can recognise a leaked key in a public repository and revoke it
automatically.

**Q: What does HMAC request signing add over a bearer key?**
The secret is never transmitted, the request body's integrity is protected, and with a timestamp and
nonce it prevents replay. It also survives TLS termination at a proxy, where a bearer key would be
visible.

**Q: Why do you need both a timestamp and a nonce?**
The timestamp bounds how long a captured request remains useful; the nonce prevents replay within
that window. Without the timestamp the nonce store would have to grow forever.

**Q: What is mTLS and when would you use it?**
Both sides present certificates, so the client is authenticated at the transport layer before any
application code runs. Used for internal service-to-service, service meshes and high-security
integrations — not for public API consumers, because certificate lifecycle management is too much
friction.

**Q: What is the risk with `X-SSL-Client-CN`?**
It is trustworthy only because your proxy set it. If the proxy does not strip the incoming header, a
client can send it directly and impersonate any identity.

**Q: What is better than an API key on a cloud platform?**
Workload identity — IAM roles, workload identity federation, Kubernetes service accounts — which
issues short-lived credentials from the workload's verified identity, so there is no static secret to
store, rotate or leak.

---

## Mini task

1. Implement key issuance: prefixed format with an embedded lookup id, SHA-256 storage, shown once,
   with scopes and an expiry.
2. Implement verification with the lookup-id index and a constant-time comparison. Confirm you never
   scan the table.
3. Add scope checking and prove a `orders:read` key cannot call a write endpoint.
4. Implement two-key rotation with `lastUsedAt` tracking, and perform a rotation with no downtime.
5. Implement HMAC signing and verification end to end. Write down your canonicalisation rules
   precisely and produce test vectors.
6. Attack your own implementation: replay a captured request. Then add the timestamp check and try
   again. Then add the nonce store and replay within the window.
7. Tamper with one byte of the body and confirm the signature fails.
8. Verify a real Stripe or GitHub webhook signature against their documented scheme.
9. Set up mTLS locally with self-signed certificates and nginx. Then send `X-SSL-Client-CN` yourself
   without a certificate and confirm whether your setup strips it.
10. Grep any project you have for secrets in URLs, source, or committed config.

---

# 🚪 Exit questions

1. What is a bearer credential, and what does that imply?
2. Why must a key never appear in a query parameter — name four places it ends up?
3. Give the four parts of a well-designed key format and the purpose of each.
4. Why is SHA-256 the right hash here, unlike for passwords?
5. What is the lookup-id pattern for, and why is constant-time comparison used?
6. What must a dashboard *not* be able to do, and what does it prove if it can?
7. Give the four steps of zero-downtime rotation and what step 3 requires.
8. Give four properties HMAC signing adds over a bearer key.
9. Why are both a timestamp and a nonce required?
10. Why is canonicalisation the usual source of integration bugs?
11. What is mTLS, when is it appropriate, and what is its dominant failure mode?
12. Why is `X-SSL-Client-CN` dangerous, and what makes it safe?
13. What replaces static API keys on a cloud platform, and why is it better?

## 🎙️ Articulation drill

Two minutes: **"How would you secure a public API for third-party integrators?"**

Scoped, prefixed API keys stored hashed and shown once, with per-key rate limits and expiry;
zero-downtime rotation with two active keys and usage tracking; HMAC signing for high-value
operations and for webhooks you send; and secret-scanning-friendly prefixes so a leak on GitHub is
caught automatically. Close with the direction of travel — short-lived workload identity instead of
static keys wherever the platform supports it.

---

**Previous:** [Day 115](Day-115.md) · **Next:** [Day 116](Day-116.md) — OAuth 2.0

> Next: the protocol that lets a user grant one application limited access to their data on another —
> its four grants, why the implicit grant died, and what PKCE actually protects against.
