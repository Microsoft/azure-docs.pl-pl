---
title: Oddzielanie danych telemetrycznych w usłudze Azure Application Insights
description: Bezpośrednie dane telemetryczne do różnych zasobów dla znaczników rozwoju, testowania i produkcji.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 565d51751ad50479f4e227b6855ac63b80bd949e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536781"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddzielanie danych telemetrycznych od programowania, testowania i produkcji

Podczas tworzenia następnej wersji aplikacji sieci web, nie chcesz mieszać dane telemetryczne [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) z nowej wersji i już wydanej wersji. Aby uniknąć nieporozumień, wyślij dane telemetryczne z różnych etapów rozwoju do oddzielnych zasobów usługi Application Insights z oddzielnymi kluczami instrumentacji (kluczami). Aby ułatwić zmianę klucza instrumentacji w miarę przechodzenia wersji z jednego etapu na drugi, może być przydatne ustawienie ikey w kodzie, a nie w pliku konfiguracyjnym. 

(Jeśli twój system jest usługą Azure Cloud Service, istnieje [inna metoda ustawiania oddzielnych kluczy jedno-.](../../azure-monitor/app/cloudservices.md)

## <a name="about-resources-and-instrumentation-keys"></a>Zasoby i klucze instrumentacji — informacje

Podczas konfigurowania monitorowania usługi Application Insights dla aplikacji sieci web, należy utworzyć *zasób* usługi Application Insights na platformie Microsoft Azure. Otwórz ten zasób w witrynie Azure portal, aby wyświetlić i przeanalizować dane telemetryczne zebrane z aplikacji. Zasób jest identyfikowany za pomocą *klucza instrumentacji* (ikey). Po zainstalowaniu pakietu usługi Application Insights do monitorowania aplikacji, należy skonfigurować go za pomocą klucza instrumentacji, tak aby wiedzieć, gdzie wysłać dane telemetryczne.

Zazwyczaj można użyć oddzielnych zasobów lub pojedynczego zasobu udostępnionego w różnych scenariuszach:

* Różne, niezależne aplikacje — użyj oddzielnego zasobu i klucza ikey dla każdej aplikacji.
* Wiele składników lub ról jednej aplikacji biznesowej — użyj [jednego zasobu udostępnionego](../../azure-monitor/app/app-map.md) dla wszystkich aplikacji składników. Dane telemetryczne mogą być filtrowane lub segmentowane przez właściwość cloud_RoleName.
* Programowanie, testowanie i zwalnianie — użyj oddzielnego zasobu i ikey dla wersji systemu w "stempla" lub etapie produkcji.
* A | B testowania — użyj jednego zasobu. Utwórz TelemetryInitializer, aby dodać właściwość do danych telemetrycznych, która identyfikuje warianty.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamiczny klucz oprzyrządowania

Aby ułatwić zmianę ikey jak kod przenosi się między etapami produkcji, należy ustawić go w kodzie, a nie w pliku konfiguracyjnym.

Ustaw klucz w metodzie inicjowania, takiej jak global.aspx.cs w usłudze ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

W tym przykładzie klucze ikey dla różnych zasobów są umieszczane w różnych wersjach pliku konfiguracji sieci Web. Zamiana pliku konfiguracji sieci Web — co można wykonać w ramach skryptu wydania — spowoduje zamianę zasobu docelowego.

### <a name="web-pages"></a>Strony sieci Web
IKey jest również używany na stronach internetowych aplikacji, w [skrypcie, który otrzymałeś od bloku szybkiego startu](../../azure-monitor/app/javascript.md). Zamiast kodować go dosłownie do skryptu, wygeneruj go ze stanu serwera. Na przykład w aplikacji ASP.NET:

*JavaScript w razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Tworzenie dodatkowych zasobów usługi Application Insights
Aby oddzielić dane telemetryczne dla różnych składników aplikacji lub dla różnych sygnatur (dev/test/production) tego samego składnika, należy utworzyć nowy zasób usługi Application Insights.

W [portal.azure.com](https://portal.azure.com)dodaj zasób usługi Application Insights:

![Kliknij kolejno polecenia Nowy, Application Insights](./media/separate-resources/01-new.png)

* **Typ aplikacji** wpływa na to, co widać na bloku przeglądu i właściwości dostępne w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md). Jeśli nie widzisz swojego typu aplikacji, wybierz jeden z typów stron internetowych.
* **Grupa zasobów** jest wygodą zarządzania właściwościami, takimi jak [kontrola dostępu.](../../azure-monitor/app/resources-roles-access-control.md) Można użyć oddzielnych grup zasobów do programowania, testowania i produkcji.
* **Subskrypcja** to Twoje konto płatnicze na platformie Azure.
* Lokalizacja to **miejsce,** w którym przechowujemy Twoje dane. Obecnie nie można go zmienić. 
* **Dodaj do pulpitu nawigacyjnego** umieszcza kafelek szybki dostęp dla zasobu na stronie głównej platformy Azure. 

Tworzenie zasobu trwa kilka sekund. Po zakończeniu zostanie wyświetlony alert.

(Skrypt programu [PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) można napisać, aby utworzyć zasób automatycznie).

### <a name="getting-the-instrumentation-key"></a>Uzyskiwanie klucza oprzyrządowania
Klucz instrumentacji identyfikuje utworzony zasób. 

![Kliknij przycisk Essentials, kliknij klawisz Instrumentacja CTRL+C](./media/separate-resources/02-props.png)

Potrzebne są klucze instrumentacji wszystkich zasobów, do których aplikacja będzie wysyłać dane.

## <a name="filter-on-build-number"></a>Filtrowanie numeru kompilacji
Podczas publikowania nowej wersji aplikacji, należy mieć możliwość oddzielenia danych telemetrycznych z różnych kompilacji.

Można ustawić właściwość Wersja aplikacji, aby można było [filtrować](../../azure-monitor/app/diagnostic-search.md) wyniki wyszukiwania i [eksploratora metryk.](../../azure-monitor/platform/metrics-charts.md)

![Filtrowanie we właściwości](./media/separate-resources/050-filter.png)

Istnieje kilka różnych metod ustawiania application version właściwości.

* Ustaw bezpośrednio:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zawijanie tego wiersza w [inicjatora telemetrii,](../../azure-monitor/app/api-custom-events-metrics.md#defaults) aby upewnić się, że wszystkie wystąpienia TelemetryClient są ustawione spójnie.
* [ASP.NET] Ustaw wersję `BuildInfo.config`w pliku . Moduł sieci web odbierze wersję z węzła BuildLabel. Dołącz ten plik do projektu i pamiętaj, aby ustawić właściwość Copy Always w Eksploratorze rozwiązań.

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
* [ASP.NET] Automatycznie generuj BuildInfo.config w MSBuild. Aby to zrobić, dodaj kilka `.csproj` wierszy do pliku:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Spowoduje to wygenerowanie pliku o nazwie *YourProjectName*. BuildInfo.config. Proces publikowania zmienia nazwę na BuildInfo.config.

    Etykieta kompilacji zawiera symbol zastępczy (AutoGen_...) podczas tworzenia za pomocą programu Visual Studio. Ale po zbudowaniu z MSBuild, jest wypełniona poprawny numer wersji.

    Aby umożliwić MSBuild generowanie numerów wersji, ustaw wersję, tak jak `1.0.*` w AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Śledzenie wersji i wydania
Aby śledzić wersje aplikacji, upewnij się, że plik `buildinfo.config` jest generowany przez proces aparatu Microsoft Build Engine. W `.csproj` pliku dodaj:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Jeśli plik zawiera informację o kompilacji, moduł sieci Web usługi Application Insights automatycznie dodaje **wersję aplikacji** jako właściwość do każdego elementu telemetrii. Pozwala to na filtrowanie według wersji podczas przeprowadzania [wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md) lub [eksplorowania metryk](../../azure-monitor/platform/metrics-charts.md).

Należy jednak zauważyć, że numer wersji kompilacji jest generowany tylko przez aparat kompilacji firmy Microsoft, a nie przez kompilację dewelopera z programu Visual Studio.

### <a name="release-annotations"></a>Adnotacje dotyczące wersji
Jeśli używasz usługi Azure DevOps, można [uzyskać znacznik adnotacji](../../azure-monitor/app/annotations.md) dodane do wykresów przy każdym wydaniu nowej wersji. Na następującej ilustracji pokazano sposób wyświetlania tego znacznika.

![Zrzut ekranu przedstawiający przykładową adnotację dotyczącą wersji widoczną na wykresie](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Następne kroki

* [Zasoby udostępnione dla wielu ról](../../azure-monitor/app/app-map.md)
* [Tworzenie inicjatora telemetrii w celu odróżnienia A| Warianty B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
