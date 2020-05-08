---
title: Mapowania obszaru roboczego Azure Automation i Log Analytics
description: W tym artykule opisano mapowania dozwolone między kontem usługi Automation i obszarem roboczym Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901003"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

W Azure Automation można włączyć następujące rozwiązania: "Update Management," "Change Tracking i spisu" oraz "Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami". Należy jednak pamiętać, że tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation w ramach subskrypcji. To mapowanie ma zastosowanie tylko do konta usługi Automation i obszaru roboczego Log Analytics. Obszar roboczy Log Analytics i konto usługi Automation muszą znajdować się w tej samej subskrypcji, ale mogą znajdować się w różnych grupach zasobów wdrożonych w tym samym regionie.

Aby uzyskać więcej informacji, zobacz [log Analytics obszaru roboczego i konta usługi Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Obsługiwane mapowania

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Log Analytics region obszaru roboczego**|**Region Azure Automation**|
|---|---|
|**USA**||
|Wschodnie<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Azja i Pacyfik**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> mapowanie Wschodnie dla obszarów roboczych log Analytics na konta usługi Automation nie jest dokładnym mapowaniem między regionami, ale jest poprawnym mapowaniem.

<sup>2</sup> ze względu na ograniczenia pojemności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to konta usługi Automation i Log Analytics obszary robocze. Jednak wcześniej istniejące połączone zasoby w regionie powinny być nadal wykonywane.

## <a name="unlink-a-workspace"></a>Odłączanie obszaru roboczego

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal. Przed kontynuowaniem należy najpierw usunąć "Update Management," "Change Tracking i spisu" oraz "Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami", jeśli są używane. Jeśli ich nie usuniesz, nie można ukończyć operacji odłączania. Zapoznaj się z artykułami dotyczącymi poszczególnych rozwiązań, które są włączane, aby poznać kroki wymagane do jego usunięcia.

Po ich usunięciu można wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation. Na stronie **konto usługi Automation** wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Na stronie **Odłącz obszar roboczy** wybierz opcję **Odłącz obszar roboczy**. Zostanie wyświetlony monit o zweryfikowanie, czy chcesz kontynuować.

3. Mimo że Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

4. Jeśli użyto "Update Management," Opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po jego usunięciu.

    * Harmonogramy aktualizacji: każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
    * Grupy hybrydowych procesów roboczych utworzone dla rozwiązania: każdy ma nazwę podobną `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`do.

5. W przypadku użycia opcji "Uruchom/Zatrzymaj maszyny wirtualne poza godzinami" można opcjonalnie usunąć następujące elementy, które nie są potrzebne po jej usunięciu.

    * Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
    * Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
    * Zmienne

Alternatywnie możesz odłączyć obszar roboczy od konta usługi Automation w obszarze roboczym.

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. 
2. Na stronie **konto usługi Automation** wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak zacząć korzystać z "Update Management" i "Change Tracking i spisu":

    * Z poziomu [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md).
    * Na [koncie usługi Automation](../automation-onboard-solutions-from-automation-account.md).
    * Podczas [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md).
    * Z [elementu Runbook](../automation-onboard-solutions.md).

* Dowiedz się, jak zacząć korzystać z funkcji "Start/zatrzymywanie maszyn wirtualnych poza godzinami":

    * [Omówienie uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami](../automation-solution-vm-management.md).
