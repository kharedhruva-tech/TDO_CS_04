# CVE-2013-2220 — PHP Radius Extension Buffer Overflow

**Author:** Dhruva ([kharedhruva@gmail.com](mailto:kharedhruva@gmail.com))

A vulnerability documentation report for **CVE-2013-2220**, a buffer overflow in the `radius_get_vendor_attr()` function of the PHP Radius extension (`php-radius`), affecting versions prior to `1.2.7`.

---

## 📌 Overview

| Field | Value |
|---|---|
| **CVE ID** | CVE-2013-2220 |
| **Component** | PHP Radius extension (`radius_get_vendor_attr`) |
| **Affected Versions** | < 1.2.7 |
| **Fixed Version** | 1.2.7+ (Debian: `php-radius 1.2.5-2.4`) |
| **Vulnerability Type** | CWE-119 — Buffer Overflow |
| **CVSS v2 Score** | 7.5 (High) |
| **CVSS v2 Vector** | `AV:N/AC:L/Au:N/C:P/I:P/A:P` |
| **Attack Vector** | Network (remote) |
| **Privileges Required** | None |
| **Impact** | Denial of Service; potential Remote Code Execution |

---

## 🧾 Summary

The Radius extension for PHP parses **Vendor Specific Attributes (VSAs)** from RADIUS server responses. The `radius_get_vendor_attr()` function reads an attacker-controlled length field and copies that many bytes into a fixed-size buffer **without properly validating the length against the buffer's capacity**.

A remote, unauthenticated attacker capable of sending (or spoofing/intercepting) a RADIUS response can supply an oversized VSA length value, triggering a buffer overflow. This can crash the affected process (DoS) and, depending on memory layout and platform mitigations (ASLR, stack canaries, DEP/NX), may allow arbitrary code execution.

## ⚙️ Attack Path

1. A target application uses `php-radius` (< 1.2.7) to authenticate against a RADIUS server.
2. An attacker controls, spoofs, or MITMs the RADIUS response path.
3. A crafted RADIUS response containing an oversized VSA length is sent to the client.
4. `radius_get_vendor_attr()` copies the oversized data into a fixed buffer, corrupting memory.

## 💥 Impact

- **Denial of Service** — crashes the PHP process handling RADIUS authentication.
- **Potential RCE** — memory corruption may be leveraged for code execution depending on compiler/OS mitigations.
- Since RADIUS clients often back authentication for VPNs, NAC, and dial-up-style services, exploitation can disrupt critical auth infrastructure.

## 🛠️ Remediation

- Upgrade `php-radius` to **1.2.7 or later** (or your distro's patched build, e.g. Debian `1.2.5-2.4+`).
- Restrict RADIUS clients to trusted, known-good RADIUS servers.
- Isolate RADIUS traffic on a trusted network path; firewall UDP `1812`/`1813` where possible.
- Monitor authentication logs and process stability for crash indicators.

## 📚 References

- CVE-2013-2220 (public CVE record)
- Debian Bug Tracking System — `php-radius` package fix (`1.2.5-2.4`)
- CWE-119: Improper Restriction of Operations within the Bounds of a Memory Buffer

## 📄 Full Report

See [(https://1drv.ms/w/c/A064A3870EE92A42/IQBlu1Ork8AnTKoHAhPvoPndAQMf-6OLpYYMVul5wrGHWJg?e=NQdEgR)] for the complete formatted write-up.

---

> This repository documents a publicly disclosed vulnerability for reference and remediation planning purposes only. It contains no exploit code and is not associated with any specific host or target system.

## 📬 Contact

Questions or corrections: **Dhruva** — [kharedhruva@gmail.com](mailto:kharedhruva@gmail.com)
