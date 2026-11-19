# Lab 4: Active Footprinting (Vulnerable Machine)

- **Name:** MUHAMMAD ARIL ILHAM BIN MOHD NIZAP
- **Class:** L01-B02

---

## Target Setup (Victims)
- **Application:** Damn Vulnerable Web Application (DVWA).
- **Target Address & Port:** `http://127.0.0.1:42001` (local web service bound to loopback).
- **Hosting Environment:** Installed and running locally within the Kali Linux virtual machine environment.
- **Scope:** Web-layer active footprinting and observation.
---

## Own Host (Attacker)
- **Operating System:** Kali Linux (virtual machine).
- **Tools Used:** Mozilla Firefox and Firefox Developer Tools (Network and Cookies inspectors) for HTTP header, cookie, and endpoint observation.

---

## Findings, Evidence, and Limitations (Interpretation/Analysis)

### Finding 1: Web Server Banner, Caching, and Compression Headers Exposed on `setup.php`
- **Observation:** Requesting `http://127.0.0.1:42001/setup.php` returned HTTP `200 OK` and exposed `Server: nginx/1.30.4`, `Content-Encoding: gzip`, `Content-Type: text/html;charset=utf-8`, and `Cache-Control: no-cache, must-revalidate` in the HTTP response headers.
- **Interpretation / Analysis:** Inspecting response headers reveals the edge/web server software and version (`nginx/1.30.4`) as well as response caching and compression behavior. In real-world engagements, verbose server banners help narrow down software versions and potential configuration baselines.
- **Evidence:** <img width="602" height="357" alt="image" src="https://github.com/user-attachments/assets/3c7c9150-349c-48b9-a20d-e35cc41fb13c" />


### Finding 2: Session Identifiers, Security Level Cookie, and Shared-Origin Cookies
- **Observation:** Inspecting the request cookies sent to `127.0.0.1:42001` revealed `PHPSESSID` and `security` (`impossible`) alongside prior origin cookies (`continueCode`, `cookieconsent_status`, `language`, and `welcomebanner_status`)[cite: 4, 5].
- **Interpretation / Analysis:** The presence of `PHPSESSID` indicates PHP session tracking, while the `security` cookie reflects the application's configured difficulty level[cite: 5]. Additional cookies from earlier local testing on the same loopback host show how browser cookies persist across applications sharing the same origin/host
