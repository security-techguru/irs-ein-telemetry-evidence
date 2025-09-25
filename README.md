# IRS EIN telemetry evidence

![Status: Research](https://img.shields.io/badge/status-research-blue)
![Focus: Privacy](https://img.shields.io/badge/focus-privacy-red)
![Focus: Security](https://img.shields.io/badge/focus-security-orange)
![Evidence](https://img.shields.io/badge/data-evidence-lightgrey)
![FedRAMP](https://img.shields.io/badge/fedramp-review-critical)
![Contact](https://img.shields.io/badge/contact-Paul%20CV-green)
> ⚠️ This repository documents capability and provenance only.  
> No real taxpayer PII is included.
---

This repository collects reproducible artifacts and analysis related to telemetry and third-party scripts observed on the IRS EIN application flow (`sa.www4.irs.gov/applyein`).

**Purpose:**  
Document capability (scripts running in page context with DOM and XHR/fetch hook potential) and provenance (Akamai edge, Google Tag Manager/GA, TLS certs, AWS load balancer hints).

**Important:**  
This repository emphasizes *capability* evidence only. Do **not** include or publish any real taxpayer PII. Some files have been redacted to prevent accidental exposure.

---

## Trust Assumptions

- **Akamai (edge CDN)**
    - TLS terminates at Akamai, making it an unavoidable man-in-the-middle.
    - Akamai has full visibility into *all* IRS EIN traffic, including POST payloads (SSNs, EIN application data, addresses).
    - This is a structural trust dependency: the IRS must trust Akamai to handle decrypted data securely.

- **Google (Tag Manager / Analytics)**
    - Scripts load via first-party `<script>` tags in the page `<head>`.
    - Running in the main document context, they have the *capability* to read DOM elements and intercept XHR/fetch payloads.
    - Whether Google actually receives PII depends entirely on IRS GTM container configuration, but the technical possibility exists.

---

## Contents (generated)

- `evidence/sa.www4.irs.gov.redacted.har` — redacted HAR (Set-Cookie values removed and request/response bodies stripped)
- `evidence/html_snapshot.html` — page HTML snapshot (from reporting)
- `evidence/console_snippets.txt` — DevTools snippets used to demonstrate capability (no PII)
- `evidence/set_cookie_lines.txt` — extracted Set-Cookie header lines (values redacted)
- `evidence/third_party_hosts.txt` — unique non-irs hosts observed in HAR
- `evidence/cert_dump.txt` — TLS certificate output (from openssl) provided for provenance
- `evidence/dig_whois.txt` — dig output for `sa.www4.irs.gov` (from reporting)
- `evidence/network_screenshots/` — DevTools screenshots (cookies, network, security)
- [`Why is IRS Sharing Sensitive Taxpayer Data with Google and Akamai?`](draft_article.md) — article draft and outline
- `NOTICE.txt` — legal/ethics guidance and redaction notice

---

## How to reproduce

1. Open a desktop browser, enable DevTools → Network → Preserve log.
2. Start at: `http://sa.www4.irs.gov/` (capture the redirect to HTTPS).
3. Save HAR and screenshots.
4. Use the console snippets in `evidence/console_snippets.txt` to demonstrate **capability** (do not type real PII).

---

## Contact

Available to consult on secure architecture and compliance review.

📄 [Paul Charlton — CV](https://techguru.byiq.org/files/cv/Paul_General_CV.pdf)
