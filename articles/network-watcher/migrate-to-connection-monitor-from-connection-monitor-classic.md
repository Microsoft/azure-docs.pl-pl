---
title: Migrowanie do Monitor połączenia z Monitor połączenia
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do Monitor połączenia z Monitor połączenia.
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
ms.openlocfilehash: fc5bcc7f0cd11160b33bb6501526fce9f29d710b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366389"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrowanie do Monitor połączenia z Monitor połączenia (klasycznej)

> [!IMPORTANT]
> Od 1 lipca 2021 r. nie będzie można dodawać nowych monitorów połączeń w programie Monitor połączenia (wersja klasyczna), ale można nadal korzystać z istniejących monitorów połączeń utworzonych przed 1 lipca 2021 r. Aby zminimalizować przerwy w działaniu usług dla bieżących obciążeń, przed 29 lutego 2024 r. przemigruj z usługi [Monitor połączenia (klasycznej)](migrate-to-connection-monitor-from-connection-monitor-classic.md)  do nowej usługi Monitor połączenia w usłudze Azure Network Watcher.

Istniejące monitory połączeń można migrować do nowych, ulepszonych Monitor połączenia zaledwie kilkoma kliknięciami i bez przestojów. Aby dowiedzieć się więcej o korzyściach, zobacz [Monitor połączenia](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Kluczowe kwestie, na które należy zwrócić uwagę

Migracja pomaga uzyskać następujące wyniki:

* Agenci i ustawienia zapory działają tak, jak są. Nie są wymagane żadne zmiany. 
* Istniejące monitory połączeń są mapowane na Monitor połączenia > testowej > formacie testowym. Wybierając pozycję **Edytuj,** można wyświetlać i modyfikować właściwości nowego Monitor połączenia, pobierać szablon w celu zmiany Monitor połączenia i przesyłać go za pośrednictwem Azure Resource Manager. 
* Maszyny wirtualne platformy Azure z rozszerzeniem Network Watcher wysyłają dane do obszaru roboczego i metryk. Monitor połączenia udostępnia dane za pośrednictwem nowych metryk (ChecksFailedPercent i RoundTripTimeMs) zamiast starych metryk (ProbesFailedPercent i AverageRoundtripMs). Stare metryki zostaną zmigrowane do nowych metryk jako ProbesFailedPercent -> ChecksFailedPercent i AverageRoundtripMs -> RoundTripTimeMs.
* Monitorowanie danych:
   * **Alerty:** migrowane automatycznie do nowych metryk.
   * **Pulpity nawigacyjne i integracje:** Wymagaj ręcznego edytowania zestawu metryk. 
    
## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli używasz niestandardowego obszaru roboczego, upewnij się, Network Watcher jest włączona w twojej subskrypcji i regionie obszaru roboczego usługi Log Analytics. Jeśli nie, zostanie wyświetlony komunikat o błędzie "Przed podjęciem próby migracji włącz rozszerzenie usługi Network Watcher w wybranej subskrypcji i lokalizacji wybranego obszaru roboczego usługi LA".
1. Jeśli maszyny wirtualne używane jako źródła w monitorze połączeń (wersja klasyczna) nie mają już włączonego rozszerzenia Network Watcher, zostanie wyświetlony komunikat o błędzie "Monitory połączeń z następującymi testami nie mogą zostać zaimportowane, ponieważ co najmniej jedna maszyna wirtualna platformy Azure nie ma zainstalowanego rozszerzenia usługi Network Watcher. Zainstaluj rozszerzenie usługi Network Watcher i kliknij przycisk Odśwież, aby je zaimportować."



## <a name="migrate-the-connection-monitors"></a>Migrowanie monitorów połączeń

1. Aby przeprowadzić migrację starszych monitorów połączeń do nowszego, wybierz pozycję Monitor połączenia , **a** następnie wybierz pozycję **Migruj monitory połączeń.**

    ![Zrzut ekranu przedstawiający migrację monitorów połączeń do Monitor połączenia.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Wybierz subskrypcję i monitory połączeń, które chcesz zmigrować, a następnie wybierz pozycję **Migruj.** 

Wystarczy kilka kliknięć, aby przeprowadzić migrację istniejących monitorów połączeń do Monitor połączenia. Po migracji z wersji klasycznej do wersji klasycznej nie będzie można wyświetlić monitora w obszarze CM (wersja klasyczna)

Teraz możesz dostosować Monitor połączenia, zmienić domyślny obszar roboczy, pobrać szablony i sprawdzić stan migracji. 

Po zakończeniu migracji zostaną wprowadzone następujące zmiany: 
* Zasób Azure Resource Manager zmienia się na nowszego monitora połączeń.
    * Nazwa, region i subskrypcja monitora połączeń pozostają niezmienione. Nie ma to wpływu na identyfikator zasobu.
    * Jeśli monitor połączeń nie zostanie dostosowany, domyślny obszar roboczy usługi Log Analytics zostanie utworzony w subskrypcji i w regionie monitora połączeń. W tym obszarze roboczym są przechowywane dane monitorowania. Dane wyniku testu są również przechowywane w metrykach.
    * Każdy test jest migrowany do grupy testowej o nazwie *defaultTestGroup*.
    * Źródłowe i docelowe punkty końcowe są tworzone i używane w nowej grupie testowej. Nazwy domyślne to *defaultSourceEndpoint* i *defaultDestinationEndpoint.*
    * Port docelowy i interwał sondowania są przenoszone do konfiguracji testowej o nazwie *defaultTestConfiguration.* Protokół jest ustawiany na podstawie wartości portów. Progi sukcesu i inne opcjonalne właściwości są pozostawiane puste.
* Alerty metryk są migrowane do Monitor połączenia metryk. Metryki są różne, dlatego zmiana. Aby uzyskać więcej informacji, zobacz [Monitorowanie łączności sieciowej za pomocą Monitor połączenia](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Zmigrowane monitory połączeń nie są już wyświetlane jako starsze rozwiązanie do monitorowania połączeń. Są one teraz dostępne tylko w Monitor połączenia.
* Wszelkie integracje zewnętrzne, takie jak pulpity nawigacyjne w usługach Power BI i Grafana, oraz integracje z systemami zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), muszą być migrowane ręcznie. Jest to jedyny krok ręczny, który należy wykonać, aby przeprowadzić migrację konfiguracji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Monitor połączenia, zobacz:
* [Migrowanie z Network Performance Monitor do Monitor połączenia](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Tworzenie Monitor połączenia przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)
