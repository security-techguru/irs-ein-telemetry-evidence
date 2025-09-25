git```text
Subject: Evidence: IRS EIN portal loads Google Tag Manager &
Akamai — capability to read SSN/EIN fields

Hi [Name],

I have reproducible evidence that the IRS EIN portal
(`sa.www4.irs.gov/applyein`) embeds Google Tag Manager
(`GTM-KV978ZL`) and GA4 (`G-ZY6FM95CS5`) directly in the page
<head>.  Akamai edge telemetry also sets cookies like `_abck`,
`bm_sz`, and `akaalb_*` on first contact.

Because these scripts run in the first-party page context, they
inherit full DOM access and can hook XHR/fetch requests — in other
words, they *can* access SSNs, EINs, addresses, and other taxpayer
fields if configured to do so.  Akamai’s role is even deeper: TLS
terminates on their edge, meaning decrypted taxpayer traffic exists
inside a commercial CDN before being re-encrypted to IRS/AWS
origins.

I have packaged the evidence (HAR, screenshots, cert dump, and
console snippets) into a structured repo for review.  This also
includes proof that Google’s analytics stack transmits full URLs
and page titles, which by itself reveals taxpayer intent (e.g.
business formation).  That data feeds directly into the adtech
ecosystem — explaining why applicants see follow-up ads for
business banking and insurance.

Happy to walk you through the artifacts or reproduce the capture
live if helpful.

Best,
[Your Name]
