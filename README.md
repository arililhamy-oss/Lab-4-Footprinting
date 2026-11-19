# Lab 4: Active Footprinting (Vulnerable Machine)

- **Name:** MUHAMMAD ARIL ILHAM BIN MOHD NIZAP
- **Class:** L01-B02

---

## Target Setup (Victims)
- **Application:** Damn Vulnerable Web Application (DVWA).
- **Target Address & Port:** `http://127.0.0.1:42001` (local web service bound to loopback).
- **Hosting Environment:** Installed and started locally on Kali Linux (`dvwa` package with PHP 8.4 dependencies and `dvwa-start` service launcher).
- **Scope:** Web-layer active footprinting and observation only.

---

## Own Host (Attacker)
- **Operating System:** Kali Linux (virtual machine).
- **Tools Used:** Kali terminal (`apt`, `dvwa-start`), Mozilla Firefox, and Firefox Developer Tools (Network, Headers, and Cookies tabs).

---

## Step-by-Step Walkthrough

### Step 1: Install DVWA on Kali Linux
1. Run `sudo apt update && sudo apt install -y dvwa` in the Kali terminal to update package lists and install `dvwa` along with its PHP 8.4 dependencies.
2. **Screenshot Placeholder:** ![Installing DVWA on Kali Linux]([screenshots/01-install-dvwa.png])[cite: 7

### Step 2: Start the DVWA Service
1. Run `sudo dvwa-start` in the terminal to launch the service and display the local Web UI address (`http://127.0.0.1:42001`) and default credentials (`admin` / `password`).
2. **Screenshot Placeholder:** ![Starting DVWA Service via dvwa-start]([screenshots/02-dvwa-start.png]).

### Step 3: Access the DVWA Web Interface
1. Open the target URL in Firefox to view the DVWA login page (`Username` and `Password` fields and `Login` button).
2. **Screenshot Placeholder:** ![DVWA Login Page in Firefox]([screenshots/03-dvwa-login.png])[cite: 9]

### Step 4: Capture Network Requests on `setup.php`
1. Open Firefox Developer Tools (`Ctrl + Shift + I`), select the **Network** tab, and inspect requests for `setup.php` and its associated static assets (`main.css`, `dvwaPage.js`, `add_event_listeners.js`, `logo.png`, `spanner.png`, `favicon.ico`) returning HTTP `200`.
2. **Screenshot Placeholder:** ![Network Tab Showing setup.php and Assets]([screenshots/04-network-setup.png])[cite: 10]

### Step 5: Inspect HTTP Response & Request Headers
1. Select `setup.php` in the Network list and open the **Headers** panel to examine **Response Headers** (such as `Server: nginx/1.30.4`, `Content-Encoding: gzip`, `Content-Type: text/html;charset=utf-8`, `Cache-Control: no-cache, must-revalidate`) and **Request Headers** (including `Cookie`).
2. **Screenshot Placeholder:** ![HTTP Response and Request Headers for setup.php]([screenshots/05-headers-setup.png])[cite: 11]

### Step 6: Inspect Request Cookies
1. Click the **Cookies** sub-tab next to **Headers** for `setup.php` to view the parsed cookies (`PHPSESSID`, `security`, `continueCode`, `cookieconsent_status`, `language`, `welcomebanner_status`).
2. **Screenshot Placeholder:** ![Parsed Request Cookies in Firefox DevTools]([screenshots/06-cookies-setup.png]).

### Step 7: Enumerate a Vulnerability Module Endpoint (`/vulnerabilities/sqli/`)
1. Navigate to the SQL Injection module (`/vulnerabilities/sqli/`) on `http://127.0.0.1:42001` while monitoring the **Network** tab to observe the `200` document response, static assets, and the **User ID** input form.
2. **Screenshot Placeholder:** ![Exposed /vulnerabilities/sqli/ Endpoint and Form]([screenshots/07-sqli-endpoint.png])[cite: 13]

---

## Findings, Evidence, and Limitations (Interpretation/Analysis)

### Finding 1: Web Server Banner, Caching, and Compression Headers Exposed on `setup.php`
- **Observation:** Requesting `http://127.0.0.1:42001/setup.php` returned HTTP `200 OK` and exposed `Server: nginx/1.30.4`, `Content-Encoding: gzip`, `Content-Type: text/html;charset=utf-8`, and `Cache-Control: no-cache, must-revalidate` in the response headers.
- **Interpretation / Analysis:** Response headers reveal the web/proxy server software and version (`nginx/1.30.4`), compression (`gzip`), and cache directives, helping identify the web stack and baseline configuration during active footprinting.
- **Evidence:** ![HTTP Response and Request Headers for setup.php]([screenshots/05-headers-setup.png])[cite: 11]

### Finding 2: Session Identifiers, Security Level Cookie, and Shared-Origin Cookies
- **Observation:** Inspecting cookies on `127.0.0.1:42001` showed `PHPSESSID` and `security` (`impossible`) alongside `continueCode`, `cookieconsent_status`, `language`, and `welcomebanner_status`.
- **Interpretation / Analysis:** `PHPSESSID` indicates PHP session management and `security` reflects the configured difficulty level, while additional cookies from prior local testing on the same loopback host demonstrate cookie persistence across applications sharing the same origin.
- **Evidence:** ![Parsed Request Cookies in Firefox DevTools]([screenshots/06-cookies-setup.png])[cite: 12]

### Finding 3: Accessible Application Module Endpoint (`/vulnerabilities/sqli/`)
- **Observation:** Accessing `/vulnerabilities/sqli/` on `127.0.0.1:42001` returned HTTP `200 OK` and loaded the SQL Injection view with a **User ID** input field and **Submit** button, plus supporting assets (`main.css`, `dvwaPage.js`, `add_event_listeners.js`, `favicon.ico`).
- **Interpretation / Analysis:** Identifying reachable functional paths and input fields maps out application entry points and interactive parameters for authorized assessment within scope.
- **Evidence:** ![Exposed /vulnerabilities/sqli/ Endpoint and Form]([screenshots/07-sqli-endpoint.png])[cite: 13]

---

## Limitations (Interpretation/Analysis)
- **Local Loopback Environment:** Because the target runs locally on `127.0.0.1:42001`, external network elements such as public DNS resolution, WAN routing, external firewalls, and internet-facing TLS exposure are not present.
- **Intentionally Vulnerable Lab Configuration:** DVWA is built for security training and may expose setup workflows, default credentials, or security configurations differently than hardened production systems.
- **Web-Only Scope & Observational Inspection:** Footprinting was restricted to web-layer observation using browser developer tools rather than full infrastructure or multi-port exploitation.
