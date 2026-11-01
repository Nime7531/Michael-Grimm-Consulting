# Design-Briefing Lindner Architekturberatung, Teil 3 von 3
Inhalt: Kontakt, Footer, komplettes JavaScript-Verhalten, Reduced-Motion, Responsive-Übersicht, Barrierefreiheit, Abnahme-Checkliste.
Voraussetzung: Teil 1 und 2.

## 13. Kontakt

Zweispaltig (.contact-grid: minmax(0,1.4fr) minmax(0,1fr), gap 3rem, unter 860px einspaltig): links Formular, rechts die Kontaktdaten.

Formular: ruhig, ohne Boxen. Die Felder sind nur Linien, wie Unterschriftszeilen auf einem Formblatt.
```css
form{display:flex;flex-direction:column;gap:1.4rem;min-width:0}
.field{display:flex;flex-direction:column;gap:.2rem}
.field label{font:500 .72rem var(--font-mono);letter-spacing:.1em;text-transform:uppercase;color:var(--muted);transition:color .25s}
.field:focus-within label{color:var(--accent-text)}
input,select,textarea{font:400 1rem var(--font-body);color:var(--fg);background:transparent;border:0;border-bottom:1px solid var(--line);border-radius:0;padding:.7rem 0;width:100%;transition:border-color .25s,box-shadow .25s}
select option{color:#2a2829;background:#fff}
input:hover,select:hover,textarea:hover{border-bottom-color:var(--muted)}
input:focus,select:focus,textarea:focus{outline:none;border-bottom-color:var(--accent);box-shadow:0 1px 0 0 var(--accent)}
textarea{min-height:8rem;resize:vertical}
.check{display:flex;gap:.7rem;align-items:flex-start;font-size:.9rem;color:var(--muted)}
.check input{width:1.1rem;height:1.1rem;margin-top:.25rem;padding:0;accent-color:var(--accent);flex:none}
.row2{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:1.4rem}  /* Name + E-Mail; unter 560px eine Spalte */
```
Verhalten beim Fokus: Die Linie wird rot und verdickt sich optisch auf 2px (box-shadow), das zugehörige Label wird rot. Hover: Linie wird --muted. Der Absende-Button ist ein Primary-Button ("Anfrage vorbereiten"). Fehler (.note, role=alert) erscheinen unter dem Button.

Ergebnisbox: Das Formular versendet nichts selbst. Nach dem Absenden erscheint eine Box mit dem vorformatierten Text zum Kopieren.
```css
.result{border:1px solid var(--accent);background:var(--surface);padding:1.3rem;display:flex;flex-direction:column;gap:.8rem}
.result h3{font-size:1.15rem}
.result pre{margin:0;white-space:pre-wrap;overflow-wrap:anywhere;font:400 .88rem/1.6 var(--font-body);color:var(--muted)}
```
Inhalt: Titel "Ihre Nachricht ist vorbereitet.", Hinweis, vorformatierter Text (Zeile 1 "Anfrage: <Anliegen>", Zeile 2 "Von: <Name> <<E-Mail>>", Leerzeile, Nachricht), Button "Text kopieren" und daneben Statusmeldung (aria-live polite, "Kopiert" oder Fehlertext, verschwindet nach 2.5s). Nach dem Einblenden scrollIntoView({block:"nearest", behavior:"smooth"}).

Kontaktdaten (.details): dl mit flex column, gap 1.4rem. dt als Mono-Label (margin-bottom .2rem), dd 1.05rem in --strong, overflow-wrap anywhere. E-Mail und Telefon stehen in einer .copy-line (flex, align center, gap .7rem, wrap) mit einem kleinen Kopieren-Button:
```css
.copy-btn{font:500 .7rem var(--font-mono);letter-spacing:.08em;text-transform:uppercase;padding:.3rem .65rem;border:1px solid var(--line);background:transparent;color:var(--muted);cursor:pointer;transition:color .25s,border-color .25s,background .25s}
.copy-btn:hover{color:var(--on-accent);background:var(--accent);border-color:var(--accent)}
```
Beim Klick wechselt der Button-Text 1.8s lang auf "Kopiert". Fallback ohne Clipboard-API: den Text markieren (Range und Selection).

## 14. Footer (dunkel, mit Raster und Glow)

Klassen: foot dark glow bleed. Padding-block: clamp(3.5rem,7vw,5.5rem) oben, 2rem unten.
- .foot-grid: flex, space-between, align-items flex-end, gap 2rem, wrap. Links: Logo (Schrift --hero-fg, Zusatz --hero-muted) und darunter der Claim "Sprechen wir über Ihr Vorhaben." in clamp(1.5rem,3.4vw,2.4rem), 600, letter-spacing -0.025em, line-height 1.15, max-width 18ch, text-wrap balance, margin-top 1.4rem. Rechts der "Nach oben"-Button.
- "Nach oben" (.totop): inline-flex, gap .7rem, kein Unterstrich, Farbe --hero-fg, border 1px --hero-line, padding .8rem 1.2rem, 500, .9rem. Pfeil "↑" als eigenes span. Hover: Hintergrund und Rahmen weiß, Text anthrazit, Pfeil hebt sich translateY(-4px) (.35s). Transition .3s.
- .foot-legal (Mono, flex, space-between, wrap, gap .6rem 2rem, margin-top 3rem, padding-top 1.2rem, border-top 1px --hero-line): links "© 2026 Lindner Architekturberatung", rechts "Impressum · Datenschutz".

## 15. JavaScript-Verhalten (Vanilla, eine IIFE, alles mit Feature-Erkennung)

Grundregel (progressive enhancement): Die Seite muss ohne JS vollständig lesbar sein. Alle "Vor-Zustände" (unsichtbar, geclippt, scaleX(0)) hängen an der Klasse .js auf html. Diese Klasse setzt das Skript erst, wenn feststeht, dass Animationen laufen dürfen: kein prefers-reduced-motion UND IntersectionObserver vorhanden.
```js
var reduce=matchMedia("(prefers-reduced-motion: reduce)").matches, hasIO="IntersectionObserver" in window,
    canHover=matchMedia("(hover: hover)").matches, root=document.documentElement;
```
1. Scroll-Handler (ein einziger, per requestAnimationFrame gedrosselt, passive Listener auf scroll und resize):
```js
y=pageYOffset; max=Math.max(1,root.scrollHeight-innerHeight);
header.style.setProperty("--p",Math.min(1,y/max).toFixed(4));          // Fortschrittsbalken
heroH=hero.offsetHeight; root.classList.toggle("past-hero", y>heroH-header.offsetHeight);
if(!reduce) root.style.setProperty("--sy",Math.min(y,heroH));          // Raster- und Bild-Parallax im Hero
stepsProgress(); parallax();
```
2. Schritte-Linie: r=steps.getBoundingClientRect(); vh=innerHeight; p=(vh*0.85-r.top)/(r.height+vh*0.1); steps.style.setProperty("--sp", clamp(p,0,1).toFixed(3)).
3. Parallax (nicht bei reduced-motion, nicht bei Breite <=680px): Elemente .portrait (Faktor 0.06) und .project (gerade Index 0.05, ungerade -0.035). Pro Element: r=getBoundingClientRect(); überspringen wenn r.bottom<-200 oder r.top>vh+200; c = r.top - ty + r.height/2 - vh/2; ty = clamp(c*faktor, -70, 70); setProperty("--ty", ty.toFixed(1)). (Das "- ty" rechnet die bereits angewendete Verschiebung heraus, damit es nicht aufschaukelt.)
4. Maus-Glow (nur wenn !reduce && canHover): für jedes .hero und .glow: pointermove setzt --mx = e.clientX - r.left px und --my = e.clientY - r.top px (r = Box-Rect), pointerleave setzt beide auf -999px.
5. Scroll-Reveal: Elemente aus den Selektoren ".sheet .wrap > .mono", ".about-text > p", ".portrait", ".facts > div", ".services li", ".project", ".steps li", ".note", "#contact-form", ".details", ".foot-grid > *", ".foot-legal" bekommen die Klasse .reveal und --i = Index im Elternelement (maximal 4, bei .project: Index modulo 2). IntersectionObserver {rootMargin:"0px 0px -6% 0px", threshold:.1}: bei Eintritt .in hinzufügen, unobserve, und nach 1400ms .reveal und .in sowie --i wieder entfernen (damit Hover-Transitions nicht von der Reveal-Transition gebremst werden).
```css
.js .reveal{opacity:0;transform:translateY(28px);transition:opacity .9s var(--ease),transform .9s var(--ease);transition-delay:calc(var(--i,0)*90ms)}
.js .reveal.in{opacity:1;transform:none}
```
Hinweis: .project und .portrait nutzen für den Parallax die CSS-Eigenschaft translate (nicht transform), damit sich Reveal-Transform und Parallax nicht gegenseitig überschreiben.
6. "seen"-Markierung: Zweiter IntersectionObserver {rootMargin:"0px 0px -10% 0px", threshold:.05} für .project, .services, .facts (setzt .seen auf das Element selbst) und für den Elterncontainer jeder ".sheet h2" (setzt .seen auf die h2). Das löst H2-Enthüllung, Linienaufbau und Zeichnungs-Aufdeckung aus.
7. Count-up: IntersectionObserver {threshold:.4} auf .facts. Bei Eintritt für jedes [data-count] (mit data-suffix "+"): Start bei "0+", Dauer 1300ms, Easing e = 1-(1-k)^3 (easeOutCubic), Anzeige Math.round(ziel*e)+suffix per requestAnimationFrame, danach observer.disconnect(). Nur wenn !reduce.
8. Scroll-Spy: IntersectionObserver {rootMargin:"-45% 0px -50% 0px"} auf den Sektionen #ueber, #leistungen, #portfolio, #kontakt. Bei Eintritt: .active von allen Nav-Links entfernen, auf den passenden Link setzen (Zuordnung über href).
9. Sprachwechsel: Alle Texte haben data-i18n="schlüssel". Wörterbuch T = {de:{...}, en:{...}}. apply(l) setzt html.lang, ersetzt textContent aller [data-i18n] (nur wenn Schlüssel existiert), setzt aria-pressed auf den DE/EN-Buttons und speichert die Wahl in localStorage (try/catch). Startsprache: gespeicherter Wert, sonst Browsersprache beginnt mit "de" ergibt DE, sonst EN. Hinweis: Die "Phase/Stage"-Beschriftung läuft über CSS, nicht über data-i18n.
10. Formular: submit mit preventDefault. Felder trimmen. Wenn Name, E-Mail, Nachricht leer oder Einwilligung fehlt: Fehlertext aus Wörterbuch (err_fill). Wenn E-Mail nicht /^[^\s@]+@[^\s@]+\.[^\s@]+$/ passt: err_mail. Sonst Ergebnisbox füllen (Betreff, Von, Nachricht), einblenden und scrollen. Der Kopieren-Button nutzt navigator.clipboard.writeText mit Statusmeldung.

## 16. Reduced-Motion
```css
@media(prefers-reduced-motion:reduce){
 html{scroll-behavior:auto}
 *,*::before,*::after{transition-duration:.01ms!important;transition-delay:0s!important;animation:none!important}
 .js .reveal{opacity:1;transform:none}}
```
Dazu im JS: kein Parallax, kein Glow, kein Count-up (Zahlen sofort), keine .js-Klasse. Der Hero-Eintritt (rise, planin) entfällt, alles ist sofort sichtbar.

## 17. Responsive Übersicht (Breakpoints und Wirkung)

- 960px: Arbeitsweise 2 Spalten.
- 900px: Hero einspaltig, Blueprint als dezenter Hintergrund (125% Breite, Opacity .3).
- 860px: Sektionen einspaltig (Label oben, nicht sticky), Über mich und Kontakt einspaltig, Porträt max 260px.
- 760px: Nav-Links weg, Planstempel 2 x 2.
- 680px: Leistungen einspaltig ohne Pfeil, Portfolio einspaltig ohne Versatz, Parallax aus.
- 560px: Buttons im Hero volle Breite, Name/E-Mail untereinander.
- 520px: Arbeitsweise einspaltig mit Trennlinien.
- 480px: Kennzahlen einspaltig, Logo ohne Zusatz.
Touch-Geräte: Alle Hover-Regeln stehen in @media (hover:hover). Safe-Area-Insets (Notch, Home-Indikator) berücksichtigen: viewport-fit=cover im Viewport-Meta, :root padding-top/bottom env(safe-area-inset-*).

## 18. Barrierefreiheit

- Fokus sichtbar (:focus-visible, 2px rot, Offset 3px). Alle Interaktionen per Tastatur nutzbar.
- aria-hidden auf Dekor (Logo-Marke, Blueprint, Fortschrittsbalken, Pfeile). Zeichnungen und Porträt role="img" mit aria-label. Nav mit aria-label "Navigation". Sprachumschalter role="group" mit aria-label "Sprache / Language" und aria-pressed.
- Fehlermeldung role="alert", Kopierstatus aria-live="polite". Formularlabels per for/id verknüpft, autocomplete name und email.
- Kontrast: Rot auf Dunkel immer #ff5a5a, Rot auf Hell #d10000. Dunkelmodus automatisch per prefers-color-scheme, manuell überschreibbar über data-theme.

## 19. Abnahme-Checkliste (Soll-Zustand)

1. Beim Laden: Hero-Text steigt gestaffelt auf, das Blueprint-Bild schärft sich ein, der Planstempel kommt zuletzt.
2. Maus über dem Hero oder einer dunklen Sektion: rotes Raster leuchtet in einem weichen Kreis unter dem Cursor.
3. Scrollen: Fortschrittsbalken wächst, Header wechselt nach dem Hero von dunkel auf hell, aktiver Nav-Link wandert mit, Sektionslabel bleibt links kleben.
4. Jede H2 wird von unten aufgedeckt, Linien über Kennzahlen und Leistungen ziehen von links auf, 15+ und 60+ zählen hoch.
5. Leistungen: Hover dimmt die anderen Zeilen, rote Linie läuft ein, roter Pfeil gleitet herein.
6. Portfolio: Zeichnungen werden von links aufgedeckt, Hover zoomt leicht, färbt Rahmen und Titel rot, roter Balken zieht unten auf. Karten bewegen sich minimal gegenläufig beim Scrollen.
7. Arbeitsweise: rote Linie wächst mit dem Scroll, "Phase 1 bis 4" (EN "Stage").
8. Formular: Fokus färbt Linie und Label rot, Prüfung funktioniert, Ergebnisbox mit Kopieren-Button erscheint.
9. DE/EN-Umschalter tauscht alle Texte inklusive Formularoptionen, Wahl bleibt nach Reload.
10. Mobil: keine horizontale Scrollleiste, Nav kompakt, Buttons volle Breite, kein Parallax, Blueprint nur als schwacher Hintergrund.
11. Mit "Bewegung reduzieren" im System: Seite statisch, aber vollständig und lesbar.
