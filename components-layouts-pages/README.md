## Components, Layouts en Pages

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Structuur](#structuur)
- [Components](#components)
   * [Component lagen](#component-lagen)
      + [Common Components](#common-components)
      + [UI Components](#ui-components)
      + [Feature Components](#feature-components)
- [Layouts](#layouts)
   * [Paar voorbeelden van layouts](#paar-voorbeelden-van-layouts)
      + [MainLayout](#mainlayout)
      + [AuthLayout](#authlayout)
      + [EmptyLayout](#emptylayout)
- [Pages](#pages)
   * [Pages versus Components](#pages-versus-components)
- [Razor markup](#razor-markup)
- [Styling](#styling)
- [Component parameters](#component-parameters)
- [Component composition](#component-composition)
- [Component naming](#component-naming)
- [Accessibility](#accessibility)
- [Testing](#testing)

<!-- TOC end -->

---

Op hoofdlijnen opgebouwd vanuit drie onderdelen:

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

<!-- TOC --><a name="structuur"></a>
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

<!-- TOC --><a name="components"></a>
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

<!-- TOC --><a name="component-lagen"></a>
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

<!-- TOC --><a name="common-components"></a>
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

<!-- TOC --><a name="ui-components"></a>
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

<!-- TOC --><a name="feature-components"></a>
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

<!-- TOC --><a name="layouts"></a>
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

<!-- TOC --><a name="paar-voorbeelden-van-layouts"></a>
#### Paar voorbeelden van layouts

<!-- TOC --><a name="mainlayout"></a>
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

<!-- TOC --><a name="authlayout"></a>
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

<!-- TOC --><a name="emptylayout"></a>
##### EmptyLayout

Minimale layout zonder standaard UI.

Gebruik voor:

- Speciale schermen
- Volledige schermweergaven
- Custom flows

<!-- TOC --><a name="pages"></a>
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

<!-- TOC --><a name="pages-versus-components"></a>
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

<!-- TOC --><a name="razor-markup"></a>
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

<!-- TOC --><a name="styling"></a>
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

<!-- TOC --><a name="component-parameters"></a>
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

<!-- TOC --><a name="component-composition"></a>
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

<!-- TOC --><a name="component-naming"></a>
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

<!-- TOC --><a name="accessibility"></a>
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

<!-- TOC --><a name="testing"></a>
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
