---
title: Oddzielanie danych telemetrycznych na platformie Azure Application Insights
description: Bezpośrednia Telemetria do różnych zasobów na potrzeby tworzenia, testowania i tworzenia sygnatur produkcji.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 565d51751ad50479f4e227b6855ac63b80bd949e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536781"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddzielanie danych telemetrycznych od opracowywania, testowania i produkcji

Podczas tworzenia kolejnej wersji aplikacji sieci Web nie chcesz mieszać danych telemetrycznych [Application Insights](../../azure-monitor/app/app-insights-overview.md) z nowej wersji i już wydanej wersji. Aby uniknąć nieporozumień, Wyślij dane telemetryczne z różnych etapów tworzenia do oddzielnych zasobów Application Insights przy użyciu oddzielnych kluczy Instrumentacji (kluczy iKey). Aby ułatwić zmianę klucza instrumentacji, ponieważ wersja jest przenoszona z jednego etapu do drugiego, można przystąpić do ustawiania iKey w kodzie, a nie w pliku konfiguracji. 

(Jeśli system jest usługą w chmurze Azure, istnieje [inna metoda ustawiania oddzielnych kluczy iKey](../../azure-monitor/app/cloudservices.md)).

## <a name="about-resources-and-instrumentation-keys"></a>Informacje o zasobach i kluczach Instrumentacji

Po skonfigurowaniu monitorowania Application Insights dla aplikacji sieci Web należy utworzyć *zasób* Application Insights w Microsoft Azure. Ten zasób jest otwierany w Azure Portal w celu wyświetlenia i przeanalizowania danych telemetrycznych zebranych z aplikacji. Zasób jest identyfikowany przez *klucz Instrumentacji* (iKey). Po zainstalowaniu pakietu Application Insights do monitorowania aplikacji należy skonfigurować go przy użyciu klucza instrumentacji, aby wiedział, gdzie należy wysłać dane telemetryczne.

Zazwyczaj wybiera się użycie oddzielnych zasobów lub pojedynczego zasobu udostępnionego w różnych scenariuszach:

* Różne aplikacje niezależne — Użyj oddzielnego zasobu i iKey dla każdej aplikacji.
* Wiele składników lub ról jednej aplikacji biznesowej — Użyj [jednego udostępnionego zasobu](../../azure-monitor/app/app-map.md) dla wszystkich aplikacji składników. Dane telemetryczne mogą być filtrowane lub segmentować według właściwości cloud_RoleName.
* Programowanie, testowanie i wydanie — Użyj oddzielnego zasobu i iKey dla wersji systemu w "sygnaturze" lub etapie produkcji.
* A | Testowanie B — Użyj jednego zasobu. Utwórz TelemetryInitializer, aby dodać właściwość do telemetrii, która identyfikuje warianty.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamiczny klucz Instrumentacji

Aby ułatwić zmianę iKey, ponieważ kod przemieszcza się między etapami produkcyjnymi, należy ustawić go w kodzie, a nie w pliku konfiguracji.

Ustaw klucz w metodzie inicjującej, na przykład global.aspx.cs w usłudze ASP.NET:

*S #*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

W tym przykładzie kluczy iKey dla różnych zasobów są umieszczane w różnych wersjach pliku konfiguracji sieci Web. Zamienianie pliku konfiguracji sieci Web, który można wykonać jako część skryptu wydania — spowoduje zamianę zasobu docelowego.

### <a name="web-pages"></a>Strony sieci Web
IKey jest również używany na stronach sieci Web aplikacji w [skrypcie uzyskanym w bloku szybki start](../../azure-monitor/app/javascript.md). Zamiast kodowania go do skryptu, wygeneruj go ze stanu serwera. Na przykład w aplikacji ASP.NET:

*Kod JavaScript w Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Tworzenie dodatkowych zasobów Application Insights
Aby oddzielić dane telemetryczne dla różnych składników aplikacji lub dla różnych sygnatur (Tworzenie/testowanie/produkcja) tego samego składnika, należy utworzyć nowy zasób Application Insights.

W [Portal.Azure.com](https://portal.azure.com)dodaj zasób Application Insights:

![Kliknij kolejno polecenia Nowy, Application Insights](./media/separate-resources/01-new.png)

* **Typ aplikacji** ma wpływ na to, co widzisz w bloku przegląd i właściwości dostępne w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md). Jeśli nie widzisz typu aplikacji, wybierz jeden z typów sieci Web dla stron sieci Web.
* **Grupa zasobów** jest wygodą do zarządzania właściwościami, takimi jak [Kontrola dostępu](../../azure-monitor/app/resources-roles-access-control.md). Do tworzenia, testowania i produkcji można używać oddzielnych grup zasobów.
* **Subskrypcja** jest kontem płatności na platformie Azure.
* **Lokalizacja** polega na tym, że przechowujemy Twoje dane. Obecnie nie można jej zmienić. 
* Opcja **Dodaj do pulpitu nawigacyjnego** umieszcza kafelek szybkiego dostępu dla zasobu na stronie głównej platformy Azure. 

Tworzenie zasobu trwa kilka sekund. Po zakończeniu zostanie wyświetlony alert.

(Można napisać [skrypt programu PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) , aby automatycznie utworzyć zasób).

### <a name="getting-the-instrumentation-key"></a>Pobieranie klucza Instrumentacji
Klucz Instrumentacji identyfikuje utworzony zasób. 

![Kliknij pozycję Essentials, kliknij klucz instrumentacji, CTRL + C](./media/separate-resources/02-props.png)

Potrzebne są klucze Instrumentacji wszystkich zasobów, do których aplikacja będzie wysyłać dane.

## <a name="filter-on-build-number"></a>Filtruj według numeru kompilacji
Po opublikowaniu nowej wersji aplikacji należy mieć możliwość oddzielenia danych telemetrycznych od różnych kompilacji.

Właściwość wersji aplikacji można ustawić tak, aby można było filtrować wyniki [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) i [Eksploratora metryk](../../azure-monitor/platform/metrics-charts.md) .

![Filtrowanie właściwości](./media/separate-resources/050-filter.png)

Istnieje kilka różnych metod ustawiania właściwości wersji aplikacji.

* Ustaw bezpośrednio:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zawiń ten wiersz w [inicjatorze telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , aby upewnić się, że wszystkie wystąpienia TelemetryClient są skonfigurowane spójnie.
* [ASP.NET] Ustaw wersję w `BuildInfo.config`. Moduł sieci Web pobierze wersję z węzła BuildLabel. Dołącz ten plik do projektu i pamiętaj, aby ustawić właściwość Copy Always w Eksplorator rozwiązań.

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
* [ASP.NET] Automatycznie Generuj plik BuildInfo. config w programie MSBuild. Aby to zrobić, Dodaj kilka wierszy do `.csproj` pliku:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Spowoduje to wygenerowanie pliku o nazwie *yourProjectName*. BuildInfo. config. proces publikowania zmienia nazwę na BuildInfo. config.

    Etykieta kompilacji zawiera symbol zastępczy (AutoGen_...) podczas kompilowania przy użyciu programu Visual Studio. Ale w przypadku skompilowania przy użyciu programu MSBuild jest on wypełniony prawidłowym numerem wersji.

    Aby umożliwić programowi MSBuild generowanie numerów wersji, ustaw wersję podobną `1.0.*` do AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Śledzenie wersji i wydania
Aby śledzić wersje aplikacji, upewnij się, że plik `buildinfo.config` jest generowany przez proces aparatu Microsoft Build Engine. W `.csproj` pliku Dodaj następujące polecenie:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Jeśli plik zawiera informację o kompilacji, moduł sieci Web usługi Application Insights automatycznie dodaje **wersję aplikacji** jako właściwość do każdego elementu telemetrii. Pozwala to na filtrowanie według wersji podczas przeprowadzania [wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md) lub [eksplorowania metryk](../../azure-monitor/platform/metrics-charts.md).

Należy jednak zauważyć, że numer wersji kompilacji jest generowany tylko przez Microsoft Build Engine, a nie przez kompilację dewelopera z programu Visual Studio.

### <a name="release-annotations"></a>Adnotacje dotyczące wersji
Jeśli używasz usługi Azure DevOps, możesz [uzyskać znacznik adnotacji](../../azure-monitor/app/annotations.md) dodany do wykresów po każdym wydaniu nowej wersji. Na następującej ilustracji pokazano sposób wyświetlania tego znacznika.

![Zrzut ekranu przedstawiający przykładową adnotację dotyczącą wersji widoczną na wykresie](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Następne kroki

* [Zasoby udostępnione dla wielu ról](../../azure-monitor/app/app-map.md)
* [Tworzenie inicjatora telemetrii w celu odróżnienia | Warianty B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
