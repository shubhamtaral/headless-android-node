# Moltbot on Android (Pixel 7) – Production-Ready Deployment Guide

> Deploy Moltbot **directly on Android** using a Pixel 7 (ARM64) with Termux.
> This is the *correct*, supported, and sane way to run Moltbot on a phone.

---

## ✅ Supported Device

This guide is **validated for Google Pixel 7**.

**Why Pixel 7 works:**

* Android 13+ (modern kernel, modern syscalls)
* ARM64 (aarch64)
* 8GB RAM
* Excellent background process handling
* Clean Android (no OEM battery sabotage)

---

## ❌ Not Supported (Important)

* Android < 10
* 32-bit devices (ARMv7)
* Legacy Termux
* Old phones (e.g. Xperia Z Ultra)

If you hit those, Moltbot **will not run reliably**.

---

## 🧠 Architecture (what you are building)

```
Pixel 7 (Android)
   └── Termux (Linux userspace)
         └── Moltbot (Python 3.11)
               ├── Telegram / Discord adapter
               ├── Local automation
               ├── Memory + state
               └── Optional remote execution
```

Android handles power, drivers, networking.
Moltbot handles logic, memory, automation.

---

## 🧰 Requirements

### Phone

* Pixel 7
* Android 13+ (14 is fine)
* Internet access

### Apps

* **F-Droid** (required)
* **Termux** (from F-Droid ONLY)

---

## ⚙️ Step 1: Install Termux (correct way)

1. Install **F-Droid**
2. Install **Termux** from F-Droid
3. Open Termux once (let it initialize)

⚠️ Do NOT use Play Store Termux (deprecated, broken)

---

## ⚙️ Step 2: Prepare Android for long-running service

### Disable battery optimizations

Settings → Apps → Termux → Battery
→ **Unrestricted**

### Allow background execution

Settings → Apps → Termux
→ Allow background activity

### (Optional but recommended)

```bash
termux-change-repo
```

Enable **main + stable + science** repos.

---

## ⚙️ Step 3: Install runtime dependencies

Inside Termux:

```bash
pkg update && pkg upgrade -y
pkg install -y \
  python git nodejs clang make cmake \
  openssl libffi rust \
  tmux htop nano wget curl
```

Verify:

```bash
python --version
```

Must be **3.10+** (Pixel 7 will give 3.11+)

---

## ⚙️ Step 4: Install Moltbot

```bash
git clone https://github.com/moltbot/moltbot.git
cd moltbot
pip install --upgrade pip
pip install -r requirements.txt
```

If install takes time → normal (compiling wheels)

---

## ⚙️ Step 5: Configure Moltbot (Android-safe)

```bash
cp config.example.yaml config.yaml
nano config.yaml
```

### REQUIRED changes

Disable desktop/system automation:

```yaml
desktop:
  enabled: false
```

Enable messaging adapter (example: Telegram):

```yaml
telegram:
  enabled: true
  token: "YOUR_BOT_TOKEN"
```

Optional (recommended):

```yaml
memory:
  persistent: true
```

---

## ⚙️ Step 6: Run Moltbot

```bash
python main.py
```

You should see:

```
Moltbot is running...
```

At this point, Moltbot is **live on Android**.

---

## 🔒 Step 7: Keep Moltbot alive (IMPORTANT)

### Use wakelock

```bash
pkg install termux-api
termux-wake-lock
```

### Use tmux (recommended)

```bash
tmux new -s moltbot
python main.py
```

Detach with `Ctrl+B` then `D`

---

## 🔁 Step 8: Auto-start Moltbot (optional but pro)

Create startup script:

```bash
nano ~/start-moltbot.sh
```

```bash
#!/data/data/com.termux/files/usr/bin/bash
termux-wake-lock
cd ~/moltbot
python main.py
```

```bash
chmod +x ~/start-moltbot.sh
```

Run anytime with:

```bash
./start-moltbot.sh
```

---

## ⚠️ Android Limitations (known, acceptable)

| Feature            | Status               |
| ------------------ | -------------------- |
| Messaging bots     | ✅                    |
| API calls          | ✅                    |
| File ops           | ✅                    |
| Memory             | ✅                    |
| Background service | ⚠️ (wakelock needed) |
| Desktop automation | ❌                    |
| Kernel hooks       | ❌                    |

---

## ✅ Recommended Production Mode

For maximum reliability:

```
Pixel 7
  └── Moltbot (logic, memory, routing)
        └── SSH / API → Linux server (heavy execution)
```

This keeps the phone fast, cool, and stable.

---

## 🧪 Tested Status

* Device: Pixel 7
* Android: 14
* Termux: F-Droid latest
* Python: 3.11
* Moltbot: latest main
* Result: **Stable**

---

## 📌 Summary

* Yes, Moltbot works on Pixel 7
* This is the correct deployment method
* Do not use legacy devices
* Do not use legacy Termux
* Android is the host, not just the client
* Pixel 7 is powerful enough to run Moltbot natively

---

## 🏁 Next Steps

* Add Telegram / Discord adapters
* Add cron-like jobs
* Add remote execution nodes
* Harden config
* Turn Pixel 7 into always-on AI node

---

> Old phones are controllers.
> New phones are servers.
> Architecture matters.
