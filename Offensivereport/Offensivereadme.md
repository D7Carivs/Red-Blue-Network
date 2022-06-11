# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services


Nmap scan results for each machine reveal the below services and OS details:

    **$nmap -sP 192.168.1.1-255**: This namp scan scans the full 192.168.1.1 network for any live hosts available. It revealed to hosts up: 192.168.1.110 and 192.168.1.115.

    https://github.com/D7Carivs/Red-Blue-Network/blob/8d270379a8e0adfe76fd3abdc87911b389e9f9b5/Offensivereport/screenshots/Kali%20nMap%20scan%20of%20network.PNG
  
    **$nmap -sV 192.168.110**: This scan was then ran to reveal any open ports on the IP address. Port 80 is left open and vulnerable. 
    
    Offensivereport/screenshots/nmap scan report for 192.168.1.110.PNG

   **$wpscan --url http://192.168.1.110/wordpress -eu**

  


This scan identifies the services below as potential points of entry:
- Target 1
  - List of
  - Exposed Services

_TODO: Fill out the list below. Include severity, and CVE numbers, if possible._

The following vulnerabilities were identified on each target:
- Target 1
  - List of
  - Critical
  - Vulnerabilities

_TODO: Include vulnerability scan results to prove the identified vulnerabilities._

### Exploitation
_TODO: Fill out the details below. Include screenshots where possible._

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: _TODO: Insert `flag1.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_
  - `flag2.txt`: _TODO: Insert `flag2.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_