---
title: Mapa aplikacji na platformie Azure Application Insights | Microsoft Docs
description: Monitorowanie złożonych topologii aplikacji za pomocą mapy aplikacji
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: d301ad1a9f5f55a41f87cd1316c9e5e4b38afd51
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980086"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikacji: Klasyfikacja aplikacje rozproszone

Mapa aplikacji pomaga dostrzec wąskie gardła wydajności oraz miejsca najczęstszych awarii we wszystkich składnikach aplikacji rozproszonej. Każdy węzeł mapy reprezentuje składnik aplikacji lub jego zależności; i ma stan KPI kondycji i alertów. Możesz kliknąć opcję z dowolnego składnika, aby uzyskać bardziej szczegółową diagnostykę, taką jak zdarzenia Application Insights. Jeśli Twoja aplikacja korzysta z usług platformy Azure, możesz również kliknąć opcję do diagnostyki platformy Azure, na przykład SQL Database Advisor zalecenia.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie wdrażane części aplikacji rozproszonej/mikrousług. Deweloperzy i zespoły operacyjne mają widoczność na poziomie kodu lub uzyskują dostęp do danych telemetrycznych generowanych przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrznych, takich jak SQL, EventHub itp., do których zespół/organizacja może nie mieć dostępu (kod lub dane telemetryczne).
* Składniki są uruchamiane na dowolnej liczbie wystąpień serwera/roli/kontenera.
* Składnikiem programu mogą być oddzielne klucze Instrumentacji Application Insights (nawet jeśli subskrypcje są różne) lub różne role zgłaszają do jednego klucza Instrumentacji Application Insights. Środowisko mapy podglądu pokazuje składniki bez względu na to, jak zostały skonfigurowane.

## <a name="composite-application-map"></a>Złożona Mapa aplikacji

Można wyświetlić pełną topologię aplikacji na wielu poziomach powiązanych składników aplikacji. Składnikami mogą być różne zasoby Application Insights lub różne role w pojedynczym zasobie. Mapa aplikacji umożliwia znalezienie składników przez następujące wywołania zależności HTTP między serwerami z zainstalowanym zestawem SDK Application Insights. 

To środowisko jest uruchamiane z progresywnym odnajdywaniem składników. Po pierwszym załadowaniu mapy aplikacji jest wyzwalany zestaw zapytań w celu odnalezienia składników związanych z tym składnikiem. Przycisk w lewym górnym rogu zostanie zaktualizowany o liczbę składników w aplikacji w miarę ich odnalezienia. 

Po kliknięciu pozycji "Aktualizuj składniki mapy", mapa jest odświeżana ze wszystkimi składnikami wykrytymi do tego momentu. W zależności od złożoności aplikacji może to chwilę potrwać.

Jeśli wszystkie składniki są rolami w ramach jednego zasobu Application Insights, ten krok odnajdywania nie jest wymagany. Początkowe obciążenie dla takiej aplikacji będzie miało wszystkie jej składniki.

![Zrzut ekranu przedstawia przykład mapy aplikacji.](media/app-map/app-map-001.png)

Jednym z kluczowych celów tego środowiska jest możliwość wizualizacji złożonych topologii przy użyciu setek składników.

Kliknij dowolny składnik, aby zobaczyć powiązane informacje, i przejdź do środowiska Klasyfikacja wydajności i niepowodzeń dla tego składnika.

![Okno wysuwane](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Zbadaj błędy

Wybierz pozycję **Zbadaj błędy** , aby uruchomić okienko błędy.

![Zrzut ekranu przedstawiający przycisk Zbadaj błędy](media/app-map/investigate-failures.png)

![Zrzut ekranu przedstawiający środowisko błędów](media/app-map/failures.png)

### <a name="investigate-performance"></a>Badanie wydajności

Aby rozwiązać problemy z wydajnością, wybierz pozycję **Zbadaj wydajność**.

![Zrzut ekranu przedstawiający przycisk Zbadaj wydajność](media/app-map/investigate-performance.png)

![Zrzut ekranu przedstawiający środowisko wydajności](media/app-map/performance.png)

### <a name="go-to-details"></a>Przejdź do szczegółów

Wybierz pozycję **Przejdź do szczegółów** , aby poznać kompleksowe środowisko transakcji, które może oferować widoki w dół do poziomu stosu wywołań.

![Zrzut ekranu przedstawiający przycisk Przejdź do szczegółów](media/app-map/go-to-details.png)

![Zrzut ekranu przedstawiający szczegółowe informacje o transakcji](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Wyświetlanie dzienników (analiza)

Aby kontynuować zapytania i zbadać dane aplikacji, kliknij pozycję **Wyświetl w obszarze Dzienniki (analiza)**.

![Zrzut ekranu przedstawiający widok na przycisku analiza](media/app-map/view-logs.png)

![Zrzut ekranu przedstawiający środowisko analizy. Wykres liniowy podsumowuje średni czas trwania odpowiedzi żądania w ciągu ostatnich 12 godzin.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alerty

Aby wyświetlić aktywne alerty i podstawowe reguły, które powodują wyzwolenie alertów, wybierz pozycję **alerty**.

![Zrzut ekranu przedstawiający przycisk alerty](media/app-map/alerts.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Ustawianie lub zastępowanie nazwy roli w chmurze

Mapa aplikacji używa właściwości **Nazwa roli chmury** , aby zidentyfikować składniki na mapie. Aby ręcznie ustawić lub zastąpić nazwę roli w chmurze i zmienić zawartość wyświetlaną na mapie aplikacji:

> [!NOTE]
> Zestaw Application Insights SDK lub agent automatycznie dodaje właściwość Nazwa roli chmury do telemetrii emitowanej przez składniki w środowisku Azure App Service.

# <a name="netnetcore"></a>[.NET/. Core](#tab/net)

**Napisz niestandardowe TelemetryInitializer jako poniżej.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET Apps: Załaduj inicjator do aktywnego TelemetryConfiguration**

W ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternatywną metodą ASP.NET Web Apps jest tworzenie wystąpienia inicjatora w kodzie, na przykład w Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Dodawanie inicjatora przy użyciu `ApplicationInsights.config` lub użycie `TelemetryConfiguration.Active` nie jest prawidłowe dla aplikacji ASP.NET Core. 

**ASP.NET Core Apps: Załaduj inicjator do TelemetryConfiguration**

W przypadku aplikacji [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) dodanie nowego `TelemetryInitializer` jest wykonywane przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Jest to wykonywane w `ConfigureServices` metodzie `Startup.cs` klasy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Agent Java**

W przypadku [agenta Java 3,0](./java-in-process-agent.md) nazwa roli chmury jest ustawiona w następujący sposób:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

Możesz również ustawić nazwę roli w chmurze przy użyciu zmiennej środowiskowej ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Zestaw SDK Java**

Jeśli używasz zestawu SDK, rozpoczynając od Application Insights 2.5.0 Java SDK, możesz określić nazwę roli w chmurze, dodając `<RoleName>` do `ApplicationInsights.xml` pliku, np.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Jeśli używasz sprężynowego rozruchu z Application Insights sprężynowego rozruchu Starter, jedyną wymaganą zmianą jest ustawienie niestandardowej nazwy aplikacji w pliku Application. Properties.

`spring.application.name=<name-of-app>`

W przypadku rozruchu sprężynowego Starter zostanie automatycznie przypisana nazwa roli chmury do wartości wprowadzonej dla właściwości spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatywna metoda dla Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

W przypadku języka Python można używać [procesorów OpenCensus języka Python](api-filtering-sampling.md#opencensus-python-telemetry-processors) .

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
// AzureLogHandler
handler.add_telemetry_processor(callback_function)

// AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Informacje o nazwie roli w chmurze w kontekście mapy aplikacji

W miarę jak sądzisz o **nazwie roli w chmurze**, warto przyjrzeć się mapie aplikacji, która ma wiele nazw ról w chmurze:

![Zrzut ekranu mapy aplikacji](media/app-map/cloud-rolename.png)

Na mapie aplikacji powyżej każdej z nazw w zielonych polach znajdują się wartości nazw ról w chmurze dla różnych aspektów tej konkretnej aplikacji rozproszonej. W przypadku tej aplikacji role składają się z: `Authentication` , `acmefrontend` , `Inventory Management` , `Payment Processing Worker Role` . 

W przypadku tej aplikacji każda z tych nazw roli w chmurze reprezentuje również inny unikatowy Application Insights zasób z własnymi kluczami Instrumentacji. Ponieważ właściciel tej aplikacji ma dostęp do każdego z czterech różnych zasobów Application Insights, Mapa aplikacji jest w stanie połączyć mapę relacji podstawowych.

Dla [oficjalnych definicji](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternatywnie, **wystąpienie roli w chmurze** może być przydatne w scenariuszach, w których **Nazwa roli w chmurze** informuje o problemie w frontonie sieci Web, ale może być uruchomiony fronton sieci Web na wielu serwerach o zrównoważonym obciążeniu, dzięki czemu można bardziej szczegółowo przechodzenie do warstwy przy użyciu zapytań Kusto i wiedzieć, czy problem ma wpływ na wszystkie serwery/wystąpienia frontonu internetowego.

Scenariusz, w którym warto zastąpić wartość wystąpienia roli w chmurze, może mieć miejsce, jeśli aplikacja działa w środowisku kontenerowym, w którym tylko wiedza na pojedynczym serwerze może nie być wystarczająco dużo informacji, aby zlokalizować dany problem.

Aby uzyskać więcej informacji na temat przesłonięcia właściwości Nazwa roli chmury z inicjatorami telemetrii, zobacz [Dodawanie właściwości: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z uzyskaniem działania mapy aplikacji zgodnie z oczekiwaniami, spróbuj wykonać następujące czynności:

### <a name="general"></a>Ogólne

1. Upewnij się, że używasz oficjalnie obsługiwanego zestawu SDK. Nieobsługiwane zestawy SDK/zestawy SDK społeczności mogą nie obsługiwać korelacji.

    Aby uzyskać listę obsługiwanych zestawów SDK, zapoznaj się z tym [artykułem](./platforms.md).

2. Uaktualnij wszystkie składniki do najnowszej wersji zestawu SDK.

3. Jeśli używasz Azure Functions w języku C#, Uaktualnij do [funkcji w wersji 2](../../azure-functions/functions-versions.md).

4. Upewnij się, że [Nazwa roli w chmurze](#set-or-override-cloud-role-name) została prawidłowo skonfigurowana.

5. Jeśli brakuje zależności, upewnij się, że znajduje się ona na liście [automatycznie zebranych zależności](./auto-collect-dependencies.md). W przeciwnym razie możesz w dalszym ciągu śledzić ją ręcznie przy użyciu [wywołania zależności śledzenia](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Zbyt wiele węzłów na mapie

Mapa aplikacji konstruuje węzeł aplikacji dla każdej unikatowej nazwy roli w chmurze znajdującej się w danych telemetrycznych żądania i w węźle zależności dla każdej unikatowej kombinacji typu, obiektu docelowego i nazwy roli w chmurze w ramach telemetrii zależności. Jeśli w danych telemetrycznych znajduje się więcej niż 10 000 węzłów, Mapa aplikacji nie będzie w stanie pobrać wszystkich węzłów i linków, więc mapa będzie niepełna. W takim przypadku podczas wyświetlania mapy zostanie wyświetlony komunikat ostrzegawczy.

Ponadto mapa aplikacji obsługuje tylko do 1000 oddzielnych węzłów niezgrupowane, renderowane jednocześnie. Mapa aplikacji zmniejsza złożoność wizualną przez grupowanie zależności, które mają ten sam typ i obiekty wywołujące, ale jeśli dane telemetryczne zawierają zbyt wiele unikatowych nazw ról w chmurze lub zbyt wiele typów zależności, ta grupa będzie niewystarczająca, a mapa nie będzie mogła być renderowana.

Aby rozwiązać ten problem, należy zmienić instrumentację, aby prawidłowo ustawić nazwę roli w chmurze, typ zależności i docelowe pola zależności.

* Element docelowy zależności powinien reprezentować nazwę logiczną zależności. W wielu przypadkach jest to odpowiednik nazwy serwera lub zasobu zależności. Na przykład w przypadku zależności HTTP, dla których ustawiono nazwę hosta. Nie powinien zawierać unikatowych identyfikatorów ani parametrów, które zmieniają się z jednego żądania na inne.

* Typ zależności powinien reprezentować typ logiczny zależności. Na przykład, HTTP, SQL lub Azure Blob są typowymi typami zależności. Nie powinien zawierać unikatowych identyfikatorów.

* W [powyższej sekcji](#set-or-override-cloud-role-name)opisano przeznaczenie nazwy roli w chmurze.

## <a name="portal-feedback"></a>Opinie portalu

Aby przekazać opinię, użyj opcji opinia.

![Obraz MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat działania korelacji w Application Insights zapoznaj się z [artykułem korelacji telemetrii](correlation.md).
* [Kompleksowa obsługa diagnostyki transakcji umożliwia](transaction-diagnostics.md) korelację danych telemetrycznych po stronie serwera między wszystkimi Application Insights monitorowanymi składnikami w jednym widoku.
* Aby uzyskać zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET, zapoznaj się z artykułem [śledzenie niestandardowych operacji](custom-operations-tracking.md) .
