## Styles

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Structuur](#structuur)
- [Architectuur](#architectuur)
- [Richtlijnen](#richtlijnen)
- [Wanneer globale CSS gebruiken?](#wanneer-globale-css-gebruiken)
- [Layers](#layers)
- [Reset](#reset)
- [Tokens](#tokens)
- [Typography](#typography)
   * [Tokens versus Typography](#tokens-versus-typography)
- [Themes](#themes)
   * [Brand Themes](#brand-themes)
      + [green.css](#greencss)
      + [red.css](#redcss)
   * [Appearance Themes](#appearance-themes)
      + [dark.css](#darkcss)
      + [light.css](#lightcss)
   * [Gebruik van de Themes](#gebruik-van-de-themes)
- [Bootstrap](#bootstrap)
   * [bootstrap-tokens.css](#bootstrap-tokenscss)
   * [Bootstrap Richtlijnen](#bootstrap-richtlijnen)
      + [Wel doen](#wel-doen)
      + [Niet doen](#niet-doen)
   * [Bootstrap architectuur](#bootstrap-architectuur)
- [Utilities](#utilities)
   * [Voorbeelden](#voorbeelden)
- [Algemene regels](#algemene-regels)
   * [Gebruik](#gebruik)
   * [Vermijd](#vermijd)
- [CSS Conventies](#css-conventies)
   * [`rem` voor schaalbare waarden](#rem-voor-schaalbare-waarden)
      + [Wanneer wél px](#wanneer-wél-px)
   * [Gebruik Design Tokens voor herhaalbare waarden](#gebruik-design-tokens-voor-herhaalbare-waarden)
   * [Gebruik geen vaste kleuren in styling](#gebruik-geen-vaste-kleuren-in-styling)
   * [Houd specificity laag](#houd-specificity-laag)
   * [Gebruik moderne CSS mogelijkheden](#gebruik-moderne-css-mogelijkheden)
   * [Mobile first](#mobile-first)

<!-- TOC end -->

---

Een CSS-first (dus geen Sass/SCSS of andere pre-processors) architectuur waarbij Bootstrap beschikbaar is voor standaard componenten en bekende patronen, maar niet de eigenaar is van het design.

De belangrijkste uitgangspunten

- Bootstrap gebruiken zonder grote hoeveelheden overrides.
- Ondersteuning voor meerdere branding-thema's.
- Ondersteuning voor light en dark mode.
- Native CSS boven Sass/SCSS.
- Voorspelbare cascade.
- Toekomstbestendig door gebruik van moderne CSS-features.

<!-- TOC --><a name="structuur"></a>
### Structuur

```text
/src/
├── ...
└── wwwroot/
    ├── ...
    └── css/
        ├── app.css
        ├── core/
        │   ├── layers.css
        │   ├── reset.css
        │   ├── tokens.css
        │   └── typography.css
        ├── themes/
        │   ├── brand/
        │   │   ├── blue.css
        │   │   ├── green.css
        │   │   ├── red.css
        │   │   └── ...
        │   └── mode/
        │       ├── light.css
        │       ├── ...
        │       └── dark.css
        ├── integrations/
        │   └── bootstrap-tokens.css
        └── utilities/
            ├── accessibility.css
            ├── display.css
            ├── spacing.css
            └── ...
```

<!-- TOC --><a name="architectuur"></a>
### Architectuur

De styling is opgebouwd uit verschillende lagen:

```text
Application Design System
        ↓
Design Tokens
        ↓
Themes
        ↓
Bootstrap Token Mapping
        ↓
Bootstrap Components
```

- Design Tokens definiëren welke waarden beschikbaar zijn.
- Themes overschrijven alleen tokenwaarden.
- Bootstrap consumeert tokens.
- Applicaties gebruiken tokens.
- Globale CSS bevat uitsluitend infrastructuur.

<!-- TOC --><a name="richtlijnen"></a>
### Richtlijnen

Een CSS-first (dus geen Sass/SCSS of andere pre-processors) architectuur waarbij Bootstrap beschikbaar is voor standaard componenten en bekende patronen, maar niet de eigenaar is van het design.

- Bootstrap gebruiken zonder grote hoeveelheden overrides.
- Ondersteuning voor meerdere branding-thema's.
- Ondersteuning voor light en dark mode.
- Native CSS boven Sass/SCSS.
- Voorspelbare cascade.
- Toekomstbestendig door gebruik van moderne CSS-features.

<!-- TOC --><a name="wanneer-globale-css-gebruiken"></a>
### Wanneer globale CSS gebruiken?

Gebruik globale CSS alleen voor styling die:

- Door meerdere onderdelen van de applicatie gebruikt wordt
- Design tokens, kleuren, spacing en typografie definieert
- Thema's (branding en light/dark mode) ondersteunt
- Algemene browserbasis en resets verzorgt
- Generieke utilities bevat die breed toepasbaar zijn
- Bootstrap integratie en globale styling regelt

Globale CSS vermijden voor:

- Component-specifieke styling
- Styling die alleen binnen één Razor component gebruikt wordt
- Layout details van specifieke pagina's
- Tijdelijke uitzonderingen of hacks
- Overrides om Bootstrap gedrag lokaal te corrigeren
- Styling die afhankelijk is van een specifieke business flow

<!-- TOC --><a name="layers"></a>
### Layers

CSS Layers zijn een manier om de volgorde en prioriteit van CSS-regels te bepalen.
In plaats van dat de "meest specifieke" CSS-regel altijd wint, bepaalt de layer-volgorde welke groep styling voorrang krijgt.
Hierdoor kunnen bijvoorbeeld Bootstrap, eigen styling en utilities naast elkaar bestaan zonder veel overrides of de `!important` traditie.
Het maakt de CSS beter voorspelbaar en makkelijker onderhoudbaar.

```css
/* core/layers.css */
@layer reset; /* ← laagste prioriteit */
@layer bootstrap;
@layer core;
@layer themes;
@layer utilities; /* ← hoogste prioriteit */
```

Dus: utilities > themes > core > bootstrap > reset

<!-- TOC --><a name="reset"></a>
### Reset

Doel:

- Consistente browserbasis
- Voorspelbare sizing
- Veilige media defaults
- Minimale reset

```css
/* core/reset.css */
*,
*::before,
*::after {
  box-sizing: border-box;
}

* {
  margin: 0;
}

html,
body {
  min-height: 100%;
}

body {
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

img,
picture,
svg,
video,
canvas {
  display: block;
  max-width: 100%;
}

input,
button,
textarea,
select {
  font: inherit;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  overflow-wrap: break-word;
}

ul,
ol {
  padding-left: 1.5rem;
}

table {
  border-collapse: collapse;
  border-spacing: 0;
}
```

<!-- TOC --><a name="tokens"></a>
### Tokens

Alle globale waarden worden beheerd via CSS Custom Properties.

```css
/* core/tokens.css */
:root {
  /* Colors */

  --color-primary: #2563eb;
  --color-secondary: #475569;

  --color-success: #22c55e;
  --color-warning: #f59e0b;
  --color-danger: #ef4444;

  --color-surface: #ffffff;
  --color-surface-alt: #f8fafc;

  --color-text: #111827;
  --color-text-muted: #64748b;

  /* Typography */

  --font-base: "Inter", sans-serif;

  --font-size-sm: 0.875rem;
  --font-size-md: 1rem;
  --font-size-lg: 1.125rem;

  /* Spacing */

  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;

  /* Radius */

  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;

  /* Layout */

  --content-width: 1200px;
}
```

<!-- TOC --><a name="typography"></a>
### Typography

Doel:

- Centrale typografie-definities
- Gebruik van Design Tokens
- Consistente heading-hiërarchie (wordt ook toegankelijkheidsteam blij van)

```css
body {
  font-family: var(--font-base);
  font-size: var(--font-size-md);
  color: var(--color-text);
  background-color: var(--color-surface);
}

h1,
h2,
h3,
h4,
h5,
h6 {
  line-height: 1.2;
  font-weight: 600;
  color: var(--color-text);
}

h1 {
  font-size: 2.25rem;
}

h2 {
  font-size: 1.875rem;
}

h3 {
  font-size: 1.5rem;
}

h4 {
  font-size: 1.25rem;
}

h5 {
  font-size: 1.125rem;
}

h6 {
  font-size: 1rem;
}

p {
  margin-bottom: var(--space-md);
}

small {
  font-size: var(--font-size-sm);
  color: var(--color-text-muted);
}

a {
  color: var(--color-primary);
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
```

<!-- TOC --><a name="tokens-versus-typography"></a>
#### Tokens versus Typography

Design Tokens definiëren waarden.

```css
:root {
  --font-base: "Inter", sans-serif;

  --font-size-sm: 0.875rem;
  --font-size-md: 1rem;
  --font-size-lg: 1.125rem;
}
```

Typography gebruikt deze waarden.

```css
body {
  font-family: var(--font-base);
  font-size: var(--font-size-md);
}
```

Voeg nieuwe waarden altijd toe aan `tokens.css`.

Gebruik deze vervolgens in `typography.css`.

<!-- TOC --><a name="themes"></a>
### Themes

Themes overschrijven uitsluitend tokenwaarden.
Nooit component-specifieke styling toevoegen aan een theme.

<!-- TOC --><a name="brand-themes"></a>
#### Brand Themes

<!-- TOC --><a name="greencss"></a>
##### green.css

```css
[data-brand="green"] {
  --color-primary: #16a34a;
}
```

<!-- TOC --><a name="redcss"></a>
##### red.css

```css
[data-brand="red"] {
  --color-primary: #dc2626;
}
```

<!-- TOC --><a name="appearance-themes"></a>
#### Appearance Themes

<!-- TOC --><a name="darkcss"></a>
##### dark.css

```css
[data-mode="dark"] {
  --color-surface: #111827;
  --color-surface-alt: #1f2937;

  --color-text: #f9fafb;
  --color-text-muted: #cbd5e1;
}
```

<!-- TOC --><a name="lightcss"></a>
##### light.css

```css
[data-mode="light"] {
  --color-surface: #ffffff;
  --color-surface-alt: #f8fafc;

  --color-text: #111827;
  --color-text-muted: #64748b;
}
```

<!-- TOC --><a name="gebruik-van-de-themes"></a>
#### Gebruik van de Themes

Branding:

```html
<html data-brand="green"></html>
```

Dark mode:

```html
<html data-mode="dark"></html>
```

Combineren:

```html
<html data-brand="green" data-mode="dark"></html>
```

Hiermee kan iedere branding gecombineerd worden met iedere appearance mode.

<!-- TOC --><a name="bootstrap"></a>
### Bootstrap

Bootstrap wordt gekoppeld aan onze Design Tokens.
Hierdoor zijn weinig tot geen overrides nodig.

<!-- TOC --><a name="bootstrap-tokenscss"></a>
#### bootstrap-tokens.css

```css
:root {
  --bs-primary: var(--color-primary);
  --bs-secondary: var(--color-secondary);
  --bs-success: var(--color-success);
  --bs-warning: var(--color-warning);
  --bs-danger: var(--color-danger);
  --bs-body-font-family: var(--font-base);
  --bs-border-radius: var(--radius-md);
}
```

<!-- TOC --><a name="bootstrap-richtlijnen"></a>
#### Bootstrap Richtlijnen

<!-- TOC --><a name="wel-doen"></a>
##### Wel doen

```html
<button class="btn btn-primary">Opslaan</button>
```

<!-- TOC --><a name="niet-doen"></a>
##### Niet doen

```css
.btn-primary {
  background: red;
}
```

Bootstrap componenten halen hun styling uit tokens.
Niet andersom.

<!-- TOC --><a name="bootstrap-architectuur"></a>
#### Bootstrap architectuur

Correct:

```text
Design Tokens
        ↓
Theme Overrides
        ↓
Bootstrap Variables
        ↓
Bootstrap Components
```

Vermijden:

```text
Bootstrap Components
        ↓
Custom Overrides
        ↓
Meer Overrides
        ↓
Nog Meer Overrides
```

<!-- TOC --><a name="utilities"></a>
### Utilities

De boilerplate probeert geen utility framework (als UnoCSS of Tailwind) na te bootsen.
Dus utilities zoveel mogelijk beperkt proberen te houden.
Gebruik utilities uitsluitend voor veelvoorkomende patronen.

<!-- TOC --><a name="voorbeelden"></a>
#### Voorbeelden

```css
.u-hidden {
  display: none;
}

.u-flex {
  display: flex;
}

.u-flex-column {
  flex-direction: column;
}

.u-text-center {
  text-align: center;
}

.u-gap-sm {
  gap: var(--space-sm);
}

.u-gap-md {
  gap: var(--space-md);
}
```

<!-- TOC --><a name="algemene-regels"></a>
### Algemene regels

<!-- TOC --><a name="gebruik"></a>
#### Gebruik

- CSS Custom Properties
- CSS Layers
- Design Tokens
- Bootstrap via tokens
- Semantische naamgeving

<!-- TOC --><a name="vermijd"></a>
#### Vermijd

- Hardcoded kleuren
- Hardcoded spacing
- Grote Bootstrap overrides
- `!important`
- Hoge specificity

<!-- TOC --><a name="css-conventies"></a>
### CSS Conventies

Richtlijnen om de styling consistent, schaalbaar en onderhoudbaar te houden.

<!-- TOC --><a name="rem-voor-schaalbare-waarden"></a>
#### `rem` voor schaalbare waarden

Voor layout, spacing en typography gebruiken we standaard `rem` in plaats van `px`.
Voor spacing en typografie gebruiken we altijd tokens en `rem`.

Waarom:

- Respecteert browser font instellingen.
- Verbetert toegankelijkheid.
- Zorgt voor schaalbare layouts.
- Past beter binnen een design system.

Goed:

```css
.card {
  padding: 1rem;
  border-radius: 0.5rem;
}

h1 {
  font-size: 2.25rem;
}
```

Vermijden:

```css
.card {
  padding: 16px;
}

h1 {
  font-size: 36px;
}
```

<!-- TOC --><a name="wanneer-wél-px"></a>
##### Wanneer wél px

`px` is niet verboden. Gebruik het alleen wanneer een vaste technische waarde gewenst is.

Goed:

```css
.divider {
  border-bottom: 1px solid;
}
```

of:

```css
.icon {
  width: 24px;
  height: 24px;
}
```

Maar dus niet:

```css
.container {
  padding: 24px;
  margin-top: 32px;
}
```

<!-- TOC --><a name="gebruik-design-tokens-voor-herhaalbare-waarden"></a>
#### Gebruik Design Tokens voor herhaalbare waarden

Nieuwe globale waarden worden toegevoegd aan:

```text
core/tokens.css
```

Goed:

```css
.card {
  padding: var(--space-md);
  color: var(--color-text);
  border-radius: var(--radius-md);
}
```

Vermijden:

```css
.card {
  padding: 1rem;
  color: #111827;
  border-radius: 8px;
}
```

Een waarde die op meerdere plekken voorkomt hoort een token te zijn.

<!-- TOC --><a name="gebruik-geen-vaste-kleuren-in-styling"></a>
#### Gebruik geen vaste kleuren in styling

Goed:

```css
.button {
  background: var(--color-primary);
}
```

Vermijden:

```css
.button {
  background: #2563eb;
}
```

Alle kleuren komen uit:

```text
core/tokens.css
```

of

```text
themes/
```

<!-- TOC --><a name="houd-specificity-laag"></a>
#### Houd specificity laag

Gebruik eenvoudige selectors.

Goed:

```css
.card {
  padding: var(--space-md);
}
```

Vermijden:

```css
.page .content .card div span {
  padding: 1rem;
}
```

Vermijd oplopende specificity problemen en gebruik geen `!important`.

<!-- TOC --><a name="gebruik-moderne-css-mogelijkheden"></a>
#### Gebruik moderne CSS mogelijkheden

Denk aan:

- CSS Custom Properties
- CSS Layers
- Flexbox
- CSS Grid
- Container Queries
- `clamp()`
- `min()`
- `max()`
- `aspect-ratio`

Voorbeeld:

```css
.title {
  font-size: clamp(1.5rem, 2vw, 2.5rem);
}
```

<!-- TOC --><a name="mobile-first"></a>
#### Mobile first

Nieuwe styling wordt standaard mobile-first opgebouwd.

Goed:

```css
.card {
  padding: 1rem;
}

@media (min-width: 48rem) {
  .card {
    padding: 2rem;
  }
}
```

Niet:

```css
.card {
  padding: 2rem;
}

@media (max-width: 48rem) {
  .card {
    padding: 1rem;
  }
}
```
