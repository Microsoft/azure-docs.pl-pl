---
title: Agregacja zdarzeń za pomocą Diagnostyka Azure systemu Linux
description: Dowiedz się więcej na temat agregowania i zbierania zdarzeń przy użyciu LAD do monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628938"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregacja i zbieranie zdarzeń przy użyciu Diagnostyka Azure systemu Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Gdy uruchamiasz klaster usługi Azure Service Fabric, dobrym pomysłem jest zebranie dzienników ze wszystkich węzłów w centralnej lokalizacji. Przechowywanie dzienników w centralnej lokalizacji ułatwia analizowanie i rozwiązywanie problemów z klastrem lub problemów z aplikacjami i usługami uruchomionymi w tym klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia systemu Linux Diagnostyka Azure (LAD), które przekazuje dzienniki do usługi Azure Storage, a także oferuje opcję wysyłania dzienników do usługi Azure Application Insights lub Event Hubs. Możesz również użyć procesu zewnętrznego, aby odczytać zdarzenia z magazynu i umieścić je w produkcie platformy analizy, takim jak [dzienniki Azure monitor](./service-fabric-diagnostics-oms-setup.md) lub inne rozwiązanie do analizy dzienników.

## <a name="log-and-event-sources"></a>Dzienniki i źródła zdarzeń

### <a name="service-fabric-platform-events"></a>Zdarzenia platformy Service Fabric
Service Fabric emituje kilka gotowych do użycia dzienników za pośrednictwem [LTTng](https://lttng.org), w tym zdarzeń operacyjnych lub zdarzeń środowiska uruchomieniowego. Te dzienniki są przechowywane w lokalizacji, którą określa szablon Menedżer zasobów klastra. Aby uzyskać lub ustawić szczegóły konta magazynu, Wyszukaj tag **AzureTableWinFabETWQueryable** i Wyszukaj pozycję **StoreConnectionString**.

### <a name="application-events"></a>Zdarzenia aplikacji
 Zdarzenia emitowane z kodu aplikacji i usług określone przez użytkownika podczas Instrumentacji oprogramowania. Można użyć dowolnego rozwiązania do rejestrowania, które zapisuje pliki dzienników na podstawie tekstu — na przykład LTTng. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją LTTng dotyczącą śledzenia aplikacji.

[Monitorowanie i diagnozowanie usług w konfiguracji tworzenia maszyn lokalnych](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Wdróż rozszerzenie diagnostyki
Pierwszym krokiem zbierania dzienników jest wdrożenie rozszerzenia diagnostyki na każdej z maszyn wirtualnych w klastrze Service Fabric. Rozszerzenie diagnostyki zbiera dzienniki na poszczególnych maszynach wirtualnych i przekazuje je do określonego konta magazynu. 

Aby wdrożyć rozszerzenie diagnostyki na maszynach wirtualnych w klastrze w ramach tworzenia klastra, ustaw opcję **Diagnostyka** na **włączone**. Po utworzeniu klastra nie można zmienić tego ustawienia przy użyciu portalu, więc konieczne będzie wprowadzenie odpowiednich zmian w szablonie Menedżer zasobów.

Spowoduje to skonfigurowanie agenta LAD do monitorowania określonych plików dziennika. Za każdym razem, gdy do pliku dołączany jest nowy wiersz, tworzy on wpis dziennika systemowego, który jest wysyłany do określonego magazynu (tabeli).


## <a name="next-steps"></a>Następne kroki

1. Aby bardziej szczegółowo zrozumieć, jakie zdarzenia należy sprawdzić podczas rozwiązywania problemów, zobacz [dokumentację LTTng](https://lttng.org/docs) i [Korzystanie z lad](../virtual-machines/extensions/diagnostics-linux.md).
2. [Skonfiguruj agenta log Analytics](service-fabric-diagnostics-event-analysis-oms.md) , aby pomóc zbierać metryki, monitorować kontenery wdrożone w klastrze i wizualizować dzienniki 
