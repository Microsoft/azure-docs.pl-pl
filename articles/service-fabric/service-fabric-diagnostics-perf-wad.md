---
title: Monitorowanie wydajności za pomocą Diagnostyka Azure systemu Windows
description: Użyj Diagnostyka Azure systemu Windows do zbierania liczników wydajności dla klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c2114f5392da788bb440589e69e704a148731e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258626"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorowanie wydajności za pomocą rozszerzenia Diagnostyka Azure systemu Windows

W tym dokumencie opisano kroki wymagane do skonfigurowania kolekcji liczników wydajności za pomocą rozszerzenia Diagnostyka Azure systemu Windows (funkcji wad) dla klastrów systemu Windows. W przypadku klastrów systemu Linux Skonfiguruj [agenta log Analytics](service-fabric-diagnostics-oms-agent.md) , aby zbierać liczniki wydajności dla węzłów. 

 > [!NOTE]
> Rozszerzenie funkcji wad należy wdrożyć w klastrze, aby wykonać te kroki. Jeśli nie jest on skonfigurowany, przejdź do [agregacji zdarzeń i kolekcji przy użyciu Diagnostyka Azure systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Zbieranie liczników wydajności za pośrednictwem WadCfg

Aby zbierać liczniki wydajności za pośrednictwem funkcji wad, należy odpowiednio zmodyfikować konfigurację w szablonie Menedżer zasobów klastra. Wykonaj następujące kroki, aby dodać licznik wydajności, który ma zostać zebrany do szablonu, i uruchom Menedżer zasobów uaktualnienie zasobów.

1. Znajdź konfigurację funkcji wad w szablonie klastra — Znajdź `WadCfg` . Dodajesz liczniki wydajności, które będą zbierane w ramach programu `DiagnosticMonitorConfiguration` .

2. Skonfiguruj konfigurację w celu zbierania liczników wydajności, dodając do niej następującą sekcję `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod`Definiuje, jak często wartości zbieranych liczników są przesyłane do tabeli usługi Azure Storage oraz do dowolnego skonfigurowanego ujścia. 

3. Dodaj liczniki wydajności, które chcesz zebrać do obiektu `PerformanceCounterConfiguration` , który został zadeklarowany w poprzednim kroku. Każdy licznik, który ma być zbierany, jest definiowany przy użyciu `counterSpecifier` , `sampleRate` ,, `unit` `annotation` , i wszelkie odpowiednie `sinks` .

Poniżej znajduje się przykład konfiguracji z licznikiem dla *łącznego czasu procesora* (czas używany przez procesor CPU do przetwarzania operacji) oraz *Service Fabric wywołań metod aktora na sekundę*, jednego z Service Fabric niestandardowych liczników wydajności. Zapoznaj się z [niezawodnymi licznikami wydajności aktora](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) i [niezawodnymi licznikami wydajności usługi](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) , aby uzyskać pełną listę niestandardowych liczników wydajności Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Częstotliwość próbkowania dla licznika można zmodyfikować zgodnie z potrzebami. Jest to format `PT<time><unit>` , więc jeśli chcesz, aby licznik był zbierany co sekundę, należy ustawić `"sampleRate": "PT15S"` .

 Możesz również użyć zmiennych w szablonie ARM, aby zebrać tablicę liczników wydajności, które mogą być przydatne podczas zbierania liczników wydajności dla poszczególnych procesów. W poniższym przykładzie zbieramy czas procesora i czas modułu wypełniania elementów bezużytecznych dla poszczególnych procesów, a następnie dwa liczniki wydajności na wszystkich węzłach, używając zmiennych. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Po dodaniu odpowiednich liczników wydajności, które mają być zbierane, należy uaktualnić zasób klastra, aby te zmiany zostały odzwierciedlone w uruchomionym klastrze. Zapisz zmodyfikowany `template.json` i Otwórz program PowerShell. Klaster można uaktualnić przy użyciu programu `New-AzResourceGroupDeployment` . Wywołanie wymaga nazwy grupy zasobów, zaktualizowanego pliku szablonu i pliku parametrów i zostanie wyświetlony komunikat z prośbą o wprowadzenie odpowiednich zmian w zaktualizowanych zasobach Menedżer zasobów. Po zalogowaniu się na konto i w odpowiedniej subskrypcji Użyj następującego polecenia, aby uruchomić uaktualnienie:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Po zakończeniu wdrażania uaktualnienia (trwa od 15-45 minut w zależności od tego, czy jest to pierwsze wdrożenie i rozmiar grupy zasobów), funkcji wad powinna zbierać liczniki wydajności i wysyłać je do tabeli o nazwie WADPerformanceCountersTable na koncie magazynu skojarzonym z klastrem. Zobacz liczniki wydajności w Application Insights przez [dodanie UJŚCIA AI do szablonu Menedżer zasobów](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Następne kroki
* Zbieraj więcej liczników wydajności dla klastra. Zobacz [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md) , aby zapoznać się z listą liczników, które należy zebrać.
* [Używaj monitorowania i diagnostyki z maszyną wirtualną z systemem Windows i szablonami Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md) , aby wprowadzić dalsze modyfikacje `WadCfg` , w tym Konfigurowanie dodatkowych kont magazynu do wysyłania danych diagnostycznych do programu.
* Odwiedź [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) , aby utworzyć szablon od podstaw i upewnić się, że składnia jest poprawna. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) Aby utworzyć szablon od podstaw i upewnić się, że składnia jest poprawna.
