---
title: Zarządzaj aktualizacjami i poprawkami dla maszyn wirtualnych w Azure Automation
description: W tym artykule opisano, jak używać Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure i innych niż platformy Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: 24dcb501872aabf9fac3da0cccc2a1af9c9b06ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222838"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych

Aktualizacje oprogramowania w programie Azure Automation Update Management udostępniają zestaw narzędzi i zasobów, które mogą pomóc w zarządzaniu złożonym zadaniem do śledzenia i stosowania aktualizacji oprogramowania na maszynach na platformie Azure i w chmurze hybrydowej. Efektywny proces zarządzania aktualizacjami oprogramowania jest konieczny do utrzymania wydajności działania, pokonania problemów z zabezpieczeniami i zmniejszenia ryzyka zwiększonego zagrożenia bezpieczeństwa cybernetycznymi. Jednakże z powodu zmienności technologii i stałego pojawiania się nowych zagrożeń bezpieczeństwa efektywne zarządzanie aktualizacjami oprogramowania wymaga stałej uwagi.

> [!NOTE]
> Update Management obsługuje wdrażanie aktualizacji pierwszej firmy oraz pobieranie ich z nich. Ta obsługa wymaga zmian w aktualizowanych systemach. Aby dowiedzieć się, jak skonfigurować te ustawienia w systemach, zobacz [Konfigurowanie ustawień Windows Update dla Azure Automation Update Management](configure-wuagent.md) .

Przed podjęciem próby zarządzania aktualizacjami dla maszyn wirtualnych upewnij się, że włączono Update Management na nich przy użyciu jednej z następujących metod:

* [Włączanie rozwiązania Update Management z poziomu konta usługi Automation](enable-from-automation-account.md)
* [Włącz Update Management, przeglądając Azure Portal](enable-from-portal.md)
* [Włączanie rozwiązania Update Management z poziomu elementu runbook](enable-from-runbook.md)
* [Włączanie rozwiązania Update Management z poziomu maszyny wirtualnej platformy Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Ograniczanie zakresu wdrożenia

Update Management używa konfiguracji zakresu w obszarze roboczym, aby określić komputery, na których mają zostać odebrane aktualizacje. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](scope-configuration.md).

## <a name="compliance-assessment"></a>Ocena zgodności

Przed wdrożeniem aktualizacji oprogramowania na maszynach Przejrzyj wyniki oceny zgodności aktualizacji dla włączonych maszyn. Dla każdej aktualizacji oprogramowania jej stan zgodności jest rejestrowany, a następnie po zakończeniu oceny jest zbierane i przekazywane zbiorczo do Azure Monitor dzienników.

Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin. Oprócz zaplanowanego skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od Log Analytics agenta dla ponownego uruchomienia systemu Windows, przed zainstalowaniem aktualizacji i po zainstalowaniu aktualizacji. Warto również zapoznać się z naszymi zaleceniami dotyczącymi [konfigurowania klienta Windows Update](configure-wuagent.md) przy użyciu Update Management, aby uniknąć problemów, które uniemożliwiają prawidłowe zarządzanie nimi.

W przypadku maszyny z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent Log Analytics dla systemu Linux zostanie ponownie uruchomiony, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Wyniki zgodności są prezentowane w Update Management dla każdej ocenianej maszyny. W przypadku nowej maszyny z możliwością zarządzania może upłynąć do 30 minut, aż na pulpicie nawigacyjnym będą wyświetlane zaktualizowane dane.

Przejrzyj [aktualizacje oprogramowania monitorowania](view-update-assessments.md) , aby dowiedzieć się, jak wyświetlić wyniki sprawdzania zgodności.

## <a name="deploy-updates"></a>Wdrażanie aktualizacji

Po przejrzeniu wyników zgodności faza wdrażania aktualizacji oprogramowania jest procesem wdrażania aktualizacji oprogramowania. Aby zainstalować aktualizacje, Zaplanuj wdrożenie, które jest wyrównane z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

Zapoznaj się z tematem [wdrażanie aktualizacji oprogramowania](deploy-updates.md) , aby dowiedzieć się, jak zaplanować wdrożenie aktualizacji.

## <a name="review-update-deployments"></a>Przegląd wdrożeń aktualizacji

Po zakończeniu wdrożenia Przejrzyj proces, aby określić powodzenie wdrażania aktualizacji według maszyny lub grupy docelowej. Zobacz [przegląd stanu wdrożenia](deploy-updates.md#check-deployment-status) , aby dowiedzieć się, jak można monitorować stan wdrożenia.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć alerty w celu powiadomienia o wynikach wdrożenia aktualizacji, zobacz [tworzenie alertów dla Update Management](configure-alerts.md).

* Możesz [wykonywać zapytania dotyczące dzienników Azure monitor](query-logs.md) , aby analizować oceny aktualizacji, wdrożenia i inne powiązane zadania zarządzania. Zawiera wstępnie zdefiniowane zapytania ułatwiające rozpoczęcie pracy.