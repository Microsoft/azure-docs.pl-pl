---
title: Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows
description: Analizowanie użycia i wydajności klasycznej aplikacji systemu Windows za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537393"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows

Wszystkie aplikacje hostowane lokalnie, na platformie Azure i w innych chmurach mogą korzystać z możliwości usługi Application Insights. Jedynym ograniczeniem jest konieczność [zezwolenia na komunikację](../../azure-monitor/app/ip-addresses.md) z usługą Application Insights. Do monitorowania aplikacji platformy uniwersalnej systemu Windows zalecamy używanie pakietu [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Aby wysłać dane telemetryczne do usługi Application Insights z klasycznej aplikacji systemu Windows
1. W witrynie [Azure Portal](https://portal.azure.com)[utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). Jako typ aplikacji wybierz ASP.NET.
2. Wykonaj kopię klucza instrumentacji. Klucz można znaleźć na liście rozwijanej Podstawy dla nowego zasobu, który właśnie został utworzony. 
3. W programie Visual Studio edytuj pakiety NuGet projektu aplikacji, a następnie dodaj interfejs Microsoft.ApplicationInsights.WindowsServer. (Lub wybierz interfejs Microsoft.ApplicationInsights, jeśli potrzebny jest tylko podstawowy interfejs API bez standardowych modułów gromadzenia telemetrii).
4. Ustaw klucz instrumentacji w kodzie:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *Twój klucz* `";`
   
    albo w pliku ApplicationInsights.config (jeśli został zainstalowany jeden ze standardowych pakietów telemetrii):
   
    `<InstrumentationKey>`*twój klucz*`</InstrumentationKey>` 
   
    Jeśli używasz pliku ApplicationInsights.config, upewnij się, że jego właściwości w Eksploratorze rozwiązań zostały ustawione na **Akcja kompilacji = Zawartość, Kopiuj do katalogu wyjściowego = Kopiuj**.
5. [Użyj interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md), aby wysłać telemetrię.
6. Uruchom aplikację i zobacz dane telemetryczne w zasobie utworzonym w witrynie Azure portal.

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

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Zastępowanie przechowywania nazwy komputera

Domyślnie ten sdk będzie zbierać i przechowywać nazwę komputera systemu emitującego dane telemetryczne. Aby zastąpić kolekcję, należy użyć inicjatora telemetrii:

**Napisz niestandardowy telemetryInitializer jak poniżej.**

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
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Smówiamy inicjatora `Program.cs` `Main()` w poniższej metodzie ustawiania klucza instrumentacji:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pulpitu nawigacyjnego](../../azure-monitor/app/overview-dashboard.md)
* [Wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Eksplorowanie metryk](../../azure-monitor/platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

