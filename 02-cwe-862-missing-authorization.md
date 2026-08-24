CWE-862: Missing Authorization
Definition

The product does not perform an authorization check when an actor attempts to access a resource or perform an action. In simple terms: the app knows who you are (you're logged in), but never actually asks "is this specific person allowed to do this specific thing?" before letting the action happen.

Where it sits in the hierarchy
Parent: CWE-285 (Improper Authorization), which sits under CWE-284 (Improper Access Control — the pillar)
Children: CWE-425 (Direct Request/Forced Browsing), CWE-638 (Not Using Complete Mediation), CWE-939 (Improper Authorization in Handler for Custom URL Scheme)

CWE-862 is an Abstraction: Class — more specific than the CWE-284 pillar, but still general enough that MITRE says it's "ALLOWED-WITH-REVIEW" for mapping real vulnerabilities (unlike 284, which is discouraged entirely). This means you can cite CWE-862 in a report, but you should double-check whether a more specific child (like 425) fits even better.

The core distinction: Missing vs. Incorrect

This is the one thing to really lock in, since you already solved the "incorrect" sibling:

	CWE-862 (Missing)	CWE-863 (Incorrect)
Does a check exist?	No — nothing was ever coded	Yes — but it's flawed/wrong
Example	Admin page has zero role check	Admin page checks role, but only on POST, not GET
Your example	Juice Shop hidden admin section	Method-based access control lab

Think of it as: 862 = the developer forgot to lock the door. 863 = the developer locked the door but used the wrong key logic.

Root cause (per MITRE)
Architecture/Design phase (Omission): the security tactic was never planned in — often happens when a single-user app is later expanded to multi-user without adding authorization
Implementation phase: developer assumes attackers "can't" modify certain inputs (headers, cookies, hidden form fields), so skips validating them server-side
Demonstrative example (from MITRE)

A private-messaging feature authenticates the user (checks username/password), but then displays a message purely based on an id parameter — without checking whether that message actually belongs to the logged-in user. Any authenticated user can change the id and read someone else's private message. Notice: authentication passed, but authorization (checking ownership) never happened.

Your real-world example

Your Juice Shop "hidden admin section" challenge is a textbook CWE-862 — the /#/administration route existed and rendered fully, with no server-side check on whether the requesting user actually had admin privileges. It wasn't that the check was wrong; there simply wasn't one at all for that route.

How to test for it (pentester checklist)
Enumerate every endpoint/page (don't just click through the UI — check robots.txt, JS bundles, API docs, sitemap)
For each sensitive page/action, try reaching it directly with a low-privilege or unauthenticated session — don't rely on the UI hiding a button
Pay special attention to newly added features — this is where checks get forgotten most often (MITRE explicitly notes this as a common cause)
Check admin/API routes that aren't linked anywhere in the UI (forced browsing territory — this overlaps with CWE-425)
Prevention (per MITRE's mitigations)
Default deny: explicitly grant access rather than assuming access unless denied
Enforce authorization server-side on every request, not just via UI hiding
Use a vetted authorization framework/library instead of hand-rolling checks (e.g., role-based access control, RBAC)
Ensure checks are tied to actual business logic, not just generic resource-level rules — e.g., a medical record should check "is this the patient or their doctor," not just "is this user authenticated"
PortSwigger labs for this CWE
Unprotected admin functionality
Unprotected admin functionality with unpredictable URL
References
CWE-862 – MITRE
OWASP A01:2021 – Broken Access Control

Want me to help you write up your Juice Shop admin-section solution as the worked example for your GitHub notes, formatted to match your CWE-284 entry?

make sure this will be like for everyone in professional way so everyone can understand and secondly also mention portswigger labs in reference unprotected admin functionality and unprotected admin functionality with unpredictable url

CWE-862: Missing Authorization
Definition

Missing Authorization occurs when a system verifies who a user is (authentication) but fails to verify what that user is allowed to do (authorization) before granting access to a resource or action. The application performs no authorization check at all — it isn't that the check is broken or incomplete, it simply doesn't exist.

Position in the CWE Hierarchy
Pillar (root): CWE-284 – Improper Access Control
Parent: CWE-285 – Improper Authorization
CWE-862 – Missing Authorization (this entry)
Children: CWE-425 – Direct Request ('Forced Browsing'), CWE-638 – Not Using Complete Mediation, CWE-939 – Improper Authorization in Handler for Custom URL Scheme

MITRE classifies CWE-862 as a Class-level weakness — more specific than the abstract "Improper Access Control" pillar, but still broad enough that its more specific children (like CWE-425) are often a better fit for a given finding.

Missing Authorization vs. Incorrect Authorization

These two are commonly confused, so it's worth being precise:

	CWE-862 (Missing)	CWE-863 (Incorrect)
Does an authorization check exist?	No	Yes, but it's flawed
Example	An admin page has no role verification whatsoever	A role check exists, but only applies to some request methods (e.g. blocks POST but not GET)

In short: CWE-862 is the absence of a control. CWE-863 is a control that exists but fails to work correctly.

Why This Happens

According to MITRE, this weakness is typically introduced in one of two ways:

Architecture and Design (Omission): Authorization was never planned into the system. This frequently happens when an application originally built for a single user (or a single trust level) is later extended to support multiple users or roles, without adding the corresponding access checks.
Implementation: A developer assumes that certain inputs — such as headers, cookies, or hidden form fields — cannot be manipulated by an attacker, and therefore does not validate them server-side.
Illustrative Example

Consider a private messaging feature. The application correctly authenticates the user (verifies username and password), but then retrieves and displays a message based solely on a user-supplied id parameter — without checking whether that message actually belongs to the logged-in user:

GET /messages?id=482

If the server never verifies that message 482 belongs to the requesting user, any authenticated user can simply change the id value and read messages belonging to other people. Authentication succeeded; authorization was never performed.

Real-World Impact

Missing authorization checks are a common root cause behind privilege escalation, unauthorized data access, and data modification in production applications. Historical CVEs mapped to this weakness include cases such as administrative scripts left reachable to any authenticated user, and REST API endpoints that omit authorization checks entirely, allowing unintended read or write access to sensitive resources.

How to Test for It
Enumerate all application endpoints, not just those visible through the UI (check JavaScript bundles, API documentation, sitemap files, and robots.txt)
Attempt to access sensitive or administrative functionality directly, using a low-privilege or unauthenticated session, rather than relying on whether a link or button is visible
Pay close attention to recently added or less-visited features, since these are statistically more likely to have been overlooked during implementation
Test both the UI-facing and API-facing versions of the same functionality — a check applied in one layer is sometimes missing in the other
Prevention
Apply a default-deny model: explicitly grant access to specific roles/users rather than assuming restriction unless denied
Enforce authorization checks server-side, on every request, regardless of what the client-side interface displays
Use a vetted, well-tested authorization framework or library rather than implementing custom logic
Tie authorization decisions to actual business logic and resource ownership — not just to "is this user logged in"
PortSwigger Web Security Academy Labs
Unprotected admin functionality
Unprotected admin functionality with unpredictable URL
References
CWE-862: Missing Authorization — MITRE
OWASP A01:2021 – Broken Access Control
PortSwigger — Access Control Vulnerabilities
