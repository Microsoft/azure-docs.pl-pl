---
title: Obsługiwane regiony połączonego obszaru roboczego usługi Log Analytics
description: W tym artykule opisano obsługiwane mapowania regionów między kontem usługi Automation a obszarem roboczym Log Analytics, które odnoszą się do niektórych funkcji Azure Automation.
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720175"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Obsługiwane regiony połączonego obszaru roboczego usługi Log Analytics

W Azure Automation można włączyć funkcje Update Management, Change Tracking i spisu oraz Start/Stop VMs during off-hours dla serwerów i maszyn wirtualnych. Te funkcje mają zależność od obszaru roboczego Log Analytics i dlatego wymagają konsolidacji obszaru roboczego z kontem usługi Automation. Jednak tylko niektóre regiony są obsługiwane do łączenia ich ze sobą. Ogólnie rzecz biorąc mapowanie *nie* ma zastosowania, jeśli planujesz połączyć konto usługi Automation z obszarem roboczym, w którym te funkcje nie są włączone.

Mapowania omówione tutaj dotyczą tylko łączenia obszaru roboczego Log Analytics z kontem usługi Automation. Nie mają zastosowania do maszyn wirtualnych, które są połączone z obszarem roboczym połączonym z kontem usługi Automation. Maszyny wirtualne nie są ograniczone do regionów obsługiwanych przez dany obszar roboczy Log Analytics. Mogą one znajdować się w dowolnym regionie. Należy pamiętać, że maszyny wirtualne w innym regionie mogą wpływać na stan, lokalne i krajowe wymagania prawne lub wymagania dotyczące zgodności firmy. Korzystanie z maszyn wirtualnych w innym regionie również może spowodować naliczenie opłat za przepustowość danych.

Przed połączeniem maszyn wirtualnych z obszarem roboczym w innym regionie należy zapoznać się z wymaganiami i potencjalnymi kosztami, aby potwierdzić i zrozumieć skutki prawne i koszty.

Ten artykuł zawiera obsługiwane mapowania, aby można było pomyślnie włączyć te funkcje i korzystać z nich na koncie usługi Automation.

Aby uzyskać więcej informacji, zobacz [log Analytics obszaru roboczego i konta usługi Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Obsługiwane mapowania

> [!NOTE]
> Jak pokazano w poniższej tabeli, może istnieć tylko jedno mapowanie między Log Analytics i Azure Automation.

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Log Analytics region obszaru roboczego**|**Region Azure Automation**|
|---|---|
|**USA**||
|Wschodnie<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Azja i Pacyfik**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Europa**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> mapowanie Wschodnie dla obszarów roboczych log Analytics na konta usługi Automation nie jest dokładnym mapowaniem między regionami, ale jest poprawnym mapowaniem.

<sup>2</sup> mapowanie EastUS2 dla obszarów roboczych log Analytics na konta usługi Automation nie jest dokładnym mapowaniem między regionami, ale jest poprawnym mapowaniem.

<sup>3</sup> w tym regionie obsługiwana jest tylko Update Management, a inne funkcje, takie jak Change Tracking i spis, nie są dostępne w tej chwili.

## <a name="unlink-a-workspace"></a>Odłączanie obszaru roboczego

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal. Przed kontynuowaniem należy najpierw [usunąć](move-account.md#remove-features) Update Management, Change Tracking i spis, a Start/Stop VMS during off-hours, jeśli są używane. Jeśli ich nie usuniesz, nie można ukończyć operacji odłączania.

Po usunięciu funkcji można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre funkcje, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji, które należy usunąć przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation. Na stronie konto usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Na stronie Odłącz obszar roboczy wybierz opcję **Odłącz obszar roboczy**. Zostanie wyświetlony monit o zweryfikowanie, czy chcesz kontynuować.

3. Gdy Azure Automation odłączania konta od obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

4. Jeśli użyto Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne:

    * Harmonogramy aktualizacji: każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
    * Grupy hybrydowych procesów roboczych utworzone dla funkcji: każdy ma nazwę podobną do  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Jeśli użyto Start/Stop VMs during off-hours, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne:

    * Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
    * Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
    * Zmienne

Alternatywnie możesz odłączyć obszar roboczy od konta usługi Automation w obszarze roboczym.

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**.
2. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Update Management w [Update Management Omówienie](../update-management/overview.md).
* Dowiedz się więcej na temat Change Tracking i spisu w temacie [Change Tracking i spisu](../change-tracking/overview.md).
* Dowiedz się więcej na temat Start/Stop VMs during off-hours w [Start/Stop VMS during off-hours Omówienie](../automation-solution-vm-management.md).