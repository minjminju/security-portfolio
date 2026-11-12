<!--
これは「書く枠」だけ。各セクションの英語は自分で書く(ここが勉強)。
Strixの penetration_test_report.md は"答え合わせ"にだけ使う。コピペ禁止。
疲れてる日の最小単位 = Summary を英語で1段落書く。それだけでも連続を切らさない。
-->

# UNION-based SQL Injection in Product Search (OWASP Juice Shop)

> Write every section in English, in your own words.

## Summary
<!-- 2-3 sentences: what the bug is, where, and why it matters. -->


## Target
- Application: OWASP Juice Shop 20.4.0
- Endpoint: `GET /rest/products/search`
- Parameter: `q`
- Access required: none (unauthenticated)
- Severity: High (CVSS 7.5)

## Steps to Reproduce
<!-- Number the exact steps. You will fill the real payload tomorrow when you reproduce it by hand (AM hands-on). For tonight, write the steps as you understand them. -->
1.
2.
3.

## Root Cause — Why It Works
<!-- Explain: string concatenation into a LIKE query, breaking out, UNION needs matching column count (10), injecting sqlite_master / Users. In YOUR words. -->


## Impact
<!-- What can an attacker actually get? Tie it to business impact. -->


## Remediation
<!-- What fix stops it? (parameterised queries / ORM predicates). One or two sentences. -->


## What I Learned
<!-- Your own takeaway. This is the part that proves it's really yours. -->


---
*Target: self-hosted OWASP Juice Shop (authorized lab). Finding originally surfaced by an AI pentest agent (Strix), then studied and re-documented by me.*
