# CLAUDE.md — tv_enoteka

## O projektu
Digital signage — TV panely v Enotéce znojemských vín, promítající weby a prezentace o znojemském regionu a firmách skupiny ZWG.WINE (LAHOFER, ZNOVÍN, HANZEL, WALDBERG). 2–3 identické Android signage panely (Huidu / RK3576).

## Stack
- **Hardware:** Huidu signage přehrávač, firmware "MagicPlayer", postavený na běžném Androidu 14 (WebView 127). Ne uzamčená smart TV — kromě vestavěného signage systému (Huidu cloud) na něm nejspíš jde nainstalovat i běžnou appku.
- **Správa obsahu — primárně:** [Fully Kiosk Browser](https://www.fully-kiosk.com/en/) sideloadovaný APK, natrvalo nastavený na jednu URL (naši `index.html` po nasazení). Appka řeší kiosk mód (fullscreen, lockdown, autostart po výpadku proudu) — rotaci obsahu řeší náš JS, ne appka ani cloud.
- **Správa obsahu — fallback**, pokud sideload appky nepůjde: cloud platforma [led-cloud.com](https://led-cloud.com/) (XiaoHui Cloud, Huidu) — bezplatná, cluster management pro víc panelů, program editor má widget "web page/HTML". Riziko: takové widgety bývají omezený/screenshot-based webview, ne jistá kvalita vykreslení naší JS rotace.
- **Vlastní obsah:** `index.html` — statická kiosk stránka (HTML/CSS/JS bez frameworku), fullscreen iframe rotátor střídající weby v cyklu. Ať už appka nebo Huidu widget, oba jen zobrazují tuhle jednu URL — úpravy pořadí/obsahu se dělají v kódu, ne přes cloud/appku.
- **Deploy (až bude odsouhlaseno):** Git → GitHub → Vercel, stejný postup jako u `menu_vinotrh.eshop` / `enoteka_vinotrh.eshop` (viz `01-projects/CLAUDE.md` → Deploy statických webů).

## Struktura
- `docs/nastaveni-tv.md` — postup registrace zařízení do cloudu, inventář panelů, otevřené body
- `data/zarizeni-foto/` — fotky z displejů panelů (device info, no-program obrazovka)
- `index.html` — kiosk rotátor (zdrojový kód obsahu pro TV)
- `output/` — zatím nepoužito

## Obsah rotace (index.html → SLIDES)
| Slide | URL | Zdrojový projekt |
|---|---|---|
| ZWG.WINE | https://www.zwg.wine/ | `www_zwg.wine` |
| Enotéka — vinná karta | https://enoteka.vinotrh.cz/ | `enoteka_vinotrh.eshop` |
| Nápojový lístek | https://menu.vinotrh.cz/ | `menu_vinotrh.eshop` |

Kandidát k doplnění: `prezentace_ZWG` (firemní prezentace skupiny) — zatím jen lokální HTML (`output/zwg-wine-prezentace.html`), není nasazená na veřejnou URL, takže ji zatím nelze zařadit do rotace přes iframe.

## Poznámky
- Displej panelu 1 hlásí rozlišení 3840×2160 @ 90° — fyzická orientace (na výšku/na šířku) je třeba potvrdit na místě, ovlivní layout `index.html` i nastavení rozlišení programu v cloudu.
- Žádný z cílových webů (zwg.wine, vinotrh.cz subdomény) nenastavuje `X-Frame-Options`/CSP blokující iframe — ověřeno, framing funguje.
- Otevřené body a přesný postup nastavení viz `docs/nastaveni-tv.md`.
