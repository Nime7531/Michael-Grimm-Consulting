# Design-Briefing Lindner Architekturberatung, Teil 2 von 3
Inhalt: Sektionsraster, Über mich, Leistungen, Portfolio mit den vier SVG-Zeichnungen, Arbeitsweise.
Voraussetzung: Teil 1 (Farben, Typografie, Buttons, Variablen wie --ease, --accent-hi, --surface).

## 8. Sektionsraster (.sheet), gilt für alle Inhaltsblöcke

Jede Sektion ist wie ein Blatt aufgebaut: links ein schmaler Streifen mit dem Sektionsnamen, rechts der Inhalt. Dadurch wirkt die Seite wie ein Katalog mit Randspalte.
```css
section.sheet{padding-block:clamp(4rem,9vw,7.5rem);border-bottom:1px solid var(--line)}
.sheet .wrap{display:grid;grid-template-columns:minmax(0,200px) minmax(0,1fr);gap:2rem clamp(1.5rem,5vw,4rem)}
.sheet .wrap>.mono{position:sticky;top:calc(env(safe-area-inset-top,0px) + 6rem);align-self:start;display:inline-flex;align-items:center;gap:.7rem}
.sheet .wrap>.mono::before{content:"";width:1.6rem;height:2px;background:var(--accent-text)}
.sheet h2{font-size:clamp(2rem,4.4vw,3.3rem);font-weight:600;max-width:18ch}
.sheet .body{display:flex;flex-direction:column;gap:3rem;min-width:0}
@media(max-width:860px){.sheet .wrap{grid-template-columns:minmax(0,1fr);gap:1.2rem}.sheet .wrap>.mono{position:static}}
```
- Das Label (Mono, Versalien) hat einen kurzen roten 2px-Strich davor und bleibt beim Scrollen am linken Rand kleben, solange der Inhalt der Sektion läuft. Das ist ein wichtiges Orientierungsmerkmal. Auf Mobil steht das Label oberhalb des Inhalts und scrollt normal.
- H2-Enthüllung: Beim Eintritt in den Viewport wird jede H2 von unten nach oben "aufgedeckt", wie hinter einer Maske hervorgeschoben.
```css
.js .sheet h2{clip-path:inset(0 0 100% 0);transform:translateY(.45em);transition:clip-path 1.1s var(--ease),transform 1.1s var(--ease)}
.js .sheet h2.seen{clip-path:inset(-.25em -.1em -.25em -.1em);transform:none}
```
(Das negative inset verhindert, dass Unterlängen und Anschnitte abgeschnitten werden.) Klasse .seen setzt JS (Teil 3). Die Klasse .js wird nur gesetzt, wenn Animationen laufen dürfen.
- Linien, die sich aufbauen: Die Listen .facts und .services haben oben eine 1px Linie in --strong, die beim Einblenden von links aufzieht.
```css
.facts,.services{position:relative}
.facts::before,.services::before{content:"";position:absolute;top:0;left:0;right:0;height:1px;background:var(--strong);transform-origin:left;transition:transform 1.5s var(--ease)}
.js .facts::before,.js .services::before{transform:scaleX(0)}
.js .facts.seen::before,.js .services.seen::before{transform:none}
```

## 9. Über mich

Zweispaltig: links Text, rechts ein Porträt-Platzhalter. Darunter die drei Kennzahlen.
- .about-grid: grid, Spalten minmax(0,1fr) und minmax(0,300px), gap 2.5rem, align-items start. Unter 860px einspaltig, das Porträt dann max-width 260px.
- Textspalte: flex column, gap 1.2rem, max-width 60ch. Der erste Absatz ist ein Einstieg: 1.12rem, Farbe --strong. Der zweite ist ruhiger in --muted.
- Porträt (.portrait): aspect-ratio 4/5, border 1px --line, Hintergrund --surface, flex mit align-items flex-end und Padding .9rem. Unten links steht die Mono-Beschriftung "Porträt · Platzhalter". Auffälligstes Detail sind die zwei roten Eckwinkel, wie Passermarken oder Eckmarkierungen eines Fotos:
```css
.portrait{position:relative}
.portrait::before,.portrait::after{content:"";position:absolute;width:1.6rem;height:1.6rem;border:2px solid var(--accent-text);pointer-events:none}
.portrait::before{top:-7px;left:-7px;border-right:0;border-bottom:0}
.portrait::after{bottom:-7px;right:-7px;border-left:0;border-top:0}
.portrait svg{position:absolute;inset:0;width:100%;height:100%}
```
Platzhalter-Zeichnung im Porträt (soll bei einem echten Foto ersetzt werden, die Eckwinkel bleiben):
```html
<svg viewBox="0 0 300 375" preserveAspectRatio="xMidYMid slice" aria-hidden="true"><g fill="none" stroke="var(--line)" stroke-width="1">
<path d="M0 300H300M0 330H300M60 0V375M120 0V375M180 0V375M240 0V375"/>
<circle cx="150" cy="140" r="52" stroke="var(--muted)"/><path d="M62 300C62 235 100 205 150 205C200 205 238 235 238 300" stroke="var(--muted)"/></g></svg>
```
- Kennzahlen (.facts): drei Spalten (unter 480px eine Spalte, Zellen mit border-bottom 1px --line und Padding-bottom .9rem). Zelle: padding 1.2rem 1rem 0 0. Zahl: 600, clamp(2rem,4.4vw,3.1rem), letter-spacing -0.035em, line-height 1.1, tabular-nums, Farbe --accent-text. Beschriftung darunter .88rem in --muted. Die Zahlen "15+" und "60+" zählen beim Einblenden hoch (Teil 3), "1–9" steht fest.

## 10. Leistungen

Keine Karten, sondern eine Liste mit großzügigen Zeilen, wie ein Leistungsverzeichnis. Das passt zur Fachlichkeit und lässt sich im Hover elegant betonen.
```css
.services{list-style:none;margin:0;padding:0}
.services li{position:relative;display:grid;grid-template-columns:minmax(0,.9fr) minmax(0,1.1fr) 2.2rem;gap:.4rem 2rem;padding:1.9rem 1.2rem;border-bottom:1px solid var(--line);transition:background .5s var(--ease),opacity .5s var(--ease)}
.services li::before{content:"";position:absolute;left:0;right:0;top:-1px;height:2px;background:var(--accent);transform:scaleX(0);transform-origin:left;transition:transform .7s var(--ease)}
.services li::after{content:"";align-self:center;justify-self:end;width:1.6rem;height:.8rem;background:var(--accent-text);
 -webkit-mask:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 12'><path d='M0 6h22M17 1l5 5-5 5' fill='none' stroke='black' stroke-width='1.5'/></svg>") center/contain no-repeat;
 mask:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 12'><path d='M0 6h22M17 1l5 5-5 5' fill='none' stroke='black' stroke-width='1.5'/></svg>") center/contain no-repeat;opacity:0;transform:translateX(-14px);transition:opacity .4s var(--ease),transform .55s var(--ease)}
.services h3{font-size:1.28rem;letter-spacing:-.015em}
.services p{color:var(--muted);transition:color .4s var(--ease)}
@media(hover:hover){
 .services:hover li:not(:hover){opacity:.5}
 .services li:hover{background:var(--surface)}
 .services li:hover::before{transform:scaleX(1)}
 .services li:hover::after{opacity:1;transform:none}
 .services li:hover p{color:var(--fg)}}
@media(max-width:680px){.services li{grid-template-columns:minmax(0,1fr)}.services li::after{display:none}}
```
Wirkung beim Hover: Die Zeile bekommt einen hellgrauen Hintergrund, oben läuft eine rote Linie von links ein, rechts gleitet ein roter Pfeil herein, der Beschreibungstext wird dunkler, und alle anderen Zeilen treten auf 50 Prozent Deckkraft zurück. Das lenkt den Blick sehr gezielt. Spalten: links Titel, rechts Beschreibung, ganz rechts der Pfeil. Auf Touch-Geräten gibt es keine Hover-Effekte (nur in @media (hover:hover)).

## 11. Portfolio

Dunkle Sektion mit Raster und Maus-Glow (siehe Teil 1, Klassen: sheet dark glow bleed). Vier Projekte in einem zweispaltigen, versetzten Raster. Jedes Projekt zeigt eine technische Strichzeichnung statt eines Fotos.
- .glow: position relative, isolation isolate, --gpos -1px -1px, Raster 56px in --hero-grid (wie Hero, ohne Parallax). Das rote Glow-Raster liegt in ::before mit opacity .4.
- .projects: grid, zwei Spalten, gap 3rem 2rem. Ab 681px sind gerade Karten (nth-child(even)) um 3.5rem nach unten versetzt, das erzeugt Rhythmus statt Tabellenoptik. Unter 680px eine Spalte, kein Versatz.
- .project: flex column, gap .9rem. Reihenfolge: Zeichnung, Meta-Zeile (Mono: Jahr, Typ, Fläche, flex wrap, gap .3rem 1.2rem), Titel (1.28rem), Beschreibung (.96rem, --muted). Darunter ein Hinweis (.note, .85rem, --muted), dass es Beispielprojekte sind.
- Zeichnungsrahmen (.drawing): position relative, overflow hidden, aspect-ratio 8/5, background var(--surface), border 1px var(--line), color var(--strong) (die SVGs nutzen currentColor), transition border-color .3s. SVG darin: display block, 100% x 100%, transition transform .9s var(--ease). Am unteren Rand ein 3px Balken in --accent-hi (::after, left 0, right 0, bottom 0, scaleX(0), origin left, .6s).
- Hover (nur bei hover:hover): Rahmen wird --accent-hi, SVG skaliert auf 1.06, der Balken fährt auf scaleX(1), der Titel wird --accent-hi.
- Eintritt: Die Zeichnung wird von links aufgedeckt: .js .project .drawing {clip-path:inset(0 100% 0 0);transition:clip-path 1.3s var(--ease) .2s,border-color .3s} und .js .project.seen .drawing {clip-path:inset(0)}.
- Parallax: .project und .portrait bekommen translate: 0 calc(var(--ty,0)*1px), --ty wird per JS gesetzt (Teil 3). Dezent, maximal 70px.

Die vier Zeichnungen (alle viewBox="0 0 320 200" fill="none" stroke="currentColor" stroke-width="1.4"; Akzentteile in var(--accent-text)):
```html
<!-- 1 Grundriss Wohnbau -->
<rect x="50" y="28" width="220" height="130"/><path d="M50 92H160M160 28V158M160 110H270"/>
<path d="M120 92a16 16 0 0 1 16 16" stroke-width=".8"/><path d="M160 70a16 16 0 0 1 16-16" stroke-width=".8"/>
<path d="M50 176H270M50 170V182M270 170V182" stroke-width=".8" stroke="var(--accent-text)"/>
<text x="160" y="193" text-anchor="middle" font-family="Poppins,sans-serif" font-size="9" fill="var(--accent-text)" stroke="none">11,00</text>
<!-- 2 Stützenraster Halle -->
<rect x="40" y="36" width="240" height="120"/>
<g stroke-dasharray="5 4" stroke-width=".7" stroke="var(--accent-text)"><path d="M100 22V170M160 22V170M220 22V170M26 76H294M26 116H294"/></g>
<g fill="currentColor" stroke="none"><rect x="96" y="72" width="8" height="8"/><rect x="156" y="72" width="8" height="8"/><rect x="216" y="72" width="8" height="8"/><rect x="96" y="112" width="8" height="8"/><rect x="156" y="112" width="8" height="8"/><rect x="216" y="112" width="8" height="8"/></g>
<g font-family="Poppins,sans-serif" font-size="8" fill="currentColor" stroke="none" text-anchor="middle"><text x="100" y="16">A</text><text x="160" y="16">B</text><text x="220" y="16">C</text><text x="16" y="79">1</text><text x="16" y="119">2</text></g>
<!-- 3 Lageplan Quartier -->
<path d="M30 30H290V170H30Z" stroke-dasharray="6 4" stroke-width=".8"/>
<rect x="52" y="50" width="70" height="30"/><rect x="140" y="50" width="40" height="30"/><rect x="198" y="50" width="70" height="46"/>
<rect x="52" y="104" width="46" height="46"/><rect x="116" y="104" width="64" height="24"/><rect x="198" y="116" width="70" height="34"/>
<path d="M30 92H290" stroke-dasharray="2 4" stroke-width=".7"/><path d="M296 24l5 14h-10z" fill="var(--accent-text)" stroke="none"/>
<!-- 4 Schnitt Kita -->
<path d="M20 160H300" stroke-width="2"/><path d="M60 160V80L160 40L260 80V160"/><path d="M60 120H260" stroke-width=".8"/>
<rect x="86" y="92" width="26" height="18"/><rect x="146" y="92" width="26" height="18"/><rect x="206" y="92" width="26" height="18"/>
<rect x="86" y="132" width="26" height="28"/><rect x="206" y="132" width="26" height="28"/>
<path d="M274 160V80M270 80h8M270 160h8" stroke-width=".8" stroke="var(--accent-text)"/>
```
Inhaltlich: 1 Aufstockung Stadthaus (2025, Wohnbau, 320 m² BGF), 2 Gewerbehalle zu Büro (2024, Umnutzung, 1.850 m²), 3 Machbarkeitsstudie Quartier (2023, 2,4 ha), 4 Kita-Neubau (2026, Neubau, "in Bau"). Aria-Labels der Zeichnungen: Grundriss eines Wohnhauses, Stützenraster einer Halle, Lageplan eines Quartiers, Schnitt eines Kita-Neubaus.

## 12. Arbeitsweise

Vier Schritte in einer Reihe, über der Reihe eine rote Linie, die mit dem Scrollen von links nach rechts "wächst". So wird der Prozess als Fortschritt erlebbar.
```css
.steps{position:relative;list-style:none;margin:0;padding:0;display:grid;grid-template-columns:repeat(4,minmax(0,1fr));border-top:1px solid var(--line);counter-reset:step}
.steps::before{content:"";position:absolute;left:0;right:0;top:-1px;height:3px;background:var(--accent);transform-origin:left;transform:scaleX(var(--sp,0))}
.steps li{padding:1.4rem 1.4rem .4rem 0;counter-increment:step;display:flex;flex-direction:column;gap:.55rem}
.steps li::before{content:"Phase " counter(step);font:500 .72rem var(--font-mono);letter-spacing:.1em;text-transform:uppercase;color:var(--accent-text)}
html[lang=en] .steps li::before{content:"Stage " counter(step)}
.steps h3{font-size:1.18rem;transition:color .3s}.steps p{color:var(--muted);font-size:.95rem}
@media(hover:hover){.steps li:hover h3{color:var(--accent-text)}}
@media(max-width:960px){.steps{grid-template-columns:repeat(2,minmax(0,1fr))}}
@media(max-width:520px){.steps{grid-template-columns:minmax(0,1fr)}.steps li{border-bottom:1px solid var(--line);padding-bottom:1.1rem}}
```
--sp (0 bis 1) berechnet JS aus der Scrollposition (Formel in Teil 3). Die Zählung "Phase 1" bis "Phase 4" kommt per CSS-Counter und wird bei englischer Sprache zu "Stage".
