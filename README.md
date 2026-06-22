<p align="center">
    <a href="https://youtu.be/10_OaidPsHw" target="_blank">
        <img src="./logo.png" alt="" width="200" height="200" />
    </a>
</p>

<h1 align="center">Conceptuele opzet van de frontend en folderstructuur.</h1>

<p align="center">
 Concept folderstructuur voor frontend binnen de .NET-template. Per onderdeel, uitgebreidde uitleg in aparte readme (check linkje ernaartoe)
</p>

## Onderdelen

- 🧩 [Components, Layouts & Pages](./components-layouts-pages/README.md)
- ⚙️ [JavaScript](./javascript/README.md)
- 🎨 [Styles](./styles/README.md)
- 🧪 [Tests](./tests/README.md)

## Concept structuur (op hoofdlijnen)
```
├── src/
│   ├── Components/
│   │   ├── Common/
│   │   │   ├── Button/
│   │   │   │   ├── AppButton.razor
│   │   │   │   └── AppButton.razor.css
│   │   │   ├── Card/
│   │   │   │   ├── AppCard.razor
│   │   │   │   └── AppCard.razor.css
│   │   │   ├── Modal/
│   │   │   │   ├── AppModal.razor
│   │   │   │   └── AppModal.razor.css
│   │   │   └── ...
│   │   │
│   │   ├── Forms/
│   │   │   ├── Input/
│   │   │   │   ├── TextInput.razor
│   │   │   │   ├── SelectInput.razor
│   │   │   │   └── CheckboxInput.razor
│   │   │   └── ...
│   │   │
│   │   ├── Feedback/
│   │   │   ├── Alert/
│   │   │   ├── Toast/
│   │   │   ├── Loading/
│   │   │   ├── EmptyState/
│   │   │   └── ...
│   │   │
│   │   ├── Navigation/
│   │   │   ├── Breadcrumbs/
│   │   │   ├── Tabs/
│   │   │   ├── Pagination/
│   │   │   └── ...
│   │   │
│   │   ├── DataDisplay/
│   │   │   ├── Table/
│   │   │   ├── Badge/
│   │   │   ├── Avatar/
│   │   │   ├── Status/
│   │   │   └── ...
│   │   │
│   │   └── Layout/
│   │       ├── Header/
│   │       ├── Sidebar/
│   │       ├── Footer/
│   │       └── ...
│   │
│   ├── Interop/
│   │   └── Clipboard/
│   │   │   └── ClipboardInterop.cs
│   │   └── Scroll/
│   │   │   └── ScrollInterop.cs
│   │   └── Clipboard/
│   │   │   └── HighchartsInterop.cs
│   │   └── ...
│   │
│   ├── Layouts/
│   │   ├── MainLayout.razor
│   │   ├── AuthLayout.razor
│   │   ├── EmptyLayout.razor
│   │   └── ...
│   │
│   ├── Pages/
│   │   ├── Home/
│   │   │   ├── Home.razor
│   │   │   └── Home.razor.css
│   │   ├── Error/
│   │   │   ├── Error.razor
│   │   │   └── Error.razor.css
│   │   └── ...
│   │
│   └── wwwroot/
│       ├── css/
│       │   ├── app.css
│       │   ├── core/
│       │   │   ├── layers.css
│       │   │   ├── reset.css
│       │   │   ├── tokens.css
│       │   │   └── typography.css
│       │   ├── themes/
│       │   │   ├── brand/
│       │   │   │   ├── blue.css
│       │   │   │   ├── green.css
│       │   │   │   ├── red.css
│       │   │   │   └── ...
│       │   │   └── mode/
│       │   │       ├── light.css
│       │   │       ├── dark.css
│       │   │       └── ...
│       │   ├── integrations/
│       │   │   └── bootstrap-tokens.css
│       │   └── utilities/
│       │       ├── accessibility.css
│       │       ├── display.css
│       │       ├── spacing.css
│       │       └── ...
│       │
│       ├── js/
│       │   ├── clipboard/
│       │   │   ├── copy.js
│       │   │   ├── paste.js
│       │   │   ├── index.js
│       │   │   └── ...
│       │   ├── scroll/
│       │   │   ├── scrollToElement.js
│       │   │   ├── scrollToTop.js
│       │   │   ├── index.js
│       │   │   └── ...
│       │   ├── dom/
│       │   │   ├── focus.js
│       │   │   ├── visibility.js
│       │   │   ├── index.js
│       │   │   └── ...
│       │   ├── media/
│       │   │   ├── fileUpload.js
│       │   │   ├── download.js
│       │   │   ├── index.js
│       │   │   └── ...
│       │   └── visuals/
│       │       ├── highcharts.js
│       │       ├── index.js
│       │       └── ...
│       │
│       └── lib/
│           ├── bootstrap/
│           │   ├── bootstrap.min.css
│           │   ├── bootstrap.bundle.min.js
│           │   └── ...
│           ├── highcharts/
│           │   └── ...
│           └── ...
│
├── tests/
│   ├── Unit/
│   │   └── ...
│   │
│   ├── Component/
│   │   ├── Common/
│   │   │   ├── Button/
│   │   │   │   └── AppButtonTests.cs
│   │   │   ├── Card/
│   │   │   │   └── AppCardTests.cs
│   │   │   └── Modal/
│   │   │       └── AppModalTests.cs
│   │   │
│   │   ├── Forms/
│   │   │   ├── InputTests.cs
│   │   │   └── ValidationTests.cs
│   │   │
│   │   ├── Feedback/
│   │   │   └── ToastTests.cs
│   │   │
│   │   ├── Navigation/
│   │   │   └── TabsTests.cs
│   │   │
│   │   └── DataDisplay/
│   │       └── TableTests.cs
│   │
│   └── E2E/
│       ├── Fixtures/
│       ├── Pages/
│       ├── Tests/
│       │   ├── Authentication/
│       │   ├── Users/
│       │   └── Orders/
│       ├── TestData/
│       ├── Helpers/
│       ├── Constants/
│       ├── appsettings.json
│       ├── appsettings.Local.json
│       └── E2E.Tests.csproj
│
└── ...
```