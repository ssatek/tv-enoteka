# CLAUDE.md — tv_enoteka

## O projektu
Digital signage — TV panely v Enotéce znojemských vín, promítající weby a prezentace o znojemském regionu a firmách skupiny ZWG.WINE (LAHOFER, ZNOVÍN, HANZEL, WALDBERG). 2–3 identické Android signage panely (Huidu / RK3576).

## Stack
- **Hardware:** Huidu signage přehrávač, firmware "MagicPlayer", postavený na běžném Androidu 14 (WebView 127). Ne uzamčená smart TV — kromě vestavěného signage systému (Huidu cloud) na něm nejspíš jde nainstalovat i běžnou appku.
- **Správa obsahu — primárně:** [Fully Kiosk Browser](https://www.fully-kiosk.com/en/) sideloadovaný APK, natrvalo nastavený na jednu URL (naši `index.html` po nasazení). Appka řeší kiosk mód (fullscreen, lockdown, autostart po výpadku proudu) — rotaci obsahu řeší náš JS, ne appka ani cloud.
- **Správa obsahu — fallback**, pokud sideload appky nepůjde: cloud platforma [led-cloud.com](https://led-cloud.com/) (XiaoHui Cloud, Huidu) — bezplatná, cluster management pro víc panelů, program editor má widget "web page/HTML". Riziko: takové widgety bývají omezený/screenshot-based webview, ne jistá kvalita vykreslení naší JS rotace.
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

Protože 2 z 5 webů nejdou vložit do iframe (blokují to hlavičkou), chovají se **všechny** dlaždice stejně — přímý odkaz, ne iframe. Návštěvník tím opouští naši stránku; návrat zpět na nabídku zatím řeší jen tlačítko zpět v prohlížeči / Fully Kiosk "Home" gesto (viz otevřený bod níže).

`www_zwg.wine` a `prezentace_ZWG` byly z nabídky vyřazeny na žádost uživatele (2026-09-08).

## Otevřené body
- **Návrat na vstupní obrazovku po odchodu na jiný web** — zatím neřešeno. Až se vrátíme ke konfiguraci Fully Kiosk, ověřit jeho funkci pro automatický návrat na Start URL po X sekundách nečinnosti.
- Displej panelu 1 hlásí rozlišení 3840×2160 @ 90° — fyzická orientace (na výšku/na šířku) je třeba potvrdit na místě, ovlivní layout `index.html`.
- Fully Kiosk na panelu 1: Kiosk Mode zatím vypnutý kvůli konfliktu s nativním launcherem MagicPlayer (blikání) + appka hlásila "Waiting for network connection" — řešení odloženo, viz `docs/nastaveni-tv.md`.
- Přesný postup nastavení viz `docs/nastaveni-tv.md`.
