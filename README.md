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

 Contact : info@kadantte.moe
============================================================================
```

---

# 🛡️ Axios npm Supply Chain Attack — Detection & Protection Guide

> **© 2026 Tomorrow Systems — All Rights Reserved**
> Authored by **NABIL** · Tomorrow Systems Security Research

---

> 🚨 **CRITICAL INCIDENT — March 31, 2026**
> The npm package **axios** (100M+ weekly downloads) was compromised in a sophisticated supply chain attack. A hacker took over the lead maintainer's account, injected a phantom dependency that deploys a cross-platform RAT in **1.1 seconds**, and the malware self-destructs to erase all evidence.

---

## 📋 Table of Contents

1. [🔍 Am I Affected?](#-am-i-affected)
2. [🤖 Run the Automated Detection Script](#-run-the-automated-detection-script)
3. [🔬 Manual Detection Commands](#-manual-detection-commands)
4. [🚨 If You're Compromised](#-if-youre-compromised)
5. [🔒 Protect Yourself Going Forward](#-protect-yourself-going-forward)
6. [⛓️ What Happened — The Full Attack Chain](#️-what-happened--the-full-attack-chain)
7. [🎯 IOCs (Indicators of Compromise)](#-iocs-indicators-of-compromise)
8. [📚 Resources](#-resources)

---

## 🔍 Am I Affected?

| Status | Versions |
|--------|----------|
| 🔴 **Malicious — do not use** | `axios@1.14.1`, `axios@0.30.4` |
| ✅ **Safe** | `axios@1.14.0`, `axios@0.30.3` |

### ⚡ Quick Check

Run these two commands first. If either returns `1.14.1` or `0.30.4`, keep reading.

```bash
# Check local project installs
npm list axios

# Check globally installed axios
npm list -g axios
```

---

## 🤖 Run the Automated Detection Script

> 💡 **Recommended first step.** The automated script checks all 6 indicators in one go: axios version, lockfile integrity, git history, malicious phantom dependency, RAT file artifacts, and active C2 connections.

### 🍎 macOS / 🐧 Linux — One-liner

Open **Terminal** and paste:

```bash
curl -sL https://raw.githubusercontent.com/Tomorrow-Systems/axios-attack-check-cleaner/main/check.sh | bash
```

**✅ Prerequisites:**
- `curl` — pre-installed on macOS 10.13+ and most Linux distros
- `bash` — pre-installed on all Unix systems
- Internet access to reach `raw.githubusercontent.com`

**📋 What to expect:**
```
[✓] axios version: 1.14.0 (safe)
[✓] Lockfile clean — no plain-crypto-js found
[✓] Git history clean
[✓] No RAT artifacts detected
[✓] No active C2 connections
✅ System appears clean.
```

> 🔴 If any line shows `[✗]` or `[!]`, jump straight to [🚨 If You're Compromised](#-if-youre-compromised).

---

### 🪟 Windows — PowerShell

Open **PowerShell as Administrator** (`Win + X` → *Windows PowerShell (Admin)*) and paste:

```powershell
irm https://raw.githubusercontent.com/Tomorrow-Systems/axios-attack-check-cleaner/main/check.ps1 | iex
```

> ⚠️ **Execution Policy error?** Run this first, then retry the command above:
> ```powershell
> Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
> ```
> This only relaxes the policy for the **current session** — it resets automatically when you close PowerShell.

**✅ Prerequisites:**
- PowerShell 5.1+ (built into Windows 10/11)
- Internet access to reach `raw.githubusercontent.com`
- Administrator privileges (required for full filesystem scan)

---

### 📦 Clone & Run Locally (All Platforms)

Prefer not to pipe scripts from the internet? Clone the repo and inspect the code first — **always a good security practice**.

```bash
# 1️⃣ Clone the repository
git clone https://github.com/Tomorrow-Systems/axios-attack-check-cleaner.git

# 2️⃣ Enter the directory
cd axios-attack-check-cleaner

# 3️⃣ Inspect the script before running (recommended)
cat check.sh        # 🍎 macOS / 🐧 Linux
cat check.ps1       # 🪟 Windows

# 4️⃣ Run
./check.sh          # 🍎 macOS / 🐧 Linux
.\check.ps1         # 🪟 Windows PowerShell
```

> 🔎 **Why inspect first?** Piping remote scripts directly into a shell (`curl ... | bash`) is convenient but means you're executing code you haven't reviewed. Cloning first lets you verify the script does exactly what it claims before running it.

---

### 🛠️ Troubleshooting the Scripts

| ❌ Problem | ✅ Fix |
|-----------|--------|
| `curl: command not found` (Linux) | `sudo apt install curl` or `sudo yum install curl` |
| `Permission denied` on `check.sh` | Run `chmod +x check.sh` first, then `./check.sh` |
| PowerShell execution policy error | `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` |
| `cannot be loaded because running scripts is disabled` | Same fix as above |
| `git: command not found` | [Install Git](https://git-scm.com/downloads), or skip cloning and use the one-liner instead |
| Script hangs on filesystem scan | The full-system `find` can be slow on large drives — wait it out, or Ctrl+C and use the manual steps below |
| False positive on C2 check | Verify with `whois 142.11.206.73` — if it resolves to `sfrclak.com`, the hit is real |

---

## 🔬 Manual Detection Commands

Prefer to check manually? Run each step in order. **All 4 steps clean = you're safe.**

---

### 🔎 Step 1 — Scan Your Entire System for Axios

**🍎 macOS / 🐧 Linux:**
```bash
find / -path "*/node_modules/axios/package.json" 2>/dev/null | while read f; do
  version=$(grep '"version"' "$f" | head -1)
  echo "$f -> $version"
done
```

**🪟 Windows (PowerShell):**
```powershell
Get-ChildItem -Path C:\ -Recurse -Filter "package.json" -ErrorAction SilentlyContinue |
  Where-Object { $_.DirectoryName -like "*node_modules\axios" } |
  ForEach-Object {
    $version = (Get-Content $_.FullName | Select-String '"version"').Line
    Write-Output "$($_.FullName) -> $version"
  }
```

> 🔴 **If any result shows `1.14.1` or `0.30.4` — you are affected. Stop here and go to [🚨 If You're Compromised](#-if-youre-compromised).**

---

### 📜 Step 2 — Check Your Lockfile History

```bash
git log -p -- package-lock.json | grep "plain-crypto-js"
```

> ⚠️ **If `plain-crypto-js` appears anywhere in your lockfile history, investigate immediately.**
>
> Legitimate axios has exactly **3 dependencies** and nothing else:
> - `follow-redirects`
> - `form-data`
> - `proxy-from-env`
>
> Any additional dependency is a red flag 🚩

---

### 🦠 Step 3 — Check for RAT Artifacts

The malware drops platform-specific payloads disguised as harmless system files.

**🍎 macOS:**
```bash
ls -la /Library/Caches/com.apple.act.mond 2>/dev/null && echo "⚠️  RAT ARTIFACT FOUND" || echo "✅ Clean"
```

**🐧 Linux:**
```bash
ls -la /tmp/ld.py 2>/dev/null && echo "⚠️  RAT ARTIFACT FOUND" || echo "✅ Clean"
```

**🪟 Windows (PowerShell):**
```powershell
if (Test-Path "$env:PROGRAMDATA\wt.exe") {
    Write-Host "⚠️  RAT ARTIFACT FOUND" -ForegroundColor Red
} else {
    Write-Host "✅ Clean" -ForegroundColor Green
}
```

| OS | Malware Path | Disguised As |
|----|-------------|--------------|
| 🍎 macOS | `/Library/Caches/com.apple.act.mond` | Apple system cache daemon |
| 🐧 Linux | `/tmp/ld.py` | Generic temp file |
| 🪟 Windows | `%PROGRAMDATA%\wt.exe` | Windows Terminal executable |

---

### 🌐 Step 4 — Check for Active C2 Communication

**🍎 macOS / 🐧 Linux:**
```bash
netstat -an | grep "142.11.206.73" && echo "⚠️  ACTIVE C2 CONNECTION DETECTED" || echo "✅ No C2 traffic"
```

**🪟 Windows (PowerShell):**
```powershell
$c2 = netstat -an | Select-String "142.11.206.73"
if ($c2) { Write-Host "⚠️  ACTIVE C2 CONNECTION DETECTED" -ForegroundColor Red; $c2 }
else { Write-Host "✅ No C2 traffic detected" -ForegroundColor Green }
```

> 💡 An active connection means the RAT is **currently running** and talking to the attacker. Cut your network connection immediately if this triggers.

---

## 🚨 If You're Compromised

> 🔴 **STOP.** If **any** indicator above triggered — do not just delete files and move on. Your machine should be treated as **fully compromised**. A RAT means the attacker may have had complete remote access.

### ✅ Immediate Response Checklist

- [ ] 🛑 **Stop all work** on this machine — do not use it for anything sensitive
- [ ] 🔑 **Rotate ALL credentials immediately:**
  - npm access tokens (`~/.npmrc`)
  - SSH keys (`~/.ssh/`)
  - API keys (AWS, GCP, Azure, GitHub, etc.)
  - Cloud IAM credentials
- [ ] 🗄️ **Rotate all database passwords**
- [ ] ⚙️ **Audit CI/CD pipelines** — check if the compromised version ran in any pipeline and what secrets it had access to
- [ ] 🔥 **Block C2 at your firewall:**
  - Domain: `sfrclak.com`
  - IP: `142.11.206.73`
  - Port: `8000`
- [ ] 🖥️ **Rebuild from a clean image** — do not trust this machine for production use
- [ ] 📖 **Audit git history** for any unauthorized commits or changes pushed under your identity
- [ ] 📢 **Notify your security team** if you're part of an organization

---

## 🔒 Protect Yourself Going Forward

### 1. ⏳ Refuse Newly Published Packages

```bash
npm config set min-release-age 3
```

> 🛡️ This tells npm to refuse any package published less than **3 days** ago.
> **This single command would have completely blocked this attack** — the malicious version was only hours old when users started installing it.

---

### 2. 🚫 Disable Postinstall Scripts

Add to your `.npmrc`:
```
ignore-scripts=true
```

> The entire attack chain depended on a `postinstall` script running automatically on `npm install`. With scripts disabled, the dropper never executes. **No scripts = no attack.**
>
> ⚠️ Note: Some legitimate packages require install scripts to build native binaries. If something breaks, you can re-enable per-install with `npm install <pkg> --ignore-scripts=false`.

---

### 3. 📌 Pin Exact Versions

Add to your `.npmrc`:
```
save-exact=true
```

> The `^` caret in `"axios": "^1.14.0"` is what silently allowed npm to auto-upgrade to `1.14.1`. Pinning exact versions means you explicitly control every upgrade.

---

### 4. 🔐 Use `npm ci` in CI/CD

```bash
npm ci  # NOT npm install
```

> `npm ci` installs **exactly** what's in your `package-lock.json` — no resolution, no surprises, no silent upgrades. Always use it in automated pipelines.

---

### 5. 📦 Consider pnpm or bun

Both package managers do **NOT** run lifecycle scripts by default. This attack would have **completely failed** on either.

| Package Manager | Runs scripts by default | Safe from this attack? |
|----------------|------------------------|------------------------|
| `npm` | ✅ Yes | 🔴 Vulnerable |
| `yarn` | ✅ Yes | 🔴 Vulnerable |
| `pnpm` | ❌ No | ✅ Attack fails |
| `bun` | ❌ No | ✅ Attack fails |

---

## ⛓️ What Happened — The Full Attack Chain

| # | 🏷️ Event | 📝 Detail |
|---|----------|----------|
| 1 | 🔑 **Token stolen** | Attacker obtained `jasonsaayman`'s long-lived npm classic access token |
| 2 | 📧 **Account hijacked** | Account email changed to `ifstap@proton.me` |
| 3 | 👻 **Phantom dep injected** | `"plain-crypto-js": "^4.2.1"` added to `package.json` — never imported anywhere in the codebase |
| 4 | 🎭 **Decoy published** | Clean-looking decoy version published **18 hours** before the malicious one to build trust |
| 5 | 🚪 **Bypass trusted publishing** | Published via npm CLI directly, bypassing GitHub Actions OIDC Trusted Publishing controls |
| 6 | ☠️ **Both branches poisoned** | `axios@1.14.1` and `axios@0.30.4` both compromised within **39 minutes** |
| 7 | 💉 **Dropper executes** | `postinstall` script auto-runs — payload is XOR + base64 obfuscated (key: `OrDeR_7077`) |
| 8 | 📡 **RAT downloaded** | Platform-specific Remote Access Trojan fetched from C2 in **1.1 seconds** |
| 9 | 💨 **Self-destructs** | Dropper deletes itself, replaces `package.json` with the clean decoy — leaves no trace |

---

## 🎯 IOCs (Indicators of Compromise)

### 🌐 Network

| Type | Value |
|------|-------|
| C2 Domain | `sfrclak.com` |
| C2 IP | `142.11.206.73` |
| C2 Port | `8000` |
| C2 Path | `/6202033` |

### 🔑 Cryptographic

| Type | Value |
|------|-------|
| XOR Obfuscation Key | `OrDeR_7077` |
| `axios@1.14.1` SHA-1 | `2553649f2322049666871cea80a5d0d6adc700ca` |
| `axios@0.30.4` SHA-1 | `d6f3f62fd3b9f5432f5782b62d8cfd5247d5ee71` |
| `plain-crypto-js@4.2.1` SHA-1 | `07d889e2dadce6f3910dcbc253317d28ca61c766` |

### 🕵️ Attacker Identity

| Type | Value |
|------|-------|
| Email 1 | `ifstap@proton.me` |
| Email 2 | `nrwise@proton.me` |

### 🦠 RAT File Paths

| OS | Path | Disguised As |
|----|------|-------------|
| 🍎 macOS | `/Library/Caches/com.apple.act.mond` | Apple system cache daemon |
| 🪟 Windows | `%PROGRAMDATA%\wt.exe` | Windows Terminal executable |
| 🐧 Linux | `/tmp/ld.py` | Generic temp file |

---

## 📚 Resources

| 🔗 Source | 📝 Description |
|-----------|---------------|
| [Socket.dev Analysis](https://socket.dev/blog/axios-npm-package-compromised) | First automated detection — caught it in **6 minutes** |
| [StepSecurity Deep Dive](https://www.stepsecurity.io/blog/axios-compromised-on-npm-malicious-versions-drop-remote-access-trojan) | Runtime telemetry & behavioral analysis |
| [GitHub Issue #10604](https://github.com/axios/axios/issues/10604) | Official maintainer confirmation of compromise |
| [Huntress Blog](https://www.huntress.com/blog/supply-chain-compromise-axios-npm-package) | 100+ confirmed compromised hosts analysis |
| [🎥 John Hammond — Video](https://youtu.be/A58cV17avpM) | Technical breakdown walkthrough |
| [🔴 John Hammond — Livestream](https://www.youtube.com/watch?v=A-KpP-6Dt8E) | Live investigation recording |

---

> © 2026 **Tomorrow Systems** — All Rights Reserved · Authored by **NABIL**
