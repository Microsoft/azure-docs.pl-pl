---
title: Wyślij Diagnostyka Azure dane do Application Insights
description: Zaktualizuj konfigurację publiczną Diagnostyka Azure, aby wysyłać dane do Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708598"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Wyślij usługę w chmurze, maszynę wirtualną lub Service Fabric dane diagnostyczne do Application Insights
Cloud Services, Virtual Machines, Virtual Machine Scale Sets i Service Fabric wszystkie używają rozszerzenia Diagnostyka Azure do zbierania danych.  Diagnostyka Azure wysyła dane do tabel usługi Azure Storage.  Można jednak również potokować wszystkie lub podzbiór danych do innych lokalizacji przy użyciu rozszerzenia Diagnostyka Azure 1,5 lub nowszego.

W tym artykule opisano sposób wysyłania danych z rozszerzenia Diagnostyka Azure do Application Insights.

## <a name="diagnostics-configuration-explained"></a>Wyjaśniono konfigurację diagnostyki
Rozszerzenie diagnostyki platformy Azure 1,5 wprowadziło ujścia, które są dodatkowymi lokalizacjami, w których można wysyłać dane diagnostyczne.

Przykładowa konfiguracja ujścia dla Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Atrybut  *nazwy* ujścia jest wartością ciągu, która jednoznacznie identyfikuje ujścia.

- Element **ApplicationInsights** określa klucz Instrumentacji zasobu usługi Application Insights, w którym są wysyłane dane diagnostyczne platformy Azure.
    - Jeśli nie masz istniejącego zasobu Application Insights, zobacz [Tworzenie nowego zasobu Application Insights](../app/create-new-resource.md) , aby uzyskać więcej informacji na temat tworzenia zasobu i uzyskiwania klucza Instrumentacji.
    - Jeśli tworzysz usługę w chmurze przy użyciu zestawu Azure SDK 2,8 lub nowszego, ten klucz Instrumentacji jest wypełniany automatycznie. Wartość jest określana na podstawie ustawienia konfiguracji usługi **APPINSIGHTS_INSTRUMENTATIONKEY** podczas pakowania projektu usługi w chmurze. Zobacz [używanie Application Insights z Cloud Services](../app/cloudservices.md).

- Element **Channels** zawiera jeden lub więcej elementów **kanału** .
    - Atrybut *name* jednoznacznie odwołuje się do tego kanału.
    - Atrybut *LogLevel* umożliwia określenie poziomu dziennika, który umożliwia kanał. Dostępne poziomy dziennika są następujące:
        - Pełny
        - Informacje
        - Ostrzeżenie
        - Błąd
        - Krytyczne

Kanał działa jak filtr i umożliwia wybranie określonych poziomów dziennika do wysłania do docelowego ujścia. Można na przykład zebrać pełne dzienniki i wysłać je do magazynu, ale wysłać tylko błędy do ujścia.

Na poniższej ilustracji przedstawiono tę relację.

![Konfiguracja publiczna diagnostyki](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Poniższa ilustracja zawiera podsumowanie wartości konfiguracji i sposobu ich działania. W konfiguracji można uwzględnić wiele zlewów na różnych poziomach w hierarchii. Ujścia na najwyższego poziomu pełni rolę ustawienia globalnego, a określony w pojedynczym elemencie zachowuje się jak przesłonięcie do tego ustawienia globalnego.

![Konfiguracja ujścia diagnostyki z Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Przykład kończenia konfiguracji ujścia
Oto kompletny przykład pliku konfiguracji publicznej, który
1. wysyła wszystkie błędy do Application Insights (określone w węźle **DiagnosticMonitorConfiguration** )
2. wysyła także pełne dzienniki poziomu dla dzienników aplikacji (określonych w węźle **dzienniki** ).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
W poprzedniej konfiguracji następujące wiersze mają następujące znaczenie:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Wyślij wszystkie dane zbierane przez diagnostykę platformy Azure

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Wysyłaj tylko dzienniki błędów do ujścia Application Insights

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Wyślij pełne dzienniki aplikacji do Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Ograniczenia

- **Tylko kanały typu dziennika, a nie liczniki wydajności.** W przypadku określenia kanału z elementem licznika wydajności zostanie on zignorowany.
- **Poziom dziennika kanału nie może przekroczyć poziomu dziennika, co jest zbierane przez diagnostykę platformy Azure.** Nie można na przykład zbierać błędów dziennika aplikacji w elemencie logs i próbować wysyłać pełne dzienniki do ujścia usługi Application Insight. Atrybut *scheduledTransferLogLevelFilter* musi zawsze zbierać równe lub więcej dzienników niż dzienniki, które próbujesz wysłać do ujścia.
- **Nie można wysłać danych obiektów BLOB zbieranych przez rozszerzenie diagnostyki platformy Azure do Application Insights.** Na przykład wszystkie elementy określone w węźle *katalogi* . W przypadku zrzutów awaryjnych rzeczywisty zrzut awaryjny jest wysyłany do usługi BLOB Storage i do Application Insights jest wysyłany tylko powiadomienie o wygenerowanym zrzucie awaryjnym.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wyświetlać informacje diagnostyczne platformy Azure](../app/cloudservices.md) w Application Insights.
* Użyj [programu PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) , aby włączyć rozszerzenie diagnostyki platformy Azure dla swojej aplikacji.
* Korzystanie z [programu Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) w celu włączenia rozszerzenia diagnostyki platformy Azure dla aplikacji

