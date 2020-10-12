---
title: Migruj Azure Monitor dzienniki aktualizacji do Azure Portal
description: W tym artykule opisano sposób migrowania wdrożeń aktualizacji Azure Monitor dzienników do Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: dabe914ffd4e84e8ded5c0e3b519036479fe33ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987598"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Migruj Azure Monitor dzienniki aktualizacji do Azure Portal

Portal pakietu Operations Management Suite (OMS) jest [przestarzały](../azure-monitor/platform/oms-portal-transition.md). Wszystkie funkcje dostępne w portalu pakietu OMS dla Update Management są dostępne w Azure Portal za pomocą dzienników Azure Monitor. Ten artykuł zawiera informacje potrzebne do migracji do Azure Portal.

## <a name="key-information"></a>Informacje o kluczu

* Istniejące wdrożenia będą nadal działały. Po ponownym utworzeniu wdrożenia na platformie Azure można usunąć stare wdrożenie.
* Wszystkie istniejące funkcje pakietu OMS są dostępne na platformie Azure. Aby dowiedzieć się więcej na temat Update Management, zobacz [Omówienie usługi Update Management](update-management/update-mgmt-overview.md).

## <a name="access-the-azure-portal"></a>Uzyskiwanie dostępu do witryny Azure Portal

1. W obszarze roboczym kliknij pozycję **Otwórz na platformie Azure**. 

    ![Otwórz na platformie Azure — Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. W Azure Portal kliknij pozycję **konto usługi Automation** .

    ![Dzienniki usługi Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

3. Na koncie usługi Automation kliknij **Update Management**.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Zrzut ekranu strony zarządzania aktualizacjami.":::

4. W Azure Portal wybierz pozycję **konta usługi Automation** w obszarze **wszystkie usługi**. 

5. W obszarze **Narzędzia do zarządzania**wybierz odpowiednie konto usługi Automation, a następnie kliknij przycisk **Update Management**.

## <a name="recreate-existing-deployments"></a>Utwórz ponownie istniejące wdrożenia

Wszystkie wdrożenia aktualizacji utworzone w portalu pakietu OMS mają [zapisane wyszukiwanie](../azure-monitor/platform/computer-groups.md) znane również jako grupa komputerów o takiej samej nazwie jak wdrożenie aktualizacji, które już istnieje. Zapisane wyszukiwanie zawiera listę maszyn, które zostały zaplanowane we wdrożeniu aktualizacji.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Zrzut ekranu strony zarządzania aktualizacjami.":::

Aby użyć istniejącego zapisanego wyszukiwania, wykonaj następujące kroki:

1. Aby utworzyć nowe wdrożenie aktualizacji, przejdź do Azure Portal, wybierz konto usługi Automation, które jest używane, a następnie kliknij pozycję **Update Management**. Kliknij pozycję **Zaplanuj wdrożenie aktualizacji**.

    ![Zaplanuj wdrożenie aktualizacji](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Zostanie otwarte okienko nowe wdrożenie aktualizacji. Wprowadź wartości dla właściwości opisanych w poniższej tabeli, a następnie kliknij pozycję **Utwórz**:

3. W przypadku **maszyn do zaktualizowania**Wybierz zapisane wyszukiwanie używane przez wdrożenie pakietu OMS.

    | Właściwość | Opis |
    | --- | --- |
    |Nazwa |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
    |System operacyjny| Wybierz pozycję **Linux** lub **Windows**.|
    |Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz maszynę z listy rozwijanej i wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md) |
    |Klasyfikacje aktualizacji|Zaznacz wszystkie wymagane klasyfikacje aktualizacji. CentOS nie obsługuje tego pola.|
    |Aktualizacje do wykluczenia|Wprowadź aktualizacje do wykluczenia. W przypadku systemu Windows wprowadź artykuł KB bez prefiksu **KB** . W przypadku systemu Linux wprowadź nazwę pakietu lub Użyj symbolu wieloznacznego.  |
    |Ustawienia harmonogramu|Wybierz godzinę do uruchomienia, a następnie wybierz **jedno lub** **cykliczne** dla cyklu. | 
    | Okno obsługi |Liczba minut ustawiona dla aktualizacji. Wartość nie może być mniejsza niż 30 minut ani więcej niż 6 godzin. |
    | Kontrola ponownego uruchamiania| Określa, jak należy obsługiwać ponowny rozruch.</br>Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

4. Kliknij pozycję **zaplanowane wdrożenia aktualizacji** , aby wyświetlić stan nowo utworzonego wdrożenia aktualizacji.

    ![Nowe wdrożenie aktualizacji](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Jak wspomniano wcześniej, po skonfigurowaniu nowych wdrożeń za pośrednictwem Azure Portal można usunąć istniejące wdrożenia z Azure Portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Update Management w Azure Automation, zobacz [omówienie Update Management](update-management/update-mgmt-overview.md).
