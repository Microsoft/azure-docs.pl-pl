---
title: Rozwiązywanie typowych błędów dołączania do usługi Azure automanage
description: Typowe błędy dołączania i rozwiązywanie problemów z nimi
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: df5133ad4bb3155afdc9d43e595591d9cfda4ea0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644446"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Rozwiązywanie typowych błędów podczas dołączania
Autozarządzanie może zakończyć się niepowodzeniem, aby dołączyć maszynę do usługi. W tym dokumencie wyjaśniono, jak rozwiązywać problemy z błędami wdrażania, udostępnia niektóre typowe przyczyny niepowodzenia wdrożeń, a także opisuje potencjalne kolejne kroki dotyczące rozwiązania problemu.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia
Rozmieszczenie maszyny do autozarządzania spowoduje utworzenie wdrożenia Azure Resource Manager. Jeśli dołączenie nie powiedzie się, warto zapoznać się z wdrożeniem w celu uzyskania dalszych szczegółów dotyczących przyczyny niepowodzenia. Istnieją linki do wdrożeń w menu wysuwanym Szczegóły niepowodzenia poniżej.

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Okno wysuwane szczegółów błędów zarządzania autozarządzaniem.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Sprawdź wdrożenia dla grupy zasobów zawierającej nieudaną maszynę wirtualną
Okno wysuwane niepowodzenie będzie zawierać link do wdrożeń w grupie zasobów zawierającej maszynę, która nie powiodła się, i nazwę prefiksu, której można użyć do filtrowania wdrożeń za pomocą programu. Kliknięcie linku spowoduje przejście do bloku wdrożenia, w którym można następnie filtrować wdrożenia, aby zobaczyć wdrożenia autodeployment na komputerze. W przypadku wdrażania w wielu regionach upewnij się, że klikniesz wdrożenie w prawidłowym regionie.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Sprawdź wdrożenia subskrypcji zawierającej nieudaną maszynę wirtualną
Jeśli nie widzisz żadnych błędów w rozmieszczeniu grupy zasobów, następnym krokiem będzie zajrzeć do wdrożeń w subskrypcji zawierającej maszynę wirtualną, która nie powiodła się. Kliknij link **wdrożenia dla subskrypcji** w menu wysuwanym niepowodzeń i przefiltruj wdrożenia przy użyciu filtru **automanage-DefaultResourceGroup** . Aby filtrować wdrożenia, użyj nazwy grupy zasobów z bloku niepowodzenie. Nazwa wdrożenia zostanie poddana sufiksowi o nazwie regionu. W przypadku wdrażania w wielu regionach upewnij się, że klikniesz wdrożenie w prawidłowym regionie.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Sprawdź wdrożenia w ramach subskrypcji połączonej z obszarem roboczym Log Analytics
Jeśli nie widzisz żadnych wdrożeń zakończonych niepowodzeniem w grupie zasobów lub subskrypcji zawierającej nieprawidłową maszynę wirtualną. Jeśli maszyna wirtualna nie została połączona z obszarem roboczym Log Analytics w innej subskrypcji, przejdź do subskrypcji połączonej z obszarem roboczym Log Analytics i sprawdź, czy wdrożenia nie powiodły się.

## <a name="common-deployment-errors"></a>Typowe błędy związane z wdrażaniem

Błąd |  Ograniczanie ryzyka
:-----|:-------------|
Błąd autozarządzania kontem niewystarczających uprawnień | Taka sytuacja może wystąpić, jeśli ostatnio przeniesiono subskrypcję zawierającą nowe konto Autozarządzanie do nowej dzierżawy. Kroki umożliwiające rozwiązanie tego problemu znajdują się [tutaj](./repair-automanage-account.md).
Region obszaru roboczego jest niezgodny z wymaganiami dotyczącymi mapowania regionów | Funkcja autozarządzania nie mogła dołączyć maszyny, ale obszar roboczy Log Analytics, z którym jest obecnie łączona maszyna, nie jest mapowany do obsługiwanego regionu automatyzacji. Upewnij się, że istniejący obszar roboczy Log Analytics i konto usługi Automation znajdują się w [obsługiwanym mapowaniu regionów](../automation/how-to/region-mappings.md).
"Przypisanie nie powiodło się; Brak dostępnych dodatkowych informacji " | Otwórz sprawę z obsługą Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)