---
title: Zbieranie informacji o licznikach wydajności na platformie Azure Cloud Services (klasyczny) | Microsoft Docs
description: Informacje na temat odnajdywania, używania i tworzenia liczników wydajności w Cloud Services z Diagnostyka Azure i Application Insights.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 16b54e8a59eb42c6e2351d37ec0a29d775161493
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739840"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service-classic"></a>Zbieranie liczników wydajności dla usługi w chmurze systemu Azure (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Liczniki wydajności umożliwiają śledzenie, jak dobrze działa aplikacja i host. System Windows Server oferuje wiele różnych liczników wydajności związanych z sprzętem, aplikacjami, systemem operacyjnym i innymi. Zbierając i wysyłając liczniki wydajności do platformy Azure, możesz analizować te informacje, aby ułatwić podejmowanie lepszych decyzji. 

## <a name="discover-available-counters"></a>Odnajdź dostępne liczniki

Licznik wydajności składa się z dwóch części, nazwy zestawu (nazywanej także kategorią) i jednego lub większej liczby liczników. Możesz użyć programu PowerShell, aby uzyskać listę dostępnych liczników wydajności:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName`Właściwość reprezentuje zestaw (lub kategorię) i jest dobrym wskaźnikiem, z jakim są powiązane liczniki wydajności. `Paths`Właściwość reprezentuje kolekcję liczników dla zestawu. Możesz również uzyskać właściwość, `Description` Aby uzyskać więcej informacji na temat zestawu liczników.

Aby uzyskać wszystkie liczniki dla zestawu, użyj `CounterSetName` wartości i rozwiń `Paths` kolekcję. Każdy element ścieżki jest licznik, który można zbadać. Aby na przykład uzyskać dostępne liczniki dotyczące `Processor` zestawu, rozwiń `Paths` kolekcję:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Te pojedyncze ścieżki liczników można dodać do struktury diagnostyki używanej przez usługę w chmurze. Aby uzyskać więcej informacji o sposobie konstruowania ścieżki licznika wydajności, zobacz [Określanie ścieżki licznika](/windows/win32/perfctrs/specifying-a-counter-path).

## <a name="collect-a-performance-counter"></a>Zbieranie danych licznika wydajności

Licznik wydajności może zostać dodany do usługi w chmurze dla Diagnostyka Azure lub Application Insights.

### <a name="application-insights"></a>Application Insights

Usługa Azure Application Insights dla Cloud Services pozwala określić, jakie liczniki wydajności mają być zbierane. Po [dodaniu Application Insights do projektu](../azure-monitor/app/cloudservices.md#sdk)plik konfiguracji o nazwie **ApplicationInsights.config** zostanie dodany do projektu programu Visual Studio. Ten plik konfiguracji definiuje typ informacji Application Insights zbieranych i wysyłanych na platformę Azure.

Otwórz plik **ApplicationInsights.config** i Znajdź element **ApplicationInsights**  >  **TelemetryModules** . Każdy `<Add>` element podrzędny definiuje typ danych telemetrycznych do zebrania wraz z konfiguracją. Typem modułu telemetrii licznika wydajności jest `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` . Jeśli ten element jest już zdefiniowany, nie należy go dodawać po raz drugi. Każdy licznik wydajności do zebrania jest zdefiniowany w węźle o nazwie `<Counters>` . Oto przykład, który zbiera liczniki wydajności dysku:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Każdy licznik wydajności jest reprezentowany jako `<Add>` element poniżej `<Counters>` . Ten `PerformanceCounter` atrybut definiuje licznik wydajności do zbierania. Ten `ReportAs` atrybut jest tytułem, który ma być wyświetlany w Azure Portal dla licznika wydajności. Każdy zbierany licznik wydajności jest umieszczany w kategorii o nazwie **Custom** w portalu. W przeciwieństwie do Diagnostyka Azure, nie można ustawić interwału, które te liczniki wydajności są zbierane i wysyłane na platformę Azure. Za pomocą Application Insights liczniki wydajności są zbierane i wysyłane co minutę. 

Application Insights automatycznie zbiera następujące liczniki wydajności:

* \Process (?? APP_WIN32_PROC??) \% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Aby uzyskać więcej informacji, zobacz [liczniki wydajności systemu w Application Insights](../azure-monitor/app/performance-counters.md) i [Application Insights dla platformy Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostyka Azure

> [!IMPORTANT]
> Chociaż wszystkie te dane są agregowane na koncie magazynu, Portal **nie zapewnia** natywnego sposobu grafowania danych. Zdecydowanie zaleca się, aby zintegrować inną usługę diagnostyczną, taką jak Application Insights, z aplikacją.

Rozszerzenie Diagnostyka Azure dla Cloud Services pozwala określić, jakie liczniki wydajności mają być zbierane. Aby skonfigurować Diagnostyka Azure, zobacz [Omówienie monitorowania usługi w chmurze](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Liczniki wydajności, które mają być zbierane, są zdefiniowane w pliku **Diagnostics. wadcfgx** . Otwórz ten plik (zdefiniowany dla roli) w programie Visual Studio i Znajdź element **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **liczniki wydajności** . Dodaj nowy element **PerformanceCounterConfiguration** jako element podrzędny. Ten element ma dwa atrybuty: `counterSpecifier` i `sampleRate` . Ten `counterSpecifier` atrybut definiuje zestaw liczników wydajności systemu (opisany w poprzedniej sekcji) do zebrania. `sampleRate`Wartość wskazuje częstotliwość sondowania wartości. Jako całość wszystkie liczniki wydajności są transferowane do platformy Azure zgodnie z `PerformanceCounters` `scheduledTransferPeriod` wartością atrybutu elementu nadrzędnego.

Aby uzyskać więcej informacji na temat `PerformanceCounters` elementu schematu, zobacz [schemat Diagnostyka Azure](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

Okres zdefiniowany przez `sampleRate` atrybut używa typu danych czas trwania XML do wskazania, jak często licznik wydajności jest sondowany. W poniższym przykładzie stawka jest ustawiona na `PT3M` , co oznacza, że `[P]eriod[T]ime[3][M]inutes` : co trzy minuty.

Aby uzyskać więcej informacji na temat `sampleRate` sposobu `scheduledTransferPeriod` definiowania i, zobacz sekcję **Typ danych czas trwania** w samouczku dotyczącym [typów daty i godziny w kodzie XML platformy W3](https://www.w3schools.com/XML/schema_dtypes_date.asp) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Utwórz nowy licznik wydajności

Nowy licznik wydajności może być tworzony i używany przez swój kod. Kod, który tworzy nowy licznik wydajności musi mieć podwyższony poziom, w przeciwnym razie zakończy się niepowodzeniem. `OnStart`Kod uruchomienia usługi w chmurze może utworzyć licznik wydajności, co wymaga uruchomienia roli w kontekście z podwyższonym poziomem uprawnień. Można też utworzyć zadanie uruchamiania, które uruchamia podwyższony poziom i tworzy licznik wydajności. Aby uzyskać więcej informacji na temat zadań uruchamiania, zobacz [jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

Aby skonfigurować rolę do uruchamiania podwyższonego poziomu uprawnień, Dodaj `<Runtime>` element do pliku [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) .

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Nowy licznik wydajności można utworzyć i zarejestrować za pomocą kilku wierszy kodu. Użyj `System.Diagnostics.PerformanceCounterCategory.Create` przeciążenia metody, które tworzy zarówno kategorię, jak i licznik. Poniższy kod najpierw sprawdza, czy kategoria istnieje, i jeśli nie ma, tworzy zarówno kategorię, jak i licznik.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Jeśli chcesz użyć licznika, wywołaj `Increment` `IncrementBy` metodę lub.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teraz, gdy aplikacja korzysta z licznika niestandardowego, należy skonfigurować Diagnostyka Azure lub Application Insights do śledzenia licznika.


### <a name="application-insights"></a>Application Insights

Jak wspomniano wcześniej, liczniki wydajności dla Application Insights są zdefiniowane w pliku **ApplicationInsights.config** . Otwórz **ApplicationInsights.config** i Znajdź element **ApplicationInsights**  >  **TelemetryModules**  >  **Add**  >  **Counters** . Utwórz `<Add>` element podrzędny i ustaw dla `PerformanceCounter` atrybutu kategorię i nazwę licznika wydajności utworzonego w kodzie. Ustaw `ReportAs` atrybut na przyjazną nazwę, która ma zostać wyświetlona w portalu.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Jak wspomniano wcześniej, liczniki wydajności, które mają być zbierane, są zdefiniowane w pliku **Diagnostics. wadcfgx** . Otwórz ten plik (zdefiniowany dla roli) w programie Visual Studio i Znajdź element **DiagnosticsConfiguration**  >  **PublicConfig**  >  **WadCfg**  >  **DiagnosticMonitorConfiguration**  >  **liczniki wydajności** . Dodaj nowy element **PerformanceCounterConfiguration** jako element podrzędny. Ustaw `counterSpecifier` atrybut na kategorię i nazwę licznika wydajności utworzonego w kodzie. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Więcej informacji

- [Usługa Application Insights dla usług Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Liczniki wydajności systemu w Application Insights](../azure-monitor/app/performance-counters.md)
- [Określanie ścieżki licznika](/windows/win32/perfctrs/specifying-a-counter-path)
- [Diagnostyka Azure schematu — liczniki wydajności](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)