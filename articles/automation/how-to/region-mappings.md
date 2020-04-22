---
title: Mapowania obszarów roboczych automatyzacji i usługi Log Analytics platformy Azure
description: W tym artykule opisano mapowania dozwolone między kontem automatyzacji a obszarem roboczym usługi Log Analytics w celu obsługi rozwiązania
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681837"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

Podczas włączania rozwiązań, takich jak zarządzanie aktualizacjami, śledzenie zmian i zapasów lub start/stop maszyn wirtualnych w godzinach poza godzinami pracy, tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego usługi Log Analytics i konta automatyzacji. To mapowanie dotyczy tylko konta automatyzacji i obszaru roboczego usługi Log Analytics. Zasoby raportujące konto automatyzacji lub obszar roboczy usługi Log Analytics mogą przebywać w innych regionach.

## <a name="supported-mappings"></a>Obsługiwane mapowania

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Region obszaru roboczego analizy dzienników**|**Region automatyzacji platformy Azure**|
|---|---|
|**USA**||
|Okręg wyborczy EastUS<sup>1</sup>|Okręg wyborczy EastUS2|
|Okręg wyborczy WestUS2|Okręg wyborczy WestUS2|
|Okręg wyborczy WestCentralUS<sup>2</sup>|Okręg wyborczy WestCentralUS<sup>2</sup>|
|**Kanada**||
|KanadaCentral|KanadaCentral|
|**Azja i Pacyfik**||
|AustraliaSoutheast|AustraliaSoutheast|
|Południowo-wschodnia Azja|Południowo-wschodnia Azja|
|CentralIndia ( CentralIndia )|CentralIndia ( CentralIndia )|
|JaponiaJast|JaponiaJast|
|**Europa**||
|UkSouth (polski)|UkSouth (polski)|
|WestEurope|WestEurope|
|**US Gov**||
|UsGovVirginia|UsGovVirginia|

<sup>1</sup> Mapowanie EastUS dla obszarów roboczych usługi Log Analytics do kont automatyzacji nie jest dokładne mapowanie regionu do regionu, ale jest poprawne mapowanie.

<sup>2</sup> Ze względu na ograniczenia wydajności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to konta automatyzacji i obszary robocze usługi Log Analytics. Jednak istniejące wcześniej powiązane zasoby w regionie powinny nadal działać.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Jeśli zdecydujesz, że nie chcesz już integrować konta automatyzacji z obszarem roboczym usługi Log Analytics, możesz odłączyć swoje konto bezpośrednio z witryny Azure portal. Przed kontynuowaniem należy najpierw usunąć rozwiązania zarządzania aktualizacjami, śledzenia zmian i zapasów oraz maszyn wirtualnych start/stop w godzinach wolnych od pracy. Jeśli nie usuniesz ich, nie możesz wykonać operacji odłączania. Zapoznaj się z artykułem dla określonego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań można wykonać następujące kroki, aby odłączyć konto automatyzacji.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania SQL platformy Azure mogły utworzyć zasoby automatyzacji i może wymagać usunięcia przed odłączeniem obszaru roboczego.

1. W witrynie Azure portal otwórz swoje konto automatyzacji. Na stronie Konto automatyzacji wybierz pozycję **Połączony obszar roboczy** w obszarze **Zasoby pokrewne**.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**. Otrzymasz monit z weryfikacją, czy chcesz kontynuować.

3. Podczas gdy usługa Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, można śledzić postęp w obszarze **Powiadomienia** z menu.

4. Jeśli użyto rozwiązania do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

    * Harmonogramy aktualizacji — każda z nich ma nazwę odpowiadającą utworzonej wdrożeniu aktualizacji.
    * Hybrydowe grupy robocze utworzone dla rozwiązania `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`— każda ma nazwę podobną do .

5. Jeśli użyto start/stop maszyn wirtualnych w godzinach pracy rozwiązania, można opcjonalnie usunąć następujące elementy, które nie są potrzebne po usunięciu rozwiązania.

    * Uruchamianie i zatrzymywania harmonogramów ujeżdnych maszyn wirtualnych
    * Uruchamianie i zatrzymywania obrażeń ekwształtów maszyn wirtualnych
    * Zmienne

Alternatywnie można odłączyć obszar roboczy od konta automatyzacji w obszarze roboczym. 

1. W obszarze roboczym wybierz pozycję **Konto automatyzacji** w obszarze **Zasoby pokrewne**. 
2. Na stronie Konto automatyzacji wybierz pozycję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak ująć rozwiązania do zarządzania aktualizacjami i śledzenia zmian i zapasów:

    * Z [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md)
    * Z [konta Automation](../automation-onboard-solutions-from-automation-account.md)
    * Podczas [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md)
    * Z [podręcznika runbook](../automation-onboard-solutions.md)

* Dowiedz się, jak włączyć maszyny wirtualne Start/Stop w godzinach pracy:

    * [Wdrażanie maszyn wirtualnych start/stop poza godzinami pracy](../automation-solution-vm-management.md)
