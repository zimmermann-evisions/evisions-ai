# Dev pipeline — z dizajnu do kódu

Vlastný proces evisions na preklopenie Figma hi-fi dizajnu do reálnej,
nasaditeľnej appky. Nadväzuje na existujúce evisions build pravidlá
(`evisions-essentials.md`, `build-guardrails.md`) — nerobí ich duplicitne,
len ich zaraďuje do konkrétnych krokov pri prechode z dizajnu na kód.

---

## Fáza 1 — Z Figmy na tokeny

**Nástroj: Impeccable Extract.**
Vytiahne dizajnové tokeny priamo z Figma súboru (farby, typografia, spacing,
tiene, radiusy...) a vygeneruje `tokens.css`. Toto je **jediný zdroj pravdy**
pre vizuálne hodnoty — nikto ich neprepisuje ručne v kóde, aby sa dizajn a
kód nerozišli pri ďalšej iterácii vo Figme.

## Fáza 2 — Statický HTML/CSS náhľad (overovací checkpoint)

Postaví sa **čistý HTML/CSS náhľad** naviazaný na `tokens.css` — rýchlo,
bez frameworku, len na jednu vec: **vizuálne overiť, že sa tokeny preklopili
správne** a frontend sedí s Figma dizajnom, kým sa investuje čas do
skutočnej appky.

⚠️ **Dôležité:** toto je **overovací medzikrok, nie finálny tech stack.**
Po schválení sa ide do skutočnej implementácie v evisions stacku
(Next.js + TypeScript + Tailwind) — trvalý holý HTML/CSS build by aj tak
neprešiel evisions CI/deploy pipeline.

## Fáza 3 — Technická špecifikácia

Zo zadania (brief + schválený dizajn) vznikne **technická špecifikácia** —
čo presne appka robí, aké dáta drží, aké connectory/formuláre/integrácie
potrebuje (presne tie, čo pribudnú po schválenom statickom frontende
z Fázy 2).

**Špecifikácia sa rozdrobí na menšie implementačné plány** — jeden plán na
jednu ucelenú časť (napr. "hero + kalkulačka", "formulár registrácie",
"napojenie na X"), nie jeden obrovský monolitický plán na celú appku naraz.
Menšie plány sa dajú samostatne reviewovať, otestovať a v prípade problému
vrátiť bez toho, aby to strhlo celý build.

**Technickú implementáciu aj samotné implementačné plány robí Opus**, nie
slabší model — toto je krok, kde sa chyby najdrahšie naprávajú spätne.

## Fáza 4 — Konvencie a tech stack

Toto **už evisions má definované** (`evisions-essentials.md`) — tento krok
len zabezpečuje, že sa to reálne dodrží, nie že sa to vymýšľa odznova
pri každom projekte:
- **Stack:** Next.js + TypeScript + Tailwind + Docker + Caddy
- **Dáta:** Postgres / SQLite / JSON súbor — nikdy hostovaná DB (Supabase,
  Firebase, PlanetScale...) — CI to aj tak odmietne
- **Deploy:** nikdy Vercel — evisions vlastná infraštruktúra
- **Repo:** zakladá AI tím z `ai-deploy-probe` template, nie ručne

## Fáza 5 — Implementácia + testy, ktoré reálne niečo testujú

Testy sa nepíšu ako formalita, ktorá vždy prejde — **musia reálne overovať
správanie appky.** Praktická kontrola kvality testu: dá sa zámerne niečo
pokaziť a test na to spadne? Ak test prejde aj na rozbitom kóde, nie je to
test, je to divadlo.

## Fáza 6 — Bezpečnostné štandardy

Nie samostatná odtrhnutá kapitola, ale priebežné pravidlo počas celej
implementácie:
- Secrets len v `.env`, nikdy v kóde ani v `docker-compose.yml`
- Auth sa **reálne overí** (request na chránenú stránku aj API route
  odhlásený, na seedovanej DB) — nie len opticky "vyzerá to chránené"
- Žiadne pridávanie závislostí, čo appka nepotrebuje

## Fáza 7 — Code review

Štandardný review implementácie pred mergom — čitateľnosť, dodržanie
konvencií z Fázy 4, žiadne halucinované knižnice/API.

## Fáza 8 — Security review

Samostatný prechod zameraný len na bezpečnosť — nie to isté ako code
review. Cielene sa pozerá na to, čo code review môže prehliadnuť: auth
medzery, exponované secrets, nedeklarované externé volania.

## Fáza 9 — Jedno veľké reálne testovanie (nie len spustiť test suite)

Toto je najdôležitejší bod na explicitné zadanie Claudovi — **nestačí
spustiť automatizované testy a nahlásiť "prešli".** Claude appku **reálne
skopíruje, spustí (`docker compose up` na čistom stave) a prejde ju ako
človek** — otestuje flow, skontroluje, že sa appka naozaj správa tak, ako
má, nie len že testy vrátili zelenú.

Toto priamo nadväzuje na existujúce pravidlo: *"Never claim a check passed
that you did not run."* Tvrdenie "funguje to" bez toho, aby to bolo naozaj
spustené a prejdené, sa nepočíta.

---

## Zhrnutie — poradie krokov

```
Figma dizajn
     │
     ▼
1. Impeccable Extract → tokens.css
     │
     ▼
2. Statický HTML/CSS náhľad → schválenie frontendu (overovací krok)
     │
     ▼
3. Technická špecifikácia → rozdrobenie na menšie plány (Opus)
     │
     ▼
4. Implementácia v evisions stacku (podľa existujúcich konvencií)
     │              │
     │       5. Testy, ktoré reálne niečo testujú (priebežne)
     │              │
     │       6. Bezpečnostné štandardy (priebežne)
     │
     ▼
7. Code review
     │
     ▼
8. Security review
     │
     ▼
9. Jedno veľké reálne testovanie — appka sa spustí a prejde naživo
     │
     ▼
Handover (existujúci /ai-handover proces)
```
