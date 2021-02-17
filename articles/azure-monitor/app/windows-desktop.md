---
title: Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows
description: Analizowanie użycia i wydajności klasycznej aplikacji systemu Windows za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583320"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows

Wszystkie aplikacje hostowane lokalnie, na platformie Azure i w innych chmurach mogą korzystać z możliwości usługi Application Insights. Jedynym ograniczeniem jest konieczność [zezwolenia na komunikację](./ip-addresses.md) z usługą Application Insights. Do monitorowania aplikacji platformy uniwersalnej systemu Windows zalecamy używanie pakietu [Visual Studio App Center](../app/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Aby wysłać dane telemetryczne do usługi Application Insights z klasycznej aplikacji systemu Windows
1. W witrynie [Azure Portal](https://portal.azure.com)[utwórz zasób usługi Application Insights](./create-new-resource.md). 
2. Wykonaj kopię klucza instrumentacji.
3. W programie Visual Studio edytuj pakiety NuGet projektu aplikacji, a następnie dodaj interfejs Microsoft.ApplicationInsights.WindowsServer. (Lub wybierz Microsoft. ApplicationInsights, jeśli chcesz tylko podstawowy interfejs API, bez standardowych modułów zbierania danych telemetrycznych).
4. Ustaw klucz instrumentacji w kodzie:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*Twój klucz*`";`
   
    albo w pliku ApplicationInsights.config (jeśli został zainstalowany jeden ze standardowych pakietów telemetrii):
   
    `<InstrumentationKey>`*Twój klucz*`</InstrumentationKey>` 
   
    Jeśli używasz pliku ApplicationInsights.config, upewnij się, że jego właściwości w Eksploratorze rozwiązań zostały ustawione na **Akcja kompilacji = Zawartość, Kopiuj do katalogu wyjściowego = Kopiuj**.
5. [Użyj interfejsu API](./api-custom-events-metrics.md), aby wysłać telemetrię.
6. Uruchom aplikację i Wyświetl dane telemetryczne w zasobie utworzonym w Azure Portal.

## <a name="example-code"></a><a name="telemetry"></a>Przykładowy kod

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Zastąp magazyn nazwy komputera

Domyślnie ten zestaw SDK będzie zbierać i przechowywać nazwę komputera, który emituje dane telemetryczne.

Nazwa komputera jest używana przez Application Insights [starszej warstwie cenowej przedsiębiorstwa (na węzeł)](./pricing.md#legacy-enterprise-per-node-pricing-tier) na potrzeby rozliczeń wewnętrznych. Domyślnie jeśli używasz inicjatora telemetrii do przesłonięcia `telemetry.Context.Cloud.RoleInstance` , zostanie wysłana oddzielna właściwość, `ai.internal.nodeName` która będzie zawierać wartość Nazwa komputera. Ta wartość nie będzie przechowywana w ramach telemetrii Application Insights, ale jest używana wewnętrznie podczas pozyskiwania, aby zapewnić zgodność z poprzednimi wersjami ze starszym modelem rozliczania opartym na węzłach.

Jeśli korzystasz z [starszej warstwy cenowej przedsiębiorstwa (na węzeł)](./pricing.md#legacy-enterprise-per-node-pricing-tier) i po prostu zachodzi potrzeba zastąpienia magazynu nazwy komputera, użyj inicjatora telemetrii:

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
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Utwórz wystąpienie inicjatora w `Program.cs` `Main()` metodzie poniżej ustawienia klucza Instrumentacji:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Zastąp transmisję nazwy komputera

Jeśli nie jesteś w [starszej warstwie cenowej przedsiębiorstwa (na węzeł)](./pricing.md#legacy-enterprise-per-node-pricing-tier) i chcesz całkowicie uniemożliwić wysyłanie danych telemetrycznych zawierających nazwę komputera, musisz użyć procesora telemetrii.

### <a name="telemetry-processor"></a>Procesor telemetrii

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Utwórz wystąpienie procesora telemetrii w `Program.cs` `Main()` metodzie poniżej ustawienia klucza Instrumentacji:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Można też używać technicznie procesora telemetrii, jak opisano powyżej, nawet jeśli korzystasz z [warstwy cenowej starszej wersji Enterprise (na węzeł)](./pricing.md#legacy-enterprise-per-node-pricing-tier), spowoduje to powstanie potencjalnego rozliczania z powodu niemożności poprawnego rozróżnienia węzłów dla cen na węzeł.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pulpitu nawigacyjnego](./overview-dashboard.md)
* [Wyszukiwanie diagnostyczne](./diagnostic-search.md)
* [Eksploruj metryki](../essentials/metrics-charts.md)
* [Pisanie zapytań analitycznych](../logs/log-query-overview.md)

