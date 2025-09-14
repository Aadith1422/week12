# 🛡️ OSINT Intelligence Report – brototype.com

**Author:** Aadith C H  
**Date:** 14 September 2025  
**Target:** brototype.com  
**Report ID:** OSINT-BROTOTYPE-2025-09-14

---

## Summary / Executive Summary
This report summarizes passive OSINT reconnaissance performed against **brototype.com**. All collection was passive (WHOIS, DNS, Certificate Transparency logs, Shodan, Google Dorks, robots/header inspection, and visual mapping). No active scans, exploitation, or credential testing was performed.

**Key takeaways (top-level):**
- Root domain resolves to `157.245.98.112` (DigitalOcean).  
- 33 public subdomains discovered — includes production, staging, and test hosts.  
- Certificate Transparency logs reveal many test/staging subdomains.  
- Shodan indicates internet-facing services: nginx, Grafana, MongoDB (port 27017), OpenSSH.  
- Mail handled via Google Workspace; SPF configured via `zcsend.in`.  
- Some HTTP headers are permissive (`X-Frame-Options: ALLOWALL`, wide CORS) — increases risk surface.

---

## 1. Scope, Rules of Engagement & Limitations

**Scope**
- `brototype.com` and discovered subdomains only (passive reconnaissance).

**Allowed Actions**
- Passive collection only: `whois`, `dig`, CT logs (crt.sh), public Shodan queries, Google searches, robots.txt & header inspection, Maltego passive transforms.


---

## 2. Methodology (detailed)
1. **WHOIS** to confirm registrar, DNS provider, and registrant privacy.  
2. **DNS** lookups (A, AAAA, MX, NS, TXT, CNAME) to map basic infrastructure and mail flows.  
3. **Certificate Transparency** (crt.sh) to enumerate subdomains embedded in certificates (often more complete than search engines).  
4. **Shodan** queries to find internet-exposed ports, service banners, and product/version metadata.  
5. **Google Dorks** to search for accidentally exposed files, pages, or configuration leaks.  
6. **robots.txt & header inspection** to note indexing preferences and risky headers.  
7. **Visual mapping** (Maltego / manual graphing) to visualize relationships and clusters.  
8. **Document** all queries, screenshots, and raw outputs into `evidence/` for chain-of-custody.

---

## 3. Recon Commands & How to Reproduce

> Run these from a terminal (replacing host/domain as needed). All are passive.

### WHOIS
```bash
whois brototype.com
```

### DNS
```bash
dig A brototype.com +noall +answer
dig AAAA brototype.com +noall +answer
dig MX brototype.com +noall +answer
dig NS brototype.com +noall +answer
dig TXT brototype.com +noall +answer
dig CNAME brototype.com +noall +answer
```

### Certificate Transparency
Open:
```
https://crt.sh/?q=%25.brototype.com
```

### Shodan (web UI or CLI)
CLI example (after `shodan init <API_KEY>`):
```bash
shodan search 'hostname:"brototype.com"' --fields ip_str,port,org,hostnames,product,version
shodan host <IP>
```

### Google Dorks (examples)
```
site:brototype.com filetype:sql
site:brototype.com intext:"password" OR intext:"passwd"
site:github.com "brototype.com" "apikey"
site:brototype.com intitle:"index of"
```

### Robots/Headers
```bash
curl -I https://brototype.com
curl -s https://brototype.com/robots.txt
```

---

## 4. Findings (detailed)


### 4.1 WHOIS
- **Registrar:** GoDaddy.com, LLC  
- **Created:** 2015-11-10  
- **Expires:** 2030-11-10  
- **Registrant:** Domains By Proxy (privacy)  
- **Name Servers:** ns1/ns2/ns3.digitalocean.com  
**Implication:** Domain uses registrar privacy; authoritative DNS is DigitalOcean (likely hosting on DigitalOcean droplets).



---

### 4.2 DNS Records
- **A:** `157.245.98.112`  
- **AAAA:** none  
- **MX:** Google Workspace (aspmx.l.google.com and alt*)  
- **TXT:** `v=spf1 include:zcsend.in ~all` (SPF references third-party)  
- **NS:** DigitalOcean

**Interpretation:** Mail flow via Google; SPF includes `zcsend.in` — check that third party for allowed senders. The main A record indicates a DigitalOcean server; subdomains may point to other hosts/CDNs.


---

### 4.3 Subdomains (enumeration)
**Total unique subdomains found:** **33**  
(abridged list; full list in Appendix)
```
www, api, app, blog, brocamp, camp, company, demo-app,
fa, grafana, graphql, learn, leet, manifest, practice, refer,
reviewer, student, study, test-api, test-app, test-company,
test-manifest, test-refer, test-student, test1-app, test2-app,
test3-app, test3-reviewer, test3-student, tv, tvc, v2-app
```

**Interpretation:** Numerous `test-*` and `v2-*` hosts — typical staging/legacy/replica environments. These should be verified for stale credentials and removed if unused.


---

### 4.4 Certificate Transparency (crt.sh)
- CT logs show many certs for `*.brototype.com` and multiple `test-*` and `v2-*` subdomains.
- Certificates issued primarily by Let's Encrypt (public CA).

**Interpretation:** CT is an excellent source for locating ephemeral or forgotten subdomains. Export SAN lists and add to monitoring.


---

### 4.5 Shodan / Internet-Exposed Services
- **Total results (hostname filter):** ~12–15 hosts  
- **Top ports:** 443, 22, 80, 27017  
- **Products observed:** nginx (majority), Apache, Grafana, MongoDB, OpenSSH  
- **OS:** Ubuntu (DigitalOcean droplets)  
- **Notable services:**
  - `grafana.brototype.com` — Grafana instance found  
  - One host responded with `MongoDB` on `27017` (authentication required in follow-up test)  
  - Multiple web apps (titles: Brocamp Portal, Reviewer Portal, etc.)

**Interpretation:** Presence of DB port on public internet is a risk even if auth is enabled (exposure to brute-force, CVEs, misconfiguration). Grafana needs strict access control. SSH should be limited to admin IPs.

---

### 4.6 Robots.txt & Headers
- `robots.txt` explicitly allows many bots (including named AI crawlers) and points to `sitemap.xml`.  
- HTTP response headers include:
  - `Server: nginx`  
  - `Access-Control-Allow-Origin: *`  
  - `X-Frame-Options: ALLOWALL`  
  - `Content-Security-Policy: frame-ancestors *`

**Interpretation:** `ALLOWALL` framing and permissive CORS open opportunities for clickjacking and cross-origin data exposure. Review whether all those bots should be allowed (especially if sensitive pages are indexed).

---

## 5. Risk Assessment & Prioritization

**High**
- Exposed MongoDB port (27017) — even when authenticated, should not be reachable from the public internet.  
- Numerous test/staging subdomains — potential for sensitive test data and misconfigurations.

**Medium**
- Grafana exposed — dashboards often contain sensitive telemetry or credentials.  
- HTTP header misconfigurations (CORS, X-Frame-Options) — practical risk for clickjacking and CSRF-like issues when combined with other weaknesses.

**Low**
- Registrar privacy — expected; not directly a vulnerability.

---

## 6. Recommended Remediation (detailed & actionable)

### Immediate (0–72 hours)
1. **Block MongoDB from public internet**  
   - Add firewall / security group rule to block 0.0.0.0/0 on port `27017`. Allow only management IPs or VPN/subnet.
2. **Identify & quarantine test/staging subdomains**  
   - Temporarily place behind VPN or auth wall; remove from public DNS if unused.
3. **Restrict SSH**  
   - Limit access to `22` to specific admin IP addresses; use key-based auth only.

### Short term (1–2 weeks)
1. **Harden Grafana & admin panels**  
   - Enforce strong auth, 2FA, and IP whitelisting for admin dashboards.
2. **CSP & X-Frame-Options**  
   - Set `X-Frame-Options: DENY` or `SAMEORIGIN` and implement a strict `Content-Security-Policy`.
3. **Harden CORS**  
   - Replace `Access-Control-Allow-Origin: *` with specific allowed origins.

### Mid / Long term (monthly & ongoing)
1. **Subdomain inventory & monitoring**  
   - Maintain a dynamic list of subdomains; monitor CT logs & Shodan for new certs or exposures.
2. **Secret scanning**  
   - Add automated secret scanning in code repos and CI.
3. **Certificate & CT monitoring**  
   - Alert on new cert issuance for brototype domains.
4. **Periodic external passive scans**  
   - Schedule weekly/monthly checks (Shodan, crt.sh) to detect drift.

---


## 10. Legal & Ethical Notice
This report contains **only passive**, publicly available data. Do not perform any intrusive testing or unauthorized access. Unauthorized exploitation of systems or data is illegal.

---

*End of report*