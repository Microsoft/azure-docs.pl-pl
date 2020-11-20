---
title: Migrowanie do monitora połączeń (wersja zapoznawcza) z Network Performance Monitor
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
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 38ea682b4f3894f373d16908b942b4fa578741c4
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985811"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrowanie do monitora połączeń (wersja zapoznawcza) z Network Performance Monitor

Testy z programu Network Performance Monitor (NPM) można migrować do nowego, ulepszonego monitora połączeń jednym kliknięciem i bez przestojów. Aby dowiedzieć się więcej o korzyściach, zobacz [monitor połączeń](./connection-monitor-overview.md).

> [!IMPORTANT]
> Monitor połączeń jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
> Tylko testy z monitora łączności usług można migrować do monitora połączeń.
>

## <a name="key-points-to-note"></a>Najważniejsze punkty do uwagi

Migracja pomaga generować następujące wyniki:

* Lokalne agenci i ustawienia zapory działają zgodnie z oczekiwaniami. Nie są wymagane żadne zmiany. Log Analytics agenci zainstalowani na maszynach wirtualnych platformy Azure muszą zostać zamienione na rozszerzenie Network Watcher.
* Istniejące testy są mapowane do monitora połączeń > grupy testowej > formacie testu. Wybierając pozycję **Edytuj**, można wyświetlić i zmodyfikować właściwości nowego monitora połączeń, pobrać szablon, aby wprowadzić w nim zmiany, i przesłać szablon za pośrednictwem Azure Resource Manager.
* Agenci wysyłają dane do obszaru roboczego Log Analytics i metryk.
* Monitorowanie danych:
   * **Dane w log Analytics**: przed migracją dane pozostają w obszarze roboczym, w którym npm jest skonfigurowany w tabeli NetworkMonitoring. Po migracji dane przechodzą do tabeli NetworkMonitoring i tabeli ConnectionMonitor_CL w tym samym obszarze roboczym. Po wyłączeniu testów w programie NPM dane są przechowywane tylko w tabeli ConnectionMonitor_CL.
   * **Alerty, pulpity nawigacyjne i integracje oparte na dzienniku**: należy ręcznie edytować zapytania w oparciu o nową tabelę ConnectionMonitor_CL. Aby ponownie utworzyć alerty w metrykach, zobacz [Monitorowanie łączności sieciowej z monitorem połączeń](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że w subskrypcji i regionie obszaru roboczego Log Analytics jest włączona Network Watcher.
* Maszyny wirtualne platformy Azure z zainstalowanymi agentami Log Analytics muszą być włączone przy użyciu rozszerzenia Network Watcher.

## <a name="migrate-the-tests"></a>Migrowanie testów

Aby przeprowadzić migrację testów z Network Performance Monitor do monitora połączeń, wykonaj następujące czynności:

1. W Network Watcher wybierz pozycję **monitor połączeń**, a następnie wybierz kartę **Migrowanie testów z npm** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migruj testy z Network Performance Monitor do monitora połączeń" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Z listy rozwijanej wybierz swoją subskrypcję i obszar roboczy, a następnie wybierz funkcję NPM, którą chcesz zmigrować. Obecnie można migrować testy tylko z monitora łączności usług.  
1. Wybierz pozycję **Importuj** , aby zmigrować testy.

Po rozpoczęciu migracji następujące zmiany zostały wykonane: 
* Zostanie utworzony nowy zasób monitora połączeń.
   * Zostanie utworzony jeden monitor połączeń na region i subskrypcję. W przypadku testów z agentami lokalnymi Nowa nazwa monitora połączeń jest formatowana jako `<workspaceName>_"on-premises"` . W przypadku testów z agentami platformy Azure Nowa nazwa monitora połączeń jest formatowana jako `<workspaceName>_<Azure_region_name>` .
   * Dane monitorowania są teraz przechowywane w tym samym obszarze roboczym Log Analytics, w którym włączono NPM, w nowej tabeli o nazwie Connectionmonitor_CL. 
   * Nazwa testu jest przenoszona jako nazwa grupy testowej. Nie przeprowadzono migracji opisu testu.
   * Źródłowe i docelowe punkty końcowe są tworzone i używane w nowej grupie testowej. W przypadku agentów lokalnych punkty końcowe są sformatowane jako `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Jeśli na platformie Azure testy migracji zawierają agentów, które nie są uruchomione, należy włączyć agentów i przeprowadzić migrację ponownie.
   * Port docelowy i interwał sondowania są przenoszone do konfiguracji testowej o nazwie *TC_ \<testname>* i *TC_ \<testname> _AppThresholds*. Protokół jest ustawiany na podstawie wartości portów. Progi sukcesu i inne opcjonalne właściwości są puste.
* NPM nie jest wyłączona, dlatego migrowane testy mogą nadal wysyłać dane do tabel NetworkMonitoring i ConnectionMonitor_CL. Takie podejście zapewnia, że nie ma to żadnego oddziaływać na istniejące alerty i integracje oparte na dzienniku.
* Nowo utworzony monitor połączeń jest widoczny w monitorze połączenia.

Po migracji upewnij się, że:
* Ręcznie Wyłącz testy w NPM. Dopóki tego nie zrobisz, nadal będą naliczone opłaty. 
* Podczas wyłączania NPM należy ponownie utworzyć alerty w tabeli ConnectionMonitor_CL lub użyć metryk. 
* Migruj integracje zewnętrzne do tabeli ConnectionMonitor_CL. Przykładami integracji zewnętrznych są pulpity nawigacyjne w Power BI i Grafana oraz integracji z systemami informacji o zabezpieczeniach i systemach zarządzania zdarzeniami (SIEM).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitora połączeń, zobacz:
* [Migrowanie z monitora połączeń do monitora połączeń](/azure/network-watcher/migrate-to-connection-monitor-from-connection-monitor-classic)
* [Utwórz monitor połączeń przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)