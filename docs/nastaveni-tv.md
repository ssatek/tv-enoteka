# Nastavení TV panelů — Enotéka

## Co je za hardware

Panel je Android digital-signage přehrávač od **Shenzhen Huidu Technology** (firmware "MagicPlayer"), postavený na běžném Androidu 14 — ne uzamčená smart TV. To znamená, že kromě vestavěného signage systému (posílání "programů" z cloudu/appky/USB) na něm jde nejspíš nainstalovat i běžnou Android appku, pokud zařízení umožňuje instalaci z neznámých zdrojů / USB.

- **Device model:** 3576V (Rockchip RK3576, Cortex-A72×4 + Cortex-A53×4)
- **Systém:** Android 14, WebView 127.0.6533.64
- **Rozlišení:** 3840×2160 @ 60 Hz, **otočeno 90°** (displej je fyzicky na výšku — potvrdit na místě)
- **RAM / úložiště:** 4 GB / 32 GB
- Fotky z displeje: `data/zarizeni-foto/`

## Zjištěná zařízení

| # | Device ID | Wi-Fi IP | Wi-Fi MAC | Umístění | Poznámka |
|---|-----------|----------|-----------|----------|----------|
| 1 | `3576V-4C6-02D1D` | 10.0.250.41 | 84:93:ec:2e:c9:11 | Enotéka — TBD | Aktuálně "no program", zapojeno na Wi-Fi, LAN nepřipojena (Ethernet IP 0.0.0.0) |
| 2 | — | — | — | — | Doplnit při zapojení druhého panelu |
| 3 | — | — | — | — | Doplnit při zapojení třetího panelu |

## Zvolený přístup: kiosk prohlížeč místo Huidu cloudu

Naše `index.html` je samostatná webová stránka, která si **sama v JS řídí rotaci obsahu** (střídání ZWG.WINE / vinné karty / nápojového lístku). Díky tomu stačí, aby na TV běžel prohlížeč natrvalo nastavený na tuhle jednu URL — není potřeba nic posílat přes Huidu cloud, žádný účet, žádné "programy". Update obsahu = úprava kódu a nový deploy, TV se samy obnoví (stránka má vestavěný auto-reload).

Prostý Chrome ale není kiosk režim (adresní řádek, dialogy po pádu, uživatel může omylem přepnout appku). Proto **Fully Kiosk Browser** — standardní appka pro přesně tenhle účel (digital signage): plná obrazovka, autostart po zapnutí/výpadku proudu, watchdog při zamrznutí, uzamčení proti vystoupení z appky.

### Postup A — Fully Kiosk Browser (primární, zkusit jako první)

1. **Ověřit, že jde instalovat APK.** V menu zařízení (4× klik na displej nebo tlačítko menu) → **System setting** → hledej "Unknown sources" / "Instalace z neznámých zdrojů" nebo "Developer options". Pokud je vypnuté, zapnout. Pokud v systému chybí Play Store, appka se instaluje jen ručně (viz krok 2).
2. **Stáhnout Fully Kiosk Browser APK** přímo z oficiálního webu [fully-kiosk.com](https://www.fully-kiosk.com/en/) (ne Play Store, pro případ, že na zařízení Play Store není) — na USB flash disk, vložit do zařízení a nainstalovat přes souborový manažer / instalaci z USB.
3. Po prvním spuštění proběhne **setup wizard** → nastavit:
   - **Start URL:** `https://<naše-nasazená-adresa>/` (Vercel URL `index.html`, viz níže)
   - **Exit PIN** (aby se appka nedala omylem/schválně opustit)
4. V nastavení appky (Device Management):
   - **Launch on Boot** → zapnout (appka naskočí sama po výpadku proudu/restartu)
   - **Keep Screen On** → zapnout, **Screen Off Timer** → vypnout
5. Zapnout **Kiosk Mode / Lockdown** (plná obrazovka, zákaz notifikační lišty, zákaz tlačítka Home/Back). Zdarma appka nabízí PLUS funkce (kiosk lockdown, remote admin) **jako neomezenou zkoušku** — pro trvalé nasazení bude potřeba licence na zařízení (cena na fully-kiosk.com), ale funkčně to jde otestovat hned zdarma.
6. Otestovat: vypnout a zapnout přívod proudu u TV → appka by se měla sama spustit a naběhnout na naši stránku bez zásahu.
7. Zopakovat pro panel 2 a 3 (appka umí export/import nastavení mezi zařízeními — nemusí se konfigurovat ručně 3×).

### Postup B — Huidu cloud (fallback, pokud sideload APK nejde)

Pokud firmware neumožní instalaci cizí appky (zamčené na signage systém bez možnosti obejít), použít vestavěnou cestu přes **led-cloud.com (XiaoHui Cloud)** — zdarma, cluster management pro víc panelů, editor programů má widget "web page/HTML", do kterého se vloží stejná URL naší stránky.

1. Menu (4× klik na displej) → **Networking and Bluetooth** → ověřit Wi-Fi (panel 1 už má: `10.0.250.41`).
2. **System setting** → **System Mode** → **Cloud networking mode**.
3. Server adresa: `led-cloud.com`, zadat uživatelské jméno účtu (založit na `https://led-cloud.com/`).
4. Uložit → zařízení by se mělo v cloudu objevit jako online (zelené).
5. V cloudu: **Program → LCD Program** → přidat widget **web page/HTML** na celou plochu → URL naší stránky → **Send → Complete Update** na skupinu zařízení.

**Riziko této cesty:** widgety "web page" v levných LED signage editorech bývají často jen omezený vestavěný webview, někdy i jen periodicky obnovovaný screenshot stránky místo živého vykreslení — kvalita/plynulost naší JS rotace není jistá. Proto je to záložní, ne primární plán.

## Nasazení `index.html`

Zatím jen lokální soubor. Až bude potvrzený obsah/pořadí, nasadíme na Vercel stejným postupem jako `menu_vinotrh.eshop` (viz `01-projects/CLAUDE.md` → Deploy statických webů) — vznikne veřejná URL, kterou vložíme jako Start URL do Fully Kiosk Browseru (nebo do Huidu programu při postupu B).

## Otevřené body / co potřebuji od tebe

- [ ] Fyzický přístup k panelu 1 — zkusit Postup A (instalace APK) a nahlásit, jestli šla
- [ ] Pokud Postup A nepůjde → přístupy pro založení účtu na led-cloud.com (Postup B)
- [ ] Fyzická orientace panelů (na výšku/na šířku) — na fotce je 90° otočení
- [ ] Přesná lokace jednotlivých panelů v Enotéce (pro tabulku výše)
- [ ] Odsouhlasit obsah/pořadí rotace na kiosk stránce (návrh v `index.html`)
- [ ] Souhlas s nasazením kiosk stránky na Vercel (nový projekt, veřejná URL)
