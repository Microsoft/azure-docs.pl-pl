---
title: Migrowanie do monitora połączeń z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do monitora połączeń z Network Performance Monitor.
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
ms.openlocfilehash: 998b0cb04d465f675423e2472a7ca8c6441b1fed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010409"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrowanie do monitora połączeń z Network Performance Monitor

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodać nowych testów w istniejącym obszarze roboczym ani włączyć nowego obszaru roboczego z Network Performance Monitor. Można nadal korzystać z testów utworzonych przed 1 lipca 2021. Aby zminimalizować zakłócenia działania usługi dla bieżących obciążeń, należy migrować testy z Network Performance Monitor do nowego monitora połączeń na platformie Azure Network Watcher przed 29 lutego 2024.

Testy z programu Network Performance Monitor (NPM) można migrować do nowego, ulepszonego monitora połączeń jednym kliknięciem i bez przestojów. Aby dowiedzieć się więcej o korzyściach, zobacz [monitor połączeń](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Najważniejsze punkty do uwagi

Migracja pomaga generować następujące wyniki:

* Lokalne agenci i ustawienia zapory działają zgodnie z oczekiwaniami. Nie są wymagane żadne zmiany. Log Analytics agenci zainstalowani na maszynach wirtualnych platformy Azure muszą zostać zamienione na [rozszerzenie Network Watcher](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).
* Istniejące testy są mapowane do monitora połączeń > grupy testowej > formacie testu. Wybierając pozycję **Edytuj**, można wyświetlić i zmodyfikować właściwości nowego monitora połączeń, pobrać szablon, aby wprowadzić w nim zmiany, i przesłać szablon za pośrednictwem Azure Resource Manager.
* Agenci wysyłają dane do obszaru roboczego Log Analytics i metryk.
* Monitorowanie danych:
   * **Dane w log Analytics**: przed migracją dane pozostają w obszarze roboczym, w którym npm jest skonfigurowany w tabeli NetworkMonitoring. Po migracji dane przechodzą do tabeli NetworkMonitoring, tabeli NWConnectionMonitorTestResult i NWConnectionMonitorPathResult tabeli w tym samym obszarze roboczym. Po wyłączeniu testów w programie NPM dane są przechowywane tylko w tabeli NWConnectionMonitorTestResult i w tabeli NWConnectionMonitorPathResult.
   * **Alerty oparte na dzienniku, pulpity nawigacyjne i integracje**: należy ręcznie edytować zapytania na podstawie nowej tabeli NWConnectionMonitorTestResult i tabeli NWConnectionMonitorPathResult. Aby ponownie utworzyć alerty w metrykach, zobacz [Monitorowanie łączności sieciowej z monitorem połączeń](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Do monitorowania ExpressRoute:
    * **Kompleksowa utrata i czas opóźnienia**: Monitor połączeń zapewni te możliwości i będzie łatwiejszy niż npm, ponieważ użytkownicy nie muszą konfigurować obwodów i komunikacji równorzędnej do monitorowania. Obwody w ścieżce zostaną automatycznie odnalezione, dane będą dostępne w metrykach (szybciej niż LA, gdzie NPM zapisane wyniki). Topologia również będzie działała.
    * **Pomiary przepustowości**: w przypadku uruchamiania metryk związanych z przepustowością podejście oparte na usłudze log Analytics npm nie obowiązuje w monitorowaniu przepustowości dla klientów ExpressRoute. Ta funkcja jest teraz niedostępna w monitorze połączeń.
    
## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że w subskrypcji i regionie obszaru roboczego Log Analytics jest włączona Network Watcher. 
* Jeśli maszyna wirtualna platformy Azure należąca do innego regionu/subskrypcji niż Log Analytics obszar roboczy jest używana jako punkt końcowy, upewnij się, że Network Watcher jest włączona dla tej subskrypcji i regionu.   
* Maszyny wirtualne platformy Azure z zainstalowanymi agentami Log Analytics muszą być włączone przy użyciu rozszerzenia Network Watcher.

## <a name="migrate-the-tests"></a>Migrowanie testów

Aby przeprowadzić migrację testów z Network Performance Monitor do monitora połączeń, wykonaj następujące czynności:

1. W Network Watcher wybierz pozycję **monitor połączeń**, a następnie wybierz kartę **Migrowanie testów z npm** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migruj testy z Network Performance Monitor do monitora połączeń" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Z listy rozwijanej wybierz swoją subskrypcję i obszar roboczy, a następnie wybierz funkcję NPM, którą chcesz zmigrować. 
1. Wybierz pozycję **Importuj** , aby zmigrować testy.

Po rozpoczęciu migracji następujące zmiany zostały wykonane: 
* Zostanie utworzony nowy zasób monitora połączeń.
   * Zostanie utworzony jeden monitor połączeń na region i subskrypcję. W przypadku testów z agentami lokalnymi Nowa nazwa monitora połączeń jest formatowana jako `<workspaceName>_"workspace_region_name"` . W przypadku testów z agentami platformy Azure Nowa nazwa monitora połączeń jest formatowana jako `<workspaceName>_<Azure_region_name>` .
   * Dane monitorowania są teraz przechowywane w tym samym obszarze roboczym Log Analytics, w którym włączono NPM, w nowych tabelach o nazwie NWConnectionMonitorTestResult tabeli i NWConnectionMonitorPathResult. 
   * Nazwa testu jest przenoszona jako nazwa grupy testowej. Nie przeprowadzono migracji opisu testu.
   * Źródłowe i docelowe punkty końcowe są tworzone i używane w nowej grupie testowej. W przypadku agentów lokalnych punkty końcowe są sformatowane jako `<workspaceName>_<FQDN of on-premises machine>` . Nie przeprowadzono migracji opisu agenta.
   * Port docelowy i interwał sondowania są przenoszone do konfiguracji testowej o nazwie `TC_<protocol>_<port>` i `TC_<protocol>_<port>_AppThresholds` . Protokół jest ustawiany na podstawie wartości portów. W przypadku protokołu ICMP konfiguracje testów są nazywane `TC_<protocol>` i `TC_<protocol>_AppThresholds` . Progi sukcesu i inne opcjonalne właściwości, jeśli zostały ustawione, w przeciwnym razie pozostaną puste.
   * Jeśli testy migracji zawierają agentów, które nie są uruchomione, należy włączyć agentów i przeprowadzić migrację ponownie.
* NPM nie jest wyłączona, dlatego migrowane testy mogą nadal wysyłać dane do tabeli NetworkMonitoring, NWConnectionMonitorTestResult tabeli i NWConnectionMonitorPathResult. Takie podejście zapewnia, że nie ma to żadnego oddziaływać na istniejące alerty i integracje oparte na dzienniku.
* Nowo utworzony monitor połączeń jest widoczny w monitorze połączenia.

Po migracji upewnij się, że:
* Ręcznie Wyłącz testy w NPM. Dopóki tego nie zrobisz, nadal będą naliczone opłaty. 
* Podczas wyłączania NPM należy ponownie utworzyć swoje alerty w tabelach NWConnectionMonitorTestResult i NWConnectionMonitorPathResult lub użyć metryk. 
* Migruj integracje zewnętrzne do tabel NWConnectionMonitorTestResult i NWConnectionMonitorPathResult. Przykładami integracji zewnętrznych są pulpity nawigacyjne w Power BI i Grafana oraz integracji z systemami informacji o zabezpieczeniach i systemach zarządzania zdarzeniami (SIEM).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitora połączeń, zobacz:
* [Migrowanie z monitora połączeń (klasycznego) do monitora połączeń](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Utwórz monitor połączeń przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)
