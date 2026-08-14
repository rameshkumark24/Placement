# Day 100A ➕

**L-100A** · Dependency & supply chain hygiene — CVE scanning, lockfiles, transitive dependencies,
licences, slopsquatting

**Time:** 2 hrs · **Mode:** NEW · **Added day** — not in the original roadmap

> **Why this day was added.** Day 073 taught how to *add* a dependency. Nothing in the roadmap ever
> asks what adding one *costs*. That gap matters more every year: **a typical Spring Boot service
> has 5–15 direct dependencies and 150+ transitive ones**, all executing with your application's
> privileges. Supply-chain attacks are the fastest-growing category in application security, and
> Log4Shell (Day 073A) proved that the expensive question is not "are we vulnerable" but "**which of
> our services use this library, at what version**" — a question most teams could not answer for a
> week.
>
> This is also the day where AI-assisted coding introduces a genuinely new risk, which is why it
> belongs in a roadmap written in 2026.

---

# Part 1 — What a dependency actually costs

Adding a line to `pom.xml` is not free. It is:

| Cost | Detail |
|---|---|
| **Code you did not write, running as you** | Full access to your filesystem, network, environment and secrets |
| **Its dependencies too** | One line can pull 40 jars (Day 073's transitivity) |
| **A permanent maintenance obligation** | Upgrades, breaking changes, CVEs, eventual abandonment |
| **A licence obligation** | Some are legally incompatible with your product |
| **Attack surface** | Every transitive dependency is a way in |
| **Build and startup time** | Classpath scanning, more classes to load (Day 076A) |

**There is no such thing as a small dependency.** `left-pad` was 11 lines and its unpublishing broke
a large fraction of the JavaScript ecosystem in 2016.

The question before adding one:

> **Could I write this in an hour? Is the library maintained? How many transitive dependencies does
> it bring? What is its licence?**

`mvn dependency:tree` (Day 073) *before* you add it, not after.

---

# Part 2 — Knowing what you have

## The dependency tree

```bash
mvn dependency:tree
mvn dependency:tree -Dverbose -Dincludes=com.fasterxml.jackson.core
mvn dependency:analyze          # declared-but-unused, and used-but-undeclared ← both are bugs
gradle dependencies
gradle dependencyInsight --dependency jackson-databind
```

`dependency:analyze` finds two distinct problems: **declared but unused** (delete it — free attack
surface reduction) and **used but undeclared** (you are relying on a transitive dependency, so an
unrelated upgrade elsewhere can remove it and break your build with no change of your own).

## The SBOM

A **Software Bill of Materials** is a machine-readable inventory of everything in your artifact —
name, version, hash, licence. Two standard formats: **CycloneDX** and **SPDX**.

```xml
<plugin>
  <groupId>org.cyclonedx</groupId>
  <artifactId>cyclonedx-maven-plugin</artifactId>
  <executions><execution><goals><goal>makeAggregateBom</goal></goals></execution></executions>
</plugin>
```

**Generate an SBOM in CI and store it with the build artifact.** The payoff is exactly the Log4Shell
scenario: when the next critical CVE lands, "which of our 40 services ship this library at what
version" becomes a grep over stored SBOMs instead of a week of archaeology. It is increasingly a
procurement and regulatory requirement too (US Executive Order 14028, the EU Cyber Resilience Act).

## Reproducible builds and lockfiles

The property you want: **the same source produces the same artifact**, today and in two years.

What breaks it:

```xml
<version>2.17.1</version>          <!-- ✅ pinned -->
<version>[2.0,3.0)</version>       <!-- 💀 range: today's build ≠ tomorrow's -->
<version>2.17.1-SNAPSHOT</version> <!-- 💀 mutable (Day 073) -->
<version>LATEST</version>          <!-- 💀 removed in Maven 3, and rightly -->
```

Java's ecosystem is better than most here — Maven Central is **immutable**, so a published version's
bytes never change (unlike npm, where a version can be unpublished, and PyPI historically). But
transitive resolution still varies with your dependency graph, so pin explicitly:

- **Maven**: `<dependencyManagement>` or a BOM for every version that matters; the
  `maven-enforcer-plugin` with `requireReleaseDeps` and `banDynamicVersions` to fail the build on
  ranges and snapshots.
- **Gradle**: `dependencyLocking` writes `gradle.lockfile`; commit it.

```groovy
dependencyLocking { lockAllConfigurations() }
```

Always use the **wrapper** (Day 073) so the build tool itself is pinned.

---

# Part 3 — Vulnerabilities

## Scanning

```bash
mvn org.owasp:dependency-check-maven:check         # OWASP Dependency-Check → NVD CVE database
gradle dependencyCheckAnalyze
grype dir:.                                        # or against an image: grype myimage:tag
trivy fs .   /   trivy image myapp:1.2.3
osv-scanner -r .                                   # Google's OSV database
```

Plus platform tooling: **GitHub Dependabot** (alerts and automated PRs), **Snyk**,
**Renovate** (more configurable auto-updates), and **GitHub's dependency review action**, which fails
a PR that *introduces* a vulnerable dependency.

**Put a scan in CI and fail the build on high or critical.** A scan whose report nobody reads is
theatre.

```yaml
# fail the build, do not just warn
- run: mvn org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=7
```

## Reading a CVE properly

Not every finding is an emergency, and treating them all as one guarantees they all get ignored.

**CVSS is severity, not risk.** Ask three questions:

1. **Do we use the vulnerable code path?** A deserialization CVE in a library you use only for
   parsing may be unreachable. *Reachability analysis* (Snyk, some Grype configurations) automates
   this partially.
2. **Is it exploitable in our deployment?** Is the affected component exposed to untrusted input?
3. **What is the actual impact if exploited?**

The prioritisation source worth knowing by name is **CISA KEV** — the Known Exploited Vulnerabilities
catalogue. A medium-severity CVE on the KEV list is being exploited *right now* and outranks a
theoretical critical.

**Suppress false positives explicitly and with a reason**, so the list stays credible:

```xml
<suppress>
  <notes>CVE-2023-XXXX affects the servlet module; we use only the core jar.</notes>
  <cve>CVE-2023-XXXX</cve>
</suppress>
```

And record an expiry date. A permanent suppression is a decision nobody revisits.

## Upgrade discipline

> **Small, frequent upgrades. Not one big upgrade a year.**

The reason is compounding: dependencies that drift three major versions cannot be upgraded at all
without a project, so they never are, and then a critical CVE forces the project under emergency
conditions — which is the worst possible time to do it.

Practical setup: Renovate or Dependabot grouping patch and minor updates into a weekly PR, merged
automatically if CI is green; major updates raised individually and reviewed. This only works if your
test suite is trustworthy (Day 097), which is one more argument for it.

---

# Part 4 — Supply-chain attacks

## The attack classes

| Attack | Mechanism |
|---|---|
| **Typosquatting** | `reqeusts`, `jackson-datbind` — a package named like a real one |
| **Dependency confusion** | A public package with your *internal* name and a higher version; the resolver prefers it |
| **Account takeover** | A maintainer's credentials are stolen and a malicious version published |
| **Malicious maintainer / handover** | An abandoned package is transferred to an attacker (`event-stream`, 2018) |
| **Protestware** | A maintainer deliberately sabotages their own package (`node-ipc`, 2022) |
| **Build-system compromise** | The attack is on CI, not the source (SolarWinds, 2020) |
| **Slopsquatting** | ⚠️ see below |

**Dependency confusion** deserves specific attention because the fix is configuration, not vigilance:
if your build can reach both an internal repository and a public one, publishing
`com.acme.internal-utils` version `99.0.0` publicly may win. **Configure your repository manager so
internal namespaces are never resolved externally**, and claim your namespaces publicly.

**SolarWinds is the one that reframes the problem**: the source code was clean and the *build* was
compromised. Hence signing artifacts, hardened build environments, and **provenance attestation**
(SLSA levels, Sigstore/cosign) — proof of *what built this artifact, from which source*.

## ⚠️ Slopsquatting — the new one

LLM coding assistants **hallucinate package names**. Ask for a library to do X and the model may
confidently suggest `com.acme:json-fast-parser` — a plausible name for a package that does not exist.

Attackers now **monitor for commonly hallucinated names and register them**, with real malicious
payloads. Research in 2024–25 found roughly 20% of package suggestions from popular models referenced
non-existent packages, with names repeating consistently enough to be predictable — which is exactly
what makes the attack viable.

The name is a play on typosquatting: the mistake is not a typo, it is *slop*.

**Defences, and they are simple:**

- **Verify every AI-suggested dependency exists on the official registry before adding it** — Maven
  Central, npm, PyPI. Check the publisher and the download count.
- **Be suspicious of a package that is new, has few downloads, and has exactly the name you needed.**
  That combination is the signature.
- Never let an agent add dependencies without human review.
- Prefer well-known libraries over ones you have never heard of, even when the obscure one looks
  perfect.

This connects directly to the roadmap's **one rule** — *no AI-generated code during lessons* — and to
the vibe-coding domains in this repository: an AI-suggested import is exactly the kind of thing that
looks correct and passes review by inattentive eyes.

## Hardening your own build

```yaml
# GitHub Actions
permissions:
  contents: read                   # ← least privilege, not the default write-all
jobs:
  build:
    steps:
      - uses: actions/checkout@8f4b7f84864484a7bf31766abe9204da3cbe65b3   # ← pin to a SHA, not @v4
```

**Pin actions to a commit SHA, not a tag.** A tag is mutable: `@v4` can be repointed at malicious
code by whoever controls the repository. This is the same content-addressing argument as Day 084.

The rest of the checklist: least-privilege tokens, no secrets in the build log (Day 073A), short-lived
credentials (OIDC rather than long-lived keys), a separate registry credential per environment, and
signing published artifacts.

---

# Part 5 — Licences

The part engineers ignore until Legal arrives.

| Family | Examples | Obligation |
|---|---|---|
| **Permissive** | MIT, Apache 2.0, BSD | Attribution. Apache 2.0 also grants patent rights. |
| **Weak copyleft** | LGPL, MPL, EPL | Modifications to *that library* must be shared |
| **Strong copyleft** | GPL v2/v3 | **Derivative works must be released under GPL** |
| **Network copyleft** | **AGPL** | ⚠️ Triggers on *network use*, not distribution |
| **Source-available** | SSPL, BUSL, Elastic | Not open source; usually restricts SaaS offerings |

**AGPL is the one that surprises people.** GPL obligations trigger on *distribution*; AGPL triggers
when users interact with the software **over a network** — so using an AGPL library in a SaaS backend
can obligate you to publish your source. Many companies ban it outright for that reason.

**SSPL and BUSL are not open-source licences** despite living in the same ecosystems (MongoDB,
Elasticsearch, Redis and Terraform all relicensed this way). They restrict offering the software as a
service. Know the difference between "free to use" and "open source".

```bash
mvn license:aggregate-add-third-party      # generate the inventory
```

Automate the check in CI with an allow-list — GPL/AGPL discovered at release time is a genuine
schedule problem, and it is trivially preventable.

---

## Common mistakes

| Mistake | Consequence |
|---|---|
| Adding a dependency without checking its tree | 40 transitive jars, unknown licences |
| Version ranges or SNAPSHOTs | Non-reproducible builds |
| No SBOM | Cannot answer "are we affected" for days |
| Scanning but not failing the build | Reports nobody reads |
| Treating every CVE as critical | Real ones get lost in noise |
| Permanent unexplained suppressions | Real vulnerabilities hidden |
| One big annual upgrade | Becomes impossible; then it is an emergency |
| CI actions pinned to tags | A mutable tag can be repointed |
| Internal names resolvable from public repos | Dependency confusion |
| Adding an AI-suggested package unverified | **Slopsquatting** |
| Ignoring licences until release | AGPL discovered at the worst moment |

---

## Interview questions

**Q: How do you manage dependency security?**
Scan in CI and fail on high or critical, generate and store an SBOM per build, pin versions and use a
BOM, upgrade in small frequent increments with automated PRs, and prioritise findings by reachability
and exploitability rather than CVSS alone — with CISA KEV outranking severity.

**Q: Log4Shell lands tomorrow. How fast can you answer "are we affected"?**
As fast as you can grep your stored SBOMs, if you generate them. Otherwise it is `mvn
dependency:tree` across every service by hand, which is what cost teams a week in 2021.

**Q: What is dependency confusion, and how do you prevent it?**
An attacker publishes a public package matching your internal package name at a higher version, and
the resolver prefers it. Prevent it by configuring the repository manager so internal namespaces
never resolve externally, and by claiming your namespaces publicly.

**Q: What is slopsquatting?**
Attackers register package names that LLM coding assistants commonly hallucinate, so a developer who
copies a suggested import installs malware. Defence: verify every AI-suggested package exists on the
official registry, and treat a brand-new low-download package with exactly the right name as a red
flag.

**Q: Why pin CI actions to a SHA?**
Tags are mutable. `@v4` can be repointed at malicious code by whoever controls the repository; a
commit SHA is content-addressed and cannot change.

**Q: What is the risk with AGPL?**
Its copyleft obligation triggers on network interaction rather than distribution, so using an AGPL
library in a hosted service can require publishing your source. SSPL and BUSL are separately
problematic — they are source-available, not open source, and restrict SaaS use.

**Q: Why are small frequent upgrades better?**
Drift compounds. Three major versions behind cannot be upgraded incidentally, so it never happens
until a critical CVE forces it under emergency conditions — the worst possible circumstances.

---

## Mini task

1. Run `mvn dependency:tree` on a project and count the transitive dependencies behind your direct
   ones. Record the ratio.
2. Run `mvn dependency:analyze` and act on both categories it reports.
3. Add the CycloneDX plugin, generate an SBOM, and open it. Then grep it for a library version, as if
   responding to a CVE.
4. Run OWASP Dependency-Check or `osv-scanner`. Triage the top five findings by reachability, not by
   CVSS.
5. Wire the scan into CI with `-DfailBuildOnCVSS=7` and confirm it fails on a deliberately outdated
   dependency.
6. Add the enforcer plugin with `banDynamicVersions` and `requireReleaseDeps`, then try to add a
   SNAPSHOT and watch the build fail.
7. Generate the licence inventory for a project. Find anything copyleft. Decide what you would do.
8. Take three package names an AI assistant has suggested to you and verify each exists on the
   official registry with a plausible download count and publisher.
9. Pin every GitHub Action in one workflow to a commit SHA.

---

# 🚪 Exit questions

1. List six costs of adding a dependency.
2. What two problems does `dependency:analyze` find, and why is each a bug?
3. What is an SBOM, and what concrete question does it answer quickly?
4. What breaks build reproducibility in Maven, and what enforces it?
5. Why is CVSS not the same as risk? Name the three questions and the KEV list.
6. Why are small frequent upgrades better than annual ones?
7. Explain dependency confusion and its configuration-level fix.
8. What did SolarWinds change about how the industry thinks about supply chain?
9. Define slopsquatting and give three defences.
10. Why must CI actions be pinned to SHAs?
11. What makes AGPL different from GPL, and why does that matter for a SaaS backend?
12. What is the difference between "source-available" and "open source"?

## 🎙️ Articulation drill

Two minutes: **"How do you think about third-party dependencies?"**

Frame it as a cost decision — code running with your privileges, plus a permanent maintenance and
licence obligation — then the controls: pin and lock, SBOM per build, scan and fail CI, prioritise by
reachability and KEV, upgrade continuously. Finish with the two modern threats, dependency confusion
and slopsquatting, because naming a 2025-era risk shows you are current rather than reciting a 2015
checklist.

---

**Previous:** [Day 100](Day-100.md) · **Next:** [Day 101](Day-101.md) — documentation and ADRs

> Tomorrow closes Stage 2: documentation that stays true — READMEs, architecture decision records,
> and diagrams that do not rot.
