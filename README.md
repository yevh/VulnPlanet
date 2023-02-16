# VulnPlanet 🪐

The most extensive collection of well-structured vulnerable code examples and fixes on the internet for Web2, Web3, API and Mobile (iOS and Android).

Do you have a great vulnerable code example? Open a PR ♥️

![VulnPlanet](vulnplanet.png)

## Structure 📚

### Web2 🕸
   
 - **OWASP Top-10 2021**
     - [A01 - Broken Access Control](web2/owasp/A01-Broken-Access-Control.md)
     - [A02 - Cryptographic Failures](web2/owasp/A02-Cryptographic-Failures.md)
     - [A03 - Injection](web2/owasp/A03-Injection.md)
     - [A04 - Insecure Design](web2/owasp/A04-Insecure-Design.md)
     - [A05 - Security Misconfiguration](web2/owasp/A05-Security-Misconfiguration.md)
     - [A06 - Vulnerable and Outdated Components]
     - [A07 - Identification and Authentication Failures]
     - [A08 - Software and Data Integrity Failures]
     - [A09 - Security Logging and Monitoring Failures]
     - [A10 - Server-Side Request Forgery]
   
 - **Per vulnerability**
     - [SQL Injection](web2/type/SQL.md)
     - [NoSQL Injection](web2/type/NoSQL.md)
     - [LDAP Injection](web2/type/LDAP.md)
     - [XSS](web2/type/xss.md)
     - [SSTI](web2/type/SSTI.md)
     - [XXE](web2/type/xxe.md)
     - [SSRF](web2/type/ssrf.md)
     - [CSRF](web2/type/csrf.md)
     - [Code Execution](web2/type/Code_Execution.md)
     - [Code Injection](web2/type/Code_Injection.md)
     - [Command Injection](web2/type/Command_Injection.md)
     - [XPATH Injection](web2/type/XPATH.md)
     - [Insecure Deserialization](web2/type/deserialization.md)
     - [Authentication Bypass](web2/type/Authentication_Bypass.md)
     - [Broken Access Control](web2/owasp/A01-Broken-Access-Control.md)
     - [IDOR](web2/type/IDOR.md)
     - [Directory traversal](web2/type/traversal.md)
     - [Prototype Pollution](web2/type/prototype_pullation.md)
     - [Insecure File Uploads](web2/type/file_upload.md)
     - [Buffer Overflow](web2/type/Buffer_Overflow.md)
     - [Integer Overflow](web2/type/Integer_Overflow.md)
     - [Denial Of Service](web2/type/DOS.md)
     - [Sensitive Data Exposure](web2/type/exposure.md)
 
### API ⚕
 
   - **OWASP API Security Top-10 2019**
     - [API1 - Broken Object Level Authorization](api/owasp/API1-Broken-Object-Level-Authorization.md)
     - [API2 - Broken User Authentication](api/owasp/API2-Broken-User-Authentication.md)
     - [API3 - Excessive Data Exposure](api/owasp/API3-Excessive-Data-Exposure.md)
     - [API4 - Lack of Resources & Rate Limiting](api/owasp/API4-Lack-of-ResourcesRate.md)
     - [API5 - Broken Function Level Authorization](api/owasp/API5-Broken-Function-Level-Authorization.md)
     - [API6 - Mass Assignment](api/owasp/API6-Mass-Assignment.md)
     - [API7 - Security Misconfiguration](api/owasp/API7-Security-Misconfiguration.md)
     - [API8 - Injection](api/owasp/API8-Injection.md)
     - [API9 - Improper Assets Management](api/owasp/API9-Improper-Assets-Management.md)
     - [API10 - Insufficient Logging & Monitoring](api/owasp/API10-Insufficient-Logging-Monitoring.md)
   
### Web3 █
 
 - **Per vulnerability**
     - [Reentrancy](web3/Reentrancy.md)
     - [Broken Access Control](web3/Access_Control.md)
     - [Arithmetic Issues](web3/Arithmetic.md)
     - [Silent failing sends](web3/Unchecked.md)
     - [Denial of Service](web3/DOS.md)
     - [Bad Randomness](web3/Bad_Randomness.md)
     - [Front-Running](web3/Front_Running.md)
     - [Time manipulation](web3/Time_manipulation.md)
     - [Short Address Attack](web3/Short_Address_Attack.md)
 
 ### Mobile 📱
 
 - Top 10 Mobile 2016
     - [M1: Improper Platform Usage](mobile/owasp/Platform.md)
     - [M2: Insecure Data Storage](mobile/owasp/Data_Storage.md)
     - [M3: Insecure Communication](mobile/owasp/Communication.md)
     - [M4: Insecure Authentication](mobile/owasp/Authentication.md)
     - [M5: Insufficient Cryptography](mobile/owasp/Cryptography.md)
     - [M6: Insecure Authorization](mobile/owasp/Authorization.md)
     - [M7: Client Code Quality](mobile/owasp/Quality.md)
     - [M8: Code Tampering](mobile/owasp/Tampering.md)
     - [M9: Reverse Engineering](mobile/owasp/Reverse.md)
     - [M10: Extraneous Functionality](mobile/owasp/Extraneous.md)

### Infrastructure-as-Code (IaC) ☁
 
 - **Per vulnerability**
     - [Ingress from public internet](infra/sec_group.md)
     - [Access keys for the root is present](infra/root_keys.md)
     - [Load balancer does not use HTTPS](infra/load_balancer_https.md)
     - [Token is not required for instance IMDS access](infra/IMDS_access.md)
     - [Root block device is not encrypted](infra/root_block.md)
     - [IAM policy use of wildcard](infra/policy_wildcard.md)
     - [Load balancer is not drop invalid headers](infra/invalid_headers.md)
     - [Load balancer is exposed publicly](infra/balancer_exposed_publicly.md)
     - [Subnet associates public IP address](infra/subnet_associates.md)
     - [S3 Access block should block public ACL](infra/public_acls.md)
     - [S3 Access block should block public policy](infra/public_policies.md)
     - [Unencrypted S3 bucket](infra/unencrypted_S3.md)
     - [CMK is not used for S3 encryption](infra/CMK_S3.md)
     - [VPC Flow Logs is not enabled for VPC](infra/VPC_flow.md)
     - [Bucket does not have logging enabled](infra/bucket_logging.md)
     - [Bucket does not have versioning enabled](infra/bucket_versioning.md)
     - [Instance has very low backup retention period](infra/low_backup_retention.md)
     - [Log group is not encrypted](infra/log_group_encrypt.md)
     - [Cluster does not have container insights enabled](infra/container_insights.md)
     - [Security group rule does not have a description](infra/rule_description.md)

## Contact 📧

All suggestions write to yevhsec1@gmail.com

## Sponsors 🧙‍

🔜
