---
title: Migrowanie do monitora połączeń z monitora połączeń
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do monitora połączeń z monitora połączeń.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d4ab5361d245ad1ee10d43184cc0a2d65fed2054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730035"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrowanie do monitora połączeń z monitora połączeń (klasyczny)

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych monitorów połączeń w monitorze połączeń (klasycznym), ale można nadal korzystać z istniejących monitorów połączeń utworzonych przed 1 lipca 2021. Aby zminimalizować przerwy w działaniu usługi z bieżącymi obciążeniami, należy [przeprowadzić migrację z monitora połączeń (klasycznego) do nowego monitora połączeń](migrate-to-connection-monitor-from-connection-monitor-classic.md)  na platformie Azure Network Watcher przed 29 lutego 2024.

Istniejące monitory połączeń można migrować do nowego, ulepszonego monitora połączeń z zaledwie kilkoma kliknięciami i bez przestojów. Aby dowiedzieć się więcej o korzyściach, zobacz [monitor połączeń](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Najważniejsze punkty do uwagi

Migracja pomaga generować następujące wyniki:

* Ustawienia agentów i zapory działają zgodnie z oczekiwaniami. Nie są wymagane żadne zmiany. 
* Istniejące monitory połączeń są mapowane do monitora połączeń > grupy testowej > formacie testu. Wybierając pozycję **Edytuj**, można wyświetlić i zmodyfikować właściwości nowego monitora połączeń, pobrać szablon w celu wprowadzenia zmian w monitorze połączeń i przesłać go za pośrednictwem Azure Resource Manager. 
* Usługa Azure Virtual Machines z rozszerzeniem Network Watcher wysyła dane do obszaru roboczego i metryk. Monitor połączeń udostępnia dane za pomocą nowych metryk (ChecksFailedPercent i RoundTripTimeMs) zamiast starych metryk (ProbesFailedPercent i AverageRoundtripMs). Stare metryki zostaną zmigrowane do nowych metryk jako ProbesFailedPercent-> ChecksFailedPercent i AverageRoundtripMs-> RoundTripTimeMs.
* Monitorowanie danych:
   * **Alerty**: automatycznie migrowane do nowych metryk.
   * **Pulpity nawigacyjne i integracje**: Wymagaj ręcznej edycji zestawu metryk. 
    
## <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz niestandardowego obszaru roboczego, upewnij się, że w subskrypcji i w regionie obszaru roboczego Log Analytics jest włączona Network Watcher. 

## <a name="migrate-the-connection-monitors"></a>Migrowanie monitorów połączeń

1. Aby migrować starsze monitory połączeń do nowszego, wybierz pozycję **monitor połączeń**, a następnie wybierz pozycję **Migruj monitory połączeń**.

    ![Zrzut ekranu przedstawiający migrację monitorów połączeń do monitora połączeń.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Wybierz swoją subskrypcję i monitory połączeń, które chcesz zmigrować, a następnie wybierz pozycję **Migruj zaznaczone**. 

Za pomocą zaledwie kilku kliknięć zostały zmigrowane istniejące monitory połączeń do monitora połączeń. Po przeprowadzeniu migracji z programu CM (klasycznego) do wersji CM nie będzie można zobaczyć monitora w obszarze CM (klasyczny)

Teraz można dostosować właściwości monitora połączeń, zmienić domyślny obszar roboczy, pobrać szablony i sprawdzić stan migracji. 

Po rozpoczęciu migracji następujące zmiany zostały wykonane: 
* Zasób Azure Resource Manager zmieni się na nowszy monitor połączeń.
    * Nazwa, region i subskrypcja monitora połączeń pozostają bez zmian. Nie dotyczy to identyfikatora zasobu.
    * Jeśli monitor połączenia nie jest dostosowany, w subskrypcji i w regionie monitora połączeń zostanie utworzony domyślny obszar roboczy Log Analytics. W tym obszarze roboczym są przechowywane dane monitorowania. Dane wynikowe testu również są przechowywane w metrykach.
    * Każdy test jest migrowany do grupy testowej o nazwie *defaultTestGroup*.
    * Źródłowe i docelowe punkty końcowe są tworzone i używane w nowej grupie testowej. Nazwy domyślne to *defaultSourceEndpoint* i *defaultDestinationEndpoint*.
    * Port docelowy i interwał sondowania są przenoszone do konfiguracji testowej o nazwie *defaultTestConfiguration*. Protokół jest ustawiany na podstawie wartości portów. Progi sukcesu i inne opcjonalne właściwości są puste.
* Alerty metryk są migrowane do alertów metryk monitora połączenia. Metryki różnią się w związku z tym zmianą. Aby uzyskać więcej informacji, zobacz [Monitorowanie łączności sieciowej z monitorem połączeń](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Monitorowane monitory połączeń nie będą już wyświetlane jako starsze rozwiązanie do monitorowania połączeń. Są one teraz dostępne do użycia tylko w monitorze połączeń.
* Wszelkie integracje zewnętrzne, takie jak pulpity nawigacyjne w Power BI i Grafana oraz integracji z systemami informacji o zabezpieczeniach i systemach zarządzania zdarzeniami (SIEM), muszą być migrowane ręcznie. Jest to jedyny krok, który należy wykonać, aby przeprowadzić migrację instalacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitora połączeń, zobacz:
* [Migrowanie z Network Performance Monitor do monitora połączeń](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Utwórz monitor połączeń przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)
