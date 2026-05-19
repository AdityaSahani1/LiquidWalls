# Security Policy

## Supported versions

Only the latest released version of LiquidWalls receives security fixes.

| Version | Supported |
|---|---|
| 1.0.0 (latest) | Yes |
| < 1.0.0 (pre-release) | No |

---

## Reporting a vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

If you discover a security issue — including but not limited to data leaks,
authentication bypasses, privilege escalation, or exposure of user secrets — please
report it privately:

**Email:** adisoftcollection@gmail.com  
**Subject line:** `[SECURITY] LiquidWalls — <brief description>`

Include as much of the following as possible:

- Type of vulnerability (e.g. improper data storage, insecure network call, exposed API key)
- Full path of the affected file(s) in the repository
- Any configuration or environment required to reproduce the issue
- Step-by-step reproduction instructions
- Proof-of-concept code or screenshots (if applicable)
- Potential impact assessment

---

## Response timeline

| Step | Target time |
|---|---|
| Acknowledgement of receipt | Within 48 hours |
| Initial assessment & severity classification | Within 5 business days |
| Fix development and testing | Depends on severity — critical: ≤ 7 days, high: ≤ 30 days |
| Public disclosure (coordinated) | After fix is released |

If you have not heard back within 48 hours, follow up to confirm receipt.

---

## Disclosure policy

Adisoft follows **coordinated disclosure**: we ask that you give us a reasonable
amount of time to release a fix before making the vulnerability public. We will
acknowledge your contribution in the release notes (unless you prefer to remain
anonymous).

---

## Known security considerations

These are known, accepted design decisions — not vulnerabilities:

| Item | Rationale |
|---|---|
| Supabase anon key shipped in APK | The anon key is designed to be public; Row-Level Security enforces all access controls. Never put the service-role key in client code. |
| Cloudinary unsigned upload preset | Scoped to a single folder + file-size limit via the Cloudinary dashboard. |
| AdMob Advertising ID accessed by SDK | LiquidWalls itself never reads the AAID; it is handled entirely by the Google Mobile Ads SDK under Google's privacy policies. |
| AI provider keys stored on-device | Keys are stored in encrypted DataStore and are never transmitted by Adisoft's servers. |

---

## Out of scope

The following are out of scope for this security policy:

- Vulnerabilities in third-party libraries (report those to the respective upstream projects)
- Issues requiring physical access to an unlocked device
- Social engineering attacks against Adisoft staff
- Theoretical attacks with no practical exploit path
