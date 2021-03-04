---
title: Reguły zbierania danych w Azure Monitor (wersja zapoznawcza)
description: Przegląd reguł zbierania danych (DCR) w Azure Monitor, w tym ich zawartości i struktury, oraz sposobu tworzenia i pracy z nimi.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: a0c5e9f89b983871224e79c2fc4f518a15d42a6f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039618"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Reguły zbierania danych w Azure Monitor (wersja zapoznawcza)
Zasady zbierania danych (DCR) definiują dane wprowadzane do Azure Monitor i określają, gdzie mają być wysyłane lub przechowywane dane. Ten artykuł zawiera omówienie zasad zbierania danych, w tym ich zawartości i struktury, oraz sposobu tworzenia i pracy z nimi.

## <a name="input-sources"></a>Źródła danych wejściowych
Reguły zbierania danych obsługują obecnie następujące źródła danych wejściowych:

- Maszyna wirtualna platformy Azure z agentem Azure Monitor. Zobacz [Konfigurowanie zbierania danych dla agenta Azure monitor (wersja zapoznawcza)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Składniki reguły zbierania danych
Reguła zbierania danych zawiera następujące składniki.

| Składnik | Opis |
|:---|:---|
| Źródła danych | Unikatowe źródło danych monitorowania z własnym formatem i metodą ujawniania swoich danych. Przykłady źródła danych obejmują dziennik zdarzeń systemu Windows, liczniki wydajności i dziennik systemowy. Każde źródło danych jest zgodne z określonym typem źródła danych, zgodnie z poniższym opisem. |
| Strumienie | Unikatowe dojście opisujące zestaw źródeł danych, które zostaną przekształcone i schematized jako jeden typ. Każde źródło danych wymaga co najmniej jednego strumienia, a jeden z nich może być używany przez wiele źródeł danych. Wszystkie źródła danych w strumieniu udostępniają wspólny schemat. Użyj wielu strumieni na przykład, gdy chcesz wysłać określone źródło danych do wielu tabel w tym samym obszarze roboczym Log Analytics. |
| Miejsca docelowe | Zestaw miejsc docelowych, do których mają być wysyłane dane. Przykładami mogą być Log Analytics obszar roboczy, metryki Azure Monitor i Event Hubs platformy Azure. | 
| Przepływy danych | Definicja, które strumienie powinny być wysyłane do których miejsc docelowych. | 

Na poniższym diagramie przedstawiono składniki reguły zbierania danych i ich relacji

[![Diagram DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Typy źródeł danych
Każde źródło danych ma typ źródła danych. Każdy typ definiuje unikatowy zestaw właściwości, które muszą być określone dla każdego źródła danych. Aktualnie dostępne typy źródeł danych przedstawiono w poniższej tabeli.

| Typ źródła danych | Opis | 
|:---|:---|
| rozszerzenie | Źródło danych oparte na rozszerzeniu maszyny wirtualnej |
| Liczniki wydajności | Liczniki wydajności dla systemów Windows i Linux |
| syslog | Zdarzenia dziennika systemowego w systemie Linux |
| windowsEventLogs | Dziennik zdarzeń systemu Windows |


## <a name="limits"></a>Limity
Aby uzyskać ograniczenia dotyczące poszczególnych reguł zbierania danych, zobacz [Azure monitor limitów usługi](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Tworzenie elementu DCR
Obecnie można użyć dowolnej z poniższych metod, aby utworzyć DCR:

- [Użyj Azure Portal,](../agents/data-collection-rule-azure-monitor-agent.md) aby utworzyć regułę zbierania danych i skojarzyć ją z co najmniej jedną maszyną wirtualną.
- Bezpośrednio Edytuj regułę zbierania danych w formacie JSON i [przesyłaj przy użyciu interfejsu API REST](/rest/api/monitor/datacollectionrules).
- Utwórz DCR i skojarzenia przy użyciu [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Utwórz DCR i skojarzenia przy użyciu Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Zasada przykładowej zbierania danych
Poniższa zasada zbierania danych przykładowych dotyczy maszyn wirtualnych z usługą Azure Management Agent i ma następujące szczegóły:

- Dane wydajności
  - Zbiera dane liczników procesora, pamięci, dysku logicznego i dysku fizycznego co 15 sekund i przekazuje co minutę.
  - Zbiera określone liczniki procesów co 30 sekund i przekazuje je co 5 minut.
- Zdarzenia systemu Windows
  - Zbiera zdarzenia zabezpieczeń systemu Windows i przekazuje je co minutę.
  - Zbiera dane dotyczące aplikacji i zdarzeń systemu Windows i przekazuje je co 5 minut.
- Dziennik systemu
  - Zbiera zdarzenia debugowania, krytyczne i awaryjne z poziomu funkcji firmy cronus.
  - Zbiera zdarzenia alertu, krytyczne i awaryjne z funkcji dziennika systemowego.
- Miejsca docelowe
  - Wysyła wszystkie dane do obszaru roboczego Log Analytics o nazwie centralWorkspace.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Następne kroki

- [Utwórz regułę zbierania danych](data-collection-rule-azure-monitor-agent.md) i skojarzenie jej z maszyną wirtualną przy użyciu agenta Azure monitor.
