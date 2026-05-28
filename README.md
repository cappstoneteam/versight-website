# Versight marketing site

Static landing site at https://versight.app — built as plain HTML/CSS, no
build step. Mirrors the iOS app's design tokens (palette in
`ios/Versight/Design/VColors.swift`, typography in `VTypography.swift`)
so the brand reads the same on web.

## Files

| File              | What it is                                                       |
|-------------------|------------------------------------------------------------------|
| `index.html`      | Landing page — hero, benefits grid, testimonial, App Store CTA   |
| `privacy.html`    | Privacy Policy (served as `/privacy`)                            |
| `terms.html`      | Terms of Use (served as `/terms`)                                |
| `contacts.html`   | Contact form (served as `/contacts`) — posts to Web3Forms        |
| `style.css`       | Shared styles + design tokens                                    |
| `icon.png`        | Copy of the iOS app icon (1024×1024)                             |
| `robots.txt`      | Allow all + sitemap pointer                                      |
| `sitemap.xml`     | 4 URLs (home + 3 docs)                                           |
| `_redirects`      | URL normalization (Cloudflare Pages / Netlify format)            |
| `_headers`        | Security + cache headers (Cloudflare Pages / Netlify format)     |

## Local preview

No tooling required — just open `index.html` in a browser, or for
proper absolute-path routing (`/style.css`, `/icon.png`) run a tiny
static server from this directory:

```sh
cd website
python3 -m http.server 4000
# → http://localhost:4000
```

## Deployment — Cloudflare Pages (recommended)

Cloudflare Pages is free for static sites and has the best routing for
`/foo.html → /foo` clean URLs out of the box. The `_redirects` and
`_headers` files are honored natively.

1. **Connect the repo** at https://dash.cloudflare.com → Pages →
   Create application → Connect to Git → pick the Versight repo.
2. **Build settings**:
   - Production branch: `main`
   - Build command: *(leave blank — no build)*
   - Build output directory: `website`
3. **Deploy**. First build takes ~30s. You'll get a default URL like
   `versight-pages.pages.dev`.
4. **Custom domain**: Pages project → Custom domains → Set up custom
   domain → enter `versight.app` and `www.versight.app`. Cloudflare
   will tell you which DNS records to add (see below).

## DNS configuration

If `versight.app` is registered with Cloudflare, the records are added
automatically when you attach the custom domain. If it's registered
elsewhere (Namecheap, GoDaddy, Porkbun, etc.), you have two paths:

### Path A — keep DNS at the current registrar

Add these records in your registrar's DNS panel:

| Type   | Name (host)      | Value                                           | TTL   |
|--------|------------------|-------------------------------------------------|-------|
| CNAME  | `@` (apex/root)  | `<your-project>.pages.dev`                      | Auto  |
| CNAME  | `www`            | `<your-project>.pages.dev`                      | Auto  |

If your registrar does not support CNAME at the apex (most don't —
e.g. Namecheap, GoDaddy), use **ALIAS** / **ANAME** if available, or
fall back to Cloudflare's anycast A records:

| Type | Name | Value           |
|------|------|-----------------|
| A    | `@`  | `192.0.2.1`     |
| AAAA | `@`  | `100::`         |

(Cloudflare Pages routes any request to those reserved IPs to your
project — the actual IPs are dynamic, but those two values work as
sentinel pointers.)

Then in Cloudflare Pages → Custom domains, click **Verify** and
Cloudflare will issue an SSL cert for both `versight.app` and
`www.versight.app` automatically (within a few minutes).

### Path B — move DNS to Cloudflare (easiest long-term)

1. Cloudflare dashboard → Add site → enter `versight.app` → choose Free.
2. Cloudflare imports your existing DNS records.
3. Copy the two nameservers it gives you (e.g. `ada.ns.cloudflare.com`
   and `kirk.ns.cloudflare.com`).
4. Go to your registrar → domain settings → **Nameservers** → switch
   from the default to Custom and paste the Cloudflare nameservers.
5. Propagation takes 1–24 hours. Once Cloudflare confirms, attach the
   custom domain in Pages and SSL provisioning is one click.

### What you'll need to configure at your registrar's dashboard

To summarize, the only thing you actually touch at the registrar:

- **Nameservers** (if going Path B) — replace with the two Cloudflare
  nameservers.
- **DNS records** (if going Path A) — add the CNAME(s) above pointing
  at `<your-project>.pages.dev`.

Nothing else (no MX, no TXT, no SPF/DKIM) is required for the
marketing site itself. If you want email forwarding for
`contact@versight.app` later, that's a separate set of MX/TXT records.

## Contact form — Web3Forms setup

`contacts.html` posts to https://web3forms.com — a free email-forwarding
service for static sites. Before going live:

1. Visit https://web3forms.com and sign up with **cappstoneteam@gmail.com**
   (free tier: 250 submissions/month).
2. From the dashboard, copy your **Access Key**.
3. In `contacts.html`, replace `YOUR_WEB3FORMS_ACCESS_KEY` with the
   real key.
4. Click the verification link in the email Web3Forms sends to
   `cappstoneteam@gmail.com` — submissions won't be delivered until
   the address is confirmed.
5. Commit + redeploy. Test by submitting the form yourself.

Alternative services (drop-in replacements — just swap the `action`
URL): Formspree, Basin, Getform, FormSubmit, Tally.

## App Store link

The CTA buttons in `index.html` currently point at a placeholder URL:

```
https://apps.apple.com/app/versight/id0000000000
```

Replace `id0000000000` with the real App ID once the app is approved
on the App Store (you'll find it in App Store Connect → App
Information → Apple ID).
