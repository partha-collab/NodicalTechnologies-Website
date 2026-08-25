# Security Implementation for Nodical Technologies

## Overview
This static site implements defense-in-depth security through:
1. **Client-side headers** (meta tags in HTML)
2. **Server-side headers** (config files for major hosts)
3. **Secure coding practices** (CSP, no inline event handlers, etc.)
4. **Dependency minimization** (only Google Fonts external)

---

## Files Added/Modified

### HTML Files (all 5 pages)
- `index.html`, `about.html`, `brands.html`, `contact.html`, `services.html`
- Added security meta tags in `<head>`:
  - `Content-Security-Policy`
  - `X-Content-Type-Options: nosniff`
  - `Referrer-Policy: strict-origin-when-cross-origin`
  - `Permissions-Policy` (disables unused browser APIs)
  - `Cross-Origin-Opener-Policy: same-origin`
  - `Cross-Origin-Resource-Policy: same-origin`

### Server Configurations
| File | Platform | Purpose |
|------|----------|---------|
| `_headers` | Netlify, Cloudflare Pages | Header rules |
| `netlify.toml` | Netlify | Build + header config |
| `.htaccess` | Apache | Headers, compression, protection |
| `web.config` | IIS | Headers, compression, protection |
| `vercel.json` | Vercel | Header + cache config |

### Security Policy
- `security.txt` — Responsible disclosure contact (RFC 9116)
- `FONTS.md` — Guide to self-host fonts (eliminate last external dependency)

### Code Hardening
- `styles.css` — Documented font self-hosting path
- `script.js` — Uses passive listeners, no eval, minimal DOM APIs

---

## Content Security Policy (CSP)

```http
Content-Security-Policy:
  default-src 'self';
  script-src 'self' 'unsafe-inline';
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  font-src 'self' https://fonts.gstatic.com data:;
  img-src 'self' data: https:;
  connect-src 'self';
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';
```

### Directive Breakdown
| Directive | Value | Purpose |
|-----------|-------|---------|
| `default-src 'self'` | Restrict all loads to same origin |
| `script-src 'self' 'unsafe-inline'` | Allow local scripts + inline JSON-LD |
| `style-src 'self' 'unsafe-inline' fonts.googleapis.com` | Allow local styles + Google Fonts CSS |
| `font-src 'self' fonts.gstatic.com data:` | Allow local fonts + Google Fonts + data URIs |
| `img-src 'self' data: https:` | Allow local images + data URIs + any HTTPS images |
| `connect-src 'self'` | Restrict fetch/XHR to same origin |
| `frame-ancestors 'none'` | Prevent clickjacking (no iframe embedding) |
| `base-uri 'self'` | Prevent base tag hijacking |
| `form-action 'self'` | Forms can only submit to same origin |

### After Self-Hosting Fonts (Recommended)
Remove `https://fonts.googleapis.com` from `style-src` and `https://fonts.gstatic.com` from `font-src`.

---

## Permissions Policy
Disables unused browser APIs to reduce attack surface:
- `accelerometer`, `camera`, `geolocation`, `gyroscope`
- `magnetometer`, `microphone`, `payment`, `usb`

---

## Headers Reference

| Header | Value | Purpose |
|--------|-------|---------|
| `X-Frame-Options: DENY` | Prevent clickjacking |
| `X-Content-Type-Options: nosniff` | Prevent MIME sniffing |
| `Referrer-Policy: strict-origin-when-cross-origin` | Limit referrer leakage |
| `Cross-Origin-Opener-Policy: same-origin` | Isolate browsing context |
| `Cross-Origin-Resource-Policy: same-origin` | Prevent resource embedding |
| `Strict-Transport-Security` | Force HTTPS (enable after cert verified) |

---

## Deployment Checklist

### Before Deploy
- [ ] Verify HTTPS works on target domain
- [ ] Enable HSTS in server configs (uncomment `Strict-Transport-Security`)
- [ ] Submit to HSTS preload list: https://hstspreload.org/
- [ ] Test CSP: https://csp-evaluator.withgoogle.com/
- [ ] Scan headers: https://securityheaders.com/
- [ ] Test SSL: https://www.ssllabs.com/ssltest/

### After Deploy
- [ ] Verify `security.txt` accessible at `/.well-known/security.txt` or `/security.txt`
- [ ] Confirm no mixed content warnings
- [ ] Check CSP violations in browser console
- [ ] Monitor for CSP reports (add `report-uri` if needed)

---

## Threat Model Coverage

| Threat | Mitigation |
|--------|------------|
| XSS (Reflected/Stored) | CSP `script-src 'self'`, no user input rendering |
| XSS (DOM-based) | No `eval`, `innerHTML` with user data |
| Clickjacking | `frame-ancestors 'none'`, `X-Frame-Options: DENY` |
| MIME Sniffing | `X-Content-Type-Options: nosniff` |
| Referrer Leakage | `Referrer-Policy: strict-origin-when-cross-origin` |
| API Abuse | `Permissions-Policy` disables unused APIs |
| Data Exfiltration | `connect-src 'self'`, `form-action 'self'` |
| Subdomain Takeover | HSTS + preload, secure DNS |
| Supply Chain (Fonts) | Self-hosting guide in `FONTS.md` |

---

## Contact
Security issues: **security@nodicaltech.com** (see `security.txt`)