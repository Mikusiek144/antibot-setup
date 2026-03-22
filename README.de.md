# antibot-setup DEUTSCH

![Minecraft](https://img.shields.io/badge/Minecraft-Network-green?style=for-the-badge)
![Proxy](https://img.shields.io/badge/Proxy-Velocity-blue?style=for-the-badge)
![AntyBot](https://img.shields.io/badge/Protection-Sonar-red?style=for-the-badge)
![Sicherheit](https://img.shields.io/badge/Security-AntiBot-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

> Vollständige Anleitung zur Konfiguration von **Sonar AntiBot als Plugin für Velocity**

---

## 📡 Architektur (so funktioniert es)

```
Spieler
  ↓
Velocity (Proxy + Sonar)
  ↓
Lobby / BoxPvP / Survival (Backends)
```

Sonar läuft **innerhalb von Velocity**, das bedeutet:

* Der Spieler verbindet sich mit dem Proxy
* Sonar fängt die Verbindung ab
* führt eine Überprüfung durch
* erst danach gelangt der Spieler zum Backend

---

## 📌 Was ist Sonar?

**Sonar** ist ein moderner Anti-Bot für Velocity.

In der Praxis:

* analysiert den Datenverkehr des Spielers (Pakete, Physik, Verhalten)
* erkennt Join-/Flood-Bots
* nutzt Limbo zur Verifizierung
* kann CAPTCHA erzwingen

→ Das Backend erhält niemals einen nicht verifizierten Spieler

---

## 💡 Warum lohnt es sich, Sonar zu verwenden?

* ✔️ läuft direkt auf dem Proxy
* ✔️ keine zusätzlichen Ports erforderlich
* ✔️ Limbo-System (effizient)
* ✔️ automatische Angriffserkennung
* ✔️ sehr effektiv gegen Join-Bots

Kurz gesagt: **Standard für gesicherte Velocity-Netzwerke**

---

## Anforderungen

* Java 17+
* Velocity
* Sonar `.jar`
* Backends (Paper / Purpur)

---

## Installation (Hosting / VPS)

### Dateistruktur

```
/server/
  ├── velocity/
  │    └── plugins/
  │         └── Sonar.jar
  ├── lobby/
  ├── boxpvp/
```

### Hosting (z. B. Pterodactyl)

* Sonar in `/plugins/` hochladen
* Server neu starten

### VPS (Linux)

```bash
screen -S velocity
java -jar velocity.jar
```

---

## ⚙️ Sonar-Konfiguration

Datei:

```
plugins/sonar/config.yml
```

---

## Allgemeine Einstellungen

```yaml
general:
  check-for-updates: true
  log-player-addresses: true
  max-online-per-ip: 2
```

→ Begrenzt mehrere Verbindungen von einer IP-Adresse

---

## Attack Tracker (automatische Angriffserkennung)

```yaml
attack-tracker:
  min-players-for-attack: 15
  min-attack-duration: 30000
  min-attack-threshold: 2
  attack-cooldown-delay: 3000
```

→ Sonar erkennt Angriffe automatisch und passt sein Verhalten an

---

## Datenbank (optional, aber empfohlen)

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

→ Speichert verifizierte Spieler
→ Weniger CAPTCHAs für legitime Spieler

---

## Warteschlange (Anti-Spam-Join)

```yaml
queue:
  max-polls: 8
```

→ Begrenzt die Anzahl der gleichzeitig zugelassenen Spieler

---

## Verifizierung (am wichtigsten)

```yaml
verification:
  timing: ALWAYS
```

→ Jeder neue Spieler wird überprüft

---

## Prüfsystem (Checks)

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

→ Erkennt Bots anhand des Client-Verhaltens

---

## CAPTCHA (WICHTIG)

```yaml
map-captcha:
  timing: DURING_ATTACK
  style: modern
  max-duration: 25000
  max-tries: 3
```

→ CAPTCHA erscheint nur während eines Angriffs
→ besseres Spielerlebnis

---

## Client-Prüfung

```yaml
client-brand:
  enabled: true
  valid-regex: ^[!-~ ]+$
  max-length: 64
```

→ Erkennt ungewöhnliche Clients / Bots

---

## Blacklist

```yaml
blacklist-threshold: 3
blacklist-time: 900000
```

→ IP-Adresse wird nach mehreren Fehlversuchen blockiert

---

## ⚡ So funktioniert es (Schritt für Schritt)

### 1. Der Spieler verbindet sich mit dem Proxy

* gelangt zu Velocity
* Sonar fängt die Verbindung ab

### 2. Limbo

* der Spieler gelangt in die Verifizierungsumgebung

### 3. Verifizierung

Sonar überprüft:

* Traffic (Gravity)
* Pakete
* Kollisionen
* Client-Marke

### 4. CAPTCHA (im Falle eines Angriffs)

* Der Spieler muss es lösen

### 5. Freigabe

→ Der Spieler gelangt in die Lobby

### 6. Sperre

→ Der Bot wird abgelehnt

---

## 🔒 Sicherheitsmaßnahmen (MUST HAVE)

* ✔️ Backends NICHT öffentlich
* ✔️ Nur der Proxy ist öffentlich zugänglich
* ✔️ Velocity Forwarding eingerichtet
* ✔️ Firewall (auf VPS)

---

## Funktionstest

* ✔️ Verifizierung funktioniert
* ✔️ CAPTCHA erscheint bei Angriff
* ✔️ Bots werden blockiert
* ✔️ Keine Verzögerungen beim Beitritt

---

## ❌ Häufigste Fehler

### Spieler können nicht beitreten

* → zu aggressive Einstellungen

### Anti-Bot funktioniert nicht

* → Plugin wurde nicht geladen

### Verzögerung beim Beitritt

* → Warteschlange zu niedrig eingestellt

---

## 🧰 Kurze Checkliste

* [ ] Plugin befindet sich in `/plugins/`
* [ ] Server wurde nach dem Hochladen von Sonar neu gestartet
* [ ] Die Konfiguration wurde korrekt generiert
* [ ] `verification: ALWAYS`
* [ ] `map-captcha: DURING_ATTACK`
* [ ] `gravity / collision / vehicle` aktiviert
* [ ] `max-online-per-ip` eingestellt
* [ ] `queue` eingestellt (z. B. 6–8)
* [ ] Datenbank funktioniert (falls verwendet)
* [ ] Backends sind NICHT öffentlich
* [ ] Verbindung erfolgt über Proxy (Velocity)
* [ ] CAPTCHA erscheint während eines Angriffs

→ Wenn alles erfüllt ist = Sonar funktioniert korrekt

---

## 📎 Weitere Informationen

* Sonar läuft als Plugin (kein separater Server)
* benötigt keine zusätzlichen Ports
* funktioniert am besten mit Velocity

---

## Autor

**noisy144 / mikusiek144**

* GitHub: https://github.com/mikusiek144
* Discord: noisy144

## Unterstützung

Wenn dir die Anleitung geholfen hat:

* Gib einen Stern ⭐
* Teile sie
* Problem? Schreib auf Discord
---
