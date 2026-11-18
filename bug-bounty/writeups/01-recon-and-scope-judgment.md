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

---

*This writeup is a work in progress. Sections still being written cover passive
reconnaissance (OSINT only), triage with `httpx`, scope discipline as the qualification
gate, reading the tech stack from the surface, the identity-provider / vendor scope line,
knowing when not to test, tooling notes, and isolating infrastructure problems.*

---
*Engagement: a large government VDP on HackerOne (anonymized, ongoing). I am enrolled in
the program and tested only in-scope assets in good faith under its policy. This writeup
is methodology only and contains no live target details.*
