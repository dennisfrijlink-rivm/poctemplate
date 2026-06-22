## Tests

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Packages](#packages)
- [Structuur](#structuur)
- [Uitvoering](#uitvoering)
   * [bUnit (Component)](#bunit-component)
      + [Voorbeeldtest](#voorbeeldtest)
   * [Playwright (E2E)](#playwright-e2e)
      + [Fixtures](#fixtures)
         - [Voorbeeld](#voorbeeld)
      + [Pages](#pages)
         - [Voorbeeld](#voorbeeld-1)
      + [Tests](#tests)
         - [Voorbeeld](#voorbeeld-2)
      + [TestData](#testdata)
         - [Voorbeeld](#voorbeeld-3)
      + [Helpers](#helpers)
         - [Voorbeelden](#voorbeelden)
      + [Constants](#constants)
         - [Voorbeelden](#voorbeelden-1)
      + [appsettings.json](#appsettingsjson)
         - [Voorbeeld](#voorbeeld-4)
      + [appsettings.Local.json](#appsettingslocaljson)
         - [Voorbeeld](#voorbeeld-5)

<!-- TOC end -->

---

<!-- TOC --><a name="packages"></a>
### Packages

| Type            | Package                                        | Doel                                                                                                      |
| --------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Component tests | [`bUnit`](https://bunit.dev/index.html)        | Testframework voor het schrijven en uitvoeren van unit- en componenttests voor Blazor-componenten in .NET |
| E2E tests       | [`Playwright`](https://playwright.dev/dotnet/) | Framework voor end-to-end browserautomatisering en UI-tests voor webapplicaties in meerdere browsers      |

<!-- TOC --><a name="structuur"></a>
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

<!-- TOC --><a name="uitvoering"></a>
### Uitvoering

- Componenttests worden uitgevoerd bij iedere build.
- E2E-tests worden uitgevoerd tijdens CI/CD of tegen een testomgeving.

<!-- TOC --><a name="bunit-component"></a>
#### bUnit (Component)

bUnit is een testframework waarmee je unit- en componenttests kunt schrijven en uitvoeren voor Blazor-componenten in .NET. Het maakt het mogelijk om Blazor UI-componenten te testen in een gesimuleerde omgeving zonder dat een echte browser nodig is. Hierdoor kun je gedrag, rendering en interacties van componenten snel en geïsoleerd valideren.

<!-- TOC --><a name="voorbeeldtest"></a>
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

<!-- TOC --><a name="playwright-e2e"></a>
#### Playwright (E2E)

Playwright is een framework voor end-to-end browserautomatisering en UI-tests van webapplicaties. Playwright heeft een eigen .NET API voor het testen van applicaties in Chromium, Firefox en WebKit. Dankzij ingebouwde automatische wachttijden en goede integratie met MSTest, NUnit en xUnit kunnen geautomatiseerde tests eenvoudig worden ontwikkeld en uitgevoerd.

<!-- TOC --><a name="fixtures"></a>
##### Fixtures

Fixtures bevatten gedeelde setup- en opruimlogica.

Denk aan zaken als:

- Browser starten en sluiten
- Testomgeving configureren
- Applicatie opstarten
- Database resetten
- Resources delen tussen tests

<!-- TOC --><a name="voorbeeld"></a>
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

<!-- TOC --><a name="pages"></a>
##### Pages

Page Objects bevatten alle interactie met een pagina. Hier wil je alle kennis van HTML- en CSS-selectors centraliseren.

Bevat:

- Selectors
- Klikacties
- Formulieracties
- Paginaspecifieke controles

<!-- TOC --><a name="voorbeeld-1"></a>
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

<!-- TOC --><a name="tests"></a>
##### Tests

De daadwerkelijke E2E-scenario's.

Tests worden georganiseerd per functionaliteit of domein. Een test beschrijft wat een gebruiker doet en verwacht.

<!-- TOC --><a name="voorbeeld-2"></a>
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

<!-- TOC --><a name="testdata"></a>
##### TestData

Herbruikbare testdata los van de testcode.

<!-- TOC --><a name="voorbeeld-3"></a>
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

<!-- TOC --><a name="helpers"></a>
##### Helpers

Generieke hulpmiddelen.

Functionaliteit die niet bij een specifieke pagina hoort. Als meerdere pagina's of tests dezelfde logica gebruiken, hoort die vaak hier thuis.

<!-- TOC --><a name="voorbeelden"></a>
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

<!-- TOC --><a name="constants"></a>
##### Constants

Vaste waarden voor de hele testsuite. Dit voorkomt dat dezelfde waarden als magic strings verspreid door de code voorkomen.

<!-- TOC --><a name="voorbeelden-1"></a>
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

<!-- TOC --><a name="appsettingsjson"></a>
##### appsettings.json

Standaardconfiguratie.

<!-- TOC --><a name="voorbeeld-4"></a>
###### Voorbeeld

```json
{
  "BaseUrl": "https://localhost:5001",
  "Browser": "Chromium",
  "Headless": true
}
```

<!-- TOC --><a name="appsettingslocaljson"></a>
##### appsettings.Local.json

Lokale overrides voor ontwikkelaars.

<!-- TOC --><a name="voorbeeld-5"></a>
###### Voorbeeld

```json
{
  "Headless": false
}
```
