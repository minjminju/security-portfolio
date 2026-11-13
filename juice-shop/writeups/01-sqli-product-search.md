# UNION-based SQL Injection in Product Search (OWASP Juice Shop)

## Summary

The product search endpoint builds its SQL query by concatenating the `q`
parameter directly into a `LIKE` clause, with no sanitization and without
requiring authentication. By breaking out of that clause with a `UNION SELECT`,
an attacker can read arbitrary data from the database — including every user's
email address and password hash.

## Target
- Application: OWASP Juice Shop 20.4.0
- Endpoint: `GET /rest/products/search`
- Parameter: `q`
- Access required: none (unauthenticated)
- Severity: High (CVSS 7.5)

## Steps to Reproduce

1. Send a normal request to confirm the baseline:
   `GET /rest/products/search?q=apple` returns matching products.
2. Find the column count that `UNION` requires. A `UNION SELECT` with 9 columns
   returns `SQLITE_ERROR: SELECTs to the left and right of UNION do not have the
   same number of result columns`; 10 columns succeeds. The error message itself
   reveals the correct count.
   ```
   q=xyz')) UNION SELECT '1','2','3','4','5','6','7','8','9','10'-- -
   ```
   The injected placeholders appear in the product fields of the response
   (`'2'` as the name, `'3'` as the description), confirming control over the
   output.
3. Replace the placeholders with real columns from the `Users` table:
   ```
   q=xyz')) UNION SELECT '1', email, password, '4','5','6','7','8','9','10' FROM Users-- -
   ```
4. The response returns the email address and password hash of all 38 users,
   including `admin@juice-sh.op`, whose MD5 hash cracks to a trivial password.

## Root Cause — Why It Works

The search query is built by string concatenation:

```sql
SELECT * FROM Products
WHERE ((name LIKE '%<q>%' OR description LIKE '%<q>%') AND deletedAt IS NULL)
ORDER BY name
```

Because `q` is inserted verbatim, the payload `xyz')) UNION SELECT ...-- -`:

- closes the string literal opened by `'%`,
- closes the two opening parentheses `((` with `))`,
- appends an attacker-controlled query with `UNION SELECT`,
- and comments out the rest of the original query with `-- -`.

This replaces the original product search with data extraction. `UNION` only
works when both SELECTs return the same number of columns (10 here); once that
matches, any table — such as `Users` — can be read.

## Impact

No authentication is required, and the credentials of every user are exposed,
administrators included. The leaked password hashes can be cracked offline, and
because people often reuse passwords, the same credentials may unlock other
services (credential stuffing). In practice this is a direct path to full
account takeover.

## Remediation

Never build SQL by concatenating user input. Use parameterized queries
(prepared statements) or the ORM's safe query builder so that `q` is always
treated as data, never as SQL. Input validation and a least-privilege database
account add defense in depth.

## What I Learned

I now understand SQL injection and UNION-based injection in practice. This time
I used Strix to run the attack and observe its behavior; next time I want to
find and exploit the bug myself, to fully understand both the attack and how to
defend against it.

---
*Target: self-hosted OWASP Juice Shop (authorized lab). Finding originally
surfaced by an AI pentest agent (Strix), then reproduced and documented by hand
by me.*
