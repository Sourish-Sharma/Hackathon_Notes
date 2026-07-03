# 🛡️ The Ultimate CTF Guide to Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) is a client-side injection vulnerability where an attacker executes malicious scripts (usually JavaScript) in a victim's web browser. In a CTF context, the goal of XSS is almost always to **steal the session cookie of an automated admin bot** that is programmed to view your payload.

---

## 1. The Three Categories of XSS

### A. Reflected XSS (Non-Persistent)
The payload is provided through a URL parameter or form input and immediately "reflected" back onto the page by the server. 
* **The Attack Vector:** Sending a crafted link to the victim (or submitting the URL to an admin bot).
* **Example:** `http://ctf.site/search?query=<script>alert(1)</script>`

### B. Stored XSS (Persistent)
The payload is saved directly into the application's backend database (e.g., a forum post, profile bio, or comment) and executes whenever *anyone* views that page.
* **The Attack Vector:** Submitting the payload once, then pointing the admin bot to the normal page URL.
* **Why it's dangerous:** The payload does not exist in the URL, bypassing many basic browser URL filters.

### C. DOM-Based XSS (Client-Side)
The vulnerability exists entirely within the client-side JavaScript processing itself. The server never sees the payload, and the HTTP response does not change.
* **How it works:** JavaScript takes data from a **Source** (attacker-controllable input) and passes it to a **Sink** (a dangerous function that executes the input).
* **Common Sources:** `location.search`, `location.hash`, `document.referrer`, `window.name`
* **Common Sinks:** `eval()`, `document.write()`, `element.innerHTML`, `setTimeout()`
* **Example:** A page takes `http://ctf.site/#<script>alert(1)</script>` and uses `innerHTML` to write the hash directly to the screen.

---

## 2. CTF Strategy: Finding the Injection Context

Before blindly pasting payloads, input a unique string like `xss_test_12345"'<>` into the target field. Inspect the page source (`Ctrl + U` or right-click -> Inspect) to see exactly where your input landed. The surrounding HTML dictates your payload structure.

### Context 1: Raw HTML Body
Your input lands between standard HTML tags.
* *Observation:* `<div> xss_test_12345"'<> </div>`
* *Payload:* `<script>alert(1)</script>` or `<img src=x onerror=alert(1)>`

### Context 2: Inside an Attribute
Your input lands inside an HTML tag's attribute (like a `value`, `href`, or `type`).
* *Observation:* `<input type="text" value="xss_test_12345"'<>">`
* *Strategy:* You must break out of the attribute and close the tag before injecting your script.
* *Payload:* `"><script>alert(1)</script>`

### Context 3: Inside Existing JavaScript
Your input lands directly inside a `<script>` block.
* *Observation:* `<script> var name = "xss_test_12345"'<>"; </script>`
* *Strategy:* Terminate the string, close the statement, add your code, and comment out the rest.
* *Payload:* `"; alert(1); //`

---

## 3. The Payload Cheat Sheet (Offline Bypasses)

CTF creators often implement Web Application Firewalls (WAFs) or basic regex filters that strip out the word `<script>`. Use these alternative vectors to achieve execution.

### Standard Execution (No Filters)
```html
<script>alert(1)</script>
<script>alert(document.cookie)</script>