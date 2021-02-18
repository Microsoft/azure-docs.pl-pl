---
title: Jak zaprojektować wdrożenie Application Insights — jeden z wielu zasobów?
description: Bezpośrednia Telemetria do różnych zasobów na potrzeby tworzenia, testowania i tworzenia sygnatur produkcji.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3964cddcf27a4b2c7397b508ccb3cc8928bd04ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589529"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Ile zasobów Application Insights należy wdrożyć

Podczas tworzenia kolejnej wersji aplikacji sieci Web nie chcesz mieszać danych telemetrycznych [Application Insights](../../azure-monitor/app/app-insights-overview.md) z nowej wersji i już wydanej wersji. Aby uniknąć nieporozumień, Wyślij dane telemetryczne z różnych etapów tworzenia do oddzielnych zasobów Application Insights przy użyciu oddzielnych kluczy Instrumentacji (kluczy iKey). Aby ułatwić zmianę klucza instrumentacji, ponieważ wersja jest przenoszona z jednego etapu do drugiego, można przystąpić do ustawiania iKey w kodzie, a nie w pliku konfiguracji.

(Jeśli system jest usługą w chmurze Azure, istnieje [inna metoda ustawiania oddzielnych kluczy iKey](../../azure-monitor/app/cloudservices.md)).

## <a name="about-resources-and-instrumentation-keys"></a>Informacje o zasobach i kluczach Instrumentacji

Po skonfigurowaniu monitorowania Application Insights dla aplikacji sieci Web należy utworzyć *zasób* Application Insights w Microsoft Azure. Ten zasób jest otwierany w Azure Portal w celu wyświetlenia i przeanalizowania danych telemetrycznych zebranych z aplikacji. Zasób jest identyfikowany przez *klucz Instrumentacji* (iKey). Po zainstalowaniu pakietu Application Insights do monitorowania aplikacji należy skonfigurować go przy użyciu klucza instrumentacji, aby wiedział, gdzie należy wysłać dane telemetryczne.

Każdy zasób Application Insights jest dostarczany z metrykami, które są dostępne jako gotowe. W przypadku całkowitego oddzielenia składników do tego samego Application Insights zasobów te metryki mogą nie mieć sensu dla pulpitu nawigacyjnego/alertu.

### <a name="when-to-use-a-single-application-insights-resource"></a>Kiedy używać pojedynczego zasobu Application Insights

-   Dla składników aplikacji, które są wdrażane razem. Zwykle opracowywane przez jednego zespołu, zarządzane przez ten sam zestaw użytkowników DevOps/ITOps.
-   Jeśli warto agregować kluczowe wskaźniki wydajności (KPI), takie jak czasy trwania odpowiedzi, współczynniki niepowodzeń na pulpicie nawigacyjnym itp., wszystkie te ustawienia są domyślnie (można wybrać segment według nazwy roli w Eksplorator metryk środowisku).
-   Jeśli nie ma potrzeby zarządzania kontrolą dostępu opartą na rolach (Azure RBAC) w różny sposób między składnikami aplikacji.
-   Jeśli nie są potrzebne kryteria alertów metryk, które różnią się między składnikami programu.
-   Jeśli nie ma potrzeby zarządzania ciągłymi eksportami w różny sposób między składnikami programu.
-   Jeśli nie jest konieczne Zarządzanie rozliczeniami/przydziałami w różny sposób między składnikami programu.
-   Jeśli nie masz klucza interfejsu API mającego taki sam dostęp do danych ze wszystkich składników programu. I 10 kluczy interfejsu API są wystarczające dla potrzeb wszystkich z nich.
-   Jeśli chcesz mieć te same ustawienia inteligentnego wykrywania i integracji elementów roboczych we wszystkich rolach.

### <a name="other-things-to-keep-in-mind"></a>Inne kwestie, na które należy pamiętać

-   Może być konieczne dodanie niestandardowego kodu w celu upewnienia się, że odpowiednie wartości są ustawione na atrybut [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) . Bez znaczących wartości ustawionych dla tego atrybutu *żadne* środowisko portalu nie będzie działało.
- W przypadku aplikacji Service Fabric i klasycznych usług Cloud Services zestaw SDK automatycznie odczytuje ze środowiska roli platformy Azure i ustawia te ustawienia. Dla wszystkich innych typów aplikacji prawdopodobnie trzeba będzie ją jawnie ustawić.
-   Środowisko metryk na żywo nie obsługuje dzielenia według nazwy roli.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamiczny klucz Instrumentacji

Aby ułatwić zmianę iKey, ponieważ kod przemieszcza się między etapami produkcyjnymi, należy odwołać się do klucza dynamicznie w kodzie zamiast używać wartości stałe/static.

Ustaw klucz w metodzie inicjującej, na przykład global.aspx.cs w usłudze ASP.NET:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

W tym przykładzie kluczy iKey dla różnych zasobów są umieszczane w różnych wersjach pliku konfiguracji sieci Web. Zamienianie pliku konfiguracji sieci Web, który można wykonać jako część skryptu wydania — spowoduje zamianę zasobu docelowego.

### <a name="web-pages"></a>Strony sieci Web
IKey jest również używany na stronach sieci Web aplikacji w [skrypcie, który został uzyskany z okienka szybkiego startu](../../azure-monitor/app/javascript.md). Zamiast kodowania go do skryptu, wygeneruj go ze stanu serwera. Na przykład w aplikacji ASP.NET:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Tworzenie dodatkowych zasobów Application Insights

Aby utworzyć zasób usługi Application Insights, postępuj zgodnie z [przewodnikiem tworzenia zasobów](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>Pobieranie klucza Instrumentacji
Klucz Instrumentacji identyfikuje utworzony zasób.

Potrzebne są klucze Instrumentacji wszystkich zasobów, do których aplikacja będzie wysyłać dane.

## <a name="filter-on-build-number"></a>Filtruj według numeru kompilacji
Po opublikowaniu nowej wersji aplikacji należy mieć możliwość oddzielenia danych telemetrycznych od różnych kompilacji.

Właściwość wersji aplikacji można ustawić tak, aby można było filtrować wyniki [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) i [Eksploratora metryk](../../azure-monitor/essentials/metrics-charts.md) .

Istnieje kilka różnych metod ustawiania właściwości wersji aplikacji.

* Ustaw bezpośrednio:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zawiń ten wiersz w [inicjatorze telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , aby upewnić się, że wszystkie wystąpienia TelemetryClient są skonfigurowane spójnie.
* [ASP.NET] Ustaw wersję w `BuildInfo.config` . Moduł sieci Web pobierze wersję z węzła BuildLabel. Dołącz ten plik do projektu i pamiętaj, aby ustawić właściwość Copy Always w Eksplorator rozwiązań.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generuj BuildInfo.config automatycznie w programie MSBuild. Aby to zrobić, Dodaj kilka wierszy do `.csproj` pliku:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Spowoduje to wygenerowanie pliku o nazwie *yourProjectName*.BuildInfo.config. Proces publikowania zmienia nazwę na BuildInfo.config.

    Etykieta kompilacji zawiera symbol zastępczy (AutoGen_...) podczas kompilowania przy użyciu programu Visual Studio. Ale w przypadku skompilowania przy użyciu programu MSBuild jest on wypełniony prawidłowym numerem wersji.

    Aby umożliwić programowi MSBuild generowanie numerów wersji, ustaw wersję podobną do `1.0.*` AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Śledzenie wersji i wydania
Aby śledzić wersje aplikacji, upewnij się, że plik `buildinfo.config` jest generowany przez proces aparatu Microsoft Build Engine. W `.csproj` pliku Dodaj następujące polecenie:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Jeśli plik zawiera informację o kompilacji, moduł sieci Web usługi Application Insights automatycznie dodaje **wersję aplikacji** jako właściwość do każdego elementu telemetrii. Pozwala to na filtrowanie według wersji podczas przeprowadzania [wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md) lub [eksplorowania metryk](../../azure-monitor/essentials/metrics-charts.md).

Należy jednak zauważyć, że numer wersji kompilacji jest generowany tylko przez Microsoft Build Engine, a nie przez kompilację dewelopera z programu Visual Studio.

### <a name="release-annotations"></a>Adnotacje dotyczące wersji
Jeśli używasz usługi Azure DevOps, możesz [uzyskać znacznik adnotacji](../../azure-monitor/app/annotations.md) dodany do wykresów po każdym wydaniu nowej wersji. 

## <a name="next-steps"></a>Następne kroki

* [Zasoby udostępnione dla wielu ról](../../azure-monitor/app/app-map.md)
* [Tworzenie inicjatora telemetrii w celu odróżnienia | Warianty B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)