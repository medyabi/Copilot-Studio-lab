---
name: it-ticket-triage
description: Use this skill whenever you must classify, prioritize or route a Contoso IT request or ticket - for example a new item in the IT Requests list, or a user who describes a problem and asks for help. It returns a category, a priority, an assigned team and a one-line reason.
---

# IT ticket triage (Contoso)

You triage IT requests for the Contoso Service Desk. Always produce the four fields below, in this order.

## 1. Category (pick exactly one)

| Category | Signals in the request |
|---|---|
| Hardware | laptop, screen, monitor, docking station, keyboard, battery, broken, lost, stolen |
| Software | install, license, Visio, Project, Power BI, app crashes, update |
| Network | VPN, Global Secure Access, Wi-Fi, slow internet, cannot reach a site |
| Account & Access | password, locked out, MFA, Authenticator, permission, access to a site or a Team |
| Security | phishing, suspicious email, MFA prompt I did not start, malware, data leak |

If two categories fit, choose the one with the **higher risk** (Security > Account & Access > Network > Software > Hardware).

## 2. Priority (pick exactly one)

- **Critical** - security incident, or more than 10 people blocked, or an executive is completely blocked.
- **High** - one person cannot work at all (no laptop, locked out, no network).
- **Normal** - the person can work but something is broken or missing.
- **Low** - a question, a "nice to have", or a request with no deadline.

Lost or stolen devices are always at least **High** (the policy requires reporting within 4 hours).

## 3. Assigned team

| Category | Team |
|---|---|
| Hardware | Endpoint |
| Software | Apps |
| Network | Network |
| Account & Access | Identity |
| Security | SecOps |

## 4. Reason

One sentence, maximum 25 words, explaining the choice. Quote the policy when one applies.

## Output format

Return exactly:

```
Category: <value>
Priority: <value>
AssignedTeam: <value>
Reason: <one sentence>
```

Never invent a category, priority or team that is not listed above.
