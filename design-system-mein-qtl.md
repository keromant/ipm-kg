# Design System — mein-qtl.de (Stand: Juni 2026)

**Basis für ipm-KG Website — alle Struktur-, Layout- und Animations-Entscheidungen**

---

## 1. CSS Custom Properties (Design Tokens)

```css
:root {
  --bg:         #0A0A0A;          /* Haupt-Hintergrund (fast Schwarz) */
  --bg-2:       #060606;          /* Dunklere Sektion-Variante */
  --bg-3:       #0F0F0F;          /* Hellere Sektion-Variante (aktuell wenig genutzt) */
  --gold:       #C9941A;          /* Primärfarbe — Akzent-Gold */
  --gold-lt:    #E8B84B;          /* Helles Gold (Cursor hover, wws-tag) */
  --gold-dim:   rgba(201,148,26,.25);  /* Transparentes Gold (Concept-Bild-Border) */
  --text:       #F5F0E8;          /* Haupttext (Warmweiß) */
  --text-2:     #8A8A8A;          /* Sekundärtext (Grau) */
  --sidebar-w:  155px;            /* Sidebar-Breite — body hat padding-left davon */
  --hero-cap-h: 76px;             /* Caption-Bar-Höhe über dem Slider */
}
```

**Regelwerk für `.gold`-Klasse:** Farbe ist `#E6A52A` (heller als `--gold`, besserer Kontrast auf Schwarz) + `font-weight: 600`.

---

## 2. Typografie

### Schriftarten (selbst-gehostet, DSGVO-konform)

| Variable/Zweck | Schrift | Datei | Besonderheit |
|---|---|---|---|
| `--font-avenir` | Avenir Light → Montserrat | `avenir-light.ttf` | Navigation, dünne UI-Labels |
| `--font-body` | Avenir → Avenir Next → Montserrat | (System) | Fließtext, Buttons |
| Überschriften | Playfair Display | `PlayfairDisplay.ttf` + Italic | H1–H3, Zitate, Eyebrows |
| Zahlen/Counter | Cormorant Garant | (nur als Font-Family referenziert) | Slide-Counter, Fact-Zahlen, Reason-Numbers |
| Markenname ipm | Arial Rounded MT Bold | (System) | `.ipm`-Klasse |

### Typografie-Skala

```
Eyebrow:        Playfair Display, 9px, letter-spacing .42em, uppercase, gold
Slide-Counter:  Cormorant Garant, 34px, gold, letter-spacing -.02em
H1 (Caption):   Playfair Display, clamp(15px, 2vw, 30px)
H2 (Konzept):   Playfair Display, clamp(34px, 4.2vw, 60px), weight 400
H2 (Gründe):    Playfair Display, clamp(38px, 5vw, 68px)
H2 (Philosoph): Playfair Display, clamp(34px, 5.5vw, 78px)
H2 (Split):     Playfair Display, clamp(30px, 3.6vw, 56px), weight 400
H2 (Fullstack): Playfair Display, clamp(34px, 5vw, 68px)
Fließtext:      Avenir/Montserrat, 17–17.5px, line-height 1.8–1.85
Fließtext-2:    --text-2 (#8A8A8A)
Blockquote:     Playfair Display, 20px, italic, border-left 1px solid gold
Glowtext:       Avenir Light, weight 300, 19–22px, gold text-shadow
Sidebar-Label:  Avenir Light, 11.5px, weight 300, letter-spacing .04em
```

### Glowing-Text-Muster (wiederkehrendes Element)

```css
font-family: var(--font-avenir);
font-weight: 300;
font-style: normal;
color: #fff;
font-size: 19px;
text-shadow: 0 0 16px rgba(230,165,42,.6), 0 0 40px rgba(230,165,42,.32);
```

---

## 3. Layout-Architektur

### Grundprinzip

- `body` hat `padding-left: var(--sidebar-w)` (155px) — der gesamte Content liegt rechts der Sidebar
- Auf Mobile (`≤960px`): `padding-left: 0`, Sidebar wird per Transform off-screen

### Sidebar (Fixed Left)

```
Position:    fixed, left:0, top:0, height:100vh, width:155px
Hintergrund: rgba(8,8,8,.97)
Border-right: 1px solid rgba(201,148,26,.1)
z-index:     1000
```

**Struktur der Sidebar:**
1. Logo (`.hero-logo`) — `width: 300px`, `margin-left: 55px` → ragt nach rechts in den Bildbereich
2. Top-Nav (`sb-nav--top`) — erste 3 Punkte, direkt am Logo
3. Nav-Wrap (scrollbar) — restliche Punkte
4. Footer-Link (Hamburger/≡) — ganz unten, scrollt zum Seitenende

### Hero Frame

```
.hero-frame:    display:flex, flex-direction:column, height:100vh
  .hero-caption:  min-height:76px (Caption-Bar, border-bottom gold)
  #hero:          flex:1, overflow:hidden (Slider)
```

---

## 4. Seiten-Sektionsstruktur (Reihenfolge)

```
1.  HERO FRAME (100vh — Slider + Caption-Bar)
    ↓ section-intro (Trenner) + band-divider (Vollbild-Foto)
2.  #konzept    — Split-Blocks (Text-Only + Fullstack)
    ↓ section-intro
3.  #gruende    — Split-Blocks (alternierend normal/reverse)
    ↓ section-intro
4.  #lebensfrage — Split-Block
    ↓ section-intro
5.  #realitaet  — Vollbreit-Banner + Split-Blocks + txt-section
    ↓ section-intro
6.  #projekte   — Split-Blocks + highlights-Grid + band-divider
    ↓ section-intro
7.  #akzeptanz  — txt-section + Video + Facts
    ↓ section-intro
8.  #wichtigste — Zentriertes Video + Split
    ↓ section-intro
9.  #wer-wir-sind — wer-head + Video + txt-section + ipm-card
    ↓ section-intro
10. #contact    — band-divider (Video) + txt-section + Kontaktformular
    ↓ <footer>
```

---

## 5. Kern-Komponenten

### A) `.split` — Bild/Text Block

```
display: flex; align-items: center; padding: 48px 6%;
.split-media: flex:none, width:57% (default), overflow:hidden
.split-text:  flex:1 1 0
.split.reverse → Bild rechts (flex-direction: row-reverse)
.split.alt     → background: var(--bg-2)
```

**Spezielle Medienbreiten (Kontext-Overrides):**

| Kontext | Breite |
|---|---|
| `.split-intro .split-media` | 42% |
| `.split-down .split-media` | 58% |
| `.split-fit .split-media` | 30% (transparente PNGs) |
| `.split-logo .split-media` | 40% |
| `#lebensfrage .split-media` | 36% |
| `#projekte .split-media` | 66% |
| `#realitaet .split-media` | 68% (float:left, Text umfließt) |
| `#wichtigste .split-media` | 46% (float:right, Text umfließt) |

### B) `.fullstack` — Vollbreites Stacked-Layout

```
padding: 56px 6%; background: var(--bg)
.fullstack-text: max-width:1100px; margin:0 auto 44px
.fs-img: width:100%; max-width:1400px; margin:0 auto (unter dem Text)
```

### C) `.section-intro` — Sektions-Trenner

```
padding: 60px 6%; text-align:center
.si-eyebrow: Avenir 10px, letter-spacing .42em, gold, fade+slide in
.si-title:   Playfair Display, clamp(26px,3.4vw,48px), gold, scale-Reveal
.si-rule:    width wächst von 0 → 80px (Goldlinie)
Trigger: JS fügt .is-in hinzu via IntersectionObserver
```

### D) `.band-divider` — Vollbreiter Bild-Trenner

```
height: clamp(360px,50vh,620px); overflow:hidden
img: transform:scale(1.05) → bei .is-in: scale(1) [2.2s cubic]
.band-divider--full: height:auto (volle natürliche Bildhöhe)
```

### E) `.highlights` — 5-Spalten-Feature-Raster

```
display:grid; grid-template-columns: repeat(5,1fr); gap:clamp(28px,3vw,52px)
.hl-cat:    Avenir, clamp(18px,1.5vw,22px), border-bottom gold, min-height:2.5em
.hl-list li: padding-left:22px, goldener Bullet (● 7px Kreis)
```

### F) `.ipm-card` — Kontaktkarte

```
display:flex; gap:48px; border-top: 1px solid rgba(201,148,26,.18)
Links:  Logo + Firmenadresse + Kontaktlinks
Rechts: .ipm-guarantee → .glow-Texte + .pt-Labels (uppercase, letter-spacing .13em)
```

### G) Kontaktformular

```
Inputs:  background:rgba(255,255,255,.04); nur border-bottom; focus → gold border
Button:  .btn-gold — outline-Stil, border:1px solid rgba(201,148,26,.32)
Hover:   translateY(-4px) scale(1.08) + gold glow text-shadow
```

---

## 6. Animationssystem

### A) Basis-Reveal (`.reveal`)

```css
opacity: 0; transform: translateY(22px);
transition: opacity 1s, transform 1s — cubic-bezier(0.22,1,0.36,1)
.reveal.visible → opacity:1; transform:translateY(0)
Trigger: IntersectionObserver threshold:0.1
```

Delay-Helfer: `.reveal-delay-1/.2/.3` → `.12s / .24s / .36s`

### B) Vertikaler Reveal (`.reveal-vert`)

```css
opacity: 0; transform: translateY(52px);
transition: opacity .85s ease, transform 1.05s cubic-bezier(.22,1,.36,1)
```

### C) Section-Intro Reveal

```css
.si-eyebrow: opacity+translateY(14px) → sichtbar (.8s/.9s)
.si-title:   @keyframes siReveal
             scale(.92) glow-aus → scale(1.05) gold-glow-max → scale(1) glow-bleibt
.si-rule:    width: 0 → 80px (1.1s, delay:.4s)
```

### D) Hero-Slider (Tiefen-Push ohne Fade)

```css
@keyframes depthIn        { translateX(99%) scale(1.38) → translate(0) scale(1) }
@keyframes depthOut       { translateX(0) → translateX(-100%) }
@keyframes depthInReverse { translateX(-99%) scale(1.38) → translateX(0) scale(1) }
@keyframes depthOutReverse{ translateX(0) → translateX(100%) }
Dauer: 1.05s cubic-bezier(0.3,0,0.1,1)

Caption herein: translateX(48px)→0 (0.95s cubic-bezier(0.22,1,0.36,1))
Caption heraus: translateX(0)→translateX(-40px) (0.45s ease)
```

### E) Split-Block Premium Reveal (Standard-Sektionen — vertikal)

```css
Bild:  scale(1.22) → scale(1) [1.9s cubic-bezier(.16,1,.3,1)]
       + Vorhang ::after scaleY(1→0) von unten [1.2s cubic-bezier(.76,0,.24,1)]

Text:  clip-path:inset(0 0 100% 0) + translateY(-16px) → offen + translateY(0)
       [clip 1.05s, opacity .6s cubic-bezier(.76,0,.24,1)]

Stagger:
  reason-no: .25s  |  split-h2: .38s  |  split-sub: .50s
  p:nth(1): .58s   |  p:nth(2): .68s  |  p:nth(3): .78s
  p:nth(4): .88s   |  p:nth(5): .98s
```

### F) Split-Block Premium Reveal (#gruende — horizontal)

```css
Bild:  scale(1.14) → scale(1) [2.3s]
       Vorhang scaleX(1→0) von rechts [1.4s]; reverse → transform-origin:left

Text:  clip-path:inset(0 100% 0 0) + translateX(-22px) → offen
       Reverse: clip-path:inset(0 0 0 100%) + translateX(22px)

Stagger:
  reason-no: .12s  |  split-h2: .30s  |  split-sub: .44s
  p:nth(1): .52s   |  p:nth(2): .66s  |  p:nth(3): .80s
  p:nth(4): .94s   |  p:nth(5): 1.08s
```

### G) `.reason` (8-Gründe-Grid-Karten)

```css
opacity:0; transform:translateY(50px) scale(0.96);
transition: opacity 1.7s, transform 1.7s — cubic-bezier(0.22,1,0.36,1)

Stagger: nth(1,2):0s | nth(3,4):.12s | nth(5,6):.24s | nth(7,8):.36s

Hover: background:rgba(201,148,26,.035)
Sweep ::after: gold-Gradient fährt horizontal durch [1.4s ease-out, delay:.25s]
reason-num: translateX(-20px)→0 + opacity, (.9s, delay:.25s)
reason-bar: width 0→36px [1s, delay:.4s]
```

### H) `.feature-list` (von links, Bounce)

```css
@keyframes featurePop:
  translateX(-28px) scaleX(.88) → translateX(6px) scaleX(1.03) → translateX(0) scaleX(1)
  Dauer: .55s cubic-bezier(.22,1,.36,1)
Stagger je 70ms: .04s / .11s / .18s / .25s / .32s / .39s / .46s / .53s
```

### I) `.num-list` (von rechts, Bounce)

```css
@keyframes numPop:
  translateX(32px) scaleX(.9) → translateX(-5px) scaleX(1.02) → translateX(0) scaleX(1)
  Dauer: .58s cubic-bezier(.22,1,.36,1)
Stagger: .04s / .16s / .28s
```

### J) `.highlights hl-group` (Aufplopp mit Overshoot)

```css
hl-cat + hl-list li: translateY(24px) scale(.92) → translateY(0) scale(1)
cubic-bezier(.34,1.72,.5,1) — Overshoot-Bounce
hl-cat:.04s | li:nth(1):.12s | je +.07s bis .54s (7 Punkte)
```

### K) `.band-divider` Scale-Einzug

```css
img: scale(1.05) → scale(1) [2.2s cubic-bezier(.22,1,.36,1)] bei .is-in
```

### L) Sidebar-Navigation Hover

```css
transform: translateY(-4px) scale(1.08) [0.4s cubic-bezier(0.34,1.56,0.64,1)]
text-shadow Gold-Glow:
  0 0 8px  rgba(212,175,55,.8),
  0 0 20px rgba(212,175,55,.4),
  0 0 40px rgba(212,175,55,.15)
```

### M) Autoplay-Progress-Bar

```css
@keyframes barFill: scaleX(0) → scaleX(1) [6s linear]
Weiße Linie, box-shadow: 0 0 6px rgba(255,255,255,.5)
```

---

## 7. Custom Cursor

```
#cursor:     38px Ring, 3px border gold, position:fixed
             Hover (Links/Buttons/Thumbs): 58px, background rgba(201,148,26,.07)
             transition: width/height/opacity/border-color/background .3s ease
#cursor-dot: 4px Punkt, gold, direkt auf clientX/Y (kein Lag)
Ring-Follow: Interpolation mit Faktor 0.11 via requestAnimationFrame (leicht trailing)
```

**Cursor-Farbzonen (via body-Klassen):**

| Zone | Ring + Dot |
|---|---|
| `cursor-zone-sidebar` | `rgba(255,255,255,.85)` |
| `cursor-zone-link` | `--gold-lt` |
| `cursor-zone-image` | `rgba(255,255,255,.75)` |

---

## 8. Video-Handling

**Format-Weiche (WebM bevorzugt):**
```js
if (vid.canPlayType('video/webm; codecs="vp9"')) → webm, sonst mp4
```

**Lazy-Loading-Strategie:**
- Hero: aktives + ±1 Nachbar-Slide werden geladen
- Section-Videos: `data-webm` + `data-mp4` Attribute, laden erst via IntersectionObserver
- GPU-Hint auf allen Videos: `transform:translate3d(0,0,0); will-change:transform; backface-visibility:hidden`

**Video-Typen:**

| Typ | Verhalten |
|---|---|
| `initSectionVideo(id)` | Loopt, stoppt/spielt bei Scroll (akzeptanz, wichtigste) |
| `initSectionVideoOnce(id, rate)` | Spielt einmal durch, Poster crossfaded am Ende (wer-wir-sind 0.8×, kontakt) |

**Poster-Crossfade:** Poster-Bild sitzt als `position:absolute` `<img>` über dem Video (`opacity:0`), wechselt zu `opacity:1` (`2s ease-in`) wenn Video 1.5s vor Ende ist.

---

## 9. JavaScript-Behaviours

| Feature | Mechanismus |
|---|---|
| Scroll-Reveals | `IntersectionObserver (threshold:0.1)` → fügt `.visible` hinzu |
| Split-Reveal | `IntersectionObserver (threshold:0.2)` → fügt `.is-in` hinzu, `unobserve` danach |
| Section-Intro | Gleicher Observer → `.is-in` auf `.section-intro` und `.band-divider` |
| Slider Autoplay | `setTimeout 6000ms`, `clearTimeout` + Neustart nach Thumb-Klick |
| Slider Klick auf Bild | Autoplay stoppen + Flash-Animation (`#hero-play-flash`) |
| Sidebar Active | `scroll`-Event, 40% vom Viewport als Trigger-Schwelle |
| Counter Animation | `requestAnimationFrame`, 2200ms, `de-DE` Locale |
| Parallax | Scroll-Event auf `.vis-break img` (translateY -30px bis +30px) |
| Nav-Klick | `scrollIntoView({ behavior:'smooth' })` auf `#${id}-intro` oder `#${id}` |
| Mobile Sidebar | `transform:translateX(-100%)` → `.is-open` → `translateX(0)` |

---

## 10. Responsive Breakpoints

```css
@media (max-width: 1200px) { highlights: 3 Spalten }
@media (max-width: 960px)  {
  body: padding-left:0
  .split → flex-direction:column (Bild immer oben, 100% Breite)
  Sidebar: off-screen, Mobile-Hamburger sichtbar
  #concept, #domusprive, #wer-wir-sind: 1-Spalten-Grid
  .reasons-grid: 1 Spalte
  .facts-grid: 1 Spalte
  .form-row: 1 Spalte
  footer: flex-direction:column
}
@media (max-width: 760px) { highlights: 2 Spalten }
@media (max-width: 460px) { highlights: 1 Spalte }
```

---

## 11. Footer & Legal

```
Flex, space-between, align-center; padding: 56px 9%
border-top: 1px solid rgba(255,255,255,.038)

.footer-brand:  Cormorant Garant, 19px, letter-spacing .12em, --text-2
.footer-meta:   12px, rgba(138,138,138,.55)
.footer-links:  Avenir, 11px, uppercase, letter-spacing .22em → gold hover
.footer-credit: 10px, rgba(138,138,138,.32) → gold hover
                position:absolute, bottom:16px, right:9%
```

---

## 12. Checkliste: Was für ipm-KG übernommen wird

Alles außer Inhalt, Farben und Logo ist 1:1 portierbar.

- [ ] Alle CSS-Tokens (nur Farbwerte tauschen)
- [ ] Fontstack (Avenir Light + Playfair Display + Montserrat)
- [ ] Sidebar-Layout mit 155px-Verschiebung
- [ ] Hero-Slider mit Tiefen-Push-Animation + Caption-Bar
- [ ] Alle Reveal-Klassen (`.reveal`, `.reveal-vert`, `.split.is-in`, `.section-intro.is-in`)
- [ ] Custom Cursor mit Farbzonen
- [ ] Section-Intro Trenner-Muster
- [ ] Band-Divider mit Scale-Reveal
- [ ] Split-Block-System (normal/reverse/alt/Medienbreiten)
- [ ] Fullstack-Block
- [ ] Highlights-Grid
- [ ] Feature-List + Num-List Bounce-Animationen
- [ ] Video Lazy-Loading + Format-Weiche + GPU-Hints
- [ ] Kontaktformular-Styling
- [ ] Komplettes Responsive-System
- [ ] Sidebar-Active-Highlight via Scroll
- [ ] Counter-Animation

**Nur zu ersetzen:** `--gold`, `--gold-lt`, `--gold-dim`, Logo, Textinhalte, Sektionsstruktur nach Bedarf.
