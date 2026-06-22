## JavaScript

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Structuur](#structuur)
- [Richtlijnen](#richtlijnen)
- [Wanneer JavaScript gebruiken?](#wanneer-javascript-gebruiken)
- [Module loading](#module-loading)
- [Voorbeeld](#voorbeeld)
   * [1. JavaScript feature module](#1-javascript-feature-module)
   * [2. Barrel export (index.js)](#2-barrel-export-indexjs)
   * [3. C# Interop service](#3-c-interop-service)
   * [4. Dependency Injection registratie](#4-dependency-injection-registratie)
   * [5. Gebruik in een Blazor component](#5-gebruik-in-een-blazor-component)
- [Externe libraries](#externe-libraries)
   * [Voorbeeld](#voorbeeld-1)
- [Testbaarheid](#testbaarheid)

<!-- TOC end -->

---

De map `wwwroot/js` bevat uitsluitend ES6-modules voor browser-specifieke functionaliteit en integraties met externe JavaScript libraries. Deze modules worden niet rechtstreeks vanuit Blazor-componenten aangeroepen, maar uitsluitend via C# Interop-services.

Door JavaScript te isoleren achter een Interop-laag blijft applicatielogica in C#, behouden we type-safety binnen Blazor en voorkomen we verspreide DOM-manipulatie door de applicatie.

<!-- TOC --><a name="structuur"></a>
### Structuur

```text
/src/
├── ...
├── Interop/
│   └── Clipboard/
│   │   └── ClipboardInterop.cs
│   └── Scroll/
│   │   └── ScrollInterop.cs
│   └── Clipboard/
│   │   └── HighchartsInterop.cs
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

<!-- TOC --><a name="richtlijnen"></a>
### Richtlijnen

- JavaScript uitsluitend voor browser/DOM-specifieke functionaliteit die niet beschikbaar is in Blazor.
- JavaScript mag nooit direct vanuit een component worden aangeroepen via `IJSRuntime`.
- Alle JavaScript-functionaliteit wordt ontsloten via een dedicated Interop-service.
- Elke feature heeft een eigen map met een `index.js` als publiek toegangspunt.
- DOM-manipulatie buiten een Interop-service vermijden.
- Nieuwe externe libraries worden altijd ingekapseld achter een C#-wrapper.
- Businesslogica blijft altijd in de C#.
- JavaScript modules lazy laden via `import()`.

<!-- TOC --><a name="wanneer-javascript-gebruiken"></a>
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

<!-- TOC --><a name="module-loading"></a>
### Module loading

JavaScript modules worden lazy geladen via `import()` wanneer een functionaliteit voor het eerst wordt gebruikt.

Voordelen:

- Lagere initiële laadtijd
- Geen globale JavaScript objecten
- Betere encapsulatie
- Automatische code-splitting
- Minder geheugenverbruik

<!-- TOC --><a name="voorbeeld"></a>
### Voorbeeld

<!-- TOC --><a name="1-javascript-feature-module"></a>
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

<!-- TOC --><a name="2-barrel-export-indexjs"></a>
#### 2. Barrel export (index.js)

```javascript
// wwwroot/js/scroll/index.js

import { scrollToElement } from "./scrollToElement.js";
import { scrollToTop } from "./scrollToTop.js";

export { scrollToElement, scrollToTop };
```

<!-- TOC --><a name="3-c-interop-service"></a>
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

<!-- TOC --><a name="4-dependency-injection-registratie"></a>
#### 4. Dependency Injection registratie

```csharp
builder.Services.AddScoped<ScrollInterop>();
```

<!-- TOC --><a name="5-gebruik-in-een-blazor-component"></a>
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

<!-- TOC --><a name="externe-libraries"></a>
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

<!-- TOC --><a name="voorbeeld-1"></a>
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

<!-- TOC --><a name="testbaarheid"></a>
### Testbaarheid

Interop-services kunnen worden getest door `IJSRuntime` te mocken.

Businesslogica blijft in C# zodat deze met unit tests getest kan worden zonder afhankelijkheid van JavaScript.

Voordelen:

- Hogere testbaarheid
- Minder afhankelijkheid van browserfunctionaliteit
- Betere scheiding van verantwoordelijkheden
- Minder complexe componenttests
- Consistente architectuur binnen de applicatie
