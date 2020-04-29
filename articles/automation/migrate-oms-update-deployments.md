---
title: Migrowanie wdrożeń aktualizacji pakietu OMS do platformy Azure
description: W tym artykule opisano sposób migrowania istniejących wdrożeń aktualizacji pakietu OMS na platformę Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680006"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrowanie wdrożeń aktualizacji pakietu OMS do platformy Azure

Portal pakietu Operations Management Suite (OMS) jest [przestarzały](../azure-monitor/platform/oms-portal-transition.md). Wszystkie funkcje dostępne w portalu pakietu OMS dla Update Management są dostępne w Azure Portal za pomocą dzienników Azure Monitor. Ten artykuł zawiera informacje potrzebne do migracji do Azure Portal.

## <a name="key-information"></a>Informacje o kluczu

* Istniejące wdrożenia będą nadal działały. Po ponownym utworzeniu wdrożenia na platformie Azure można usunąć stare wdrożenie z pakietu OMS.
* Wszystkie istniejące funkcje pakietu OMS są dostępne na platformie Azure, aby dowiedzieć się więcej na temat Update Management, zobacz [Update Management Omówienie](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Uzyskiwanie dostępu do witryny Azure Portal

W obszarze roboczym pakietu OMS kliknij pozycję **Otwórz na platformie Azure**. Wybranie tej opcji spowoduje przejście do obszaru roboczego Log Analytics, którego używa pakiet OMS.

![Otwórz na platformie Azure — Portal pakietu OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

W Azure Portal kliknij pozycję **konto usługi Automation** .

![Dzienniki usługi Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na koncie usługi Automation kliknij **Update Management**.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/azure-automation.png)

W Azure Portal wybierz pozycję **konta usługi Automation** w obszarze **wszystkie usługi**. 

W obszarze **Narzędzia do zarządzania**wybierz odpowiednie konto usługi Automation, a następnie kliknij przycisk **Update Management**.

## <a name="recreate-existing-deployments"></a>Utwórz ponownie istniejące wdrożenia

Wszystkie wdrożenia aktualizacji utworzone w portalu pakietu OMS mają [zapisane wyszukiwanie](../azure-monitor/platform/computer-groups.md) znane również jako grupa komputerów o takiej samej nazwie jak wdrożenie aktualizacji, które już istnieje. Zapisane wyszukiwanie zawiera listę maszyn, które zostały zaplanowane we wdrożeniu aktualizacji.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/oms-deployment.png)

Aby użyć istniejącego zapisanego wyszukiwania, wykonaj następujące kroki:

Aby utworzyć nowe wdrożenie aktualizacji, przejdź do Azure Portal, wybierz konto usługi Automation, które jest używane, a następnie kliknij pozycję **Update Management**. Kliknij pozycję **Zaplanuj wdrożenie aktualizacji**.

![Zaplanuj wdrożenie aktualizacji](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Zostanie otwarte okienko nowe wdrożenie aktualizacji. Wprowadź wartości dla właściwości opisanych w poniższej tabeli, a następnie kliknij pozycję **Utwórz**:

W przypadku **maszyn do zaktualizowania**Wybierz zapisane wyszukiwanie używane przez istniejące wdrożenie pakietu OMS.

| Właściwość | Opis |
| --- | --- |
|Nazwa |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| Wybierz pozycję **Linux** lub **Windows**.|
|Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md) |
|Klasyfikacje aktualizacji|Zaznacz wszystkie wymagane klasyfikacje aktualizacji. CentOS nie obsługuje tego pola.|
|Aktualizacje do wykluczenia|Wprowadź aktualizacje do wykluczenia. W przypadku systemu Windows wprowadź artykuł KB bez prefiksu **KB** . W przypadku systemu Linux wprowadź nazwę pakietu lub Użyj symbolu wieloznacznego.  |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia, a następnie wybierz **jedno lub** **cykliczne** dla cyklu. | 
| Okno obsługi |Liczba minut ustawiona dla aktualizacji. Wartość nie może być mniejsza niż 30 minut ani więcej niż 6 godzin. |
| Kontrola ponownego uruchamiania| Określa, jak należy obsługiwać ponowny rozruch.</br>Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Kliknij pozycję **zaplanowane wdrożenia aktualizacji** , aby wyświetlić stan nowo utworzonego wdrożenia aktualizacji.

![Nowe wdrożenie aktualizacji](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak wspomniano wcześniej, po skonfigurowaniu nowych wdrożeń za pośrednictwem Azure Portal istniejące wdrożenia mogą zostać usunięte z portalu pakietu OMS.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Update Management na platformie Azure, zobacz [Update Management](automation-update-management.md).
