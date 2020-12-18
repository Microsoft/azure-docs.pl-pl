---
title: Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines | Microsoft Docs
description: Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 4bccc2922cf20262149ef54fbe2a1a821d9551ab
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673505"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines

Jeśli aplikacja ASP.NET lub ASP.NET Core działa w Azure App Service, zdecydowanie zaleca się [włączenie Snapshot Debugger za pomocą strony portalu Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Jeśli jednak aplikacja wymaga dostosowanej konfiguracji Snapshot Debugger lub wersji zapoznawczej platformy .NET Core, należy postępować ***zgodnie*** z instrukcjami dotyczącymi [włączania za pomocą strony portalu Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Jeśli aplikacja działa na platformie Azure Service Fabric, usłudze w chmurze, Virtual Machines lub maszynach lokalnych, należy użyć poniższych instrukcji. 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie kolekcji migawek dla aplikacji ASP.NET

1. [Włącz Application Insights w aplikacji sieci Web](./asp-net.md), jeśli jeszcze nie zostało to zrobione.

2. Dołącz pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do swojej aplikacji.

3. W razie potrzeby dostosowana konfiguracja Snapshot Debugger dodana do [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Domyślna konfiguracja Snapshot Debugger jest w większości puste i wszystkie ustawienia są opcjonalne. Oto przykład przedstawiający konfigurację równoważną konfiguracji domyślnej:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Migawki są zbierane tylko na wyjątkach, które są zgłaszane do Application Insights. W niektórych przypadkach (na przykład starszych wersji platformy .NET) może być konieczne [skonfigurowanie kolekcji wyjątków](./asp-net-exceptions.md#exceptions) , aby zobaczyć wyjątki z migawkami w portalu.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurowanie kolekcji migawek dla aplikacji przy użyciu ASP.NET Core 2,0 lub nowszej

1. [Włącz Application Insights w aplikacji internetowej ASP.NET Core](./asp-net-core.md), jeśli jeszcze nie zostało to zrobione.

    > [!NOTE]
    > Upewnij się, że aplikacja odwołuje się do wersji 2.1.1 lub nowszej pakietu Microsoft. ApplicationInsights. AspNetCore.

2. Dołącz pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do swojej aplikacji.

3. Zmodyfikuj klasę aplikacji, `Startup` Aby dodać i skonfigurować procesor telemetrii Snapshot collector.
    1. Jeśli użyto pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w wersji 1.3.5 lub nowszej, Dodaj następujące instrukcje using do `Startup.cs` .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Dodaj następujące na końcu metody ConfigureServices w `Startup` klasie w `Startup.cs` .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Jeśli użyto pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w wersji 1.3.4 lub starszej, Dodaj następujące instrukcje using do `Startup.cs` .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       Dodaj następującą `SnapshotCollectorTelemetryProcessorFactory` klasę do `Startup` klasy.

       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;

               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;

               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Dodaj `SnapshotCollectorConfiguration` usługi i `SnapshotCollectorTelemetryProcessorFactory` do potoku uruchamiania:

        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

               // TODO: Add other services your application needs here.
           }
       }
       ```

4. W razie potrzeby dostosuj konfigurację Snapshot Debugger, dodając sekcję SnapshotCollectorConfiguration do appsettings.jsna. Wszystkie ustawienia w konfiguracji Snapshot Debugger są opcjonalne. Oto przykład przedstawiający konfigurację równoważną konfiguracji domyślnej:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie kolekcji migawek dla innych aplikacji platformy .NET

1. Jeśli aplikacja nie ma już Instrumentacji Application Insights, Zacznij od [włączenia Application Insights i ustawienia klucza Instrumentacji](./windows-desktop.md).

2. Dodaj pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w aplikacji.

3. Migawki są zbierane tylko na wyjątkach, które są zgłaszane do Application Insights. Może być konieczne zmodyfikowanie kodu w celu zgłoszenia go. Kod obsługi wyjątków zależy od struktury aplikacji, ale przykład jest poniżej:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Następne kroki

- Generuj ruch do aplikacji, która może wyzwolić wyjątek. Następnie odczekaj od 10 do 15 minut na wysłanie migawek do wystąpienia Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w Azure Portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Snapshot Debugger, zobacz [Snapshot Debugger Rozwiązywanie problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

