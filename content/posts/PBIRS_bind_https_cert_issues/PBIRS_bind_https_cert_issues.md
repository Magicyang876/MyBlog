---
title: "PBIRS_bind_https_cert_issues"
date: 2024-04-26T14:18:48+08:00
draft: false
categories:
    - "PBIRS"
---

## Desc
### Sometimes when we update SSL Cert, reporting service manager won’t bind HTTPS to new certificate — “We are unable to create the certificate binding”. And Reporting Service Configuration Manager removes the HTTPS binding. We checked and the certificate is installed correctly.
![Alt text](image.png)
## Root Cause
### The old certificate is still there. You may use netsh to check.
    netsh http show sslcert
![Alt text](image-1.png)
NETSH showing the old certificate bound

## Fix
### Removing the certificate that was still bound to port 443
    netsh http delete sslcert ipport=[::]:443
    netsh http delete sslcert ipport=0.0.0.0:443

![Alt text](image-2.png)

### We could then bind the new certificate in Reporting Service Configuration Manager.

## Appendix
### Use following cmd to list URL Reservations
    netsh http show urlacl


