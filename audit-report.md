# Latteria Sociale di Fondo — Site Audit

**Client:** Latteria Sociale di Fondo — Società Cooperativa Agricola
**URL:** https://www.latteriasocialefondo.it
**Date:** April 2026
**Auditor:** Flareo

---

## 1. Executive summary

Latteria Sociale di Fondo is one of the oldest cheese cooperatives in the Val di Non (Trentino), founded in **1845**. The cooperative gathers **40 member farms** and **10 employees**, produces Trentingrana, Nostrano, Caciotte, Smeraldo, Ciasolot, Monte Mais Kofel (aged inside a WWI/WWII bunker), yogurt, panna cotta, butter, cream, and also runs two retail shops (Fondo and Revò/Novella).

The brand has **extraordinary editorial material**: 180 years of heritage, a unique bunker-aged cheese, a protected alpine territory (Dolomiti di Brenta UNESCO buffer), a genuine cooperative story, and traceable farm-to-form ingredients.

The current website **wastes all of it**. It is a dated PHP build (jQuery 2.2.4, Vegas slider plugin, lazyload shim, no mobile nav, no srcset, thin shop pages, a PHP form with a manual "security code" captcha, encoding bugs on product pages, a cookie banner that covers a quarter of the viewport, and no meaningful SEO structure).

This is exactly the kind of asset Flareo can transform: great content, terrible container.

---

## 2. Complete page inventory

### 2.1 Italian site (9 pages)

| Path | Title | Purpose |
|---|---|---|
| `/index.php` | Latteria Sociale di Fondo Val di Non \| Formaggio Trentino | Home — heritage, hero slider, product categories, shop, territory |
| `/latteria.php` | La nostra storia | 1845 origins, 40 soci, 10 dipendenti, 2000 rebuild |
| `/prodotti.php` | I nostri formaggi | Product catalogue grid + values + home delivery CTA |
| `/prodotti-formaggio-monte-mais-kofel.php` | Monte Mais Kofel | Editorial page on the bunker-aged signature cheese |
| `/territorio.php` | Gusto e tradizione in Valle di Non | Editorial page on the valley, Dolomiti di Brenta UNESCO, Maddalene, Paganella |
| `/contatti.php` | Contatti | Two locations, opening hours, contact form |
| `/psr-2014-2022.php` | P.S.R. 2014-2022 | EU rural development fund disclosure (€50,410 investment) |
| `/informativa-privacy.php` | Privacy | Privacy policy |
| `/media/pdf/informativa-clienti.pdf` | — | Customer privacy PDF |
| `/media/pdf/informativa-fornitori.pdf` | — | Supplier privacy PDF |

### 2.2 German site (5 pages — full mirror of main navigation)

| Path | Title |
|---|---|
| `/de/index.php` | Latteria Sociale di Fondo Val di Non \| Trentiner Käse |
| `/de/molkerei.php` | Unsere Geschichte |
| `/de/produkte.php` | Unsere Käse |
| `/de/das-territorium.php` | Geschmack und Tradition im Val di Non |
| `/de/kontakte.php` | Kontakte |
| `/de/informationsschreiben-zum-datenschutz.php` | Datenschutz |

The German build only covers the top-level pages — it does NOT link to the 24 individual product pages nor to Monte Mais Kofel. **Gap:** German-speaking visitors (Südtirol, Germany, Austria — a huge market for Trentino cheese) get no product depth.

### 2.3 Shop / product detail pages (24 pages under `/negozio/...`)

All single-product pages live under four category folders:

**Formaggio (10):** fontal, ciaselot, caprino-fresco-di-fondo, smeraldo, formaggio-nostrano-vecchio, formaggio-nostrano-fresco, formaggio-nostrano-mezzano, formaggio-fuso-da-fon, monte-mais-kofel, (plus the top-level monte-mais editorial).

**Caciotta (9):** erba-cipollina, ai-fiori, al-pepe, alle-noci, al-cumino, al-peperoncino, bianca, senza-lattosio, capricciotta.

**Yogurt (2):** yogurt-da-fon-1-kg, yogurt-da-fon-150-g.

**Panna (1):** panna-cotta-da-fon.

**Shop content quality:** extremely thin. Each detail page is templated with the cooperative's boilerplate tagline, sometimes a single line of ingredients ("latte pastorizzato, sale, caglio, erba cipollina, fermenti selezionati"), and a "Ti potrebbe interessare" grid. No prices, no pack sizes (beyond the name), no add-to-cart, no stock status, no photography beyond one hero image. **There is no real e-commerce** — `/negozio/` redirects to the homepage, and there is no cart, no checkout, no payment. It is a glorified catalogue pretending to be a shop.

### 2.4 Totals

- **38 unique pages** in Italian
- **6 pages** in German
- **2 PDF documents** (customer + supplier privacy)
- Menu items (desktop): 5 primary nav + 1 language switcher (DE flag only, no IT flag because IT is default)
- No dedicated search, blog, recipes, where-to-buy, press, or careers section

---

## 3. Navigation, menu and interactive elements

### 3.1 Primary navigation

`Home · La Latteria · I prodotti · Il territorio · Contatti`

- Desktop: horizontal top-bar, class `top-bar top-bar--style-1` + `navigation text-lg-right`
- There is a `has-submenu` LI under "I prodotti" with a single child pointing to Monte Mais Kofel. Everything else lives at root level.
- **No mobile hamburger menu is actually rendered.** The markup contains `top-bar__navigation-toggler` but `mobileMenuPresent = false` at runtime — the toggler is hidden and no alternative mobile nav is shown. This is a serious mobile usability problem.
- The meta viewport is `initial-scale=1.0, user-scalable=no` — blocks pinch-zoom, violates WCAG 1.4.4.

### 3.2 Language switcher

A single Italian flag linking to `/de/index.php` (alt text "bandiera tedesca", which is wrong — the flag is Italian if shown on DE, German on IT). When on the DE pages, there is no visible IT flag to go back (confirmed: `langSwitcher` array returns just one entry per page).

### 3.3 CTAs across the site

Every non-shop page ends with the same CTA block:

> "Vuoi informazioni sui nostri prodotti, sulle consegne, sulla nostra latteria? Contattaci senza impegno."

plus the contact form page is the only action target. There is no newsletter, no WhatsApp, no "order online", no "visit the shop", no "book a tour" — despite the La Latteria page saying "Vieni alla Latteria Sociale di Fondo per una visita al caseificio."

### 3.4 Contact form (`/contatti.php`)

`POST` to `/contatti.php`. Fields:

```
hidden  invia
text    name         (placeholder "Nome")
text    email        (placeholder "E-mail")
textarea message     (placeholder "Messaggio")
text    security_code (required, placeholder "Security code *")
checkbox privacy
submit  Invia
```

Issues:
- Name, email, message are NOT required at the HTML level (the asterisks on-page lie)
- Privacy checkbox is NOT required at the HTML level
- `email` field is `type="text"` not `type="email"` — no validation, no mobile keyboard
- "security_code" is a manual text captcha, no reCAPTCHA v3 / hCaptcha / honeypot — easy to spam
- No success state, no AJAX, form reloads the entire page
- No `tel` field, no company field (despite the B2B mention of hotels/restaurants)

### 3.5 Footer (`.footer--style-1`)

```
LATTERIA SOCIALE DI FONDO Società Cooperativa Agricola
tel. +39 0463.831294
info@latteriasocialefondo.it
Privacy policy

Via 4 Novembre, 70 · 38013 Borgo d'Anaunia · Fraz. Fondo (TN)
Mon–Sat: 8.00–12.30 / 15.30–19.00
Sun: 9.00–12.30 / 15.30–19.00

P.zza della Madonna Pellegrina, 1 · 38023 Novella · Fraz. Revò (TN)
Mon–Thu: 8.30–12.30
Fri–Sat: 8.30–12.30 / 16.00–19.00
Sun: closed

INFORMATIVE PRIVACY
Informativa clienti (PDF)
Informativa fornitori (PDF)

Home · La Latteria · I prodotti · Il territorio · Contatti

© 2026 Latteria Sociale Fondo. Powered by Nitida Immagine
```

### 3.6 Social presence

**Only one link in the entire site:** Facebook page `Latteria-sociale-di-Fondo-102600158291866`. No Instagram, no YouTube, no TikTok, no LinkedIn, no Trip advisor, no Google Maps embed verified. For a food brand selling to tourists and Südtirol residents this is a **huge** miss.

### 3.7 Cookie banner

CookieYes (`cdn-cookieyes.com/client_data/6d54389b1dd89adaede20bd0/`), full-width bottom sheet with six category toggles. Extremely verbose Italian copy. It is the first thing a visitor sees, covers 25–30% of the hero on mobile, and its "submit" buttons are the majority of clickable buttons counted on every page (explains the 15–16 buttons/page figure).

---

## 4. Technology stack

### 4.1 Frontend

| Asset | Detail |
|---|---|
| Framework | Server-rendered PHP, no SPA |
| jQuery | `ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js` (2016-05, EOL) |
| Hero slider | **Vegas** (jQuery plugin, `/css/vegas-styles.css`) — 9 vegas-class nodes on home |
| Lazyload | `vanilla-lazyload@10.19.0` via jsdelivr (1x1 blank.gif placeholders) |
| Fonts | Google Fonts Open Sans (300–800) + Raleway (100–900, +italics) — **13 weight/style variants**, hundreds of KB |
| CSS | `critical.min.css`, `vegas-styles.css`, `style.min.css`, `custom.css` — four separate files, no bundling strategy |
| JS | `device.min.js`, `main.min.js`, webfont loader, jQuery, lazyload |

### 4.2 Analytics / tag

- Google Analytics classic (`analytics.js`)
- GTM container `GTM-W2TF583K`
- GA4 `G-YSM5LRMNRG`
- CookieYes client `6d54389b1dd89adaede20bd0`

### 4.3 Current agency

"Powered by Nitida Immagine" in the footer — `nitidaimmagine.it`, a small Trento-area web studio. No proprietary CMS flagged; the site behaves as a hand-coded PHP template per page.

### 4.4 Branding tokens pulled from `<head>`

- Theme color: **#0086b9** (mid-cyan / turquoise-blue)
- `msapplication-navbutton-color`: #0086b9
- Apple status bar: #0086b9

### 4.5 Image strategy

All 27 images on the home are lazy `<img src="blank.gif">` with the real file in `data-src`. No `srcset`, no `<picture>`, no WebP/AVIF, no width/height attributes → **CLS catastrophe** and oversized JPEG delivery on mobile.

Hero backgrounds use CSS `background-image` with files like `latteria-sociale-fondo-troggle-menu.jpg` and `latteria-sociale-fondo-drone.jpg` (drone footage of the latteria — useful asset we should re-use).

Catalogue image filenames we captured:

```
ico-formaggio.png / ico-burro.png / ico-yogurt.png / ico-latte.png / ico-panna.png
caciotta.jpg / formaggi-nostrani.jpg / yogurt.jpg / pannacotta.jpg
milk.png / bottiglie-di-latte.png
latteria-fondo-100-anni.png / latteria-fondo-soci.png / latteria-fondo-dipendenti2.png
territtorio-val-di-non.jpg / vendita-formaggi-val-di-non-trentino.jpg / prodotti-tipici-trentini.jpg
logo-latteria-sociale-fondo-footer.png
```

Note the typo `territtorio-val-di-non.jpg` — stays in production. Typical sign of "ship and forget."

---

## 5. Content deep-dive (usable copy for the rebuild)

### 5.1 Home — the story in the brand's own words

> "Una delle più antiche società di caseificio della Val di Non, in Trentino. La nostra latteria sociale nasce nel 1845 da una cultura autentica, ricca di valori e tradizione."

> "Da oltre un secolo lavoriamo con cura e passione il latte raccolto esclusivamente nelle stalle e nei masi dei nostri soci allevatori e seguiamo con attenzione ogni aspetto della produzione del miglior formaggio del Trentino."

> "Qualità, tradizione, innovazione e sostenibilità sono i valori a cui ci ispiriamo per rendere unici i nostri prodotti lattiero caseari."

> "Dai verdi pascoli ricchi di erbe aromatiche e fiori profumati e dalla antica pratica della monticazione uniti all'amore e alla cura per il prodotto nascono i nostri formaggi. La mano sapiente dei nostri casari sa lavorare con maestria il latte vaccino o di capra fino a trasformarlo in un prodotto d'assoluta eccellenza."

**Taglines we can lift:**
- "Il formaggio trentino dal sapore di montagna"
- "Dal 1845 la latteria sociale di Fondo produce con sapienza e maestria un formaggio trentino di primissima qualità."
- "Casari di generazioni"

### 5.2 La Latteria (history page)

- "Casari per passione. Dal 1845 siamo produttori di formaggi di alta qualità ed eccellenza."
- Key numbers: **40 aziende agricole** conferring milk twice a day, **10 employees**
- In 2000 the cooperative built a modern caseificio: salatura, stagionatura, cold rooms, retail shop
- "Vieni alla Latteria Sociale di Fondo per una visita al caseificio" — there is a visitor program, but NO booking UI anywhere on the site

### 5.3 Il territorio

> "Perché i formaggi della latteria Sociale di Fondo sono così buoni? Semplice, perché nascono nel cuore verde della Valle di Non, in Trentino. Un territorio di rara bellezza, circondato dalle Alpi, protetto dalla catena montuosa delle Maddalene, dal massiccio della Paganella e dalle Dolomiti di Brenta, oggi Patrimonio dell'Umanità UNESCO."

**UNESCO + Dolomiti di Brenta + Maddalene + Paganella** — four incredibly strong hooks for a hero/values page.

### 5.4 Monte Mais Kofel — the story to lead with

This is the page I would rebuild first. In the brand's own words:

> "Il formaggio 'Monte Mais Kofel', prodotto dalla Latteria Sociale di Fondo, in Alta Val di Non, viene ora stagionato nel Gampen Bunker a Passo Palade, opera bellica risalente al periodo tra la Prima e la Seconda guerra mondiale."

> "Dopo un primo periodo di stagionatura nelle sale del caseificio della latteria che va dai 30 ai 40 giorni, durante il quale si forma la crosta superficiale, le forme sono trasferite nel bunker. Qui, in una sala dedicata, vengono messe a riposo sulle scalere in legno dove stagionano per circa 2 mesi."

> "La costruzione si interruppe quando era già a buon punto e per molti anni è stata dimenticata. Dal 2010 il luogo è tornato finalmente visitabile."

Opening hours are also listed: "da maggio alla fine di ottobre dal lunedì alla domenica dalle 10 alle 17.00. Vengono forniti elmetto e torcia perché non c'è la corrente elettrica."

**This is a hero-grade editorial story the current site buries inside a 27-image page with a cookie banner on top.** It is the Flareo angle.

### 5.5 P.S.R. 2014-2022

EU rural development funding disclosure, Focus Area 3A. Investment €50,410 (EU quota €8,666.49 + State €8,048.26 + PAT €3,449.25). Used to buy a form lifter, brush machine, 4-column lifter. Legally required content but needs to live in the footer, not as a top-level page.

---

## 6. SEO posture

- Title tags: present, reasonable, but stuffed with "Latteria Sociale di Fondo" prefix on every H1 (anti-pattern)
- Meta descriptions: present on most pages, repetitive and flat
- `lang="it"` is correct
- No hreflang pair for IT/DE — Google will not treat `/de/*` as a translation
- No Open Graph / Twitter card meta captured
- No structured data (no `LocalBusiness`, `Organization`, `Product`, `Recipe`, `FAQ`, `Place`)
- Sitemap: not verified, likely absent
- `user-scalable=no` + no mobile nav = mobile usability penalty in Google Search Console

---

## 7. Accessibility audit (quick pass)

- `user-scalable=no` blocks pinch-zoom — WCAG 1.4.4 fail
- Images use empty/placeholder `alt` like "blank.gif" placeholders with decorative alts that don't match the real image
- No visible skip-link
- Form fields have no `<label>` (placeholders only) — WCAG 3.3.2
- Contact form captcha is a plain text field, inaccessible for screen readers
- Cookie banner traps focus
- Language switcher is a flag icon only, no text — WCAG 3.1.2 fail
- Color contrast on `#0086b9` text over hero images not verified but likely poor

---

## 8. Performance (observed/estimated)

- 4 CSS files + Google Fonts with 13 weight/style variants (Open Sans + Raleway)
- 11 JS bundles loaded, including jQuery 2.2.4 from Google CDN and a separate device.min.js + main.min.js
- Every image lazy with blank.gif → FCP OK, LCP hurt because hero is a CSS background image not prioritized
- Home page `scrollHeight` on desktop: **7401 px** — 5+ full viewports. Way too much, users never reach the fold that matters.
- No HTTP/2 push, no preconnect, no priority hints

---

## 9. Content gaps & business-opportunity gaps

1. **No real e-commerce.** 24 "shop" pages with no add-to-cart, no price, no shipping info. Losing direct-to-consumer revenue.
2. **No "visit us" booking.** Caseificio tours mentioned but no UI.
3. **No shop finder / map.** Two physical locations, one of them not even shown on the map on the home.
4. **No recipes / food pairing.** Huge content gap for a cheese brand.
5. **No press / media kit.** Cooperative sense of heritage with nothing to send to journalists.
6. **No newsletter.** Zero owned audience.
7. **No Instagram.** Mandatory for food/territory storytelling.
8. **No English version.** German is covered, English isn't — Germans often read EN too, and the Dolomiti UNESCO audience is international.
9. **Monte Mais Kofel is buried.** The best story the brand has gets one page nobody finds.
10. **No traceability page.** "40 soci, stalle e masi, filiera corta" — prime ingredients for a traceability / farmers page.
11. **No sustainability page** despite sustainability being one of the four stated values.
12. **No wholesale/HORECA form** despite B2B mention ("alberghi, bar e ristoranti").
13. **No gift cards / gift boxes** despite being a perfect souvenir for Val di Non tourists.

---

## 10. Bugs and broken things

- **UTF-8 mojibake** on product detail pages — "caff�, mirtillo, miele..." on the yogurt page (should be "caffè"). Encoding bug in the PHP template.
- **Alt text contradiction** on language switcher ("bandiera tedesca" shown as the Italian flag).
- **`territtorio` typo** in image filename `territtorio-val-di-non.jpg`.
- **`/negozio/` index** redirects to homepage — dead entry point, broken catalog hub.
- **`javascript:void(0);`** used for the products submenu toggle — inaccessible for keyboard users.
- **Mobile menu toggler markup exists but doesn't render** — broken component.
- **Contact form required="false"** on all required fields — server must validate.

---

## 11. Brand assets observed

- Logo: `logo-latteria-sociale-fondo-footer.png` (footer-only, no SVG)
- Brand color: `#0086b9`
- Fonts: Open Sans + Raleway (we should replace with a more editorial serif for the rebuild — e.g. GT Sectra, Noe Display, or Canela for headlines + Neue Haas Unica / Söhne for body)
- Drone footage of the caseificio referenced as `latteria-sociale-fondo-drone.jpg` — prime asset
- Photography style: literal, stock-ish, low resolution, no consistent grading

---

## 12. The Flareo pitch (one sentence)

> "Latteria Sociale di Fondo has 180 years of story, 40 farms, a cheese aged in a forgotten Alpine war bunker, and UNESCO mountains at its door — and a website that buries all of it under a jQuery 2016 slider and a cookie banner. Flareo rebuilds it as an editorial, territory-first experience with real commerce, real bilingual reach, and the Monte Mais Kofel story where it belongs: front and center."

---

## 13. Raw coverage log (what was crawled)

**Italian pages scraped:** home, latteria, prodotti, prodotto-monte-mais-kofel, territorio, contatti, psr, privacy
**German pages scraped:** de_home, de_molkerei, de_produkte, de_territorium, de_kontakte
**Shop product detail samples:** prod_fontal, prod_yogurt, prod_caciotta, prod_panna, prod_nostrano_vecchio
**Total captures in sessionStorage:** 18 pages, ~181 KB of extracted DOM data
**Pages not individually captured** (but URL-mapped): 19 remaining `/negozio/*.html` product pages (templated identically to the 5 samples)
**PDFs indexed but not downloaded:** `/media/pdf/informativa-clienti.pdf`, `/media/pdf/informativa-fornitori.pdf`
