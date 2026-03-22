# antibot-setup ENGLISH

![Minecraft](https://img.shields.io/badge/Minecraft-Network-green?style=for-the-badge)
![Proxy](https://img.shields.io/badge/Proxy-Velocity-blue?style=for-the-badge)
![AntyBot](https://img.shields.io/badge/Protection-Sonar-red?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-AntiBot-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

> Complete guide to configuring **Sonar AntyBot as a plugin on Velocity**

---

## 📡 Architecture (how it works)

```
Player
  ↓
Velocity (proxy + Sonar)
  ↓
Lobby / BoxPvP / Survival (backends)
```

Sonar operates **within Velocity**, meaning:

- the player connects to the proxy
- Sonar intercepts the connection
- performs verification
- only then does the player reach the backend

---

## 📌 What is Sonar?

**Sonar** is a modern anti-bot solution for Velocity.

In practice:

- analyzes player activity (packets, physics, behavior)
- detects join/flood bots
- uses limbo for verification
- can enforce CAPTCHA

→ The backend never receives an unverified player

---

## 💡 Why use Sonar?

- ✔️ Runs directly on the proxy
- ✔️ No additional ports required
- ✔️ Limbo system (high-performance)
- ✔️ Automatic attack detection
- ✔️ Highly effective against join bots

In short: **the standard for secure Velocity networks**

---

## Requirements

- Java 17+
- Velocity
- Sonar `.jar`
- Backends (Paper / Purpur)

---

## Installation (Hosting / VPS)

### File structure

```
/server/
  ├── velocity/
  │    └── plugins/
  │         └── Sonar.jar
  ├── lobby/
  ├── boxpvp/
```

### Hosting (e.g., Pterodactyl)

- Upload Sonar to `/plugins/`
- Restart the server

### VPS (Linux)

```bash
screen -S velocity
java -jar velocity.jar
```

---

## ⚙️ Sonar Configuration

File:

```
plugins/sonar/config.yml
```

---

## General Settings

```yaml
general:
  check-for-updates: true
  log-player-addresses: true
  max-online-per-ip: 2
```

→ limits multi-join from a single IP

---

## Attack Tracker (automatic attack detection)

```yaml
attack-tracker:
  min-players-for-attack: 15
  min-attack-duration: 30000
  min-attack-threshold: 2
  attack-cooldown-delay: 3000
```

→ Sonar automatically detects attacks and adjusts its behavior

---

## Database (optional but recommended)

```yaml
database:
  type: mariadb
  filename: verified_players
  host: host
  port: 3306
  name: database
  username: user
  password: pass
  maximum-age: 10
```

→ Stores verified players
→ Fewer CAPTCHAs for legitimate players

---

## Queue (anti-spam join)

```yaml
queue:
  max-polls: 8
```

→ limits how many players are admitted at once

---

## Verification (most important)

```yaml
verification:
  timing: ALWAYS
```

→ every new player goes through verification

---

## Checking system (checks)

```yaml
checks:
  gravity:
    enabled: true
    captcha-on-fail: true
    max-movement-ticks: 8

  collision:
    enabled: true

  vehicle:
    enabled: true
    minimum-packets: 2
```

→ detects bots based on client behavior

---

## CAPTCHA (KEY)

```yaml
map-captcha:
  timing: DURING_ATTACK
  style: modern
  max-duration: 25000
  max-tries: 3
```

→ CAPTCHA appears only during an attack
→ better UX for players

---

## Client check

```yaml
client-brand:
  enabled: true
  valid-regex: ^[!-~ ]+$
  max-length: 64
```

→ detects non-standard clients / bots

---

## Blacklist

```yaml
blacklist-threshold: 3
blacklist-time: 900000
```

→ IP is blacklisted after several failures

---

## ⚡ How it works (step by step)

### 1. Player connects to the proxy

- goes to Velocity
- Sonar intercepts the connection

### 2. Limbo

- the player is sent to the verification environment

### 3. Verification

Sonar checks:

- traffic (gravity)
- packets
- collisions
- client brand

### 4. CAPTCHA (if an attack)

- the player must retype it

### 5. Pass

→ player goes to the lobby

### 6. Block

→ bot is rejected

---

## 🔒 Security Measures (MUST HAVE)

- ✔️ backends NOT public
- ✔️ only proxy publicly accessible
- ✔️ Velocity forwarding enabled
- ✔️ Firewall (on VPS)

---

## Test if it works

- ✔️ Verification works
- ✔️ CAPTCHA appears during an attack
- ✔️ Bots are blocked
- ✔️ No lag when joining

---

## ❌ Most common errors

### Players cannot enter

- → settings are too aggressive

### Anti-bot not working

- → plugin not loaded

### Lag upon entry

- → queue is too low

---

## 🧰 Quick checklist

- [ ] plugin is located in `/plugins/`
- [ ] server was restarted after uploading Sonar
- [ ] config generated correctly
- [ ] `verification: ALWAYS`
- [ ] `map-captcha: DURING_ATTACK`
- [ ] `gravity / collision / vehicle` enabled
- [ ] `max-online-per-ip` set
- [ ] `queue` set (e.g., 6–8)
- [ ] database is working (if used)
- [ ] backends are NOT public
- [ ] you are connecting via a proxy (Velocity)
- [ ] captcha appears during an attack

→ If everything is checked = Sonar is working correctly

---

## 📎 Additional Information

- Sonar runs as a plugin (not a separate server)
- Does not require additional ports
- Works best with Velocity

---

## Author

**noisy144 / mikusiek144**

- GitHub: [https://github.com/mikusiek144](https://github.com/mikusiek144)
- Discord: noisy144

## Support

If this guide helped you:

- leave a star
- share it
- having a problem? message me on Discord

---
