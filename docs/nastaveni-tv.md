# Nastavení TV panelů — Enotéka

## Co je za hardware

Panel je Android digital-signage přehrávač od **Shenzhen Huidu Technology** (firmware "MagicPlayer"), postavený na běžném Androidu 14, dotykový displej — ne uzamčená smart TV.

- **Device model:** 3576V (Rockchip RK3576, Cortex-A72×4 + Cortex-A53×4)
- **Systém:** Android 14, WebView 127.0.6533.64
- **Rozlišení:** 3840×2160 @ 60 Hz, **otočeno 90°** (displej je fyzicky na výšku — potvrdit na místě)
- **RAM / úložiště:** 4 GB / 32 GB
- Fotky z displeje: `data/zarizeni-foto/`

## Zjištěná zařízení

| # | Device ID | Wi-Fi IP | Wi-Fi MAC | Umístění | Stav |
|---|-----------|----------|-----------|----------|------|
| 1 | `3576V-4C6-02D1D` | 10.0.250.41 | 84:93:ec:2e:c9:11 | Enotéka — TBD | ✅ Fully Kiosk Browser nainstalován a funkční |
| 2 | — | — | — | — | Doplnit při zapojení druhého panelu |
| 3 | — | — | — | — | Doplnit při zapojení třetího panelu |

## Zvolený přístup: Fully Kiosk Browser (funguje, panel 1 hotový)

Appka se natrvalo drží na jedné URL — naší vstupní obrazovce `https://tv-enoteka.vercel.app/` s dlaždicemi. Update obsahu = úprava kódu v repu a push, appka se sama obnoví.

### Funkční postup instalace a nastavení (ověřeno na panelu 1)

1. **Instalace APK z USB.** Stáhnout **Fully Kiosk Browser APK ver. 1.61.3 (regular edition)** z [fully-kiosk.com](https://www.fully-kiosk.com/en/) (ne Play Store, ne EMM/Single App/Video/Exam edice) na USB disk, vložit do zařízení — na tomhle firmwaru appka po vložení USB **automaticky nabídla instalaci**, nebylo potřeba ručně hledat "Unknown sources".
2. V **Quick Start Settings** appky nastavit:
   - **Start URL:** `https://tv-enoteka.vercel.app/` — **pozor, bez `www.`** (s `www.` to timeoutuje, tahle subdoména neexistuje)
   - **Fullscreen Mode:** zapnuto
   - **Show Address Bar:** vypnuto
   - **Kiosk Mode:** zatím **vypnuto** (viz Otevřené body — konflikt s nativním launcherem MagicPlayer)
3. V **Toolbars and Appearance**:
   - **Show Action Bar:** zapnuto
   - **Show Back Button** (a volitelně **Show Forward Button**): zapnuto

   → Řeší krok zpět *uvnitř* prohlíženého webu (např. z detailu vína na `vinotrh.cz` zpět na seznam) — na rozdíl od Auto Reload on Idle níže, který vrací až na naši úvodní nabídku. **Ověřeno funkční.**
4. V **Web Auto Reload**:
   - **Auto Reload on Idle:** zapnuto, 30–60 s
   - **Load Current Page on Auto Reload:** **vypnuto** (aby se po timeoutu vracelo na Start URL — naši nabídku — a ne jen obnovilo aktuální cizí stránku)
   - volitelně **Skip Auto Reload if Showing the Start URL:** zapnuto
   
   → Tohle řeší návrat na hlavní nabídku: návštěvník klikne na dlaždici (VINOTRH.CZ, LAHOFER…), prohlíží si cizí web, po 30–60 s nečinnosti appka sama naskočí zpátky na naši vstupní obrazovku. **Ověřeno funkční.**
5. Zopakovat pro panel 2 a 3, jakmile budou fyzicky zapojené (appka umí export/import nastavení mezi zařízeními, nemusí se klikat ručně 3×).

### Fallback — Huidu cloud (led-cloud.com), pokud by sideload APK na jiném panelu nešel

Bezplatná cloud platforma XiaoHui Cloud (Huidu) — cluster management pro víc panelů, editor programů má widget "web page/HTML". Riziko: takové widgety bývají omezený/screenshot-based webview.

1. Menu (4× klik na displej) → **Networking and Bluetooth** → ověřit Wi-Fi.
2. **System setting** → **System Mode** → **Cloud networking mode**.
3. Server adresa: `led-cloud.com`, zadat uživatelské jméno účtu (založit na `https://led-cloud.com/`).
4. V cloudu: **Program → LCD Program** → widget **web page/HTML** → URL naší stránky → **Send → Complete Update**.

## Nasazení `index.html`

Živé: **https://tv-enoteka.vercel.app/** (GitHub `ssatek/tv-enoteka` → Vercel auto-deploy na push do `main`).

## Otevřené body

- [ ] **Kiosk Mode zůstává vypnutý** — při zapnutí docházelo ke konfliktu s nativním launcherem MagicPlayer (appka ho opakovaně blokovala → blikání "Blocked Magic Player"). Řešení: nejdřív nastavit Fully Kiosk jako výchozí Home/launcher zařízení (v appce sekce "Motion & Launcher" → "Set as Home/Device Launcher"), pak teprve zapnout Kiosk Mode.
- [ ] Fyzická orientace panelu (na výšku/na šířku) — displej hlásí 90° otočení, potvrdit na místě a případně upravit CSS layout dlaždic v `index.html`.
- [ ] Přesná lokace jednotlivých panelů v Enotéce (pro tabulku výše).
- [ ] Panely 2 a 3 — fyzicky zapojit, zopakovat instalaci/nastavení, doplnit Device ID/IP do tabulky.
- [x] Nasazení kiosk stránky na Vercel — hotovo.
- [x] Instalace a základní funkčnost Fully Kiosk Browser na panelu 1 — hotovo.
- [x] Automatický návrat na vstupní nabídku po nečinnosti — hotovo (Auto Reload on Idle).
- [x] Krok zpět uvnitř prohlíženého webu (detail → seznam) — hotovo (Show Action Bar + Show Back Button).
