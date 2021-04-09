---
title: Monitorowanie zdarzeń klastra systemu Linux w usłudze Azure Service Fabric
description: Informacje o monitorowaniu zdarzeń klastra Service Fabric Linux przez zapisanie zdarzeń platformy Service Fabric do dziennika systemowego.
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 895ff340c1b649c1ba8a20bf95edcefb9a72e246
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626966"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Zdarzenia klastra Service Fabric systemu Linux w dzienniku systemu

Service Fabric uwidacznia zestaw zdarzeń platformy w celu poinformowania o ważnych działaniach w klastrze. Pełna lista zdarzeń, które są uwidocznione, jest dostępna [tutaj](service-fabric-diagnostics-event-generation-operational.md). Istnieją różne sposoby używania tych zdarzeń. W tym artykule omówiono sposób konfigurowania Service Fabric zapisywania tych zdarzeń w dzienniku systemowym.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Wprowadzenie

W wersji 6,4 SyslogConsumer został wprowadzony do wysyłania zdarzeń platformy Service Fabric do dziennika systemowego dla klastrów systemu Linux. Po włączeniu zdarzenia będą automatycznie przepływać do dziennika systemowego, który będzie można zbierać i wysyłać przez agenta Log Analytics.

Każde zdarzenie dziennika systemu zawiera 4 składniki
* Urządzenia
* Tożsamość
* Komunikat
* Ważność

SyslogConsumer zapisuje wszystkie zdarzenia platformy przy użyciu funkcji `Local0` . Możesz zaktualizować do dowolnego prawidłowego obiektu, zmieniając konfigurację konfiguracji. Używana tożsamość to `ServiceFabric` . Pole Message zawiera całe zdarzenie serializowane w formacie JSON, tak aby można było wykonywać zapytania i korzystać z różnych narzędzi. 

## <a name="enable-syslogconsumer"></a>Włącz SyslogConsumer

Aby włączyć SyslogConsumer, należy przeprowadzić uaktualnienie klastra. Należy `fabricSettings` zaktualizować sekcję przy użyciu następującego kodu. Uwaga ten kod zawiera tylko sekcje powiązane z SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Poniżej przedstawiono zmiany, które należy wywoływać
1. W sekcji Common jest wywoływany nowy parametr `LinuxStructuredTracesEnabled` . **Jest to wymagane, aby zdarzenia systemu Linux były strukturalne i serializowane podczas wysyłania do dziennika systemowego.**
2. W sekcji Diagnostyka został dodany nowy ConsumerInstance: SyslogConsumer. Oznacza to, że platforma jest innym konsumentem zdarzeń. 
3. Nowa sekcja SyslogConsumer musi mieć `IsEnabled` jako `true` . Jest ona konfigurowana do automatycznego używania funkcji Local0. Można to zastąpić przez dodanie innego parametru.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integracja dzienników Azure Monitor
Te zdarzenia dziennika systemowego można odczytać w narzędziu do monitorowania, takim jak dzienniki Azure Monitor. Obszar roboczy Log Analytics można utworzyć przy użyciu witryny Azure Marketplace, korzystając z następujących [instrukcji]. (.. /Azure-monitor/Logs/Quick-Create-Workspace.MD) musisz również dodać agenta Log Analytics do klastra, aby zebrać i wysłać te dane do obszaru roboczego. Jest to ten sam Agent, który służy do zbierania liczników wydajności. 

1. Przejdź do `Advanced Settings` bloku

    ![Ustawienia obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Kliknij pozycję `Data`.
3. Kliknij pozycję `Syslog`.
4. Skonfiguruj Local0 jako funkcję do śledzenia. Możesz dodać kolejną funkcję, jeśli została zmieniona w fabricSettings

    ![Konfigurowanie dziennika systemowego](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Przejdź do Eksploratora zapytań, klikając `Logs` menu zasobów obszaru roboczego, aby rozpocząć wykonywanie zapytań

    ![Dzienniki obszaru roboczego](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Można wykonać zapytanie względem `Syslog` tabeli szukanej `ServiceFabric` jako elementu ProcessName. Poniższe zapytanie przedstawia przykład sposobu analizowania kodu JSON w zdarzeniu i wyświetlania jego zawartości

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Zapytanie dziennika systemowego](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Powyższy przykład dotyczy zdarzenia NodeDown. Pełną listę zdarzeń można zobaczyć [tutaj](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Następne kroki
* [Wdróż agenta log Analytics](service-fabric-diagnostics-oms-agent.md) w węzłach w celu zebrania liczników wydajności i zebrania statystyk i dzienników platformy Docker dla kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/logs/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor
* [Używanie projektanta widoków do tworzenia widoków niestandardowych w dziennikach Azure Monitor](../azure-monitor/visualize/view-designer.md)
* Informacje dotyczące sposobu [Azure Monitornia dzienników integracja z dziennikiem](../azure-monitor/agents/data-sources-syslog.md)systemowym.
