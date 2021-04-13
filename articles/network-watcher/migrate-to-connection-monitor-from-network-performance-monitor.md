---
title: Migrowanie do Monitor połączenia z Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak przeprowadzić migrację do Monitor połączenia z Network Performance Monitor.
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
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366355"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrowanie do Monitor połączenia z Network Performance Monitor

> [!IMPORTANT]
> Od 1 lipca 2021 r. nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego przy użyciu Network Performance Monitor. Możesz nadal używać testów utworzonych przed 1 lipca 2021 r. Aby zminimalizować zakłócenia w działaniu usług dla bieżących obciążeń, przemigruj testy z programu Network Performance Monitor do nowej usługi Monitor połączenia na platformie Azure Network Watcher do 29 lutego 2024 r.

Testy można migrować Network Performance Monitor (NPM) do nowych, ulepszonych Monitor połączenia jednym kliknięciem i bez przestojów. Aby dowiedzieć się więcej o korzyściach, zobacz [Monitor połączenia](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Kluczowe kwestie, na które należy zwrócić uwagę

Migracja pomaga uzyskać następujące wyniki:

* Agenci lokalni i ustawienia zapory działają tak, jak są. Nie są wymagane żadne zmiany. Agentów usługi Log Analytics zainstalowanych na maszynach wirtualnych platformy Azure należy zastąpić rozszerzeniem [Network Watcher](../virtual-machines/extensions/network-watcher-windows.md).
* Istniejące testy są mapowane na Monitor połączenia > grupy testów > formacie testu. Wybierając pozycję **Edytuj,** można wyświetlić i zmodyfikować właściwości nowego Monitor połączenia, pobrać szablon, aby wprowadzić w nim zmiany, a następnie przesłać szablon za pośrednictwem Azure Resource Manager.
* Agenci wysyłają dane do obszaru roboczego usługi Log Analytics i metryk.
* Monitorowanie danych:
   * **Dane usługi Log Analytics:** przed migracją dane pozostają w obszarze roboczym, w którym skonfigurowano program NPM w tabeli NetworkMonitoring. Po migracji dane trafiają do tabeli NetworkMonitoring, tabeli NWConnectionMonitorTestResult i tabeli NWConnectionMonitorPathResult w tym samym obszarze roboczym. Po wyłączeniu testów w programie NPM dane są przechowywane tylko w tabeli NWConnectionMonitorTestResult i tabeli NWConnectionMonitorPathResult.
   * **Alerty,** pulpity nawigacyjne i integracje oparte na dzienniku: należy ręcznie edytować zapytania na podstawie nowej tabeli NWConnectionMonitorTestResult i tabeli NWConnectionMonitorPathResult. Aby ponownie utworzyć alerty w metrykach, zobacz [Monitorowanie łączności sieciowej](./connection-monitor-overview.md#metrics-in-azure-monitor)za pomocą Monitor połączenia .
* W przypadku monitorowania usługi ExpressRoute:
    * **End to end loss and latency**(End to end loss and latency): Monitor połączenia to zasilanie i będzie łatwiejsze niż npm, ponieważ użytkownicy nie muszą konfigurować obwodów i komunikacji równorzędnej do monitorowania. Obwody w ścieżce zostaną automatycznie odnalezione , dane będą dostępne w metrykach (szybciej niż w przypadku la, w którym npm zapisywał wyniki). Topologia będzie również działać bezbłędnie.
    * **Pomiary przepustowości:** wraz z uruchamianiem metryk związanych z przepustowością podejście oparte na analizie dzienników npm nie było skuteczne w monitorowaniu przepustowości dla klientów usługi ExpressRoute. Ta funkcja nie jest teraz dostępna w Monitor połączenia.
    
## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, Network Watcher jest włączona w twojej subskrypcji i regionie obszaru roboczego usługi Log Analytics. Jeśli nie zostanie to zrobione, zostanie wyświetlony komunikat o błędzie "Przed podjęciem próby migracji włącz rozszerzenie usługi Network Watcher w wybranej subskrypcji i lokalizacji wybranego obszaru roboczego usługi LA".
* Jeśli maszyna wirtualna platformy Azure należąca do innego regionu/subskrypcji niż obszar roboczy usługi Log Analytics jest używana jako punkt końcowy, upewnij się, Network Watcher jest włączona dla tej subskrypcji i regionu.   
* Maszyny wirtualne platformy Azure z zainstalowanymi agentami usługi Log Analytics muszą być włączone z Network Watcher rozszerzenia.

## <a name="migrate-the-tests"></a>Migrowanie testów

Aby przeprowadzić migrację testów Network Performance Monitor do Monitor połączenia, wykonaj następujące czynności:

1. W Network Watcher wybierz pozycję Monitor połączenia , **a** następnie wybierz **kartę Migrowanie testów z narzędzia NPM.** 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrowanie testów Network Performance Monitor do Monitor połączenia" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Na listach rozwijanych wybierz subskrypcję i obszar roboczy, a następnie wybierz funkcję NPM, którą chcesz zmigrować. 
1. Wybierz **pozycję Importuj,** aby przeprowadzić migrację testów.
* Jeśli w obszarze roboczym nie włączono programu NPM, zostanie wyświetlony komunikat o błędzie "Nie znaleziono prawidłowej konfiguracji NPM". 
* Jeśli w funkcji wybranej w kroku 2 nie ma żadnych testów, zostanie wyświetlony komunikat o błędzie "Wybrany obszar roboczy nie ma <feature> konfiguracji".
* Jeśli nie ma prawidłowych testów, zostanie wyświetlony komunikat o błędzie "Wybrany obszar roboczy nie ma prawidłowych testów"
* Testy mogą zawierać agentów, którzy nie są już aktywni, ale w przeszłości być aktywni. Zostanie wyświetlony komunikat o błędzie "Kilka testów zawiera agentów, którzy nie są już aktywni. Lista nieaktywnych agentów — {0} . Ci agenci mogą być uruchamiani w przeszłości, ale są już wyłączeni lub nie działają. Włączanie agentów i migrowanie do Monitor połączenia. Kliknij przycisk Kontynuuj, aby przeprowadzić migrację testów, które nie zawierają agentów, którzy nie są aktywni".

Po zakończeniu migracji zostaną wprowadzone następujące zmiany: 
* Tworzony jest nowy zasób monitora połączeń.
   * Tworzony jest jeden monitor połączeń na region i subskrypcję. W przypadku testów z agentami lokalnymi nazwa nowego monitora połączeń jest formatowana jako `<workspaceName>_"workspace_region_name"` . W przypadku testów z agentami platformy Azure nowa nazwa monitora połączeń jest sformatowana jako `<workspaceName>_<Azure_region_name>` .
   * Dane monitorowania są teraz przechowywane w tym samym obszarze roboczym usługi Log Analytics, w którym włączono funkcję NPM, w nowych tabelach o nazwach NWConnectionMonitorTestResult i NWConnectionMonitorPathResult. 
   * Nazwa testu jest przesyłana jako nazwa grupy testowej. Opis testu nie jest migrowany.
   * Źródłowe i docelowe punkty końcowe są tworzone i używane w nowej grupie testowej. W przypadku agentów lokalnych punkty końcowe są sformatowane jako `<workspaceName>_<FQDN of on-premises machine>` . Opis agenta nie jest migrowany.
   * Port docelowy i interwał sondowania są przenoszone do konfiguracji testowej o nazwie `TC_<protocol>_<port>` i `TC_<protocol>_<port>_AppThresholds` . Protokół jest ustawiany na podstawie wartości portów. W przypadku ICMP konfiguracje testów mają nazwy i `TC_<protocol>` `TC_<protocol>_AppThresholds` . Progi sukcesu i inne opcjonalne właściwości, jeśli zostały zmigrowane, w przeciwnym razie zostaną pozostawione puste.
   * Jeśli testy migracji zawierają agentów, którzy nie są uruchomione, należy włączyć agentów i przeprowadzić migrację ponownie.
* Program NPM nie jest wyłączony, więc zmigrowane testy mogą nadal wysyłać dane do tabeli NetworkMonitoring, tabeli NWConnectionMonitorTestResult i tabeli NWConnectionMonitorPathResult. Takie podejście zapewnia, że nie ma to wpływu na istniejące alerty i integracje oparte na dziennikach.
* Nowo utworzony monitor połączeń jest widoczny w Monitor połączenia.

Po migracji upewnij się, że:
* Ręcznie wyłącz testy w programie NPM. Dopóki tego nie zrobisz, nadal będą naliczane za nie opłaty. 
* Podczas wyłączania programu NPM utwórz ponownie alerty w tabelach NWConnectionMonitorTestResult i NWConnectionMonitorPathResult lub użyj metryk. 
* Przemigruj wszystkie integracje zewnętrzne do tabel NWConnectionMonitorTestResult i NWConnectionMonitorPathResult. Przykładami integracji zewnętrznych są pulpity nawigacyjne w systemach Power BI i Grafana oraz integracje z systemami zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Monitor połączenia, zobacz:
* [Migrowanie z Monitor połączenia (klasycznej) do Monitor połączenia](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Tworzenie Monitor połączenia przy użyciu Azure Portal](./connection-monitor-create-using-portal.md)
