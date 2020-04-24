---
title: Mapowania obszaru roboczego Azure Automation i Log Analytics
description: W tym artykule opisano mapowania dozwolone między kontem usługi Automation i obszarem roboczym Log Analytics do obsługi rozwiązania
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1bc47fe6e1f0730bcff49e138df92f85ba3ef1a8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114483"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

Podczas włączania Update Management, Change Tracking i spisu lub Start/Stop VMs during off-hours tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation w ramach subskrypcji. To mapowanie ma zastosowanie tylko do konta usługi Automation i obszaru roboczego Log Analytics. Obszar roboczy Log Analytics i konto usługi Automation muszą znajdować się w tej samej subskrypcji, ale mogą znajdować się w różnych grupach zasobów wdrożonych w tym samym regionie.

Aby uzyskać więcej informacji, zobacz [log Analytics obszaru roboczego i konta usługi Automation](../azure-monitor/insights/solutions.md#log-analytics- workspace-and-automation-account).

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

<sup>1</sup> mapowanie Wschodnie dla obszarów roboczych log Analytics na konta usługi Automation nie jest dokładnym mapowaniem regionu do regionu, ale jest poprawnym mapowaniem.

<sup>2</sup> ze względu na ograniczenia pojemności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to konta usługi Automation i Log Analytics obszary robocze. Jednak wcześniej istniejące połączone zasoby w regionie powinny być nadal wykonywane.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal. Przed kontynuowaniem należy najpierw usunąć Update Management, Change Tracking i spis, a Start/Stop VMs during off-hours, jeśli są używane. Jeśli ich nie usuniesz, nie można ukończyć operacji odłączania. Zapoznaj się z artykułem dotyczącym każdego elementu, który jest włączany, aby zrozumieć kroki wymagane do jego usunięcia.

Po ich usunięciu można wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation. Na stronie konto usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**. Zostanie wyświetlony monit o zweryfikowanie, czy chcesz kontynuować.

3. Mimo że Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

4. Jeśli użyto Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po jego usunięciu.

    * Harmonogramy aktualizacji — każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
    * Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każdy ma nazwę podobną `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`do.

5. Jeśli użyto Start/Stop VMs during off-hours, możesz opcjonalnie usunąć następujące elementy, które nie są potrzebne po jego usunięciu.

    * Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
    * Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
    * Zmienne

Alternatywnie możesz odłączyć obszar roboczy od konta usługi Automation w obszarze roboczym.

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. 
2. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak dołączyć Update Management i Change Tracking i spisu:

    * Z [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md)
    * Na [koncie usługi Automation](../automation-onboard-solutions-from-automation-account.md)
    * Podczas [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md)
    * Z [elementu Runbook](../automation-onboard-solutions.md)

* Dowiedz się, jak dołączyć Start/Stop VMs during off-hours:

    * [Przegląd Start/Stop VMs during off-hours](../automation-solution-vm-management.md)
