# 📡 Moldova Telegram Directory

Un director public de grupuri și canale Telegram din Moldova.

---

## 🗂 Structura proiectului

```
/
├── .github/
│   └── workflows/
│       └── scan-and-deploy.yml   # Automatizare: scan + build + deploy
├── public/
│   └── avatars/                  # Avatare descărcate de script (256×256 JPG)
├── scripts/
│   └── scan_telegram_moldova.py  # Script Python pentru colectarea datelor
├── src/
│   ├── components/
│   │   └── GroupCard.astro       # Card reutilizabil per grup/canal
│   ├── data/
│   │   └── telegram_groups_moldova.json  # Date generate de script
│   └── pages/
│       └── index.astro           # Pagina principală cu filtrare
├── astro.config.mjs
├── package.json
└── README.md
```

---

## 🚀 Setup local

### Cerințe

- Node.js 18+
- Python 3.9+
- Un cont Telegram cu API ID și API Hash (obținute de pe [my.telegram.org](https://my.telegram.org/apps))

### Instalare

```bash
# Clonează repo-ul
git clone https://github.com/username/moldova-telegram-directory.git
cd moldova-telegram-directory

# Instalează dependențele Astro
npm install

# Instalează dependențele Python
pip install telethon pillow
```

### Configurare script

Deschide `scripts/scan_telegram_moldova.py` și completează:

```python
API_ID   = 0          # numărul întreg de pe my.telegram.org
API_HASH = ""         # hash-ul de pe my.telegram.org
PHONE    = "+373..."  # numărul tău de telefon cu prefix internațional
```

> ⚠️ Nu commit-a niciodată aceste valori în repo. Folosește variabile de mediu sau `.env` local (adaugă `.env` și fișierele `.session` în `.gitignore`).

---

## 🔄 Workflow

### 1. Colectare date (manual sau automat)

```bash
cd scripts
python scan_telegram_moldova.py
```

La prima rulare, Telegram trimite un cod de confirmare **ca mesaj în aplicația Telegram** (nu SMS). Introdu codul în terminal. Sesiunea se salvează local și nu mai cere cod la rulările ulterioare.

Scriptul generează:

- `src/data/telegram_groups_moldova.json` — datele tuturor grupurilor/canalelor
- `public/avatars/*.jpg` — avatarele redimensionate la 256×256

La rulările ulterioare, scriptul **actualizează** datele existente (membri, descriere) și **adaugă** doar intrările noi, fără să re-descarce avatarele existente.

### 2. Previzualizare locală

```bash
npm run dev
```

Deschide [http://localhost:4321](http://localhost:4321).

### 3. Build static

```bash
npm run build
```

Fișierele statice sunt generate în `dist/`.

---

## ⚙️ GitHub Actions (automatizare completă)

Pentru a automatiza scanarea și deploy-ul, adaugă aceste secrete în repo-ul GitHub (`Settings → Secrets and variables → Actions`):

| Secret        | Descriere                                         |
| ------------- | ------------------------------------------------- |
| `TG_API_ID`   | API ID de pe my.telegram.org                      |
| `TG_API_HASH` | API Hash de pe my.telegram.org                    |
| `TG_SESSION`  | String session generat de Telethon (vezi mai jos) |

#### Generare TG_SESSION (o singură dată, local)

```python
from telethon.sync import TelegramClient
from telethon.sessions import StringSession

with TelegramClient(StringSession(), API_ID, API_HASH) as client:
    print(client.session.save())
```

Rulează acest script o singură dată local, autentifică-te, copiază string-ul afișat și pune-l ca secret `TG_SESSION` în GitHub.

Workflow-ul rulează automat **zilnic** și la fiecare push pe `main`.

---

## 🛠 Tehnologii

- [Astro](https://astro.build) — generare site static
- [Telethon](https://docs.telethon.dev) — client Python pentru Telegram MTProto API
- [Pillow](https://pillow.readthedocs.io) — procesare imagini (redimensionare avatare)
- GitHub Pages — hosting static gratuit
- GitHub Actions — automatizare scan + deploy

---

## 📋 Date colectate

Pentru fiecare grup/canal public sunt stocate:

- Nume și username
- Link public (`t.me/username`)
- Tip (canal sau grup)
- Număr de membri
- Descriere
- Status verificat / scam / fake (din metadatele Telegram)
- Avatar (256×256 JPG, stocat local)

Nu sunt colectate mesaje, membri individuali sau date private de niciun fel. Doar metadate publice ale grupurilor/canalelor.

---

## 🤝 Contribuții

Grupurile și canalele sunt descoperite automat prin căutare pe cuvinte cheie. Dacă vrei să adaugi manual un grup/canal public care nu apare în listă, deschide un [Issue](https://github.com/username/moldova-telegram-directory/issues) cu linkul grupului.

---

## 📄 Licență

MIT — vezi [LICENSE](./LICENSE)
