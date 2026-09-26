## ⚠️ Confidentiality Notice   

This repository is a sanitized representation of an authorized security
assessment.

To protect the organization and its infrastructure, the following information
has intentionally been removed or generalized:

•⁠  ⁠Company/domain name
•⁠  ⁠IP addresses
•⁠  ⁠Internal hostnames
•⁠  ⁠Production URLs
•⁠  ⁠Authentication/session information
•⁠  ⁠User/customer information
•⁠  ⁠API credentials and tokens
•⁠  ⁠Internal source-code paths
•⁠  ⁠Raw production request/response data
•⁠  ⁠Infrastructure-specific identifiers
•⁠  ⁠Original confidential assessment reports

The purpose of this repository is to demonstrate my security testing
methodology, vulnerability validation process, technical analysis,
risk assessment, and remediation approach.

---

# 1. Project Overview

## Objective

The objective of the assessment was to evaluate the security posture of an
authorized web application through external reconnaissance, automated
security scanning, manual penetration testing, and source-code-assisted
validation where authorized.

The assessment followed an OWASP-oriented methodology and combined automated
and manual testing techniques.

## Assessment Type

•⁠  ⁠Web Application VAPT
•⁠  ⁠External Security Assessment
•⁠  ⁠Black-box / Grey-box Testing
•⁠  ⁠Manual Security Validation
•⁠  ⁠Security Configuration Review
•⁠  ⁠API Endpoint Testing
•⁠  ⁠Source-Code-Assisted Verification

---

# 2. My Responsibilities

My responsibilities during the assessment included:

•⁠  ⁠Reconnaissance and attack-surface analysis
•⁠  ⁠DNS and infrastructure enumeration
•⁠  ⁠Port and service discovery
•⁠  ⁠HTTP/HTTPS security analysis
•⁠  ⁠Security-header testing
•⁠  ⁠Web application vulnerability testing
•⁠  ⁠API endpoint testing
•⁠  ⁠Input validation testing
•⁠  ⁠Authentication and authorization testing
•⁠  ⁠SQL Injection testing
•⁠  ⁠XSS testing
•⁠  ⁠CORS testing
•⁠  ⁠Clickjacking testing
•⁠  ⁠IDOR / Broken Access Control testing
•⁠  ⁠Manual vulnerability validation
•⁠  ⁠CVSS-based risk assessment
•⁠  ⁠Evidence collection
•⁠  ⁠Technical documentation
•⁠  ⁠Remediation recommendations
•⁠  ⁠Retesting / verification planning

---

# 3. Methodology

The assessment followed the following workflow:

Reconnaissance
        ↓
DNS & Infrastructure Enumeration
        ↓
Port & Service Discovery
        ↓
Application Mapping
        ↓
Automated Scanning
        ↓
Manual Testing
        ↓
Vulnerability Validation
        ↓
Source-Code Verification
        ↓
Risk Assessment
        ↓
Evidence Collection
        ↓
Remediation Recommendations
        ↓
Retesting

---

# 4. Tools Used

| Tool | Purpose |
|---|---|
| Burp Suite Professional | HTTP interception, request manipulation and manual testing |
| OWASP ZAP | Automated web vulnerability scanning |
| Nmap | Port and service enumeration |
| Nikto | Web server/security configuration checks |
| Kali Linux | Security testing environment |
| cURL | HTTP response/header analysis |
| DNS / DIG | DNS enumeration |
| VS Code / Grep | Source-code-assisted security review |

---

# 5. Reconnaissance & Attack Surface Analysis

The assessment began with external reconnaissance and infrastructure
enumeration.

Activities included:

•⁠  ⁠DNS resolution
•⁠  ⁠WHOIS/domain information review
•⁠  ⁠Network reachability testing
•⁠  ⁠Port enumeration
•⁠  ⁠Service identification
•⁠  ⁠TLS certificate inspection
•⁠  ⁠HTTP response analysis
•⁠  ⁠Security-header inspection

The external assessment identified a limited public-facing service footprint
with standard HTTP/HTTPS exposure.

	⁠Production infrastructure details have intentionally been removed from
	⁠this repository.

---

# 6. Security Testing Coverage

The following security areas were assessed:

### Web Application

•⁠  ⁠OWASP Top 10
•⁠  ⁠Input validation
•⁠  ⁠SQL Injection
•⁠  ⁠Cross-Site Scripting
•⁠  ⁠Authentication
•⁠  ⁠Authorization
•⁠  ⁠IDOR / Broken Access Control
•⁠  ⁠Session handling
•⁠  ⁠Security headers
•⁠  ⁠CORS
•⁠  ⁠Clickjacking
•⁠  ⁠Information disclosure

### API

•⁠  ⁠REST API endpoint behavior
•⁠  ⁠Parameter manipulation
•⁠  ⁠Authorization controls
•⁠  ⁠Input validation
•⁠  ⁠CORS behavior
•⁠  ⁠Object-level access control
•⁠  ⁠Error handling

---

# 7. Key Findings

## Finding 01 — Broken Access Control / IDOR

*Severity:* Medium

*OWASP Category:* A01 – Broken Access Control

### Description

During manual API security testing, an object-level authorization issue was
identified in an authenticated order-viewing functionality.

The testing demonstrated that modifying an object identifier could result in
access to another user's order data when ownership authorization was not
properly enforced.

### Testing Approach

1.⁠ ⁠Authenticated using an authorized test account.
2.⁠ ⁠Captured the relevant API request using Burp Suite.
3.⁠ ⁠Identified the object identifier parameter.
4.⁠ ⁠Modified the identifier.
5.⁠ ⁠Replayed the request.
6.⁠ ⁠Compared the returned object with the authenticated user's expected
   authorization scope.
7.⁠ ⁠Validated the issue through application/source-code analysis.

### Technical Root Cause

The vulnerable implementation retrieved an object using its identifier
without enforcing ownership against the authenticated user's identity.

### Security Impact

Successful exploitation could allow an authenticated user to access data
belonging to another user.

Potential impact includes:

•⁠  ⁠Unauthorized data exposure
•⁠  ⁠Privacy violation
•⁠  ⁠Broken tenant/user isolation
•⁠  ⁠Unauthorized access to business records

### Recommended Remediation

Enforce server-side object-level authorization.

The application should verify that the requested object belongs to the
authenticated user before returning the object.

Example conceptual implementation:

```javascript
const order = await Order.findOne({
    where: {
        id: orderId,
        userId: authenticatedUser.id
    }
});

if (!order) {
    return res.status(403).json({
        error: "Unauthorized access"
    });
}
