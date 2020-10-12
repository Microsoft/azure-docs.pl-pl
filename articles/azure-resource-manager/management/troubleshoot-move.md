---
title: Rozwiązywanie problemów z błędami przenoszenia
description: Rozwiązywanie problemów z przeniesieniem zasobów do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054166"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji

Ten artykuł zawiera sugestie ułatwiające rozwiązywanie problemów dotyczących przemieszczania zasobów.

## <a name="upgrade-a-subscription"></a>Uaktualnianie subskrypcji

Jeśli chcesz uaktualnić subskrypcję platformy Azure (np. przełączenie z bezpłatnej do płatnej zgodnie z rzeczywistym użyciem), musisz przekonwertować swoją subskrypcję.

* Aby uaktualnić bezpłatną wersję próbną, zobacz [uaktualnianie bezpłatnej wersji próbnej lub Microsoft Imagine subskrypcję platformy Azure na płatność zgodnie z rzeczywistym](../../cost-management-billing/manage/upgrade-azure-subscription.md)użyciem.
* Aby zmienić konto z opcją płatność zgodnie z rzeczywistym użyciem, zobacz artykuł [zmiana subskrypcji systemu Azure z płatność zgodnie z rzeczywistym użyciem na inną ofertę](../../cost-management-billing/manage/switch-azure-offer.md).

Jeśli nie możesz przekonwertować subskrypcji, [Utwórz żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Wybierz pozycję **Zarządzanie subskrypcją** dla typu problemu.

## <a name="service-limitations"></a>Ograniczenia usługi

Niektóre usługi wymagają dodatkowych zagadnień podczas przesuwania zasobów. Jeśli przenosisz następujące usługi, upewnij się, że zostały sprawdzone wskazówki i ograniczenia.

* [Usługi aplikacji](./move-limitations/app-service-move-limitations.md)
* [Usługa Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasyczny model wdrażania](./move-limitations/classic-model-move-limitations.md)
* [Sieć](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Duże żądania

Gdy jest to możliwe, podział dużych operacji przenoszenia na oddzielne operacje przeniesienia. Menedżer zasobów natychmiast zwraca błąd, jeśli w jednej operacji znajduje się więcej niż 800 zasobów. Jednak przeniesienie mniejsze niż 800 zasobów może również zakończyć się niepowodzeniem przez przekroczenie limitu czasu.

## <a name="resource-not-in-succeeded-state"></a>Zasób nie jest w stanie pomyślnym

Gdy zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można przenieść zasobu, ponieważ nie jest on w stanie "powodzenie", może to być zależny zasób, który blokuje przeniesienie. Zazwyczaj kod błędu to **MoveCannotProceedWithResourcesNotInSucceededState**.

Jeśli źródłowa lub docelowa Grupa zasobów zawiera sieć wirtualną, Stany wszystkich zasobów zależnych dla sieci wirtualnej są sprawdzane podczas przenoszenia. Kontrola obejmuje te zasoby bezpośrednio i pośrednio zależne od sieci wirtualnej. Jeśli którykolwiek z tych zasobów jest w stanie niepowodzenia, przeniesienie jest zablokowane. Na przykład jeśli maszyna wirtualna, która używa sieci wirtualnej, zakończyła się niepowodzeniem, przenoszenie jest zablokowane. Przenoszenie jest blokowane nawet wtedy, gdy maszyna wirtualna nie jest jednym z przenoszonych zasobów i nie znajduje się w jednej z grup zasobów dla przeniesienia.

Po otrzymaniu tego błędu dostępne są dwie opcje. Przenieś zasoby do grupy zasobów, która nie ma sieci wirtualnej, lub [skontaktuj się z pomocą techniczną](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).
