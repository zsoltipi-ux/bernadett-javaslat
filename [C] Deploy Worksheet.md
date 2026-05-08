# Deploy Worksheet — Bernadett Asszisztens

> Ez egy munkalap. Töltsd ki sorban, fölülről lefelé. Soha nem kell visszaugrani.
> A 6 megnyitott böngészőfül pont ennek a sorrendje.

---

## ☐ FÜL 1 — OpenAI (platform.openai.com/api-keys)

```
[ ] Belépve / regisztrálva
[ ] Billing oldalon $10 hitel hozzáadva
[ ] Create new secret key → "bernadett-asszisztens" névvel

OPENAI_API_KEY = sk-_______________________________
```

---

## ☐ FÜL 2 — Telegram BotFather (t.me/BotFather)

Üzenetek sorrendben:

```
[ ] /newbot
[ ] Display name:    Bernadett Asszisztens
[ ] Username:        bernadett_asszisztens_bot
                     (ha foglalt: bernadett_asszisztens_zs_bot)

TELEGRAM_BOT_TOKEN = _____________________________________

[ ] /setprivacy → válaszd ki a botot → Disable

[ ] Találj ki egy random titkot:
TELEGRAM_WEBHOOK_SECRET = bernadett-asz-2026-_______
                         (csak betűk, számok, kötőjel, 20+ karakter)
```

---

## ☐ FÜL 3 — User ID-k (t.me/userinfobot)

```
[ ] Küldj a botnak egy "hi" üzenetet → visszaírja a saját Telegram ID-d:

ZSOLT_TELEGRAM_ID = _________________

[ ] (Bernadetté csak később, az onboarding hívásnál)

(most az env varokba indulunk csak a tieddel, később bővítjük)
```

---

## ☐ FÜL 4 — Upstash Redis (console.upstash.com)

```
[ ] GitHub-bal belépve
[ ] Create Database
    Name:      bernadett-asszisztens
    Type:      Regional
    Region:    eu-west-1 (Frankfurt)
    Eviction:  Disable
    Tier:      Free

[ ] DB megnyit → görgess a "REST API" szekcióhoz → másold:

UPSTASH_REDIS_REST_URL   = https://_____________.upstash.io
UPSTASH_REDIS_REST_TOKEN = AY________________________________
```

---

## ☐ FÜL 5 — Resend (resend.com/signup)

```
[ ] GitHub-bal belépve
[ ] API Keys → Create:
    Name:        bernadett-asszisztens
    Permission:  Full access
    Domain:      All domains

RESEND_API_KEY = re_____________________________

[ ] Sender email:
RESEND_FROM    = asszisztens@onboarding.resend.dev
                 (ez most az ingyenes/azonnal-működő, később saját domain)

[ ] Bernadett email-je (egyelőre a sajátod a teszthez!):
BERNADETT_EMAIL = ___________________________
```

---

## ☐ FÜL 6 — Vercel (a deploy gomb)

A 6. fül egy "Deploy" oldal, ahol importálódik a privát repo, és kéri az env varokat.
Mivel a repo PRIVATE, **a GitHub auth scope-on engedélyezni kell hozzáférést**:

```
[ ] Continue with GitHub
[ ] (Ha kéri) Install GitHub App → Only select repositories → "bernadett-asszisztens"
[ ] Import gomb a "bernadett-asszisztens" mellett

[ ] Configure Project:
    Framework Preset:  Other
    Root Directory:    (üresen hagyja, ha kéri: ".")

[ ] Environment Variables — illeszd be sorban (a fenti értékekből):
```

| Név | Érték |
|---|---|
| `OPENAI_API_KEY` | (FÜL 1) |
| `TELEGRAM_BOT_TOKEN` | (FÜL 2) |
| `TELEGRAM_WEBHOOK_SECRET` | (FÜL 2) |
| `ALLOWED_USER_IDS` | (FÜL 3) — `ZSOLT_TELEGRAM_ID` |
| `BERNADETT_TELEGRAM_ID` | (FÜL 3) — most ugyanaz, mint Zsolt ID |
| `BERNADETT_EMAIL` | (FÜL 5) — most a sajátod, később az övé |
| `UPSTASH_REDIS_REST_URL` | (FÜL 4) |
| `UPSTASH_REDIS_REST_TOKEN` | (FÜL 4) |
| `RESEND_API_KEY` | (FÜL 5) |
| `RESEND_FROM` | `asszisztens@onboarding.resend.dev` |
| `TZ` | `Europe/Budapest` |

```
[ ] Deploy gomb
[ ] Vár 30-60 mp → kapsz egy URL-t:

DEPLOY_URL = https://bernadett-asszisztens-________.vercel.app
```

---

## ☐ UTOLSÓ LÉPÉSEK — Webhook + parancs-menü + teszt

Másold be a böngészőbe sorban (cseréld a két csillagos részt!):

```
[ ] 1. Webhook regisztrálása:
https://**DEPLOY_URL**/api/setup-webhook?secret=**TELEGRAM_WEBHOOK_SECRET**

→ JSON: "ok": true, "description": "Webhook was set"

[ ] 2. Telegram menü-parancsok beregisztrálása (a / gomb mutassa a parancsokat):
https://**DEPLOY_URL**/api/setup-commands?secret=**TELEGRAM_WEBHOOK_SECRET**

→ JSON: "ok": true, "commands_count": 7

[ ] 3. Health check:
https://**DEPLOY_URL**/api/health

→ "ok": true és "missing": [] — minden env var bent van

[ ] 4. Dashboard (Te magad nézhetsz rá Bernadett adataira):
https://**DEPLOY_URL**/api/dashboard?secret=**TELEGRAM_WEBHOOK_SECRET**

→ Egy szép HTML oldal: ma rögzítve / holnapi reminders / heti stat
   (Ezt mentsd el bookmark-ként, ezen tudod monitorozni az első hetet!)

[ ] 5. Saját telefonon teszt:
   • Telegramban keresd a botod
   • /start → üdvözlet
   • 🎙 ikon → "Kovács István hívott, lakást keres a Bartók úton, hívj vissza május 22-én reggel 9-kor"
   • 5-10 mp múlva: ✅ Rögzítettem... + ID
   • Próbáld: /lista → mai/holnapi
   • Próbáld: /torol XXXX → törlés

[ ] 6. Force email teszt:
https://**DEPLOY_URL**/api/digest-evening?force=1

→ Megérkezik az esti összefoglaló a `BERNADETT_EMAIL`-re

[ ] 7. Reggeli email teszt:
https://**DEPLOY_URL**/api/digest-morning?force=1
```

---

## Ha valami nem megy

- **Vercel deploy fail (build error)** → másold ide a Vercel logban a piros sort, megnézzük
- **Telegram bot nem válaszol** → `/api/health` mit ír? `/api/setup-webhook` újra kell?
- **OpenAI 401** → API key rosszul van bemásolva (gyakran ott van egy elkószált space a végén)
- **Email nem jön** → Resend dashboard → Emails tab → status (kék = elküldve, piros = hibás)
- **Redis 401** → Upstash token elgépelve

Bármelyik fail → ide a console output / képernyőkép, javítjuk.

---

## Ha minden zöld → már működik. Most jön Bernadett.

Az ő részéről:
1. Letölti a Telegram appot (ha nincs még)
2. Keresi a botod nevén → `/start`
3. Bot kiírja az ID-jét → ezt küldi neked
4. Te frissíted Vercel-en az `ALLOWED_USER_IDS`-t (most `ZSOLT_ID,BERNADETT_ID`)
5. `BERNADETT_TELEGRAM_ID` = csak az övé
6. `BERNADETT_EMAIL` = az övé
7. Vercel auto-redeploy ~30 mp
8. Bernadett tesztel → ✅
9. **Indul a 2 hetes próba**
