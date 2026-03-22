# antibot-setup POLSKI

![Minecraft](https://img.shields.io/badge/Minecraft-Network-green?style=for-the-badge)
![Proxy](https://img.shields.io/badge/Proxy-Velocity-blue?style=for-the-badge)
![AntyBot](https://img.shields.io/badge/Protection-Sonar-red?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-AntiBot-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

> Kompletny poradnik konfiguracji **Sonar AntyBot jako plugin na Velocity**

---

## 📡 Architektura (jak to działa)

```
Gracz
  ↓
Velocity (proxy + Sonar)
  ↓
Lobby / BoxPvP / Survival (backendy)
```

Sonar działa **wewnątrz Velocity**, czyli:

- gracz łączy się do proxy
- Sonar przechwytuje połączenie
- wykonuje weryfikację
- dopiero potem gracz trafia na backend

---

## 📌 Czym jest Sonar?

**Sonar** to nowoczesny antybot dla Velocity.

W praktyce:

- analizuje ruch gracza (pakiety, fizyka, zachowanie)
- wykrywa boty join / flood
- używa limbo do weryfikacji
- może wymuszać CAPTCHA

→ Backend nigdy nie dostaje niezweryfikowanego gracza

---

## 💡 Dlaczego warto używać Sonara?

- ✔️ działa bezpośrednio na proxy
- ✔️ brak dodatkowych portów
- ✔️ system limbo (wydajny)
- ✔️ automatyczne wykrywanie ataku
- ✔️ bardzo skuteczny na boty join

Krótko: **standard przy zabezpieczonych sieciach Velocity**

---

## Wymagania

- Java 17+
- Velocity
- Sonar `.jar`
- Backendy (Paper / Purpur)

---

## Instalacja (Hosting / VPS)

### Struktura plików

```
/server/
  ├── velocity/
  │    └── plugins/
  │         └── Sonar.jar
  ├── lobby/
  ├── boxpvp/
```

### Hosting (np. Pterodactyl)

- wrzuć Sonar do `/plugins/`
- restart serwera

### VPS (Linux)

```bash
screen -S velocity
java -jar velocity.jar
```

---

## ⚙️ Konfiguracja Sonara

Plik:

```
plugins/sonar/config.yml
```

---

## Ustawienia ogólne

```yaml
general:
  check-for-updates: true
  log-player-addresses: true
  max-online-per-ip: 2
```

→ ogranicza multi-join z jednego IP

---

## Attack Tracker (automatyczne wykrywanie ataku)

```yaml
attack-tracker:
  min-players-for-attack: 15
  min-attack-duration: 30000
  min-attack-threshold: 2
  attack-cooldown-delay: 3000
```

→ Sonar sam wykrywa ataki i zmienia zachowanie

---

## Baza danych (opcjonalne ale polecam)

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

→ zapamiętuje zweryfikowanych graczy
→ mniej captcha dla legitnych graczy

---

## Kolejka (anty spam join)

```yaml
queue:
  max-polls: 8
```

→ ogranicza ile graczy jest wpuszczanych jednocześnie

---

## Weryfikacja (najważniejsze)

```yaml
verification:
  timing: ALWAYS
```

→ każdy nowy gracz przechodzi weryfikację

---

## System sprawdzania (checks)

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

→ wykrywa boty po zachowaniu klienta

---

## CAPTCHA (KLUCZOWE)

```yaml
map-captcha:
  timing: DURING_ATTACK
  style: modern
  max-duration: 25000
  max-tries: 3
```

→ CAPTCHA pojawia się tylko podczas ataku
→ lepszy UX dla graczy

---

## Client check

```yaml
client-brand:
  enabled: true
  valid-regex: ^[!-~ ]+$
  max-length: 64
```

→ wykrywa niestandardowe klienty / boty

---

## Blacklista

```yaml
blacklist-threshold: 3
blacklist-time: 900000
```

→ IP trafia na blacklistę po kilku failach

---

## ⚡ Jak działa (krok po kroku)

### 1. Gracz łączy się z proxy

- trafia do Velocity
- Sonar przechwytuje połączenie

### 2. Limbo

- gracz trafia do środowiska weryfikacyjnego

### 3. Weryfikacja

Sonar sprawdza:

- ruch (gravity)
- pakiety
- kolizję
- client brand

### 4. CAPTCHA (jeśli atak)

- gracz musi ją przepisać

### 5. Przepuszczenie

→ gracz trafia na lobby

### 6. Blokada

→ bot zostaje odrzucony

---

## 🔒 Zabezpieczenia (MUST HAVE)

- ✔️ backendy NIE publiczne
- ✔️ tylko proxy dostępne publicznie
- ✔️ velocity forwarding ustawiony
- ✔️ firewall (na VPS)

---

## Test czy działa

- ✔️ weryfikacja działa
- ✔️ captcha pojawia się przy ataku
- ✔️ boty są blokowane
- ✔️ brak lagów przy join

---

## ❌ Najczęstsze błędy

### Gracze nie mogą wejść

- → za agresywne ustawienia

### Antybot nie działa

- → plugin nie wczytany

### Lag przy wejściu

- → za niski queue

---

## 🧰 Szybka checklista

- [ ] plugin znajduje się w `/plugins/`
- [ ] serwer został zrestartowany po wgraniu Sonara
- [ ] config wygenerował się poprawnie
- [ ] `verification: ALWAYS`
- [ ] `map-captcha: DURING_ATTACK`
- [ ] `gravity / collision / vehicle` włączone
- [ ] `max-online-per-ip` ustawione
- [ ] `queue` ustawiona (np. 6–8)
- [ ] baza danych działa (jeśli używana)
- [ ] backendy NIE są publiczne
- [ ] łączysz się przez proxy (Velocity)
- [ ] captcha pojawia się podczas ataku

→ Jeśli wszystko zaznaczone = Sonar działa poprawnie

---

## 📎 Dodatkowe informacje

- Sonar działa jako plugin (nie osobny serwer)
- nie wymaga dodatkowych portów
- najlepiej działa z Velocity

---

## Autor

**noisy144 / mikusiek144**

- GitHub: [https://github.com/mikusiek144](https://github.com/mikusiek144)
- Discord: noisy144

## Wsparcie

Jeśli poradnik pomógł:

- zostaw gwiazdkę
- udostępnij
- masz problem? napisz na Discord

---
