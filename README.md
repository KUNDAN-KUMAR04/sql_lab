# SQLab — SQL Injection Learning Lab

> **⚠ FOR EDUCATIONAL AND AUTHORIZED TESTING USE ONLY**
> All demonstrations run entirely in JavaScript (client-side). No real database, no real server, no real HTTP requests. Everything is simulated for safe, legal learning.

---

## 📖 What Is This?

**SQLab** is a fully self-contained, browser-based educational lab for learning SQL Injection attacks and mitigation techniques. It is designed for:

- Students, developers, and security professionals who want to **understand** SQLi
- Project presentations and classroom demonstrations
- Interview prep and OWASP Top 10 study
- Penetration testing students preparing for OSCP, CEH, or similar certifications

No server required. Open `index.html` in any browser. Everything works offline.

---

## 🗂 File Structure

```
sql-injection-demo/
├── index.html     ← The entire application (single file)
└── README.md      ← This file
```

---

## 🗺 Site Map — All Pages

| Page | Nav | Description |
|------|-----|-------------|
| **Home** | Home | Overview, attack category cards, stats |
| **Attack Reference** | Attacks | Deep-dive docs for every attack type |
| **Live Lab** | Lab | 16 interactive attack simulations |
| **Victim Sites** | Victim Sites | 6 themed vulnerable demo applications |
| **Mitigation** | Mitigation | All defense techniques with code samples |
| **Cheat Sheet** | Cheat Sheet | Quick-reference payload tables |
| **Tools** | Tools | sqlmap, Burp Suite, and defense tools guide |
| **SQL Playground** | Playground | Free-form SQL sandbox against simulated DB |
| **User Roles** | User Roles | 6 role types, permissions, impact analysis |
| **Real Breaches** | Real Breaches | Timeline of major SQLi incidents |
| **Quiz** | Quiz | 15-question interactive knowledge test |
| **Scanner** | Scanner | Simulated automated vulnerability scanner |

---

## 💉 Attack Types Covered (16 Total)

### Critical Severity
| Attack | Description | Lab Demo |
|--------|-------------|----------|
| Classic / In-Band SQLi | String concatenation, comment bypass | ✅ |
| Authentication Bypass | `' OR '1'='1`, `admin' --` | ✅ |
| UNION-Based Extraction | Extract data from any table | ✅ |
| Second-Order / Stored SQLi | Payload stored safely, executes later | ✅ |
| Stacked Queries | `;` to run multiple statements | ✅ |
| Mass DB Extraction | information_schema full dump | ✅ |
| File Read / Write | LOAD_FILE, INTO OUTFILE, web shells | ✅ |

### High Severity
| Attack | Description | Lab Demo |
|--------|-------------|----------|
| Blind Boolean-Based | True/false page differences | ✅ |
| Blind Time-Based | SLEEP() timing side-channel | ✅ |
| Error-Based Injection | extractvalue(), FLOOR(RAND()) | ✅ |
| Out-of-Band (OOB) | DNS/HTTP exfiltration | ✅ |
| NoSQL Injection | MongoDB `$gt`, `$ne`, `$regex` | ✅ |
| Cookie-Based Injection | Injection via cookie values | ✅ |
| HTTP Header Injection | User-Agent, X-Forwarded-For | ✅ |

### Medium Severity
| Attack | Description | Lab Demo |
|--------|-------------|----------|
| ORDER BY Injection | Injection in sort clause (no quotes) | ✅ |
| WAF Bypass | Case mixing, comments, URL encoding | ✅ |

---

## 🎯 Victim Sites (6 Demo Applications)

Each simulates a real-world application with intentionally vulnerable SQL logic:

| Site | Theme | Vulnerability Focus |
|------|-------|---------------------|
| **SecureBank™** | Online banking | Classic auth bypass, session hijack |
| **ShopZone** | E-commerce | UNION extraction via product search |
| **CorpIntranet** | Employee portal | All 6 user roles, OR bypass |
| **TalkZone Forum** | Community forum | Second-order / stored injection |
| **DataSearch API** | Search endpoint | Boolean-based blind |
| **EasyCMS** | Content management | Auth bypass, privilege escalation |

---

## 👥 Simulated User Accounts

All 6 role levels are pre-configured in the simulated database:

| Username | Password | Role | Privilege Level |
|----------|----------|------|----------------|
| `admin` | `admin123` | super_admin | Full system access |
| `john.doe` | `password1` | manager | Team + reports |
| `jane.smith` | `qwerty` | user | Own data only |
| `superuser1` | `sup3r!` | super_user | Elevated read/write |
| `mgr.ops` | `ops2024` | manager | Team management |
| `guest` | `guest` | user | Minimal access |

### Injecting as Any Role
- Login as admin without password: `admin' --`
- Bypass any login: `' OR '1'='1' --`
- Access by role: `' OR role='super_admin' --`

---

## 🧪 Live Lab — Quick Reference

| Lab Name | Key Payload to Try |
|----------|--------------------|
| Auth Bypass | `admin' --` |
| UNION Dump | `' UNION SELECT username,password,email FROM users --` |
| Blind Boolean | `1 AND SUBSTRING(password,1,1)='a' --` |
| Blind Time | `1 AND SLEEP(3) --` |
| Error-Based | `1 AND extractvalue(1,concat(0x7e,version())) --` |
| Stored (register) | `admin'--` as username |
| Stacked Queries | `1; INSERT INTO users(username,password,role) VALUES('evil','evil','super_admin') --` |
| ORDER BY | `(CASE WHEN 1=1 THEN username ELSE password END)` |
| Mass Extract | `' UNION SELECT table_name,NULL,NULL FROM information_schema.tables --` |
| WAF Bypass | `SeLeCt` / `SEL/**/ECT` / `%53%45%4C%45%43%54` |
| OOB | Select DB + attacker server → see simulated DNS query |
| NoSQL | `{"$gt":""}` in both username and password |
| File Read | Path: `/etc/passwd` or `/var/www/html/config.php` |
| Cookie Inject | `42 UNION SELECT username,password,email FROM users --` |
| HTTP Header | `Mozilla', (SELECT password FROM users WHERE username='admin'), '1` |

---

## 🛡 Mitigation Techniques Covered

### Primary Defense (Must Have)
```php
// PHP — PDO Parameterized Query
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :u");
$stmt->bindParam(':u', $_POST['username'], PDO::PARAM_STR);
$stmt->execute();
```

```python
# Python — Parameterized
cursor.execute("SELECT * FROM users WHERE username = %s", (username,))
```

```java
// Java — PreparedStatement
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE username = ?");
ps.setString(1, username);
```

### Defense-in-Depth Stack

| Layer | Defense | Stops SQLi? |
|-------|---------|-------------|
| Code | Parameterized queries | ✅ Yes |
| Code | ORMs (Django, Hibernate) | ✅ Yes |
| DB | Least privilege DB user | Limits damage |
| Network | WAF (ModSecurity + OWASP CRS) | Partial |
| App | Input validation (whitelist) | Partial |
| Config | Disable DB error output | Reduces info leak |
| Monitor | DB activity monitoring | Detection only |
| Infra | Secrets management (Vault) | Limits exposure |

---

## 🧰 Tools Covered

### Offensive (for testing)
- **sqlmap** — automated SQLi detection + exploitation
- **Burp Suite** — HTTP proxy, manual testing, fuzzing
- **OWASP ZAP** — free scanner + proxy
- **BBQSQL** — blind SQLi framework
- **jSQL Injection** — Java GUI tool
- **NoSQLMap** — MongoDB/NoSQL injection
- **SQLNinja** — MSSQL-focused exploitation

### Defensive
- **ModSecurity + OWASP CRS** — open source WAF
- **Snyk / SonarQube** — SAST for code-level detection
- **HashiCorp Vault** — dynamic DB credentials
- **IBM Guardium** — DB activity monitoring

---

## 🌍 Real Breaches Timeline

| Year | Target | Records | Method |
|------|--------|---------|--------|
| 2007–11 | Heartland Payments | 134M cards | SQLi → malware implant |
| 2008 | RockYou | 32M passwords | SQLi (plaintext stored) |
| 2012 | Yahoo Voices | 450K credentials | SQLi |
| 2015 | TalkTalk | 157K customers | SQLi (legacy PHP) |
| 2019 | Fortnite / Epic | 200M+ users | SQLi (found by researchers) |
| 2022 | Neopets | 69M accounts | SQLi + backdoor |
| 2023 | MOVEit (CVE-2023-34362) | 2000+ orgs | SQLi zero-day |

---

## 🧠 Quiz Topics

The 15-question quiz covers:
1. Basic SQLi definitions
2. Classic payload identification
3. Best defense method
4. UNION attack requirements
5. Time-based functions per DB
6. Stored/second-order SQLi
7. Header injection vectors
8. information_schema usage
9. WAF bypass techniques
10. Least privilege importance
11. UNION keyword function
12. Boolean blind mechanics
13. File read functions
14. NoSQL operator injection
15. Why WAF alone is insufficient

Grading: 90%+ Expert | 70%+ Proficient | 50%+ Learning | <50% Beginner

---

## 🔍 Scanner Simulation

The simulated scanner demonstrates what automated tools do:

1. **Enumerate parameters** (GET, POST, headers, cookies)
2. **Send payloads** — quote, OR bypass, UNION, SLEEP, error-trigger
3. **Analyze responses** — time delta, error messages, row count changes
4. **Classify vulnerability** — UNION-based, Error-based, Time-based Blind
5. **Generate report** — parameter, type, severity, remediation

This shows students *why* sqlmap is so powerful and *what* it's actually doing.

---

## 🗃 Simulated Database Schema

```sql
-- Users table (6 rows)
CREATE TABLE users (
  id       INT,
  username VARCHAR(50),
  password VARCHAR(100),  -- plaintext for demo purposes
  email    VARCHAR(100),
  role     VARCHAR(20),   -- super_admin|admin|manager|super_user|user
  salary   VARCHAR(20),
  ssn      VARCHAR(15)    -- simulates PII exposure
);

-- Products table (3 rows)
CREATE TABLE products (id, name, price, category);

-- Secret keys table (3 rows)
CREATE TABLE secret_keys (id, key_name, value);  -- JWT, DB passwords, API keys

-- Orders table (4 rows)
CREATE TABLE orders (id, user_id, amount, status);
```

---

## 🚀 How to Use for a Presentation

### Suggested Demo Flow (30 minutes)

1. **Open Home page** (2 min) — show attack categories, explain what SQLi is
2. **Victim Site: SecureBank** (5 min) — live login bypass with `admin' --`
3. **Lab: UNION Dump** (5 min) — extract users table via product search
4. **Lab: Blind Time-Based** (3 min) — run `autoExtract()` to show char-by-char extraction
5. **Lab: Stacked Queries** (3 min) — insert backdoor admin account live
6. **Real Breaches** (5 min) — show TalkTalk, MOVEit, Heartland
7. **Mitigation page** (5 min) — show before/after code, prepared statements
8. **Quiz** (2 min) — audience participation

### Talking Points for Each Demo

**Auth Bypass:** "The developer wrote `WHERE username='$input'`. I close the string, add a comment, and the password check disappears."

**UNION Dump:** "The search box queries products. I append a second SELECT. The DB doesn't know whose SELECT it is — it runs both and returns the results together."

**Blind Extraction:** "There's no output at all. But the page is different when my condition is true vs false. I send 300 requests to extract one password, one character at a time."

**Stored SQLi:** "I registered safely. But my username was used in another query later — without sanitization. My payload fires two days after I registered."

**Defense:** "One line of code: a prepared statement. The database *literally cannot* interpret the input as SQL. Not even `'; DROP TABLE users; --` can do anything."

---

## ⚖ Legal & Ethical Notice

This tool is designed for:
- ✅ Security education and training
- ✅ CTF (Capture The Flag) preparation
- ✅ Developer awareness programs
- ✅ Authorized penetration testing practice
- ✅ Academic research and presentations

**Never use these techniques on:**
- ❌ Systems you don't own
- ❌ Systems without written permission
- ❌ Production databases of any kind
- ❌ Any system where you haven't received explicit authorization

Unauthorized access to computer systems is a criminal offense under the Computer Fraud and Abuse Act (USA), Computer Misuse Act (UK), IT Act (India), and equivalent laws worldwide.

---

## 📚 Further Learning Resources

| Resource | Type | Link |
|----------|------|-------|
| OWASP Top 10 | Guide | owasp.org/Top10 |
| PortSwigger Web Security Academy | Free Labs | portswigger.net/web-security/sql-injection |
| DVWA (Damn Vulnerable Web App) | Real server lab | github.com/digininja/DVWA |
| HackTheBox / TryHackMe | Practice CTF | hackthebox.com |
| sqlmap Documentation | Tool | sqlmap.org |
| OWASP Testing Guide | Methodology | owasp.org/OWASP_Testing_Guide |

---

*Built with pure HTML, CSS, and JavaScript — no frameworks, no dependencies, no server.*
*Open index.html in any modern browser to start learning.*
