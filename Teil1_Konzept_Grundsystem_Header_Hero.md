# Design-Briefing Lindner Architekturberatung, Teil 1 von 3
Inhalt: Designkonzept, Grundsystem (Farben, Schrift, Raster), Header, Hero, Buttons.
Teil 2: Über mich, Leistungen, Portfolio, Arbeitsweise. Teil 3: Kontakt, Footer, JavaScript-Verhalten, Responsive, Barrierefreiheit, Abnahme-Checkliste.

Zweck: Die Seite soll im Aussehen und Verhalten exakt dem Original entsprechen. Die erstellte Datei darf beliebig groß werden. Texte (DE und EN) kommen aus der kompakten Version (Lindner_Architekturberatung_kompakt.html), die als inhaltliche Basis dient. Dieses Briefing beschreibt alles Gestalterische, das dort fehlt, und legt zusätzlich die Gesamtidee fest.
Arbeitsauftrag an den Coder: Ein einzelnes HTML-File, kein Framework, kein Build-Schritt, reines HTML, CSS und etwas Vanilla-JS. Poppins per Google-Fonts-Link laden (400, 500, 600, 700), das Hero-Bild als separate Datei hero_blueprint.webp einbinden.

## 1. Gestalterische Gesamtidee

Die Seite ist ein Planblatt. Ein unabhängiger Architekt berät, also darf die Seite wie ein sauber gezeichneter Bauplan wirken: Raster, Maßlinien, ein Planstempel (Titleblock) unter dem Hero, Mono-artige Beschriftungen in Großbuchstaben, dünne Linien. Rot ist die einzige Akzentfarbe und verhält sich wie der Rotstift im Plan: Es markiert, was gerade wichtig oder aktiv ist (Hover, Fokus, Fortschritt, aktive Navigation), nie flächig dekorativ.

Grundhaltung: streng, präzise, ruhig, fachlich. Viel Weißraum, große Typografie, kein Schnickschnack.
- Keine abgerundeten Ecken (überall Radius 0), keine Schatten, keine Verläufe als Flächenfüllung. Verläufe kommen nur als Masken vor.
- Linien statt Flächen: 1px für Struktur, 2px für Akzente und Hover-Linien, 3px für Fortschritt und Hervorhebung.
- Hell und dunkel wechseln sich ab: Hero dunkel, Inhalt hell, Portfolio dunkel, Footer dunkel. Dadurch entsteht Rhythmus beim Scrollen.
- Alle Dark-Flächen tragen ein feines Raster (56px) wie Millimeterpapier.

Bewegungsprinzip: Die Seite "zeichnet sich". Linien ziehen von links nach rechts auf (transform scaleX mit transform-origin left), Inhalte steigen 26 bis 28px auf und blenden ein, Zeichnungen werden von links aufgedeckt. Immer dieselbe Kurve cubic-bezier(.2,.7,.2,1): schneller Start, weiches Ausrollen. Hover-Effekte dauern .3 bis .55s, Eintritte .9 bis 1.5s. Nichts blinkt, pulsiert oder läuft in Schleife. Bewegung hilft bei der Orientierung und ist nie Selbstzweck.

Interaktionsprinzip: Bei Hover und Fokus erscheint Rot (Linie, Füllung, Pfeil, Titelfarbe). Der Rest der Oberfläche bleibt neutral.

## 2. Farben (CSS-Variablen)

```css
:root{
 --bg:#fff; --surface:#f5f4f4; --fg:#2a2829; --strong:#000; --muted:#6a6667; --line:#dcd9da;
 --accent:#d10000; --accent-dark:#960000; --accent-text:#d10000; --accent-hi:#ff5a5a; --on-accent:#fff;
 --hero-bg:#2a2829; --hero-fg:#fff; --hero-muted:#cbc7c8; --hero-grid:#373435; --hero-line:#5e5a5b; --hero-surface:#322f30;
 --top-bg:var(--hero-bg); --top-fg:var(--hero-fg); --top-muted:var(--hero-muted); --top-line:var(--hero-line);
 --ease:cubic-bezier(.2,.7,.2,1);
 --font-display:"Poppins","Helvetica Neue",Arial,sans-serif; --font-body:var(--font-display); --font-mono:var(--font-display);
 --gl:max(16px,env(safe-area-inset-left,0px)); --gr:max(16px,env(safe-area-inset-right,0px));
}
@media(prefers-color-scheme:dark){:root:not([data-theme=light]){--bg:#000;--surface:#161415;--fg:#e9e7e7;--strong:#fff;--muted:#a29d9e;--line:#2f2c2d;--accent-text:#ff5a5a;color-scheme:dark}}
:root[data-theme=dark]{/* gleiche Werte wie oben */}
html.past-hero{--top-bg:var(--bg);--top-fg:var(--strong);--top-muted:var(--muted);--top-line:var(--line)}
.dark{--fg:#fff;--strong:#fff;--muted:var(--hero-muted);--line:var(--hero-line);--surface:var(--hero-surface);--accent-text:var(--accent-hi);background:var(--hero-bg);color:var(--fg)}
```
Regel: Rot auf hellem Grund ist #d10000 (--accent-text), auf dunklem Grund das hellere #ff5a5a (--accent-hi), damit der Kontrast stimmt. Flächen in Rot (Button, Logo-Mark) bleiben #d10000. Der .dark-Scope schaltet die Variablen für Sektionen um, so reagieren alle Kindelemente automatisch.

## 3. Typografie

Eine Schriftfamilie für alles: Poppins, Fallback "Helvetica Neue", Arial, sans-serif. Gewichte 400 (Fließtext), 500 (Labels, Nav, Werte), 600 (Überschriften), 700 (H1, Logo).
- Body: 16px, line-height 1.7, -webkit-font-smoothing antialiased.
- H1: clamp(2.3rem, 6.2vw, 4.7rem), 700, letter-spacing -0.035em, line-height 1.06, max-width 13ch.
- H2: clamp(2rem, 4.4vw, 3.3rem), 600, max-width 18ch. H1-H3 allgemein: letter-spacing -0.02em, line-height 1.15, text-wrap balance.
- H3 in Listen und Karten: 1.28rem (Schritte 1.18rem), letter-spacing -0.015em.
- Lead (Hero): clamp(1rem, 1.7vw, 1.12rem), max-width 46ch.
- Mono-Label (Klasse .mono): 500, 0.72rem, letter-spacing .1em, uppercase, Farbe --muted. Für Sektionslabels, Meta-Zeilen, Formularlabels, Planstempel-Titel. Obwohl "Mono" genannt, ist es ebenfalls Poppins. Der Charakter entsteht nur durch Versalien und Sperrung.
- Zahlen: tabular-nums, damit das Hochzählen nicht springt.
- Selektion, Fokus: :focus-visible { outline: 2px solid var(--accent-text); outline-offset: 3px }, im Hero --accent-hi.

## 4. Layout-System

- Container .wrap: width 100%, max-width 1120px, margin-inline auto. Seitenränder kommen vom body (padding-left var(--gl), padding-right var(--gr)).
- .bleed: für dunkle Vollbreite-Sektionen. margin-left calc(-1 * var(--gl)), margin-right calc(-1 * var(--gr)), padding-left var(--gl), padding-right var(--gr). Der Hintergrund läuft so bis zum Rand, der Inhalt bleibt im Container.
- Sektionen (.sheet) haben ein Zweispalten-Raster: schmale Labelspalte (max 200px) und Inhaltsspalte. Details in Teil 2.
- html: scroll-behavior smooth, scroll-padding-top 4.5rem (Anker landen nicht unter dem Header). box-sizing border-box überall.

## 5. Header (sticky, 4rem hoch)

Aufbau: Logo links, Navigation rechts (Links und Sprachumschalter), darunter absolut positioniert der Fortschrittsbalken.
- Position sticky, top env(safe-area-inset-top), z-index 40, Hintergrund var(--top-bg), Text var(--top-fg), border-bottom 1px var(--top-line). Transition .35s auf background, border-color, color.
- Farbwechsel: Der Header ist über dem Hero dunkel und wird hell, sobald der Hero verlassen ist (Klasse past-hero auf html, siehe Teil 3). Das Umschalten ist ein weicher Crossfade und dient als Signal "du bist jetzt im Inhalt".
- Zusatz für iOS: body::before, position fixed, inset 0 0 auto, Höhe env(safe-area-inset-top), z-index 30, Hintergrund var(--top-bg), Transition mit. So passt die Statusleiste zur Headerfarbe.
- Logo (.brand): inline-flex, gap .7rem, 700, 1.02rem, kein Unterstrich. Text "Lindner" in Normalfarbe, "Architekturberatung" (span.sub) in --top-muted und 500. Davor die Marke: Quadrat 1.4rem x 1.4rem in --accent, darin per ::after ein weißer L-Winkel (inset .32rem, border-left und border-bottom je 2px weiß). Hover auf dem Logo dreht die Marke um 90 Grad (.5s ease).
- Nav-Links: 0.9rem, 500, Farbe --top-muted, Abstand 1.8rem. Beim Hover und im aktiven Zustand Farbe --top-fg und ein 2px Strich in --accent-hi unter dem Text (::after, scaleX 0 auf 1, origin left, .35s). Der aktive Link wird per Scroll-Spy gesetzt.
- Sprachumschalter: zwei Buttons "DE" und "EN" in gemeinsamer 1px Umrandung (--top-line), Schrift 0.72rem/500, letter-spacing .1em, Padding .4rem .65rem. Aktiver Button ist invertiert (Hintergrund --top-fg, Text --top-bg).
- Fortschrittsbalken: absolut an der Unterkante (bottom -1px), 2px hoch, --accent-hi, transform scaleX(var(--p)) mit origin left. Zeigt den Scrollfortschritt der ganzen Seite.
- Responsive: unter 760px verschwinden die Nav-Links, Logo und Umschalter bleiben. Unter 480px entfällt der Zusatz "Architekturberatung" im Logo.

## 6. Hero

Dunkles Planblatt mit Raster. Links Text, rechts ein im Hintergrund verblassender perspektivischer Grundriss, unten der Planstempel.

Hintergrund und Raster:
```css
.hero{position:relative;isolation:isolate;overflow-x:clip;background-color:var(--hero-bg);color:var(--hero-fg);
 --gpos:-1px calc(-1px + var(--sy,0)*0.12px);
 background-image:linear-gradient(var(--hero-grid) 1px,transparent 1px),linear-gradient(90deg,var(--hero-grid) 1px,transparent 1px);
 background-size:56px 56px;background-position:var(--gpos);
 padding-top:clamp(3rem,7vw,5.5rem);padding-bottom:clamp(3rem,6vw,5rem)}
```
Das Raster verschiebt sich beim Scrollen minimal (0.12 px pro Scroll-Pixel), das gibt Tiefe. --sy wird per JS auf html gesetzt (min(scrollY, Hero-Höhe)).

Maus-Glow: Über dem Raster liegt eine zweite, rote Rasterebene, die nur in einem Kreis um den Cursor sichtbar ist. Dadurch leuchtet das Millimeterpapier unter dem Zeiger rot auf, wie ein Rotstift-Hinweis.
```css
.hero::before,.glow::before{content:"";position:absolute;inset:0;z-index:-1;pointer-events:none;
 background-image:linear-gradient(var(--accent-hi) 1px,transparent 1px),linear-gradient(90deg,var(--accent-hi) 1px,transparent 1px);
 background-size:56px 56px;background-position:var(--gpos);opacity:.55;
 -webkit-mask-image:radial-gradient(220px circle at var(--mx,-999px) var(--my,-999px),#000,transparent 72%);
 mask-image:radial-gradient(220px circle at var(--mx,-999px) var(--my,-999px),#000,transparent 72%)}
.glow::before{opacity:.4}
```
--mx und --my setzt JS beim pointermove (Teil 3). Nur auf Geräten mit Hover und ohne reduced-motion.

Layout: .hero-grid mit grid-template-columns minmax(0,1.12fr) minmax(0,1fr), gap 2.5rem clamp(1.5rem,3vw,3rem), align-items end. Der Textblock hat unten Padding clamp(1rem,3vw,2.5rem).
- Eyebrow (Mono, Farbe --accent-hi): inline-flex, gap .8rem, davor eine 2.2rem lange 1px Linie in --accent-hi.
- H1 mit margin-top 1.1rem, Lead mit margin-top 1.6rem, Button-Zeile (.cta-row) margin-top 2.2rem, flex, wrap, gap .8rem. Unter 560px füllen die Buttons die volle Breite.

Einblend-Animation "rise": Jedes Hero-Element steigt auf.
```css
@keyframes rise{from{opacity:0;transform:translateY(26px)}to{opacity:1;transform:none}}
.rise{animation:rise 1s var(--ease) both;animation-delay:calc(var(--d,0)*1ms)}
```
Staffelung per Inline-Style --d: Eyebrow 100, H1 220, Lead 360, Buttons 500, Blueprint-Block 300, Planstempel 650 (Millisekunden). Ergebnis: ein ruhiger Aufbau von oben nach unten, rechts leicht versetzt.

Blueprint-Bild (hero_blueprint.webp, 1200x730, weiße Linien mit roten Akzenten, transparenter Hintergrund, perspektivischer Grundriss mit Maßketten):
```css
.plan{display:block;width:100%;height:auto;opacity:var(--po,.8);user-select:none;pointer-events:none;
 -webkit-mask-image:linear-gradient(to bottom,transparent 0,#000 26%,#000 78%,transparent 100%),linear-gradient(to right,transparent 0,#000 20%,#000 76%,transparent 100%);
 -webkit-mask-composite:source-in;
 mask-image:linear-gradient(to bottom,transparent 0,#000 26%,#000 78%,transparent 100%),linear-gradient(to right,transparent 0,#000 20%,#000 76%,transparent 100%);
 mask-composite:intersect;animation:planin 1.8s var(--ease) .5s both}
@keyframes planin{from{opacity:0;transform:scale(1.05);filter:blur(6px)}to{opacity:var(--po,.8);transform:none;filter:blur(0)}}
@media(min-width:901px){.plan{max-height:360px;width:auto;max-width:100%;margin-left:auto}}
.par{transform:translateY(calc(var(--sy,0)*-0.06px))}  /* Parallax, Wrapper um das Bild */
```
Das Bild fadet an allen vier Rändern weich aus (zwei Masken überlagert), es scheint aus dem Raster aufzutauchen. Eintritt: Es "fokussiert" sich aus Unschärfe und leichter Vergrößerung. Wrapper .blueprint hat min-width 0 und align-self end.
Mobil (unter 900px): Hero einspaltig. Das Bild liegt absolut hinter dem Text (.blueprint: position absolute, top 7rem, left 0, right 0, z-index -1, pointer-events none), Breite 125%, margin-left -2%, --po .3 (sehr dezent). So bleibt der Text lesbar und das Planblatt-Gefühl erhalten.

Planstempel (.titleblock): Vier Zellen mit Kurzprofil (Tätigkeit, Region, Leistungsbild, Sprachen), wie das Schriftfeld rechts unten auf einem Bauplan.
- display grid, 4 gleiche Spalten, margin-top clamp(2.5rem,5vw,4rem), border 1px var(--hero-line), transparenter Hintergrund.
- Zelle: padding .9rem 1.1rem 1rem, border-left 1px var(--hero-line) (erste Zelle ohne). Oben das Mono-Label, darunter der Wert (b: display block, 500, 0.93rem, margin-top .2rem, overflow-wrap anywhere).
- Unter 760px zwei Spalten: 3. Zelle ohne border-left, Zellen ab der dritten mit border-top 1px.

## 7. Buttons (.btn)

Eckig, Versalien, mit Pfeil und einer Füllung, die beim Hover von links hereinfährt.
```css
.btn{position:relative;isolation:isolate;overflow:hidden;display:inline-flex;align-items:center;justify-content:space-between;gap:1.2rem;
 min-height:3.5rem;padding:0 1.5rem;text-decoration:none;cursor:pointer;font:600 .78rem var(--font-body);letter-spacing:.12em;text-transform:uppercase;
 border:1px solid var(--accent);transition:color .4s var(--ease),border-color .4s var(--ease)}
.btn::before{content:"";position:absolute;inset:0;z-index:-1;transform:translateX(-101%);transition:transform .55s var(--ease)}
.btn::after{content:"";flex:none;width:1.5rem;height:.75rem;background:currentColor;
 -webkit-mask:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 12'><path d='M0 6h22M17 1l5 5-5 5' fill='none' stroke='black' stroke-width='1.5'/></svg>") center/contain no-repeat;
 mask:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 12'><path d='M0 6h22M17 1l5 5-5 5' fill='none' stroke='black' stroke-width='1.5'/></svg>") center/contain no-repeat;
 transition:transform .45s var(--ease)}
.btn:hover::before{transform:none}.btn:hover::after{transform:translateX(6px)}
.btn.primary{background:var(--accent);color:var(--on-accent)} .btn.primary::before{background:var(--accent-dark)} .btn.primary:hover{border-color:var(--accent-dark)}
.btn.ghost{background:transparent;color:var(--hero-fg);border-color:var(--hero-muted)} .btn.ghost::before{background:var(--hero-fg)} .btn.ghost:hover{color:var(--hero-bg);border-color:var(--hero-fg)}
```
Primary = roter Block, beim Hover wird er dunkelrot (Füllung fährt ein). Ghost = nur Umrandung, beim Hover füllt sich der Button weiß und der Text wird anthrazit. In beiden Fällen schiebt sich der Pfeil 6px nach rechts. Dieselbe Pfeil-Maske wird später bei den Leistungszeilen verwendet.
