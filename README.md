## Components, Layouts en Pages

Opgebouwd vanuit drie onderdelen:

- Components
- Layouts
- Pages

De architectuur is opgebouwd vanuit een component-first aanpak waarbij UI wordt opgebouwd uit kleine, herbruikbare onderdelen.

De belangrijkste uitgangspunten:

- Herbruikbare UI componenten
- Duidelijke scheiding tussen bouwblokken, layouts en schermen
- Voorspelbare structuur voor nieuwe applicaties
- Consistente gebruikersinterface
- Minimale overlap tussen componenten
- Composition over inheritance

### Structuur

De frontend UI structuur bestaat uit drie hoofdonderdelen:

```text
Components
    ↓
Layouts
    ↓
Pages
```

- Componenten vormen de herbruikbare UI bouwblokken.

- Layouts bepalen de globale applicatiestructuur.

- Pages combineren componenten tot complete schermen.

```text
/src/
├── ...
│
├── Components/
│   ├── Common/
│   │   ├── Button/
│   │   │   ├── AppButton.razor
│   │   │   ├── AppButton.razor.cs
│   │   │   └── AppButton.razor.css
│   │   │
│   │   ├── Card/
│   │   │   ├── AppCard.razor
│   │   │   └── AppCard.razor.css
│   │   │
│   │   ├── Modal/
│   │   │   ├── AppModal.razor
│   │   │   └── AppModal.razor.css
│   │   │
│   │   └── ...
│   │
│   ├── Forms/
│   │   ├── Input/
│   │   │   ├── TextInput.razor
│   │   │   ├── SelectInput.razor
│   │   │   └── CheckboxInput.razor
│   │   │
│   │   └── ...
│   │
│   ├── Feedback/
│   │   ├── Alert/
│   │   ├── Toast/
│   │   ├── Loading/
│   │   ├── EmptyState/
│   │   └── ...
│   │
│   ├── Navigation/
│   │   ├── Breadcrumbs/
│   │   ├── Tabs/
│   │   ├── Pagination/
│   │   └── ...
│   │
│   ├── DataDisplay/
│   │   ├── Table/
│   │   ├── Badge/
│   │   ├── Avatar/
│   │   ├── Status/
│   │   └── ...
│   │
│   └── Layout/
│       ├── Header/
│       ├── Sidebar/
│       ├── Footer/
│       └── ...
│
├── Layouts/
│   ├── MainLayout.razor
│   ├── AuthLayout.razor
│   ├── EmptyLayout.razor
│   └── ...
│
├── Pages/
│   ├── Home/
│   │   ├── Home.razor
│   │   └── Home.razor.css
│   │
│   ├── Error/
│   │   ├── Error.razor
│   │   └── Error.razor.css
│   │
│   └── ...
│
└── ...
```

### Components

Components zijn de herbruikbare bouwblokken van de gebruikersinterface.

Een component is verantwoordelijk voor:

- UI rendering
- Presentatie
- Gebruikersinteractie
- Compositie van andere componenten
- Lokale UI state

Components bevatten geen:

- Businesslogica
- API communicatie
- Data toegang
- Applicatiebrede state
- Directe JavaScript interactie

#### Component lagen

De componenten zijn verdeeld in verschillende lagen.

```text
Common Components

        ↓

UI Components

        ↓

Feature Components
```

Elke laag heeft een eigen verantwoordelijkheid.

##### Common Components

Common components zijn volledig generieke UI bouwblokken.

Deze componenten bevatten:

- Geen domeinkennis
- Geen applicatielogica
- Geen pagina-specifieke kennis

Voorbeelden:

```text
Button
Card
Modal
Dropdown
Icon
Badge
Tooltip
```

Gebruik:

```razor
<AppButton Variant="Primary">
    Opslaan
</AppButton>
```

Niet:

```razor
<UserSaveButton />
```

Een Common component weet niet waarvoor hij gebruikt wordt.

##### UI Components

UI components combineren meerdere Common components tot herbruikbare patronen.

Voorbeelden:

```text
SearchBar
DataTable
FilterPanel
FormSection
ConfirmationDialog
```

Een UI component beschrijft een algemeen interactiepatroon.

Voorbeeld:

```razor
<DataTable />

<UserTable />

<OrderTable />
```

Specifieke toepassingen gebruiken dezelfde UI bouwblokken.

##### Feature Components

Feature components zijn specifieke UI onderdelen binnen een applicatie.

Deze componenten mogen domeinspecifieke termen bevatten.

Voorbeeld:

```text
Users/

    UserTable.razor
    UserFilter.razor
    UserDetails.razor
```

Feature components gebruiken:

```text
Common Components

        ↓

UI Components
```

maar andersom niet.

### Layouts

- Layouts definiëren de globale structuur van de applicatie.
- Een Layout bevat onderdelen die op meerdere pagina's voorkomen.
- Een Layout bepaalt niet de inhoud van een pagina.
- De inhoud wordt via `@Body` geplaatst.

Voorbeeld:

```razor
<div class="app-layout">

    <Header />

    <Sidebar />


    <main>

        @Body

    </main>

</div>
```

#### Paar voorbeelden van layouts

##### MainLayout

De standaard applicatielayout.

Gebruik voor:

- Applicaties met navigatie
- Dashboards
- Business applicaties

Voorbeeld:

```razor
@inherits LayoutComponentBase


<AppHeader />


<AppSidebar />


<main>

    @Body

</main>
```

##### AuthLayout

Layout voor schermen zonder standaard applicatiestructuur.

Voorbeelden:

```text
Login
Forgot password
Register
```

Voorbeeld:

```razor
<div class="auth-layout">

    @Body

</div>
```

##### EmptyLayout

Minimale layout zonder standaard UI.

Gebruik voor:

- Speciale schermen
- Volledige schermweergaven
- Custom flows

### Pages

Pages zijn routebare onderdelen van de applicatie.

Een Page:

- Heeft een `@page` directive
- Vertegenwoordigt een scherm
- Combineert componenten
- Gebruikt layouts

Voorbeeld:

```razor
@page "/users"


<PageHeader Title="Users" />


<UserFilter />


<UserTable />
```

Een Page bevat geen grote hoeveelheden markup.

Niet:

```razor
@page "/users"


<table>

    honderden regels markup

</table>
```

Grote UI onderdelen worden altijd opgesplitst in Components.

#### Pages versus Components

Pages:

- Hebben routing
- Zijn volledige schermen
- Bepalen compositie

Components:

- Zijn herbruikbare UI onderdelen
- Hebben geen routing
- Kunnen op meerdere plekken gebruikt worden

Voorbeeld:

```text
Pages

    Users.razor

        ↓


Components

    UserTable.razor

    UserFilter.razor
```

### Razor markup

De `.razor` file bevat uitsluitend presentatie.

Voorbeeld:

```razor
<button class="@CssClass"
        disabled="@Disabled">

    @ChildContent

</button>
```

Componentlogica staat in een partial class.

Voorbeeld:

```csharp
public partial class AppButton
{
    [Parameter]
    public RenderFragment? ChildContent { get; set; }


    [Parameter]
    public bool Disabled { get; set; }


    private string CssClass =>
        Disabled
            ? "btn disabled"
            : "btn";
}
```

Voordelen:

- Kleinere Razor bestanden
- Betere leesbaarheid
- Betere testbaarheid
- Minder merge conflicten

### Styling

Component-specifieke styling gebruikt CSS isolation.

Structuur:

```text
Button/

    AppButton.razor

    AppButton.razor.css
```

Component styling gebruikt:

- CSS Custom Properties
- Design tokens
- Globale styling afspraken

Niet:

```css
.button {
  background: #2563eb;
}
```

Wel:

```css
.button {
  background: var(--color-primary);
}
```

### Component parameters

Component API's blijven klein en voorspelbaar.

Goed:

```csharp
[Parameter]
public string Text { get; set; } = "";


[Parameter]
public EventCallback OnClick { get; set; }
```

Vermijden:

```csharp
[Parameter]
public ApplicationConfiguration Config { get; set; }
```

Een component ontvangt alleen wat het nodig heeft.

### Component composition

Componenten worden opgebouwd door compositie.

Goed:

```razor
<AppCard>

    <UserAvatar />

    <UserInformation />

</AppCard>
```

Vermijden:

```razor
<UserCardWithEverything />
```

Kleine componenten combineren tot grotere patronen.

### Component naming

Componentnamen beschrijven het doel.

Goed:

```text
Button
Modal
UserCard
OrderTable
SearchInput
```

Vermijden:

```text
GenericComponent
BaseComponent
HelperComponent
CustomButtonComponent
```

Een `.razor` bestand is al een component.

### Accessibility

Alle UI onderdelen moeten standaard toegankelijk zijn.

Richtlijnen:

Gebruik:

- Semantische HTML
- Correcte ARIA attributen
- Keyboard navigatie
- Focus management

Goed:

```html
<button>Opslaan</button>
```

Vermijden:

```html
<div onclick="save()">Opslaan</div>
```

### Testing

Componenttests staan los van de component implementatie.

De tests volgen dezelfde functionele structuur.

Voorbeeld:

```text
Components/

    Common/

        Button/

            AppButton.razor


Tests/

    Component/

        Common/

            Button/

                AppButtonTests.cs
```

Componenttests gebruiken `bUnit`.

Test:

- Rendering
- Parameters
- Events
- Interactie
- Component gedrag

Niet testen:

- API gedrag
- Businessregels

## Styles

Een CSS-first (dus geen Sass/SCSS of andere pre-processors) architectuur waarbij Bootstrap beschikbaar is voor standaard componenten en bekende patronen, maar niet de eigenaar is van het design.

De belangrijkste uitgangspunten

- Bootstrap gebruiken zonder grote hoeveelheden overrides.
- Ondersteuning voor meerdere branding-thema's.
- Ondersteuning voor light en dark mode.
- Native CSS boven Sass/SCSS.
- Voorspelbare cascade.
- Toekomstbestendig door gebruik van moderne CSS-features.

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
        ├── bootstrap/
        │   └── bootstrap-bridge.css
        └── utilities/
            ├── accessibility.css
            ├── display.css
            ├── spacing.css
            └── ...
```

### Architectuur

De styling is opgebouwd uit verschillende lagen:

```text
Design Tokens
        ↓
Themes
        ↓
Bootstrap Bridge
        ↓
Application Styling
        ↓
Utilities
```

- Design Tokens definiëren welke waarden beschikbaar zijn.
- Themes overschrijven alleen tokenwaarden.
- Bootstrap consumeert tokens.
- Applicaties gebruiken tokens.
- Globale CSS bevat uitsluitend infrastructuur.

### Richtlijnen

Een CSS-first (dus geen Sass/SCSS of andere pre-processors) architectuur waarbij Bootstrap beschikbaar is voor standaard componenten en bekende patronen, maar niet de eigenaar is van het design.

- Bootstrap gebruiken zonder grote hoeveelheden overrides.
- Ondersteuning voor meerdere branding-thema's.
- Ondersteuning voor light en dark mode.
- Native CSS boven Sass/SCSS.
- Voorspelbare cascade.
- Toekomstbestendig door gebruik van moderne CSS-features.

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

### Themes

Themes overschrijven uitsluitend tokenwaarden.
Nooit component-specifieke styling toevoegen aan een theme.

#### Brand Themes

##### green.css

```css
[data-brand="green"] {
  --color-primary: #16a34a;
}
```

##### red.css

```css
[data-brand="red"] {
  --color-primary: #dc2626;
}
```

#### Appearance Themes

##### dark.css

```css
[data-mode="dark"] {
  --color-surface: #111827;
  --color-surface-alt: #1f2937;

  --color-text: #f9fafb;
  --color-text-muted: #cbd5e1;
}
```

##### light.css

```css
[data-mode="light"] {
  --color-surface: #ffffff;
  --color-surface-alt: #f8fafc;

  --color-text: #111827;
  --color-text-muted: #64748b;
}
```

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

### Bootstrap

Bootstrap wordt gekoppeld aan onze Design Tokens.
Hierdoor zijn weinig tot geen overrides nodig.

#### bootstrap-bridge.css

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

#### Bootstrap Richtlijnen

##### Wel doen

```html
<button class="btn btn-primary">Opslaan</button>
```

##### Niet doen

```css
.btn-primary {
  background: red;
}
```

Bootstrap componenten halen hun styling uit tokens.
Niet andersom.

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

### Utilities

De boilerplate probeert geen utility framework (als UnoCSS of Tailwind) na te bootsen.
Dus utilities zoveel mogelijk beperkt proberen te houden.
Gebruik utilities uitsluitend voor veelvoorkomende patronen.

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

### Algemene regels

#### Gebruik

- CSS Custom Properties
- CSS Layers
- Design Tokens
- Bootstrap via tokens
- Semantische naamgeving

#### Vermijd

- Hardcoded kleuren
- Hardcoded spacing
- Grote Bootstrap overrides
- `!important`
- Hoge specificity

### CSS Conventies

Richtlijnen om de styling consistent, schaalbaar en onderhoudbaar te houden.

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

## JavaScript

De map `wwwroot/js` bevat uitsluitend ES6-modules voor browser-specifieke functionaliteit en integraties met externe JavaScript libraries. Deze modules worden niet rechtstreeks vanuit Blazor-componenten aangeroepen, maar uitsluitend via C# Interop-services.

Door JavaScript te isoleren achter een Interop-laag blijft applicatielogica in C#, behouden we type-safety binnen Blazor en voorkomen we verspreide DOM-manipulatie door de applicatie.

### Structuur

```text
/src/
├── ...
├── Interop/
│   ├── ClipboardInterop.cs
│   ├── ScrollInterop.cs
│   ├── ChartInterop.cs
│   └── ...
└── wwwroot/
    ├── ...
    ├── js/
    │   ├── clipboard/
    │   │   ├── copy.js
    │   │   ├── paste.js
    │   │   ├── index.js
    │   │   └── ...
    │   ├── scroll/
    │   │   ├── scrollToElement.js
    │   │   ├── scrollToTop.js
    │   │   ├── index.js
    │   │   └── ...
    │   ├── dom/
    │   │   ├── focus.js
    │   │   ├── visibility.js
    │   │   ├── index.js
    │   │   └── ...
    │   ├── media/
    │   │   ├── fileUpload.js
    │   │   ├── download.js
    │   │   ├── index.js
    │   │   └── ...
    │   └── visuals/
    │       ├── highcharts.js
    │       ├── index.js
    │       └── ...
    └── lib/
        ├── bootstrap/
        │   ├── bootstrap.min.css
        │   ├── bootstrap.bundle.min.js
        │   └── ...
        ├── highcharts/
        │   └── ...
        └── ...
```

### Richtlijnen

- JavaScript uitsluitend voor browser/DOM-specifieke functionaliteit die niet beschikbaar is in Blazor.
- JavaScript mag nooit direct vanuit een component worden aangeroepen via `IJSRuntime`.
- Alle JavaScript-functionaliteit wordt ontsloten via een dedicated Interop-service.
- Elke feature heeft een eigen map met een `index.js` als publiek toegangspunt.
- DOM-manipulatie buiten een Interop-service vermijden.
- Nieuwe externe libraries worden altijd ingekapseld achter een C#-wrapper.
- Businesslogica blijft altijd in de C#.
- JavaScript modules lazy laden via `import()`.

### Wanneer JavaScript gebruiken?

Gebruik JavaScript alleen voor functionaliteit die:

- Direct toegang vereist tot browser API's
- DOM-manipulatie vereist
- Externe JavaScript libraries gebruikt
- Browser events afhandelt die niet beschikbaar zijn in Blazor
- Bestand uploads of downloads faciliteert
- Scroll-, focus- of viewport-functionaliteit verzorgt

JavaScript vermijden voor:

- Businesslogica
- Data-transformatie
- Validatie
- State management (met uitzondering van zaken als bijv. local storage of cookies)
- Autorisatie- of authenticatielogica

### Module loading

JavaScript modules worden lazy geladen via `import()` wanneer een functionaliteit voor het eerst wordt gebruikt.

Voordelen:

- Lagere initiële laadtijd
- Geen globale JavaScript objecten
- Betere encapsulatie
- Automatische code-splitting
- Minder geheugenverbruik

### Voorbeeld

#### 1. JavaScript feature module

```javascript
// wwwroot/js/scroll/scrollToElement.js

export function scrollToElement(id) {
  const element = document.getElementById(id);

  if (!element) {
    return;
  }

  element.scrollIntoView({
    behavior: "smooth",
    block: "center",
  });
}
```

```javascript
// wwwroot/js/scroll/scrollToTop.js

export function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth",
  });
}
```

#### 2. Barrel export (index.js)

```javascript
// wwwroot/js/scroll/index.js

import { scrollToElement } from "./scrollToElement.js";
import { scrollToTop } from "./scrollToTop.js";

export { scrollToElement, scrollToTop };
```

#### 3. C# Interop service

```csharp
using Microsoft.JSInterop;

public sealed class ScrollInterop : IAsyncDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private IJSObjectReference? _module;

    public ScrollInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    private async Task<IJSObjectReference> GetModuleAsync()
    {
        if (_module is not null)
        {
            return _module;
        }

        _module = await _jsRuntime.InvokeAsync<IJSObjectReference>(
            "import",
            "./js/scroll/index.js");

        return _module;
    }

    public async Task ScrollToElementAsync(string elementId)
    {
        var module = await GetModuleAsync();

        await module.InvokeVoidAsync(
            "scrollToElement",
            elementId);
    }

    public async Task ScrollToTopAsync()
    {
        var module = await GetModuleAsync();

        await module.InvokeVoidAsync(
            "scrollToTop");
    }

    public async ValueTask DisposeAsync()
    {
        if (_module is not null)
        {
            await _module.DisposeAsync();
        }
    }
}
```

#### 4. Dependency Injection registratie

```csharp
builder.Services.AddScoped<ScrollInterop>();
```

#### 5. Gebruik in een Blazor component

```razor
@inject ScrollInterop Scroll

<button @onclick="GoToSection">
    Ga naar sectie
</button>

<div style="height: 1000px;"></div>

<h2 id="targetSection">
    Doel sectie
</h2>

@code {
    private async Task GoToSection()
    {
        await Scroll.ScrollToElementAsync(
            "targetSection");
    }
}
```

### Externe libraries

Externe JavaScript libraries proberen we nooit rechtstreeks vanuit componenten gebruikt.
Iedere library wordt afgeschermd achter een eigen Interop-service.

Voorbeeld:

```text
ChartInterop
→ Highcharts initialiseren

ClipboardInterop
→ Clipboard API gebruiken

FileUploadInterop
→ Browser upload functionaliteit
```

#### Voorbeeld

```csharp
public sealed class ChartInterop
{
    public async Task InitializeChartAsync(
        string elementId,
        ChartOptions options)
    {
        var module = await GetModuleAsync();

        await module.InvokeVoidAsync(
            "initializeChart",
            elementId,
            options);
    }
}
```

### Testbaarheid

Interop-services kunnen worden getest door `IJSRuntime` te mocken.

Businesslogica blijft in C# zodat deze met unit tests getest kan worden zonder afhankelijkheid van JavaScript.

Voordelen:

- Hogere testbaarheid
- Minder afhankelijkheid van browserfunctionaliteit
- Betere scheiding van verantwoordelijkheden
- Minder complexe componenttests
- Consistente architectuur binnen de applicatie

## Testen

### Packages

| Type            | Package                                        | Doel                                                                                                      |
| --------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Component tests | [`bUnit`](https://bunit.dev/index.html)        | Testframework voor het schrijven en uitvoeren van unit- en componenttests voor Blazor-componenten in .NET |
| E2E tests       | [`Playwright`](https://playwright.dev/dotnet/) | Framework voor end-to-end browserautomatisering en UI-tests voor webapplicaties in meerdere browsers      |

### Structuur

```text
/src/
└── ...

/tests/
├── Unit/
│   └── ...
├── Component/
    ├── Common/
    │   ├── Button/
    │   │   └── AppButtonTests.cs
    │   │
    │   ├── Card/
    │   │   └── AppCardTests.cs
    │   │
    │   └── Modal/
    │       └── AppModalTests.cs
    │
    ├── Forms/
    │   ├── InputTests.cs
    │   └── ValidationTests.cs
    │
    ├── Feedback/
    │   └── ToastTests.cs
    │
    ├── Navigation/
    │   └── TabsTests.cs
    │
    └── DataDisplay/
        └── TableTests.cs
└── E2E/
    ├── Fixtures/
    │   ├── PlaywrightFixture.cs
    │   ├── WebApplicationFixture.cs
    │   └── ...
    ├── Pages/
    │   ├── LoginPage.cs
    │   ├── DashboardPage.cs
    │   └── ...
    ├── Tests/
    │   ├── Authentication/
    │   │   ├── LoginTests.cs
    │   │   └── ...
    │   ├── Users/
    │   ├── Orders/
    │   └── ...
    ├── TestData/
    │   ├── Users/
    │   ├── Orders/
    │   └── ...
    ├── Helpers/
    │   ├── ScreenshotHelper.cs
    │   ├── WaitHelper.cs
    │   └── ...
    ├── Constants/
    │   ├── Routes.cs
    │   ├── TestUsers.cs
    │   └── ...
    ├── appsettings.json
    ├── appsettings.Local.json
    └── E2E.Tests.csproj
```

### Uitvoering

- Componenttests worden uitgevoerd bij iedere build.
- E2E-tests worden uitgevoerd tijdens CI/CD of tegen een testomgeving.

#### bUnit (Component)

bUnit is een testframework waarmee je unit- en componenttests kunt schrijven en uitvoeren voor Blazor-componenten in .NET. Het maakt het mogelijk om Blazor UI-componenten te testen in een gesimuleerde omgeving zonder dat een echte browser nodig is. Hierdoor kun je gedrag, rendering en interacties van componenten snel en geïsoleerd valideren.

##### Voorbeeldtest

```csharp
// tests/Component/Shared/Button/ButtonTests.cs

public class ButtonTests : TestContext
{
    [Fact]
    public void Render_WithPrimaryVariant_AddsCorrectClass()
    {
        var cut = RenderComponent<App.Button>(parameters => parameters
            .Add(p => p.Variant, "primary")
            .Add(p => p.Text, "Click Me")
        );

        var buttonElement = cut.Find("button");

        buttonElement.ClassList.Should().Contain("btn-primary");
        buttonElement.TextContent.Should().Be("Click Me");
    }

    [Theory]
    [InlineData(true)]
    [InlineData(false)]
    public void Render_WithDisabledState_ChangesButtonState(bool isDisabled)
    {
        var cut = RenderComponent<App.Button>(p => p
            .Add(pb => pb.IsDisabled, isDisabled)
        );

        var element = cut.Find("button");

        if (isDisabled)
        {
            element.HasAttribute("disabled").Should().BeTrue();
            element.ClassList.Should().Contain("btn-disabled");
        }
        else
        {
            element.HasAttribute("disabled").Should().BeFalse();
        }
    }

    [Fact]
    public async Task OnClick_InvokesCallback_WhenNotDisabled()
    {
        bool clicked = false;

        var cut = RenderComponent<App.Button>(p => p
            .Add(pb => pb.IsDisabled, false)
            .Add(pb => pb.OnClick, () => clicked = true)
        );

        await cut.Find("button").ClickAsync();

        clicked.Should().BeTrue();
    }
}
```

#### Playwright (E2E)

Playwright is een framework voor end-to-end browserautomatisering en UI-tests van webapplicaties. Playwright heeft een eigen .NET API voor het testen van applicaties in Chromium, Firefox en WebKit. Dankzij ingebouwde automatische wachttijden en goede integratie met MSTest, NUnit en xUnit kunnen geautomatiseerde tests eenvoudig worden ontwikkeld en uitgevoerd.

##### Fixtures

Fixtures bevatten gedeelde setup- en opruimlogica.

Denk aan zaken als:

- Browser starten en sluiten
- Testomgeving configureren
- Applicatie opstarten
- Database resetten
- Resources delen tussen tests

###### Voorbeeld

**PlaywrightFixture**
→ Chromium starten

```csharp
using Microsoft.Playwright;

public class PlaywrightFixture : IAsyncLifetime
{
    public IBrowser Browser { get; private set; } = null!;

    public async Task InitializeAsync()
    {
        var playwright = await Playwright.CreateAsync();

        Browser = await playwright.Chromium.LaunchAsync(new()
        {
            Headless = true
        });
    }

    public async Task DisposeAsync()
    {
        await Browser.CloseAsync();
    }
}
```

**WebApplicationFixture**
→ Testserver starten

```csharp
using Microsoft.AspNetCore.Mvc.Testing;

public class WebApplicationFixture
    : WebApplicationFactory<Program>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment("Testing");
    }
}
```

##### Pages

Page Objects bevatten alle interactie met een pagina. Hier wil je alle kennis van HTML- en CSS-selectors centraliseren.

Bevat:

- Selectors
- Klikacties
- Formulieracties
- Paginaspecifieke controles

###### Voorbeeld

```text
LoginPage
→ E-mail invullen
→ Wachtwoord invullen
→ Inloggen klikken
```

```csharp
using Microsoft.Playwright;

public class LoginPage
{
    private readonly IPage _page;

    public LoginPage(IPage page)
    {
        _page = page;
    }

    private ILocator Email =>
        _page.GetByLabel("Email");

    private ILocator Password =>
        _page.GetByLabel("Wachtwoord");

    private ILocator LoginButton =>
        _page.GetByRole(
            AriaRole.Button,
            new() { Name = "Inloggen" });

    public async Task LoginAsync(
        string email,
        string password)
    {
        await Email.FillAsync(email);
        await Password.FillAsync(password);
        await LoginButton.ClickAsync();
    }
}
```

##### Tests

De daadwerkelijke E2E-scenario's.

Tests worden georganiseerd per functionaliteit of domein. Een test beschrijft wat een gebruiker doet en verwacht.

###### Voorbeeld

```text
Authentication
→ LoginTests

Users
→ UserCrudTests

Orders
→ OrderFlowTests
```

```csharp
public class DashboardPage
{
    private readonly IPage _page;

    public DashboardPage(IPage page)
    {
        _page = page;
    }

    public ILocator WelcomeMessage =>
        _page.GetByText("Welkom");
}
```

```csharp
public class LoginTests
{
    [Fact]
    public async Task User_can_login()
    {
        var admin = UserTestData.LoadAdmin();

        await page.GotoAsync(Routes.Login);

        var loginPage = new LoginPage(page);

        await loginPage.LoginAsync(
            admin.Email,
            admin.Password);

        var dashboardPage =
            new DashboardPage(page);

        await Assertions.Expect(
            dashboardPage.WelcomeMessage)
            .ToBeVisibleAsync();
    }
}
```

##### TestData

Herbruikbare testdata los van de testcode.

###### Voorbeeld

**AdminUser.json**

```json
{
  "email": "admin@test.local",
  "password": "Password123!"
}
```

**UserTestData.cs**

```csharp
using System.Text.Json;

public static class UserTestData
{
    public static TestUser LoadAdmin()
    {
        var json = File.ReadAllText(
            "TestData/Users/AdminUser.json");

        return JsonSerializer.Deserialize<TestUser>(json)!;
    }
}
```

**Gebruik in een test**

```csharp
var admin = UserTestData.LoadAdmin();

await loginPage.LoginAsync(
    admin.Email,
    admin.Password);
```

Voordelen:

- Geen hardcoded waarden in tests
- Makkelijk onderhoudbaar
- Herbruikbaar tussen meerdere tests

##### Helpers

Generieke hulpmiddelen.

Functionaliteit die niet bij een specifieke pagina hoort. Als meerdere pagina's of tests dezelfde logica gebruiken, hoort die vaak hier thuis.

###### Voorbeelden

```text
ScreenshotHelper
→ Screenshot maken bij fouten

WaitHelper
→ Wachten op laadschermen

RandomDataHelper
→ Testdata genereren
```

**ScreenshotHelper.cs**

```csharp
using Microsoft.Playwright;

public static class ScreenshotHelper
{
    public static async Task SaveAsync(
        IPage page,
        string fileName)
    {
        await page.ScreenshotAsync(new()
        {
            Path = $"Screenshots/{fileName}.png",
            FullPage = true
        });
    }
}
```

**Gebruik**

```csharp
await ScreenshotHelper.SaveAsync(
    page,
    "LoginFailed");
```

##### Constants

Vaste waarden voor de hele testsuite. Dit voorkomt dat dezelfde waarden als magic strings verspreid door de code voorkomen.

###### Voorbeelden

```csharp
public static class Routes
{
    public const string Login = "/login";
    public const string Dashboard = "/dashboard";
}
```

```csharp
public static class TestUsers
{
    public const string AdminEmail = "admin@test.local";
}
```

##### appsettings.json

Standaardconfiguratie.

###### Voorbeeld

```json
{
  "BaseUrl": "https://localhost:5001",
  "Browser": "Chromium",
  "Headless": true
}
```

##### appsettings.Local.json

Lokale overrides voor ontwikkelaars.

###### Voorbeeld

```json
{
  "Headless": false
}
```
