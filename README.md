# Vibe-Coding Security Checklist

A pre-launch security checklist for apps built with AI tools — Lovable, Bolt, v0, Cursor, Replit, and the Supabase or Firebase backends behind them.

AI builders ship fast, and most of what they skip is security. You don't need to be an expert to close the common holes. Work through the list below before you share your app, and re-check it whenever you add a feature that touches data or auth.

Want it checked for you? [Scan your URL free at bleek.dev](https://www.bleek.dev/scan) — 26 checks, plain-English fixes, no signup.

---

## Database access

- [ ] **Row-level security is on for every table.** On Supabase, any table with RLS off can be read with the public anon key that ships in your front-end. This is the [most common leak in AI-built apps](https://www.bleek.dev/cve-2025-48757).
- [ ] **Policies are default-deny.** Turn RLS on, then write policies that open only what should be public. See the [Supabase RLS guide](https://supabase.com/docs/guides/database/postgres/row-level-security) or [Firebase Security Rules](https://firebase.google.com/docs/rules).
- [ ] **You tested as a logged-out user.** Try to read a table you shouldn't be able to. If rows come back, the rule is wrong.
- [ ] Deep dive: [Supabase security](https://www.bleek.dev/supabase-security) · [Firebase security](https://www.bleek.dev/firebase-security)

## Secrets and API keys

- [ ] **No secret keys in the client bundle.** Stripe `sk_live`, OpenAI, AWS, and Supabase service-role keys must never appear in front-end JavaScript. Only publishable/anon keys belong there.
- [ ] **Anything exposed has been rotated.** If a secret ever shipped to the browser, treat it as compromised and regenerate it.
- [ ] **Secrets live in environment variables**, set server-side, not hard-coded.

## HTTP security headers

- [ ] **Content-Security-Policy is set** to limit where scripts and resources can load from.
- [ ] **HSTS** forces HTTPS.
- [ ] **X-Content-Type-Options: nosniff** and a sensible **Referrer-Policy** are present.
- [ ] Reference: [MDN security headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) · check yours with the [security headers tool](https://www.bleek.dev/tools/security-headers).

## Exposed files

- [ ] **`.env` and config files are not publicly served.** Visit `/.env`, `/.git/config`, and `/config.json` on your live site — none should return real contents.
- [ ] **No database dumps or backups** (`backup.sql`, `dump.sql`) are reachable.

## Cookies and sessions

- [ ] Session cookies carry **`Secure`**, **`HttpOnly`**, and **`SameSite`** flags.
- [ ] Auth tokens are not stored where any script on the page can read them.

## Transport and email

- [ ] **HTTPS everywhere**, with a valid certificate. Check with the [SSL/TLS tool](https://www.bleek.dev/tools/ssl-checker).
- [ ] **SPF, DKIM, and DMARC** records exist if your app sends email. Check with the [email security tool](https://www.bleek.dev/tools/email-security).

## Dependencies

- [ ] **No known-vulnerable libraries.** Run `npm audit` (or your ecosystem's equivalent) and update anything flagged.

## Auth and business logic

- [ ] **Users can only reach their own data.** Changing an ID in a URL or request should not expose someone else's records.
- [ ] **Sensitive actions re-check permissions server-side**, not just in the UI.
- [ ] Payment webhooks verify signatures before granting access.

---

## Verify it

A checklist tells you what to look for. A scan tells you what's actually exposed right now.

- **Free scan:** [bleek.dev/scan](https://www.bleek.dev/scan) — 26 automated checks against your live URL.
- **Human audit:** [bleek.dev/human-audit](https://www.bleek.dev/human-audit) — a real engineer battle-tests the logic a scanner can't reach.

## License

Released into the public domain under [CC0](LICENSE). Copy it, adapt it, ship it with your project.

---

Maintained by the team at [bleek.dev](https://www.bleek.dev) — we find the security leaks in vibe-coded apps.
