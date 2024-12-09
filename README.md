# HTB-Writeup-Fingerprinting
HackTheBox Writeup: Fingerprinting using curl, nmap, and WhatWeb to identify server configurations, CMS, and operating systems.

# Context
As part of a web fingerprinting exercise, I worked on identifying key components of the inlanefreight.local environment. The goal was to gather the following information from the target system:

Apache version running on app.inlanefreight.local
CMS used on app.inlanefreight.local
Operating system on which dev.inlanefreight.local is running
The provided target IP was 10.129.30.217, and the virtual hostnames (vHosts) were app.inlanefreight.local and dev.inlanefreight.local. I leveraged tools such as curl, WhatWeb, and nmap to achieve the objectives.

# Steps Performed
1. Mapping Hostnames to IP
Since the hostnames needed to resolve to 10.129.30.217, I updated the /etc/hosts file with the correct mappings. This ensures that requests to app.inlanefreight.local and dev.inlanefreight.local are routed to the target IP.

# Command Used:

bash
Copy code
sudo sh -c "echo '10.129.30.217 app.inlanefreight.local dev.inlanefreight.local' >> /etc/hosts"
Verification: I confirmed the mapping by inspecting the /etc/hosts file:

bash
Copy code
cat /etc/hosts
Result: The file contained the following entry:

plaintext
Copy code
10.129.30.217 app.inlanefreight.local dev.inlanefreight.local
2. Determining Apache Version
To identify the version of Apache running on app.inlanefreight.local, I fetched the HTTP headers using the curl command with the -I option.

# Command Used:

bash
Copy code
curl -I http://app.inlanefreight.local
Output:

plaintext
Copy code
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
Result:

Apache version: 2.4.41
3. Identifying the CMS
To detect the CMS used on app.inlanefreight.local, I analyzed the HTTP headers and used WhatWeb for confirmation.

Step 1: Checking HTTP Headers Command Used:

bash
Copy code
curl -I http://app.inlanefreight.local
Output:

plaintext
Copy code
Server: Apache/2.4.41 (Ubuntu)
MetaGenerator: Joomla!
Step 2: Using WhatWeb I ran the WhatWeb tool for a detailed analysis.

Command Used:

bash
Copy code
whatweb http://app.inlanefreight.local
Output:

plaintext
Copy code
http://app.inlanefreight.local [200 OK] Apache[2.4.41], MetaGenerator[Joomla! - Open Source Content Management]
Result:

CMS: Joomla
4. Identifying the Operating System
To determine the operating system running on dev.inlanefreight.local, I used nmap and also analyzed the HTTP headers.

Step 1: Using Nmap Command Used:

bash
Copy code
nmap -O dev.inlanefreight.local
Output:

plaintext
Copy code
Host is up (0.067s latency).
OS: Linux (Ubuntu 20.04)
Step 2: Checking HTTP Headers Command Used:

bash
Copy code
curl -I http://dev.inlanefreight.local
Output:

plaintext
Copy code
Server: Apache/2.4.41 (Ubuntu)
Result:

Operating System: Ubuntu

# Summary of Findings
Target Host	Information Gathered	Methodology
app.inlanefreight.local	Apache/2.4.41 (Ubuntu)	curl -I
app.inlanefreight.local	Joomla	curl -I, WhatWeb
dev.inlanefreight.local	Ubuntu	nmap, curl -I
Tools Used
curl: For fetching HTTP headers and server information.
WhatWeb: To detect CMS and other web technologies.
nmap: For OS fingerprinting and network scanning.
/etc/hosts: To map hostnames to the target IP.

# Challenges and Lessons Learned
Hostname Resolution: Initially, app.inlanefreight.local and dev.inlanefreight.local could not be resolved. Updating the /etc/hosts file resolved this issue.
Combining Tools: Using multiple tools like curl, WhatWeb, and nmap helped validate findings and provided comprehensive results.
