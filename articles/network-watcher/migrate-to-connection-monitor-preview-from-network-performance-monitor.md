---
title: Migrowanie do monitora połączeń (wersja zapoznawcza) z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do monitora połączeń (wersja zapoznawcza) z Network Performance Monitor.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701836"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrowanie do monitora połączeń (wersja zapoznawcza) z Network Performance Monitor

Możesz migrować testy z Network Performance Monitor do nowego i udoskonalonego monitora połączeń (wersja zapoznawcza) w jednym kliknięciem i bez przestojów. Aby dowiedzieć się więcej na temat korzyści, możesz odczytać [monitor połączeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Tylko testy z monitora łączności usług można migrować do monitora połączeń (wersja zapoznawcza).
>

## <a name="key-points-to-note"></a>Najważniejsze punkty do uwagi

* Lokalne agenci i ustawienia zapory będą działały zgodnie z oczekiwaniami. Nie są wymagane żadne zmiany. Log Analytics agenci zainstalowani na platformie Azure Virtual Machines muszą zostać zamienione na rozszerzenie Network Watcher
* Istniejące testy zostaną zamapowane na monitor połączeń (wersja zapoznawcza) — > > grupy testowej. Użytkownicy mogą kliknąć przycisk *Edytuj* , aby wyświetlić i zmodyfikować właściwości nowego monitora połączeń i pobrać szablon, aby wprowadzić zmiany w monitorze połączeń i przesłać go za pośrednictwem Azure Resource Manager.
* Agenci wysyłają dane do obszaru roboczego i metryk Log Analytics.
* Dane monitorowania
    * Dane w Log Analytics — wszystkie wstępne migracje danych będą nadal znajdować się w obszarze roboczym, w którym NPM jest skonfigurowany w tabeli NetworkMonitoring. Po migracji dane przechodzą do tabeli NetworkMonitoring i tabeli ConnectionMonitor_CL w tym samym obszarze roboczym. Gdy testy są wyłączone z NPM, dane będą przechowywane tylko w tabeli ConnectionMonitor_CL
    * Alerty oparte na dziennikach, pulpity nawigacyjne i integracje — trzeba ręcznie edytować zapytania w oparciu o nową tabelę ConnectionMonitor_CL. Możesz również ponownie utworzyć alerty w metrykach za pomocą tego linku. Możliwość migracji dzienników opartych na alertach w tabeli NetworkMonitoring do alertów opartych na metrykach automatycznie w ramach migracji będzie dostępna wkrótce
    
## <a name="prerequisites"></a>Wymagania wstępne

*   Upewnij się, że Network Watcher jest włączona w subskrypcji i regionie obszaru roboczego Log Analytics
*   Usługa Azure Virtual Machines z zainstalowanymi agentami usługi log Analytics będzie musiała zostać włączona z rozszerzeniem Network Watcher

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Kroki migracji testów z Network Performance Monitor do monitora połączeń (wersja zapoznawcza)

1.  Kliknij pozycję "Monitor połączeń", przejdź do "Migruj testy z NPM", aby przeprowadzić migrację testów do monitora połączeń (wersja zapoznawcza)

    ![Zrzut ekranu przedstawiający migrację testów z NPM do podglądu monitora połączeń](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Wybierz pozycję subskrypcja, obszar roboczy i funkcję NPM, którą chcesz zmigrować. Obecnie można migrować tylko testy z monitora łączności usług.  
1.  Kliknij przycisk "Importuj", aby zmigrować testy
1.  Po rozpoczęciu migracji następuje wprowadzenie następujących zmian: 
    1. Zostanie utworzony nowy zasób monitora połączeń
        1. Zostanie utworzony jeden monitor połączeń na region i subskrypcję. W przypadku testów z agentami lokalnymi Nowa nazwa monitora połączeń ma format <workspaceName> _"lokalna". W przypadku testów z agentami platformy Azure Nowa nazwa monitora połączeń ma format <workspaceName> _<Azure_region_name>
        1. Dane monitorowania są teraz przechowywane w tym samym obszarze roboczym Log Analytics, w którym włączono NPM, w nowej tabeli o nazwie Connectionmonitor_CL tabeli. 
        1. Nazwa testu jest przenoszona do przodu do nazwy grupy testowej. Opis testu nie zostanie zmigrowany.
        1. Źródłowe i docelowe punkty końcowe są tworzone i używane w utworzonej grupie testowej. W przypadku agentów lokalnych punkty końcowe są nazywane w formacie <workspaceName> _"punkt końcowy"_ <FQDN of on-premises machine> . W przypadku platformy Azure, jeśli testy migracji zawierają agentów nie są uruchomione, należy włączyć agentów i przeprowadzić migrację ponownie.
        1. Port docelowy i interwał sondowania są przenoszone do konfiguracji testu, a mianowicie "TC"_ <testname> "i" TC "_ <testname> _" AppThresholds ". Na podstawie wartości portów ustawiony jest protokół. Progi sukcesu i inne opcjonalne właściwości są puste.
    1. NPM nie jest wyłączona. W związku z tym migrowane testy nadal wysyłają dane do tabeli NetworkMonitoring oraz tabeli ConnectionMonitor_CL. Ten krok zapewnia, że nie ma to wpływu na istniejące alerty i integracje oparte na dzienniku. Migrowanie alertów opartych na dziennikach w tabeli NetworkMonitoring do alertów opartych na metrykach automatycznie w ramach migracji będzie dostępne wkrótce.
    1. Nowo utworzony monitor połączeń będzie widoczny w monitorze połączeń (wersja zapoznawcza)
1.  Po migracji należy ręcznie wyłączyć testy w NPM. Dopóki tego nie zrobisz, nadal będzie naliczana opłata za ten sam sposób. Podczas wyłączania NPM upewnij się, że ponownie utworzysz alerty w tabeli ConnectionMonitor_CL lub użyj metryk. Należy również upewnić się, że wszystkie zewnętrzne integracje, takie jak pulpity nawigacyjne w Power BI Grafana, integracje z systemami SIEM, będą musiały zostać zmigrowane do ConnectionMonitor_CL tabeli


## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak przeprowadzić migrację z monitora połączeń do monitora połączeń (wersja zapoznawcza)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Dowiedz się [, jak utworzyć monitor połączeń (wersja zapoznawcza) przy użyciu Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
