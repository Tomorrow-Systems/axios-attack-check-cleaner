```
============================================================================

  ████████╗ ██████╗ ███╗   ███╗ ██████╗ ██████╗ ██████╗  ██████╗ ██╗    ██╗
     ██╔══╝██╔═══██╗████╗ ████║██╔═══██╗██╔══██╗██╔══██╗██╔═══██╗██║    ██║
     ██║   ██║   ██║██╔████╔██║██║   ██║██████╔╝██████╔╝██║   ██║██║ █╗ ██║
     ██║   ██║   ██║██║╚██╔╝██║██║   ██║██╔══██╗██╔══██╗██║   ██║██║███╗██║
     ██║   ╚██████╔╝██║ ╚═╝ ██║╚██████╔╝██║  ██║██║  ██║╚██████╔╝╚███╔███╔╝
     ╚═╝    ╚═════╝ ╚═╝     ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝  ╚══╝╚══╝

                          S Y S T E M S

============================================================================
 Project       : Axios Supply Chain Attack — Detection & Protection Guide
 Platform      : Cross-Platform (Windows / macOS / Linux)
 Author        : NABIL
 Organization  : Tomorrow Systems
 Version       : 1.0.0
 Created       : 2026
----------------------------------------------------------------------------
 © 2026 Tomorrow Systems. All Rights Reserved.

 This documentation and its associated scripts are the exclusive intellectual
 property of Tomorrow Systems. Unauthorized copying, modification,
 distribution, or use of this material, in whole or in part, without prior
 written permission from Tomorrow Systems is strictly prohibited.

 THIS DOCUMENTATION IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
 EXPRESS OR IMPLIED. IN NO EVENT SHALL TOMORROW SYSTEMS BE LIABLE FOR
 ANY CLAIM, DAMAGES, OR OTHER LIABILITY ARISING FROM THE USE OF THIS
 MATERIAL.

 Contact : info@tomorrowsystems.com
============================================================================
```

---

# Axios npm Supply Chain Attack — Detection & Protection Guide

> **© 2026 Tomorrow Systems — All Rights Reserved**
> Authored by **NABIL** · Tomorrow Systems Security Research

---

> ⚠️ **CRITICAL INCIDENT — March 31, 2026**
> The npm package **axios** (100M+ weekly downloads) was compromised in a sophisticated supply chain attack. A hacker took over the lead maintainer's account, injected a phantom dependency that deploys a cross-platform RAT in 1.1 seconds, and the malware self-destructs to erase all evidence.

---

## Table of Contents

1. [Am I Affected?](#am-i-affected)
2. [Manual Detection Commands](#manual-detection-commands)
3. [If You're Compromised](#if-youre-compromised)
4. [Protect Yourself Going Forward](#protect-yourself-going-forward)
5. [What Happened — The Full Attack Chain](#what-happened--the-full-attack-chain)
6. [IOCs (Indicators of Compromise)](#iocs-indicators-of-compromise)
7. [Resources](#resources)

---

## Am I Affected?

| Status | Versions |
|--------|----------|
| 🔴 **Malicious** | `axios@1.14.1`, `axios@0.30.4` |
| ✅ **Safe** | `axios@1.14.0`, `axios@0.30.3` |

### Quick Check

```bash
npm list axios
npm list -g axios
```

### Run the Full Detection Script

**Mac/Linux:**
```bash
curl -sL https://raw.githubusercontent.com/Tomorrow-Systems/axios-attack-check-cleaner/main/check.sh | bash
```

**Windows (PowerShell):**
```powershell
irm https://raw.githubusercontent.com/Tomorrow-Systems/axios-attack-check-cleaner/main/check.ps1 | iex
```

**Or clone and run locally:**
```bash
git clone https://github.com/Tomorrow-Systems/axios-attack-check-cleaner.git
cd axios-attack-check-cleaner
./check.sh        # Mac/Linux
.\check.ps1       # Windows PowerShell
```

> The scripts check all 6 indicators: axios version, lockfile, git history, malicious dependency, RAT artifacts, and C2 connections.

---

## Manual Detection Commands

### Step 1 — Scan Your Entire System for Axios

**Mac/Linux:**
```bash
find / -path "*/node_modules/axios/package.json" 2>/dev/null | while read f; do
  version=$(grep '"version"' "$f" | head -1)
  echo "$f -> $version"
done
```

**Windows (PowerShell):**
```powershell
Get-ChildItem -Path C:\ -Recurse -Filter "package.json" -ErrorAction SilentlyContinue |
  Where-Object { $_.DirectoryName -like "*node_modules\axios" } |
  ForEach-Object {
    $version = (Get-Content $_.FullName | Select-String '"version"').Line
    Write-Output "$($_.FullName) -> $version"
  }
```

> 🔴 If any result shows version **1.14.1** or **0.30.4** — you are affected.

---

### Step 2 — Check Your Lockfile History

```bash
git log -p -- package-lock.json | grep "plain-crypto-js"
```

> ⚠️ If `plain-crypto-js` appears anywhere in your lockfile history, investigate immediately.
> Legitimate axios has exactly **3 dependencies**: `follow-redirects`, `form-data`, `proxy-from-env`. Anything else is a red flag.

---

### Step 3 — Check for RAT Artifacts

The malware drops platform-specific payloads disguised as system files:

| OS | Command |
|----|---------|
| **macOS** | `ls -la /Library/Caches/com.apple.act.mond 2>/dev/null` |
| **Linux** | `ls -la /tmp/ld.py 2>/dev/null` |
| **Windows** | `Test-Path "$env:PROGRAMDATA\wt.exe"` |

---

### Step 4 — Check for C2 Communication

```bash
netstat -an | grep "142.11.206.73"
```

---

## If You're Compromised

> 🚨 If you found **ANY** indicator above, treat your machine as **fully compromised**.

1. **STOP** — do not just delete files
2. **Rotate ALL credentials** — npm tokens, SSH keys, API keys, cloud credentials
3. **Rotate all database passwords**
4. **Check CI/CD pipelines** for affected installs
5. **Block C2 traffic** — `sfrclak.com` and `142.11.206.73` at your firewall
6. **Rebuild from a clean image** if possible
7. **Audit git history** for unauthorized changes

---

## Protect Yourself Going Forward

### 1. Refuse newly published packages

```bash
npm config set min-release-age 3
```

> This tells npm to refuse any package published less than 3 days ago.
> **This one command would have blocked this attack.**

---

### 2. Disable postinstall scripts

Add to your `.npmrc`:
```
ignore-scripts=true
```

> The entire attack depended on a postinstall script running automatically. No scripts = no attack.

---

### 3. Pin exact versions

Add to your `.npmrc`:
```
save-exact=true
```

> The `^` in your version ranges is what let npm auto-upgrade to the compromised version.

---

### 4. Use `npm ci` in CI/CD

```bash
npm ci  # NOT npm install
```

> Installs exactly what's in your lockfile. No surprises.

---

### 5. Consider pnpm or bun

Both package managers do **NOT** run lifecycle scripts by default. This attack would have completely failed on pnpm or bun.

---

## What Happened — The Full Attack Chain

| Step | What Happened |
|------|--------------|
| 1 | Attacker obtained lead maintainer's (`jasonsaayman`) long-lived npm classic access token |
| 2 | Account email changed to `ifstap@proton.me` |
| 3 | One line added to `package.json`: `"plain-crypto-js": "^4.2.1"` — never imported anywhere |
| 4 | Clean decoy version published **18 hours** before the malicious one |
| 5 | Published via npm CLI, bypassing GitHub Actions OIDC Trusted Publishing |
| 6 | Both `axios@1.14.1` and `axios@0.30.4` poisoned within **39 minutes** |
| 7 | Postinstall dropper auto-executes — XOR + base64 obfuscation (key: `OrDeR_7077`) |
| 8 | Platform-specific RAT downloaded from C2 in **1.1 seconds** |
| 9 | Malware self-destructs — deletes dropper, replaces `package.json` with clean decoy |

---

## IOCs (Indicators of Compromise)

### Network

| Type | Value |
|------|-------|
| C2 Domain | `sfrclak.com` |
| C2 IP | `142.11.206.73` |
| C2 Port | `8000` |
| C2 Path | `/6202033` |

### Cryptographic

| Type | Value |
|------|-------|
| XOR Key | `OrDeR_7077` |
| `axios@1.14.1` SHA-1 | `2553649f2322049666871cea80a5d0d6adc700ca` |
| `axios@0.30.4` SHA-1 | `d6f3f62fd3b9f5432f5782b62d8cfd5247d5ee71` |
| `plain-crypto-js@4.2.1` SHA-1 | `07d889e2dadce6f3910dcbc253317d28ca61c766` |

### Attacker Identity

| Type | Value |
|------|-------|
| Email 1 | `ifstap@proton.me` |
| Email 2 | `nrwise@proton.me` |

### RAT File Paths

| OS | Path | Disguised As |
|----|------|-------------|
| macOS | `/Library/Caches/com.apple.act.mond` | Apple system cache |
| Windows | `%PROGRAMDATA%\wt.exe` | Windows Terminal |
| Linux | `/tmp/ld.py` | Generic temp file |

---

## Resources

- [Socket.dev Analysis](https://socket.dev/blog/axios-npm-package-compromised) — First automated detection (6 minutes)
- [StepSecurity Deep Dive](https://www.stepsecurity.io/blog/axios-compromised-on-npm-malicious-versions-drop-remote-access-trojan) — Runtime telemetry
- [GitHub Issue #10604](https://github.com/axios/axios/issues/10604) — Maintainer confirms compromise
- [Huntress Blog](https://www.huntress.com/blog/supply-chain-compromise-axios-npm-package) — 100+ confirmed compromised hosts
- [John Hammond Video](https://youtu.be/A58cV17avpM)
- [John Hammond Livestream](https://www.youtube.com/watch?v=A-KpP-6Dt8E)

---

> © 2026 **Tomorrow Systems** — All Rights Reserved · Authored by **NABIL**
