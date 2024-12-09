# HTB-Writeup-Fingerprinting
HackTheBox Writeup: Fingerprinting using curl, nmap, and WhatWeb to identify server configurations, CMS, and operating systems.
By Ramyar Daneshgar 

⚠️ Disclaimer: All actions were performed in a controlled, authorized environment. Unauthorized use is strictly prohibited.

## **Context**
As part of a web fingerprinting exercise, I worked on identifying key components of the `inlanefreight.local` environment. The goal was to gather the following information from the target system:
1. **Apache version** running on `app.inlanefreight.local`
2. **CMS used** on `app.inlanefreight.local`
3. **Operating system** on which `dev.inlanefreight.local` is running

The provided target IP was `10.129.30.217`, and the virtual hostnames (vHosts) were `app.inlanefreight.local` and `dev.inlanefreight.local`. I leveraged tools such as `curl`, `WhatWeb`, and `nmap` to achieve the objectives.

---

## **Steps Performed**

### **1. Mapping Hostnames to IP**
Since the hostnames needed to resolve to `10.129.30.217`, I updated the `/etc/hosts` file with the correct mappings. This ensures that requests to `app.inlanefreight.local` and `dev.inlanefreight.local` are routed to the target IP.

**Command Used:**
```bash
sudo sh -c "echo '10.129.30.217 app.inlanefreight.local dev.inlanefreight.local' >> /etc/hosts"
```

**Verification:**
I confirmed the mapping by inspecting the `/etc/hosts` file:
```bash
cat /etc/hosts
```

**Result:**
The file contained the following entry:
```plaintext
10.129.30.217 app.inlanefreight.local dev.inlanefreight.local
```

---

### **2. Determining Apache Version**
To identify the version of Apache running on `app.inlanefreight.local`, I fetched the HTTP headers using the `curl` command with the `-I` option.

**Command Used:**
```bash
curl -I http://app.inlanefreight.local
```

**Output:**
```plaintext
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
```

**Result:**
- **Apache version:** `2.4.41`

---

### **3. Identifying the CMS**
To detect the CMS used on `app.inlanefreight.local`, I analyzed the HTTP headers and used `WhatWeb` for confirmation.

#### **Step 1: Checking HTTP Headers**
**Command Used:**
```bash
curl -I http://app.inlanefreight.local
```

**Output:**
```plaintext
Server: Apache/2.4.41 (Ubuntu)
MetaGenerator: Joomla!
```

#### **Step 2: Using WhatWeb**
I ran the `WhatWeb` tool for a detailed analysis.

**Command Used:**
```bash
whatweb http://app.inlanefreight.local
```

**Output:**
```plaintext
http://app.inlanefreight.local [200 OK] Apache[2.4.41], MetaGenerator[Joomla! - Open Source Content Management]
```

**Result:**
- **CMS:** `Joomla`

---

### **4. Identifying the Operating System**
To determine the operating system running on `dev.inlanefreight.local`, I used `nmap` and also analyzed the HTTP headers.

#### **Step 1: Using Nmap**
**Command Used:**
```bash
nmap -O dev.inlanefreight.local
```

**Output:**
```plaintext
Host is up (0.067s latency).
OS: Linux (Ubuntu 20.04)
```

#### **Step 2: Checking HTTP Headers**
**Command Used:**
```bash
curl -I http://dev.inlanefreight.local
```

**Output:**
```plaintext
Server: Apache/2.4.41 (Ubuntu)
```

**Result:**
- **Operating System:** `Ubuntu`

---

## **Summary of Findings**
| Target Host             | Information Gathered          | Methodology                 |
|-------------------------|-------------------------------|-----------------------------|
| `app.inlanefreight.local` | Apache/2.4.41 (Ubuntu)        | `curl -I`                   |
| `app.inlanefreight.local` | Joomla                       | `curl -I`, `WhatWeb`        |
| `dev.inlanefreight.local` | Ubuntu                       | `nmap`, `curl -I`           |

---

## **Tools Used**
- **`curl`**: For fetching HTTP headers and server information.
- **`WhatWeb`**: To detect CMS and other web technologies.
- **`nmap`**: For OS fingerprinting and network scanning.
- **`/etc/hosts`**: To map hostnames to the target IP.

---

## **Challenges and Lessons Learned**
1. **Hostname Resolution:** Initially, `app.inlanefreight.local` and `dev.inlanefreight.local` could not be resolved. Updating the `/etc/hosts` file resolved this issue.
2. **Combining Tools:** Using multiple tools like `curl`, `WhatWeb`, and `nmap` helped validate findings and provided comprehensive results.
