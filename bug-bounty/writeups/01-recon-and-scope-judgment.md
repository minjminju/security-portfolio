<!--
DRAFT SKELETON — fill in the [TODO] parts in your own words (DeepL -> understand -> retype; don't paste blindly).

SANITIZATION RULE (read before committing/pushing):
This is a PUBLIC writeup of an ONGOING engagement under a coordinated-disclosure window.
Keep it METHODOLOGY ONLY. Do NOT add any of the following:
  - the real program / organization name
  - real hostnames, subdomains, or product names that identify the target
  - the specific "which asset is testable / which door is open" map
  - any credentials or test-account details
Generalize everything: "a large government VDP", "the downstream application",
"the vendor identity provider". Use invented examples like `api.example.gov` if needed.
Avoid narrowing words that could fingerprint the target.
-->

# Recon and Scope Judgment on a Large Government VDP — A Methodology Walkthrough

## Context

This writeup documents the reconnaissance methodology and the judgment calls I made
during my first real-world engagement on a public **Vulnerability Disclosure Program
(VDP)** run by a large government organization on HackerOne.
To be clear up front, this engagement was never about landing a bug — I took it on
purely to learn. A large government VDP offers an enormous attack surface: hundreds of
subdomains and applications, which means plenty of material to practice reconnaissance
and triage on. Its scope is also clearly published, so it is a safe, legal environment to
practice in. The program pays no bounty, but that was fine — my goal was not money. I
wanted to build a recon-and-triage process I could reuse, and to earn public recognition
I could point to (a Thanks / Hall of Fame entry) and put in my portfolio.

> **Authorization & ethics.** I am enrolled in this VDP and tested only assets that are
> explicitly in scope, acting in good faith under the program policy. All target names
> and internal hostnames are anonymized here because the engagement is ongoing and
> subject to a coordinated-disclosure window. This writeup covers methodology and
> judgment only — it contains no live target details and no reproducible attack paths.

## The mindset: judgment before firing

Most of bug bounty hunting is empty space. Most of the hosts you look at are dead ends —
hardened and locked down, out of scope, or impossible to test legitimately. Coming up
empty is the normal case, and hammering a dead target wastes time. Worse, testing
something out of scope is unauthorized access, and firing off false-positive reports both
wastes time and damages your reputation (your "signal") with the program. So before I
sent a single request, I always asked two questions: is this target in scope, and can I
actually test it?

## 1. Passive reconnaissance (OSINT only)

I started with passive enumeration — no packets sent to the target.

- **Subdomain enumeration** with `subfinder` surfaced [TODO: a large, rounded number]
  of subdomains across the organization's domains.
- [TODO: your own words — the shared-codebase observation: many near-identical sites run
  on one codebase, so a single bug likely exists across all of them, but it should be
  reported only once to avoid duplicates and protect signal.]

<!-- Keep numbers rounded/vague. Do not list real subdomains. -->

## 2. Triage with httpx

[TODO: your own words — how you used `httpx` with rate limiting to check which hosts were
live, plus their status codes, titles, and detected tech. Mention that you distinguished
two kinds of `403`: a WAF / bot-management block versus a plain load-balancer `403`, and
that you deliberately did NOT attempt to bypass the WAF, because evasion crosses a line
the program does not authorize.]

## 3. Scope discipline as the qualification gate

Scope is the one real gate that keeps testing authorized, so I treated it as a hard
precondition, not an afterthought.

- **Asset types matter.** A wildcard asset (`*.example.gov`) puts every subdomain in
  scope; a URL-type asset (`https://host.example.gov`) covers only that one exact host.
  [TODO: your own words — reading the asset type wrong is a life-or-death mistake.]
- **Verify scope live, every time.** [TODO: your own words — tell the story where one of
  my own older notes claimed an asset was out of scope, but checking the live scope list
  showed it was actually in scope and rated critical. The lesson: never trust a stale
  note for scope; confirm against the source of truth before touching anything.]

## 4. Reading the tech stack from the surface

[TODO: your own words — how you inferred the backend from URLs and page structure. For
example, one login surface revealed a classic server-rendered framework whose objects
used sequential integer IDs (a natural IDOR candidate), while another delegated
authentication to an external identity provider. Keep product names generic if naming
them would fingerprint the target.]

## 5. The identity-provider / vendor scope line

When authentication is delegated to a third-party **identity provider (IdP)**, the auth
flow itself is usually out of scope — it belongs to the vendor, and bugs there go to the
vendor. But the application *behind* the IdP can still be in scope and testable.

[TODO: your own words — the decisive question is whether **public self-registration** is
possible. If you can create your own test accounts, the downstream application is
testable with a classic two-account approach. If registration is gated behind data you
cannot legitimately obtain, it is not. I compared two targets on exactly this line: one
was a dead end, the other was live.]

## 6. Knowing when NOT to test

[TODO: your own words, 3-4 sentences — the ethics centerpiece. One target required
real, government-issued identifiers to create an account. I did not have them, and I
refused to fabricate them. That made a two-account test impossible there, so I retreated.
Make clear this was a deliberate discipline call, not a dead end I stumbled into — the
willingness to walk away from a target you cannot test legitimately is part of the job.]

## 7. Tooling notes

[TODO: your own words — your setup was a browser proxied through an intercepting proxy
with its CA certificate installed in the browser's own trust store. One practical problem
and fix worth sharing: the proxy's DNS resolution intermittently failed with "unknown
host," even though the OS resolved the name fine, so I added a static hostname-resolution
override (pinning the host to a resolved IP) to bypass the proxy's flaky resolver.]

## 8. Isolating infrastructure problems

A good tester separates "is it me or the target?" quickly instead of burning hours
debugging the wrong layer.

[TODO: your own words — I hit repeated gateway timeouts on the core authenticated pages.
I confirmed, using the CDN's own diagnostic page, that the failing component was the
origin server — not my browser and not the CDN. That told me the blocker was on the
target's side, so the correct move was to record the state and retry later, not to keep
re-debugging my own environment.]

## Lessons learned

- [TODO: Scope discipline is the real qualification gate — your own words.]
- [TODO: A clean "no finding" is a legitimate, professional result — not a failure.]
- [TODO: Refusing to fabricate data to reach a target is part of the job.]
- [TODO: Verify scope live; never trust a stale note.]
- [TODO: Judgment beats tooling — deciding *what* and *whether* to test is the real skill.]

---
*Engagement: a large government VDP on HackerOne (anonymized, ongoing). I am enrolled in
the program and tested only in-scope assets in good faith under its policy. This writeup
is methodology only and contains no live target details.*
