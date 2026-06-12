## Styles

To continue

## Javascript

De map wwwroot/js/ bevat enkel gescheiden ES6-modules: interop/ voor onze C#-wrappers (focus, theming) en libs/ voor externe dependencies. Geen van deze bestanden mag direct in HTML worden geladen; alle logica loopt uitsluitend via gedefinieerde C#-services in de Services-map. Dit zorgt ervoor dat backend developers nooit de DOM manipuleren en volledige type-safety behouden binnen Blazor.

### Structuur

```
/src/
├── ...
├── Interop/
│   ├── ScrollInterop.cs
│   └── ...
└── wwwroot/
    ├── css (🚫 Gecompileerde output)
    ├── js/
    │   ├── clipboard/
    │   │   ├── copy.js
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
    │       ├── echarts.js
    │   │   ├── index.js
    │       └── ...
    └── lib/
        ├── bootstrap/
        │   ├── bootstrap.min.css
        │   ├── bootstrap.bundle.min.js
        │   └── ...
        └── jquery/
            ├── jquery.min.js
            └── ...
```

### Voorbeeld

#### 1. JS feature module

```js
// src/wwwroot/js/scroll/scrollToElement.js
export function scrollToElement(id) {
  const el = document.getElementById(id);
  if (!el) return;

  el.scrollIntoView({ behavior: "smooth", block: "center" });
}
```

```js
// src/wwwroot/js/scroll/scrollToTop.js
export function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth",
  });
}
```

#### 2. Index.js (barrel / aggregator)

```js
import { scrollToElement } from "./scrollToElement.js";
import { scrollToTop } from "./scrollToTop.js";

export { scrollToElement, scrollToTop };
```

#### 3. C# Interop service

```cs
// src/Interop/ScrollInterop

using Microsoft.JSInterop;

public class ScrollInterop
{
    private readonly IJSRuntime _js;
    private IJSObjectReference? _module;

    public ScrollInterop(IJSRuntime js)
    {
        _js = js;
    }

    private async Task<IJSObjectReference> Module()
    {
        if (_module is not null)
            return _module;

        _module = await _js.InvokeAsync<IJSObjectReference>(
            "import", "./js/features/scroll/index.js");

        return _module;
    }

    public async Task ScrollToElement(string elementId)
    {
        var module = await Module();
        await module.InvokeVoidAsync("scrollToElement", elementId);
    }

    public async Task ScrollToTop()
    {
        var module = await Module();
        await module.InvokeVoidAsync("scrollToTop");
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

#### 4. DI registratie

```cs
// program.cs
builder.Services.AddScoped<ScrollInterop>();
```

#### 5. Gebruik in Blazor component

```cshtml
@inject ScrollInterop Scroll

<button @onclick="GoToSection">
    Ga naar sectie
</button>

<div style="height: 1000px;"></div>

<h2 id="targetSection">Doel sectie</h2>

@code {
    private async Task GoToSection()
    {
        await Scroll.ScrollToElement("targetSection");
    }
}
```

## Testen

### Packages

| Package                                        | Doel                                                                                                      |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| [`bUnit`](https://bunit.dev/index.html)        | Testframework voor het schrijven en uitvoeren van unit- en componenttests voor Blazor-componenten in .NET |
| [`playwright`](https://playwright.dev/dotnet/) | Framework voor end-to-end browserautomatisering en UI-tests voor webapplicaties in meerdere browsers      |

#### bUnit (Component)

bUnit is een testframework waarmee je unit- en componenttests kunt schrijven en uitvoeren voor Blazor-componenten in .NET. Het maakt het mogelijk om Blazor UI-componenten te testen in een gesimuleerde omgeving zonder dat een echte browser nodig is. Hierdoor kun je gedrag, rendering en interacties van componenten snel en geïsoleerd valideren.

##### Structuur

```
/src/
└── ...
/tests/
├── Unit/
│   └── ...
├── Component/
│   ├── Layout/
│   │   ├── MainLayoutTests.cs
│   │   └── ...
│   ├── Shared/
│   │   ├── Button/
│   │   │   ├── ButtonTests.cs
│   │   │   └── ...
│   │   ├── Input/
│   │   │   ├── TextInputTests.cs
│   │   │   └── ...
│   │   └── Modal/
│   │       ├── ModalTests.cs
│   │       └── ...
│   ├── Forms/
│   │   ├── CreateUserFormTests.cs
│   │   └── ...
│   └── Pages/
│       └── Users/
│           ├── UserIndexPageTests.cs
│           └── ...
└── E2E/
│   └── ...
```

##### Voorbeeld test:

```cs
// tests/Component/Shared/Button/ButtonTests.cs

public class ButtonTests : TestContext
{
    [Fact]
    public void Render_WithPrimaryVariant_AddsCorrectClass()
    {
        // Arrange & Act
        var cut = RenderComponent<App.Button>(parameters => parameters
            .Add(p => p.Variant, "primary")
            .Add(p => p.Text, "Click Me")
        );

        // Assert
        var buttonElement = cut.Find("button");

        // Check CSS classes (gebruik string matching of ClassList)
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
            element.ClassList.Should().Contain("btn-disabled"); // Je custom class
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

        // Act
        await cut.Find("button").ClickAsync();

        // Assert
        clicked.Should().BeTrue();
    }
}
```

#### Playwright (E2E)

Playwright is een framework voor end-to-end browserautomatisering en UI-tests van webapplicaties. Binnen .NET biedt het een krachtige C#-API voor het testen van applicaties in Chromium, Firefox en WebKit. Dankzij ingebouwde automatische wachttijden en goede integratie met MSTest, NUnit en xUnit kunnen geautomatiseerde tests eenvoudig worden ontwikkeld en uitgevoerd.

##### Structuur

```
/src/
└── ...
/tests/
├── unit/
│   └── ...
├── component/
│   └── ...
└── e2e/
    ├── Fixtures/
    │   ├── PlaywrightFixture.cs
    │   └── WebApplicationFixture.cs
    │   └── ...
    ├── Pages/
    │   ├── LoginPage.cs
    │   └── DashboardPage.cs
    │   └── ...
    ├── Tests/
    │   ├── Authentication/
    │   ├── Users/
    │   └── Orders/
    │   └── ...
    ├── TestData/
    │   ├── Users/
    │   └── Orders/
    │   └── ...
    ├── Helpers/
    │   ├── ScreenshotHelper.cs
    │   └── Waithelper.cs
    │   └── ...
    ├── Constants/
    │   ├── Routes.cs
    │   └── TestUsers.cs
    │   └── ...
    ├── appsettings.json
    ├── appsettings.Local.json
    └── e2e.tests.csproj
```

##### Fixtures

Gedeelde testsetup en opruimlogica

- Browser starten/sluiten
- Testomgeving configureren
- Applicatie opstarten
- Database resetten
- Resources delen tussen tests

voorbeeld:

```
PlaywrightFixture
→ Chromium starten

WebApplicationFixture
→ Testserver starten
```

##### Pages

Page Objects: representatie van schermen in de applicatie

Hier wil je alle kennis van de HTML en CSS-selectors centraliseren.

Bevat:

- Selectors
- Klikacties
- Formulieracties
- Paginaspecifieke controles

```
LoginPage
→ Email invullen
→ Wachtwoord invullen
→ Login klikken
```

##### Tests

De daadwerkelijke E2E-scenario's

Georganiseerd per functionaliteit of domein. Een test beschrijft wat een gebruiker doet en verwacht.

Voorbeelden:

```
Authentication
→ LoginTests
→ LogoutTests

Users
→ UserCrudTests

Orders
→ OrderFlowTests
```

##### TestData

Herbruikbare testgegevens

Voorbeelden:

```
AdminUser.json
StandardUser.json
SampleOrder.json
```

Voordelen:

- Geen hardcoded waarden in tests
- Makkelijk onderhoudbaar
- Herbruikbaar tussen meerdere tests

##### Helpers

Generieke hulpmiddelen

Functionaliteit die niet bij een specifieke pagina hoort.
Als meerdere pagina's of tests dezelfde logica gebruiken, hoort die vaak hier thuis.

Voorbeelden

```
ScreenshotHelper
→ Screenshot maken bij fouten

WaitHelper
→ Wachten op laadschermen

RandomDataHelper
→ Testdata genereren
```

##### Constants

Vaste waarden voor de hele testsuite. Voorkomt "magic strings" verspreid door de code.

Voorbeelden:

```c#
public static class Routes
{
    public const string Login = "/login";
    public const string Dashboard = "/dashboard";
}
```

```c#
public static class TestUsers
{
    public const string AdminEmail = "admin@test.local";
}
```

##### appsettings.json

Standaard configuratie

Voorbeeld:

```json
{
  "BaseUrl": "https://localhost:5001",
  "Browser": "Chromium",
  "Headless": true
}
```

##### appsettings.Local.json

Lokale overrides

Voorbeeld:

```json
{
  "Headless": false
}
```
