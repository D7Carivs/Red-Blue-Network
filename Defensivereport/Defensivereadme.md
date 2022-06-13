# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

The following machines were identified on the network:
- VM 1: Hyper V Host 
  - **Operating System**: Windows 10
  - **Purpose**: Contains the vulnerable and attacking machines
  - **IP Address**: 192.168.1.1
- VM 2: Kali
  - **Operating System**: Linux 5.4.0
  - **Purpose**: Attacking machine
  - **IP Address**: 192.168.1.90
- VM 3: ELK
  - **Operating System**: Linux
  - **Purpose**: Used for monitoring network and sending Metricbeat, Filebeats, and Packetbeats to Kibana.
  - **IP Address**: 192.168.1.100    
- VM 4: Target 1
  - **Operating System**: Linux
  - **Purpose**: Vm with vulnerable server
  - **IP Address**: 192.168.1.110   
- VM 5: Target 2
  - **Operating System**: Linux
  - **Purpose**: VM with vulnerable server
  - **IP Address**: 192.168.1.115 

  
### Description of Targets

The target of this attack was: `Target 1` 192.168.1.110.

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:
  - Excessive HTTP errors
  - HTTP Request Size Monitor
  - CPU Usage Monitor

Excess HTTP errors is implemented as follows:
  - **Metric**: Packetbeat: http.response.status_code > 400
  - **Threshold**: grouped http response status codes above 400 every 5 minutes
    - **When count() Grouped over top 5 'http.response.status_code is above 400 for the last 5 minutes**
  - **Vulnerability Mitigated**: 
    - Used for intrusion detection/prevention for attacks
    - IPS would block and non verified or suspicious IP addresses
    - Request Users Passwords be changed every 30 days
    - Filter/block/close port 22
  - **Reliability**: This alert has medium reliablility and will not generate a lot of false positives when looking for brute force attacks. 
   
 HTTP Request Size Monitor is implemented as follows:
  - **Metric**: Packetbeat: http.request.bytes
  - **Threshold**: The sum of the requested bytes is over 3500 in 1 minute
    - **When sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute**
  - **Vulnerability Mitigated**: f http request size is controlled through a filter, protection would be enabled to detect/prevent DdOS attack for IPS/IDS.
  - **Reliability**: This Alert has medium reliability. It does not generate a lot of false positives. A DdOS attack generates several request submissions within seconds not minutes. 

CPU Usage Monitor is implemented as follows:
  - **Metric**: Metricbeat: system.process.cpu.total.pct
  - **Threshold**: The max cpu total percent is over .5 in 5 minutes
    - **When max() OF WHEN max OF system.process.total.pct Over all documents IS ABOVE .5 FOR THE LAST 5 minutes**
  - **Vulnerability Mitigated**: It is set to control CPU usage at 50%, where it will trigger and alert if the CPU remains at or above 50% for more than 5 minutes continuously. Virus or malware could cause this. 
  - **Reliability**: This Alert has high reliability. It can cause a lot of false positives due to CPU usage spike on start up when some integrations are initiated.  

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Enumeration and Brute Force Attacks
  - **Patch**: WordPress Hardening
    - Lock out accounts after set number of failed login attempts
    - Disable teh WordPress REST API if not needed. Also configure webserver to block requests to /?author=.
    - Only allow access to necessary personel to /wp-admin and /wp-login.php
    - Implement Multifactor authentication 
  - **Why It Works**:
    - Inhibit Brute Force attack with account lockout and Multifactor Authentication
    - WPScan uses REST API to enumerate users
    - WordPress permalinks can be set to include author and blocking that access will help mitigate Brute Force attacks   






