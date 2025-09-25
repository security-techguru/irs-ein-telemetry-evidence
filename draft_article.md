# Why is IRS Sharing Sensitive Taxpayer Data with Google and Akamai?

## Lede

When Americans apply for an EIN — registering a business, trust, or estate — they expect the IRS to protect their most sensitive data.  
Our analysis shows the EIN portal includes first-party Google Tag Manager (`GTM-KV978ZL`) and GA4 (`G-ZY6FM95CS5`) scripts in the page `<head>`, and Akamai edge telemetry cookies (`_abck`, `bm_sz`, `akaalb_*`) on initial responses.  
Because the Google and Akamai scripts execute in the main document context, they have the technical capability to read page DOM elements (including form inputs) and hook XHR/fetch to observe payloads.

---

## Key Evidence (see `/evidence`)

1. **HTML snapshot** with GTM/GA scripts in the `<head>`.
2. **HAR capture** showing `Set-Cookie` for Akamai cookies and network requests to `googletagmanager`/`google-analytics`.
3. **TLS cert dump** showing Let's Encrypt issuer on the Akamai edge.
4. **Console snippets** demonstrating that DOM and XHR hook capability exists (no PII recorded).
5. **AWS Application Load Balancer cookies (`AWSALB*`)** observed in responses, indicating IRS origin traffic traverses AWS-managed infrastructure.

---

## Trust Assumptions

1. **Google scripts**: Any JS loaded from `googletagmanager.com` runs in the same execution context as the IRS application, with full DOM access and ability to hook network calls.
2. **Akamai edge**: TLS terminates on Akamai infrastructure. This means decrypted taxpayer traffic exists in memory at a commercial CDN.
3. **AWS load balancer**: Presence of `AWSALB*` cookies shows another termination and routing point in the data path.
4. **Validator absence**: There is no public evidence of FedRAMP validators or independent oversight certifying these third-party components in this workflow.
5. **End-user opacity**: Taxpayers’ browsers cannot verify how traffic is protected or segmented once it leaves Akamai.

---

## TLS Termination at Akamai: A Perfect MITM Setup

- **Observed**: TLS certificates are issued by Let’s Encrypt, not a .gov CA, and bound to Akamai edge servers.
- **Implication**: Although the browser shows a lock, taxpayer data is decrypted at the CDN edge before being forwarded to the IRS.
- **Result**: Akamai sits in the “man-in-the-middle” position — a role normally considered a security red flag — except here it is authorized by contract.

---

## Data Path Between TLS Endpoint and IRS/AWS Origins

Once TLS terminates at Akamai’s edge:

1. **Re-encryption hop:** Akamai re-establishes a new TLS tunnel from its PoP to the IRS/AWS origin (SSL bridging).
2. **Trust transfer:** Two distinct TLS sessions exist:
    - Client ↔ Akamai (cert = Let’s Encrypt, observed).
    - Akamai ↔ IRS origin (unknown cert chain; invisible to taxpayer).
3. **Opaque to taxpayer:** The second leg is not verifiable by the end user. No evidence exists that it uses government CAs, FIPS 140-validated ciphers, or FedRAMP-scoped enclaves.
4. **AWS involvement:** `AWSALB*` cookies show traffic is routed through AWS Application Load Balancer before reaching IRS application servers.
5. **Auditability gap:** No client-side proof exists of FedRAMP-compliant handling between Akamai, AWS, and IRS servers.

**Implication:** Even if the browser shows a lock icon, decrypted taxpayer traffic exists at the Akamai edge and again at the AWS load balancer. Each trust transfer expands the potential exposure surface.

---

## Policy & Oversight Questions

- Why is a core taxpayer service embedding Google analytics and marketing infrastructure?
- Why is TLS terminating at Akamai rather than a federal enclave or .gov-operated CA?
- Are Akamai and AWS components in this workflow covered by FedRAMP authorization?
- Has IRS or Treasury performed independent code and traffic audits of these third-party components?
- What assurances do taxpayers have that DOM-level access and XHR hook capability are not being used for analytics or worse?

---

## Commercial Exploitation Scenario

Even without exfiltrating form fields, the Google analytics stack embedded in the EIN portal (`gtag.js`, `gtm.js`) transmits **page URLs (`dl`) and titles (`dt`)** to Google with each workflow step.

- **Implication:** Simply starting an EIN application is itself **sensitive taxpayer data** — it reveals that a citizen or entity is creating a new business, trust, or estate.
- **Commercial linkage:** These telemetry hits are ingested by Google Ads systems, where they can be linked to browser cookies, device fingerprints, and logged-in Google accounts (e.g. Gmail, YouTube).
- **Observed outcome:** Users subsequently report receiving ads for business banking, incorporation services, and insurance shortly after interacting with the IRS EIN portal.
- **Risk vector:** Even absent SSNs or names, the *fact of applying* is monetizable intelligence, and it is shared with a commercial adtech ecosystem.

**Bottom line:** taxpayer intent data — who is founding a new entity and when — is not merely technical metadata. It is a regulated class of sensitive information, and its leakage through analytics constitutes a breach of trust.

---

## Conclusion

This repository focuses on **capability and provenance**. It does **not** assert that taxpayer PII was exfiltrated.

However, the presence of first-party adtech and commercial CDN telemetry in sensitive IRS workflows raises immediate questions:
- About the adequacy of current FedRAMP oversight,
- About the chain of trust from browser to federal servers,
- And about whether taxpayer expectations of confidentiality are being met.

**Bottom line:** If the IRS requires Americans to transmit their most sensitive identifiers, it must be able to prove — not merely assert — that no commercial third party can access or intercept that data.

---

## Author’s Note

If the **Treasury Department** or oversight bodies require independent technical expertise to evaluate this risk chain, I am available to assist.

📄 CV: [Paul — General CV (PDF)](https://techguru.byiq.org/files/cv/Paul_General_CV.pdf)
