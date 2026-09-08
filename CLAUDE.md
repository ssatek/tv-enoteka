# CLAUDE.md — tv_enoteka

## O projektu
Digital signage / dotykový kiosek — TV panely v Enotéce znojemských vín. Vstupní obrazovka s dlaždicemi, ze které si návštěvník klepnutím vybere web k prohlédnutí (e-shop, nápojový lístek, vinná karta, zážitky v regionu, LAHOFER). 2–3 identické Android signage panely (Huidu / RK3576).

## Stack
- **Hardware:** Huidu signage přehrávač, firmware "MagicPlayer", postavený na běžném Androidu 14 (WebView 127), dotykový displej. Ne uzamčená smart TV — kromě vestavěného signage systému (Huidu cloud) na něm nejspíš jde nainstalovat i běžnou appku.
- **Správa obsahu — primárně:** [Fully Kiosk Browser](https://www.fully-kiosk.com/en/) sideloadovaný APK, natrvalo nastavený na jednu URL (naši `index.html`). Na panelu 1 nainstalováno a funkční — fullscreen, autoreload-on-idle vrací na nabídku po nečinnosti. Kiosk Mode (lockdown) zatím vypnutý, viz Otevřené body.
- **Správa obsahu — fallback**, pokud sideload appky nepůjde: cloud platforma [led-cloud.com](https://led-cloud.com/) (XiaoHui Cloud, Huidu) — bezplatná, cluster management pro víc panelů, program editor má widget "web page/HTML". Riziko: takové widgety bývají omezený/screenshot-based webview, kvalita vykreslení naší stránky není jistá.
- **Vlastní obsah:** `index.html` — vstupní/výběrová obrazovka (HTML/CSS/JS bez frameworku, dotykové dlaždice). Žádná automatická rotace — návštěvník si klepnutím vybere web, prohlížeč na něj přímo naviguje (top-level, ne iframe).
- **Deploy:** živé na **https://tv-enoteka.vercel.app/** — GitHub `ssatek/tv-enoteka` → Vercel auto-deploy na push do `main` (stejný postup jako u `menu_vinotrh.eshop` / `enoteka_vinotrh.eshop`, viz `01-projects/CLAUDE.md` → Deploy statických webů).

## Struktura
- `docs/nastaveni-tv.md` — postup registrace zařízení do cloudu/appky, inventář panelů, otevřené body
- `data/zarizeni-foto/` — fotky z displejů panelů (device info, no-program obrazovka)
- `index.html` — vstupní obrazovka (zdrojový kód obsahu pro TV)
- `output/` — zatím nepoužito

## Obsah nabídky (index.html → dlaždice #tiles)
| Dlaždice | URL | Framing |
|---|---|---|
| VINOTRH.CZ | https://vinotrh.cz/ | ❌ `X-Frame-Options: SAMEORIGIN` |
| Nápojový lístek | https://menu.vinotrh.cz/ | ✅ lze i do iframe |
| Enotéka — vinná karta | https://enoteka.vinotrh.cz/ | ✅ lze i do iframe |
| Ochutnej Znojmo | https://ochutnejznojmo.cz/ | ❌ `X-Frame-Options: SAMEORIGIN` |
| LAHOFER | https://lahofer.cz/ | ✅ lze i do iframe |

Protože 2 z 5 webů nejdou vložit do iframe (blokují to hlavičkou), chovají se **všechny** dlaždice stejně — přímý odkaz, ne iframe. Návštěvník tím opouští naši stránku a prohlíží cizí web běžnou navigací:
- **Krok zpět uvnitř webu** (např. detail vína → seznam vín) — Fully Kiosk **Show Action Bar + Show Back Button**, funkční.
- **Návrat až na naši nabídku** po nečinnosti — Fully Kiosk **Auto Reload on Idle** (30–60 s → návrat na Start URL), funkční.

`www_zwg.wine` a `prezentace_ZWG` byly z nabídky vyřazeny na žádost uživatele (2026-09-08).

## Otevřené body
- **Kiosk Mode zůstává vypnutý** — konflikt appky s nativním launcherem MagicPlayer (opakované "Blocked Magic Player" blikání). Řešení: nejdřív nastavit Fully Kiosk jako výchozí Home/launcher zařízení, pak zapnout Kiosk Mode. Viz `docs/nastaveni-tv.md`.
- Displej panelu 1 hlásí rozlišení 3840×2160 @ 90° — fyzická orientace (na výšku/na šířku) je třeba potvrdit na místě, ovlivní layout `index.html`.
- Panely 2 a 3 — fyzicky zapojit, zopakovat instalaci Fully Kiosk (funkční postup viz `docs/nastaveni-tv.md`), doplnit Device ID/IP do inventáře.
- Přesný postup nastavení viz `docs/nastaveni-tv.md`.
