# 🛡️ OSINT Intelligence Report – brototype.com

**Author:** Aadith C H  
**Date:** 14 September 2025  
**Target:** brototype.com  

---

## 1. WHOIS Information
- **Registrar:** GoDaddy.com, LLC  
- **Creation Date:** 10 November 2015  
- **Expiry Date:** 10 November 2030  
- **Registrant:** Privacy Protected (Domains By Proxy, LLC)  
- **Name Servers:** ns1.digitalocean.com, ns2.digitalocean.com, ns3.digitalocean.com  

📌 **Screenshot Placeholder:**  
![WHOIS Screenshot](evidence/whois.png)

---

## 2. DNS Records
- **A Record:** 157.245.98.112 (DigitalOcean, India)  
- **MX Records:** Google mail servers (aspmx.l.google.com, etc.)  
- **NS Records:** DigitalOcean authoritative servers  
- **TXT Record:** SPF → `v=spf1 include:zcsend.in ~all`  

📌 **Screenshot Placeholder:**  
![DNS Screenshot](evidence/dns.png)

---

## 3. Subdomains Discovered
A total of **33 subdomains** were identified. Examples include:  

- `www.brototype.com`  
- `api.brototype.com`  
- `grafana.brototype.com`  
- `student.brototype.com`  
- `reviewer.brototype.com`  
- `test-app.brototype.com`  

📌 **Screenshot Placeholder:**  
![Subdomains Screenshot](evidence/subdomains.png)

---

## 4. Robots.txt & Sitemap
- Allows most crawlers, including **AI bots (ChatGPT, Claude, Perplexity, etc.)**  
- Sitemap available: [https://brototype.com/sitemap.xml](https://brototype.com/sitemap.xml)  

📌 **Screenshot Placeholder:**  
![Robots Screenshot](evidence/robots.png)

---

## 5. Shodan Findings
- **Open Ports:** 443 (HTTPS), 22 (SSH), 27017 (MongoDB), 80 (HTTP)  
- **Technologies:** Nginx, Apache, Grafana, MongoDB, OpenSSH  
- **OS:** Ubuntu (DigitalOcean Cloud)  
- **TLS Support:** TLSv1.2, TLSv1.3  

📌 **Screenshot Placeholder:**  
![Shodan Screenshot](evidence/shodan.png)

---

## 6. HTTP Headers
- **Server:** nginx  
- **X-Frame-Options:** ALLOWALL → **Clickjacking Risk**  
- **CSP:** `frame-ancestors *` → allows embedding on any site  

📌 **Screenshot Placeholder:**  
![Headers Screenshot](evidence/headers.png)

---

## 7. Risks Identified
1. **Exposed MongoDB (port 27017):** Authentication required, but exposure itself is a misconfiguration.  
2. **Weak Security Headers:** Clickjacking possible due to `ALLOWALL`.  
3. **Numerous Subdomains:** Test and staging portals may leak credentials.  
4. **AI Bot Access Allowed:** Risk of data scraping by automated crawlers.  

---

## 8. Recommendations
- Restrict MongoDB to internal access or VPN.  
- Harden HTTP headers (CSP, X-Frame-Options, HSTS).  
- Audit and secure subdomains, remove unused test portals.  
- Review robots.txt policy for AI crawlers.  
- Implement monitoring for brute force & scanning attempts.  

---

## 9. References
- [OSINT Framework](https://osintframework.com/)  
- [Maltego](https://www.maltego.com/)  
- [Shodan](https://www.shodan.io/)  
- [Exploit-DB Google Dorks](https://www.exploit-db.com/google-hacking-database)  

---

✅ **This report is for educational and authorized security research only. Unauthorized exploitation of systems is illegal.**
