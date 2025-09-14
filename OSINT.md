# OSINT: Open-Source Intelligence 

## What is OSINT?

**OSINT (Open-Source Intelligence)** is the practice of collecting and analyzing information from publicly available sources. These sources can include:

- **Social Media:** Twitter, LinkedIn, Instagram, etc.
- **Public Records:** WHOIS, domain registries, government databases.
- **News & Publications:** Press releases, blogs, articles.
- **Internet Services:** Websites, cloud services, APIs.

**Purpose:** OSINT is used by cybersecurity professionals, investigators, journalists, and law enforcement to gather intelligence in a **legal and ethical manner**.  
> ⚠️ Key Principle: OSINT involves only **information that is publicly available**—never bypass passwords, hacking protections, or access controls without explicit permission.

---

## OSINT Methodologies

OSINT can be divided into two main approaches:

### 1. Passive Collection
Passive collection means **observing without interacting** with the target.  

**Examples:**

| Task | Tool / Method |
|------|---------------|
| Domain info | `whois example.com`, `dig example.com` |
| Subdomain enumeration | Certificate Transparency (crt.sh), DNS history |
| Social media research | Twitter searches, LinkedIn profiles |
| Search engines | Google, Bing, using advanced operators |

**Advantages:**  
- Low risk of detection  
- Safe legally and ethically  
- Often sufficient to map the target’s footprint  

### 2. Active Collection
Active collection involves **interacting directly** with the target. It may be legal if in scope, but can be detectable.  

**Examples:**

| Task | Tool / Method |
|------|---------------|
| Port scanning | Nmap, Masscan |
| Banner grabbing | Netcat, Shodan (passive + active) |
| Service probing | Attempting to connect to exposed services |

**Note:** Always get **explicit permission** for active tasks. Unauthorized scanning or probing can be illegal.

---

## Key OSINT Tools & How They Work

### 1. Maltego
Maltego is a **graphical link analysis and data visualization tool**.

**How it works:**

1. Start with an entity (like a domain, IP, or email).
2. Run **transforms** to gather related entities.
3. Expand entities recursively to build a **graph of relationships**.

**Practical Example:**

- Start with `brototype.com`
- Run `Domain → To DNS Names` → discovers subdomains
- Run `Domain → To IPs` → maps which servers host these subdomains
- Run `IP → To Reverse DNS / To Domains on same IP` → finds co-hosted domains

**Purpose:**  
- Identifies relationships between domains, subdomains, IPs, and emails  
- Reveals non-obvious infrastructure connections

---

### 2. Google Dorks
Google Dorks are **advanced search queries** designed to find sensitive or hidden data.

**Common Operators:**

| Operator | Example | Purpose |
|----------|---------|---------|
| `site:` | `site:brototype.com` | Search within a specific domain |
| `filetype:` | `filetype:pdf` | Find files of a specific type |
| `intitle:` | `intitle:"index of"` | Detect directory listings |
| `inurl:` | `inurl:admin` | Locate login portals |

**Examples of High-Value Dorks:**

```text
site:brototype.com filetype:sql
site:brototype.com intext:"password"
site:github.com "brototype.com" "apikey"
```

**Caution:**  
- Never download files or access credentials unless authorized  
- Use cached versions or Wayback Machine to reference exposures safely

---

### 3. OSINT Framework
The **OSINT Framework** is a curated map of thousands of OSINT tools organized by category.  

**How to use:**

1. Visit [OSINT Framework](https://osintframework.com/)
2. Select a category (e.g., "Username")
3. Explore linked tools (e.g., Check multiple social networks for a username)
4. Repeat for domains, IPs, emails, or people

**Purpose:**  
- Quick reference for analysts  
- Discover tools for passive reconnaissance without trial-and-error searching

---

### 4. Shodan
Shodan is the "**search engine for Internet-connected devices**".

**How it works:**

- Scans internet-connected devices for open ports and banners
- Indexes server types, versions, protocols, and metadata

**Practical Searches:**

```text
port:27017 hostname:"brototype.com"  # MongoDB instances
product:"Apache" port:80              # Apache web servers
org:"Brototype"                        # All devices associated with the organization
```

**Purpose:**  
- Discover exposed servers, IoT devices, and services  
- Detect misconfigurations or outdated software  

**Tip:** Free accounts have limited results; paid accounts allow full API access.

---

## Putting It All Together

**Step-by-step OSINT workflow:**

1. **Define scope**: Domain names, people, organizations
2. **Passive collection**: WHOIS, DNS, CT logs, archives
3. **Search engine and code search**: Google Dorks, GitHub
4. **Internet-facing devices**: Shodan, Censys (passive)
5. **Visual mapping**: Maltego graphs for relationships
6. **Document findings**: Include screenshots, URLs, and timestamps

---

## Practical Tips

- **Respect privacy & law:** Only gather publicly available data or have authorization
- **Record everything:** Maintain logs, screenshots, and query lists
- **Avoid exploitation:** The goal is observation, not hacking
- **Check for false positives:** Not every exposed file or IP is exploitable

---

## References & Learning Resources

- [Maltego Official Website](https://www.maltego.com/)  
- [OSINT Framework](https://osintframework.com/)  
- [Shodan](https://www.shodan.io/)  
- [Google Dorking Guide](https://www.exploit-db.com/google-hacking-database)  
- [Certificate Transparency Logs](https://crt.sh/)

---

**Disclaimer:** This guide is intended for educational purposes and for authorized OSINT investigations only. Unauthorized access or exploitation of systems, data, or devices is illegal and unethical.
