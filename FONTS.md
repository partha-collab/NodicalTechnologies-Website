# Self-Hosting Fonts Guide (Security & Privacy)

## Why Self-Host?
- **No external requests** — eliminates Google Fonts tracking
- **Stricter CSP** — remove `https://fonts.googleapis.com` and `https://fonts.gstatic.com`
- **Faster loads** — fonts served from your CDN with your cache headers
- **GDPR/privacy compliance** — no third-party calls

## Fonts Used
| Font | Weights | Source |
|------|---------|--------|
| Space Grotesk | 500, 600, 700 | https://fonts.google.com/specimen/Space+Grotesk |
| Manrope | 400, 500, 600, 700, 800 | https://fonts.google.com/specimen/Manrope |
| IBM Plex Mono | 500, 600 | https://fonts.google.com/specimen/IBM+Plex+Mono |

## Steps

### 1. Download WOFF2 Files
Use [google-webfonts-helper](https://gwfh.mranftl.com/fonts) or:
```bash
# Example using google-webfonts-helper (select "Modern Browsers" = WOFF2 only)
# Download each font family, extract to /assets/fonts/
```

Directory structure:
```
assets/
  fonts/
    space-grotesk/
      space-grotesk-latin-500.woff2
      space-grotesk-latin-600.woff2
      space-grotesk-latin-700.woff2
    manrope/
      manrope-latin-400.woff2
      manrope-latin-500.woff2
      manrope-latin-600.woff2
      manrope-latin-700.woff2
      manrope-latin-800.woff2
    ibm-plex-mono/
      ibm-plex-mono-latin-500.woff2
      ibm-plex-mono-latin-600.woff2
```

### 2. Replace @import in styles.css
Remove line 15 (`@import url(...)`) and add:

```css
/* Space Grotesk */
@font-face {
  font-family: 'Space Grotesk';
  font-style: normal;
  font-weight: 500;
  font-display: swap;
  src: url('../assets/fonts/space-grotesk/space-grotesk-latin-500.woff2') format('woff2');
}
@font-face {
  font-family: 'Space Grotesk';
  font-style: normal;
  font-weight: 600;
  font-display: swap;
  src: url('../assets/fonts/space-grotesk/space-grotesk-latin-600.woff2') format('woff2');
}
@font-face {
  font-family: 'Space Grotesk';
  font-style: normal;
  font-weight: 700;
  font-display: swap;
  src: url('../assets/fonts/space-grotesk/space-grotesk-latin-700.woff2') format('woff2');
}

/* Manrope */
@font-face {
  font-family: 'Manrope';
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url('../assets/fonts/manrope/manrope-latin-400.woff2') format('woff2');
}
@font-face {
  font-family: 'Manrope';
  font-style: normal;
  font-weight: 500;
  font-display: swap;
  src: url('../assets/fonts/manrope/manrope-latin-500.woff2') format('woff2');
}
@font-face {
  font-family: 'Manrope';
  font-style: normal;
  font-weight: 600;
  font-display: swap;
  src: url('../assets/fonts/manrope/manrope-latin-600.woff2') format('woff2');
}
@font-face {
  font-family: 'Manrope';
  font-style: normal;
  font-weight: 700;
  font-display: swap;
  src: url('../assets/fonts/manrope/manrope-latin-700.woff2') format('woff2');
}
@font-face {
  font-family: 'Manrope';
  font-style: normal;
  font-weight: 800;
  font-display: swap;
  src: url('../assets/fonts/manrope/manrope-latin-800.woff2') format('woff2');
}

/* IBM Plex Mono */
@font-face {
  font-family: 'IBM Plex Mono';
  font-style: normal;
  font-weight: 500;
  font-display: swap;
  src: url('../assets/fonts/ibm-plex-mono/ibm-plex-mono-latin-500.woff2') format('woff2');
}
@font-face {
  font-family: 'IBM Plex Mono';
  font-style: normal;
  font-weight: 600;
  font-display: swap;
  src: url('../assets/fonts/ibm-plex-mono/ibm-plex-mono-latin-600.woff2') format('woff2');
}
```

### 3. Update CSP in All HTML Files
Change `style-src` and `font-src` directives:

**Before:**
```html
<meta http-equiv="Content-Security-Policy" content="... style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com data:; ...">
```

**After:**
```html
<meta http-equiv="Content-Security-Policy" content="... style-src 'self' 'unsafe-inline'; font-src 'self' data:; ...">
```

### 4. Update Server Configs
- `_headers` (Netlify)
- `.htaccess` (Apache)
- `web.config` (IIS)
- `vercel.json` (Vercel)
- `netlify.toml` (Netlify)

Remove font domain allowances from CSP in all configs.

## Verify
1. Open DevTools → Network → Fonts — should only show your domain
2. Run CSP evaluator: https://csp-evaluator.withgoogle.com/
3. Test on https://securityheaders.com/