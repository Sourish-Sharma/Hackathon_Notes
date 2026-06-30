# Cryptographic Hashes: CTF Prep Notes

## 1. High-Yield Assessment Objectives

Entry-level practical exams focus on **systems-level operations** rather than theoretical mathematics:

* **Hash Identification:** Deducing the underlying hashing algorithm based on spatial properties and structural markers.
* **Dictionary Attacks (Hash Cracking):** Utilizing offline processing tools to perform pre-image attacks via wordlists.

---

## 2. Core Hash Identification Matrix

| Algorithm | Length (Characters) | Character Set | Notes / Format |
| --- | --- | --- | --- |
| **MD5** | 32 | Hexadecimal (`0-9`, `a-f`) | Ubiquitous in beginner challenges. |
| **SHA-1** | 40 | Hexadecimal (`0-9`, `a-f`) | Often used for basic integrity checks. |
| **SHA-256** | 64 | Hexadecimal (`0-9`, `a-f`) | Modern standard; high entropy. |

### Automated CLI Tool

If offline, bypass manual calculation using the native utility:

```bash
hash-identifier
# Paste the raw target hash when prompted by the CLI

```

---

## 3. Offline Exploitation Workflow: Identify & Crack

### Step A: Locate & Decompress Environment Wordlist

The standard wordlist dictionary (`rockyou.txt`) is stored natively within Kali distributions. Ensure it is decompressed before utilization:

```bash
# Decompress if file is appended with .gz
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

```

### Step B: Execution via John the Ripper

```bash
# 1. Stage the target hash into a localized file
echo "8743b52063cd84097a65d1633f5c74f5" > target_hash.txt

# 2. Execute dictionary attack pointing to the wordlist
john --wordlist=/usr/share/wordlists/rockyou.txt target_hash.txt

# 3. View the decrypted output plain-text
john --show target_hash.txt

```

---

## 4. Automation & Integrity Verification via Python

When handling automated challenges (e.g., verifying file payloads across large datasets), use the native `hashlib` standard library.

```python
import hashlib

# Scripting Template: Generating MD5 Digests
data_payload = b"PicoCTF"  # Input must be in bytes

# Initialize and update hash object
hash_instance = hashlib.md5(data_payload)
hex_digest = hash_instance.hexdigest()

print(f"MD5 Hash: {hex_digest}") 
# Output: 32-character hexadecimal string

```

> [!TIP]
> **Quick Revision Plan:** Confirm `rockyou.txt` is unzipped, run a single mock execution of `john` locally, and memorize the `hashlib` syntax before entering the environment.