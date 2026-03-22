# antibot-setup POLSKI

![Minecraft](https://img.shields.io/badge/Minecraft-Network-green?style=for-the-badge)
![Proxy](https://img.shields.io/badge/Proxy-Velocity-blue?style=for-the-badge)
![AntyBot](https://img.shields.io/badge/Protection-Sonar-red?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-AntiBot-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

> Kompletny poradnik konfiguracji **Sonar AntiBot jako pluginu dla Velocity**

---

## 📡 Architektura (jak to działa)

```id="h9g2kp"
Gracz
  ↓
Velocity (proxy + Sonar)
  ↓
Lobby / BoxPvP / Survival (backendy)
```

Sonar działa **wewnątrz Velocity**, co oznacza:

* gracz łączy się z proxy
* Sonar przechwytuje połączenie
* przeprowadza weryfikację
* dopiero potem gracz trafia na backend

---

## 📌 Czym jest Sonar?

**Sonar** to nowoczesny system antybotowy dla Velocity.

W praktyce:

* analizuje ruch gracza (pakiety, fizyka, zachowanie)
* wykrywa boty typu join/flood
* wykorzystuje limbo do weryfikacji
* może wymuszać CAPTCHA

→ Backend nigdy nie otrzymuje niezweryfikowanego gracza

---

## 💡 Dlaczego warto używać Sonara?

* ✔️ działa bezpośrednio na proxy
* ✔️ nie wymaga dodatkowych portów
* ✔️ system limbo (wydajny)
* ✔️ automatyczne wykrywanie ataków
* ✔️ bardzo skuteczny przeciwko botom join

Krótko: **standard w zabezpieczonych sieciach Velocity**

---

## Wymagania

* Java 17+
* Velocity
* Sonar `.jar`
* Backendy (Paper / Purpur)

---

## Instalacja (Hosting / VPS)

### Struktura plików

```id="v7k3df"
/server/
  ├── velocity/
  │    └── plugins/
  │         └── Sonar.jar
  ├── lobby/
  ├── boxpvp/
```

### Hosting (np. Pterodactyl)

* wrzuć Sonar do `/plugins/`
* zrestartuj serwer

### VPS (Linux)

```bash id="c4k8n1"
screen -S velocity
java -jar velocity.jar
```

---

## ⚙️ Konfiguracja Sonara

Plik:

```id="xj2n9p"
plugins/sonar/config.yml
```

---

## Ustawienia ogólne

```yaml id="r6t1sm"
general:
  check-for-updates: true
  log-player-addresses: true
  max-online-per-ip: 2
```

→ ogranicza liczbę połączeń z jednego adresu IP

---

## Attack Tracker (automatyczne wykrywanie ataków)

```yaml id="m8z3qk"
attack-tracker:
  min-players-for-attack: 15
  min-attack-duration: 30000
  min-attack-threshold: 2
  attack-cooldown-delay: 3000
```

→ Sonar sam wykrywa ataki i odpowiednio dostosowuje swoje działanie

---

## Baza danych (opcjonalna, ale zalecana)

```yaml id="p2l6xs"
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

→ zapisuje zweryfikowanych graczy
→ mniej CAPTCHA dla prawdziwych graczy

---

## Kolejka (anti-spam join)

```yaml id="w9q4bn"
queue:
  max-polls: 8
```

→ ogranicza liczbę graczy wpuszczanych jednocześnie

---

## Weryfikacja (najważniejsze)

```yaml id="z1c7yt"
verification:
  timing: ALWAYS
```

→ każdy nowy gracz przechodzi weryfikację

---

## System sprawdzania (checks)

```yaml id="d5n8kr"
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

→ wykrywa boty na podstawie zachowania klienta

---

## CAPTCHA (KLUCZOWE)

```yaml id="u3h9vx"
map-captcha:
  timing: DURING_ATTACK
  style: modern
  max-duration: 25000
  max-tries: 3
```

→ CAPTCHA pojawia się tylko podczas ataku
→ lepsze doświadczenie dla graczy

---

## Sprawdzanie klienta

```yaml id="k7m2qa"
client-brand:
  enabled: true
  valid-regex: ^[!-~ ]+$
  max-length: 64
```

→ wykrywa niestandardowe klienty / boty

---

## Blacklista

```yaml id="s4f8jw"
blacklist-threshold: 3
blacklist-time: 900000
```

→ adres IP trafia na blacklistę po kilku nieudanych próbach

---

## ⚡ Jak to działa (krok po kroku)

### 1. Gracz łączy się z proxy

* trafia do Velocity
* Sonar przechwytuje połączenie

### 2. Limbo

* gracz trafia do środowiska weryfikacyjnego

### 3. Weryfikacja

Sonar sprawdza:

* ruch (gravity)
* pakiety
* kolizje
* client brand

### 4. CAPTCHA (w trakcie ataku)

* gracz musi ją przepisać

### 5. Przepuszczenie

→ gracz trafia na lobby

### 6. Blokada

→ bot zostaje odrzucony

---

## 🔒 Zabezpieczenia (MUST HAVE)

* ✔️ backendy NIE są publiczne
* ✔️ tylko proxy jest dostępne publicznie
* ✔️ włączony Velocity forwarding
* ✔️ firewall (na VPS)

---

## Test działania

* ✔️ weryfikacja działa
* ✔️ CAPTCHA pojawia się podczas ataku
* ✔️ boty są blokowane
* ✔️ brak opóźnień przy dołączaniu

---

## ❌ Najczęstsze błędy

### Gracze nie mogą wejść

* → ustawienia są zbyt agresywne

### Antybot nie działa

* → plugin nie został wczytany

### Opóźnienie przy wejściu

* → kolejka ustawiona zbyt nisko

---

## 🧰 Szybka checklista

* [ ] plugin znajduje się w `/plugins/`
* [ ] serwer został zrestartowany po wgraniu Sonara
* [ ] konfiguracja wygenerowała się poprawnie
* [ ] `verification: ALWAYS`
* [ ] `map-captcha: DURING_ATTACK`
* [ ] `gravity / collision / vehicle` włączone
* [ ] `max-online-per-ip` ustawione
* [ ] `queue` ustawiona (np. 6–8)
* [ ] baza danych działa (jeśli używana)
* [ ] backendy NIE są publiczne
* [ ] łączysz się przez proxy (Velocity)
* [ ] CAPTCHA pojawia się podczas ataku

→ Jeśli wszystko jest zaznaczone = Sonar działa poprawnie

---

## 📎 Dodatkowe informacje

* Sonar działa jako plugin (nie jako osobny serwer)
* nie wymaga dodatkowych portów
* najlepiej działa z Velocity

---

## Autor

**noisy144 / mikusiek144**

* GitHub: https://github.com/mikusiek144
* Discord: noisy144

## Wsparcie

Jeśli poradnik Ci pomógł:

* zostaw gwiazdkę ⭐
* udostępnij go
* masz problem? napisz na Discord
---
