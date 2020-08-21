---
title: Migrowanie do monitora połączeń (wersja zapoznawcza) z monitora połączeń
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do monitora połączeń (wersja zapoznawcza) z monitora połączeń.
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
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701839"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrowanie do monitora połączeń (wersja zapoznawcza) z monitora połączeń

Istniejące monitory połączeń można migrować do nowego i udoskonalonego monitora połączeń (wersja zapoznawcza) w jednym kliknięciem i bez przestojów. Aby dowiedzieć się więcej na temat korzyści, możesz odczytać [monitor połączeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Najważniejsze punkty do uwagi

* Ustawienia agentów i zapory będą działały zgodnie z oczekiwaniami (bez konieczności dotknięcia) 
* Istniejące monitory połączeń zostaną zamapowane na monitor połączeń (wersja zapoznawcza) — > > grupy testowej. Użytkownicy mogą kliknąć przycisk *Edytuj* , aby wyświetlić i zmodyfikować właściwości nowego monitora połączeń i pobrać szablon, aby wprowadzić zmiany w monitorze połączeń i przesłać go za pośrednictwem Azure Resource Manager. 
* Usługa Azure Virtual Machines z rozszerzeniem Network Watcher wysyła dane do obszaru roboczego i metryk. Monitory połączeń udostępniają dane za pomocą nowych metryk (ChecksFailedPercent (wersja zapoznawcza) i RoundTripTimeMs (wersja zapoznawcza) zamiast zatrzymywania starych metryk (ProbesFailedPercent i AverageRoundtripMs) 
* Dane monitorowania
    * Alerty — zostaną zmigrowane do nowych metryk w ramach migracji
    * Pulpity nawigacyjne i integracje — należy ręcznie edytować zestaw metryk. 
    
## <a name="prerequisites"></a>Wymagania wstępne

W przypadku korzystania z niestandardowego obszaru roboczego upewnij się, że Network Watcher jest włączona w obszarze subskrypcja i region obszaru roboczego Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Kroki migracji z monitora połączeń do monitora połączeń (wersja zapoznawcza)

1. Kliknij pozycję "Monitor połączeń", przejdź do "Migrowanie monitorów połączeń", aby przeprowadzić migrację monitorów połączeń ze starszego do nowszego rozwiązania.

    ![Zrzut ekranu przedstawiający Migrowanie monitorów połączeń do wersji zapoznawczej monitora połączeń](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Wybierz pozycję Monitory subskrypcji i połączeń, a następnie kliknij pozycję "Migruj wybrane". W jednym kliknięciem Migruj istniejące monitory połączeń do monitora połączeń (wersja zapoznawcza) 
1. Można dostosować właściwości monitora połączeń, zmienić domyślny obszar roboczy, pobrać szablon i sprawdzić stan migracji. 
1. Po rozpoczęciu migracji następuje wprowadzenie następujących zmian: 
    1. Azure Resource Manager zmiany zasobów w nowszym monitorze połączeń
        1. Nazwa, region i subskrypcja monitora połączeń pozostaje niezmieniona. W związku z tym nie ma to wpływu na identyfikator zasobu.
        1. O ile nie jest to dostosowane, w regionie i subskrypcji monitora połączeń zostanie utworzony domyślny obszar roboczy Log Analytics. W tym obszarze roboczym będą przechowywane dane monitorowania. Dane wynikowe testu również będą przechowywane w metrykach.
        1. Każdy test jest migrowany do grupy testowej o nazwie * defaultTestGroup *
        1.  Źródłowe i docelowe punkty końcowe są tworzone i używane w utworzonej grupie testowej. Nazwy domyślne to *defaultSourceEndpoint* i *defaultDestinationEndpoint*
        1. Port docelowy i interwał sondowania są przenoszone do konfiguracji testu o nazwie *defaultTestConfiguration*. Na podstawie wartości portów ustawiony jest protokół. Progi sukcesu i inne opcjonalne właściwości są puste.
    1. Alerty metryk są migrowane do alertów metryk monitora połączeń (wersja zapoznawcza). Metryki są różne <link to metric section in the doc> , w związku z czym zmiana
    1. Zmigrowane monitory połączeń nie będą wyświetlane w starszym rozwiązaniu monitora połączeń, będą teraz dostępne tylko do użytku w monitorze połączeń (wersja zapoznawcza)
    1. Wszystkie zewnętrzne integracje, takie jak pulpity nawigacyjne w Power BI Grafana, integracje z systemami SIEM, muszą być migrowane bezpośrednio przez użytkownika. Jest to jedyny krok, który użytkownik musi wykonać, aby przeprowadzić migrację instalacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak przeprowadzić migrację z Network Performance Monitor do monitora połączeń (wersja zapoznawcza)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Dowiedz się [, jak utworzyć monitor połączeń (wersja zapoznawcza) przy użyciu Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
