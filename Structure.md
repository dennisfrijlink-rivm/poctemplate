```
/src/
├── ...
├── Components/
│   ├── Layouts/
│   │   ├── MainLayout
│   │   ├── ErrorLayout
│   │   └── ...
│   ├── Shared/
│   │   ├── Forms/
│   │   │   ├── Input
│   │   │   ├── RadioButton
│   │   │   ├── Error
│   │   │   └── ...
│   │   ├── Button
│   │   └── ...
│   └── Pages/
│       ├── HomePage
│       ├── LoginPage
│       ├── ErrorPage
│       └── ...
├── Styles
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
    │       ├── index.js
    │       └── ...
    └── lib/
        ├── bootstrap/
        │   ├── bootstrap.min.css
        │   ├── bootstrap.bundle.min.js
        │   └── ...
        └── jquery/
            ├── jquery.min.js
            └── ...
/tests/
├── unit/
│   └── ...
├── component/
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
└── e2e/
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
    │   │   └── ...
    │   ├── Users/
    │   │   └── ...
    │   └── Orders/
    │       └── ...
    ├── TestData/
    │   ├── Users/
    │   │   └── ...
    │   └── Orders/
    │       └── ...
    ├── Helpers/
    │   ├── ScreenshotHelper.cs
    │   └── Waithelper.cs
    ├── Constants/
    │   ├── Routes.cs
    │   └── TestUsers.cs
    ├── appsettings.json
    ├── appsettings.Local.json
    └── e2e.tests.csproj
```
