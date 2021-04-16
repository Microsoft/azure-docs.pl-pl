---
title: Omówienie Azure Automation Start/Stop VMs during off-hours usuwania
description: W tym artykule opisano, jak usunąć Start/Stop VMs during off-hours i odłączyć konto usługi Automation od obszaru roboczego usługi Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533959"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Usuwanie Start/Stop VMs during off-hours z konta usługi Automation

Po włączeniu tej Start/Stop VMs during off-hours zarządzania stanem działania maszyn wirtualnych platformy Azure możesz przestać z niego korzystać. Usunięcie tej funkcji można wykonać przy użyciu jednej z następujących metod opartych na obsługiwanych modelach wdrażania:

* Usuń grupę zasobów zawierającą konto usługi Automation i Azure Monitor obszaru roboczego usługi Log Analytics, z których każda jest dedykowana do obsługi tej funkcji.
* Odłącz obszar roboczy usługi Log Analytics od konta usługi Automation i usuń konto usługi Automation dedykowane dla tej funkcji.
* Usuń tę funkcję z konta usługi Automation i połączonego obszaru roboczego, które obsługują inne cele zarządzania i monitorowania.

Usunięcie tej funkcji powoduje usunięcie tylko skojarzonych elementów Runbook, nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas wdrażania, ani żadnych niestandardowych elementów utworzonych po.

> [!NOTE]
> Przed podjęciem dalszej czynności sprawdź, czy nie zastosowano żadnych [blokad Resource Manager](../azure-resource-manager/management/lock-resources.md) subskrypcji, grupy zasobów lub zasobu, co zapobiega przypadkowemu usunięciu lub modyfikacji krytycznych zasobów. Podczas wdrażania rozwiązania Start/Stop VMs during off-hours ustawia poziom blokady na **CanNotDelete** względem kilku zasobów zależnych na koncie usługi Automation (w szczególności jego runbook i zmiennych). Aby można było usunąć konto usługi Automation, należy usunąć wszystkie blokady.

## <a name="delete-the-dedicated-resource-group"></a>Usuwanie dedykowanej grupy zasobów

Aby usunąć grupę zasobów, wykonaj kroki opisane w Azure Resource Manager grupy zasobów [i usuwania](../azure-resource-manager/management/delete-resource-group.md) zasobów.

## <a name="delete-the-automation-account"></a>Usuwanie konta usługi Automation

Aby usunąć konto usługi Automation dedykowane Start/Stop VMs during off-hours, wykonaj następujące kroki.

1. Zaloguj się do platformy Azure na stronie [https://portal.azure.com](https://portal.azure.com) .

2. Przejdź do konta usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby.**

3. Wybierz **pozycję Przejdź do obszaru roboczego.**

4. Kliknij **pozycję Rozwiązania** w obszarze **Ogólne.**

5. Na stronie Rozwiązania wybierz pozycję **Start-Stop-VM[Workspace]**.

6. Na stronie **VMManagementSolution[Workspace]** wybierz **pozycję Usuń** z menu.

7. Gdy informacje zostaną zweryfikowane i funkcja zostanie usunięta, możesz śledzić postęp w obszarze **Powiadomienia** wybrane z menu. Po zakończeniu procesu usuwania wrócisz do strony Rozwiązania.

### <a name="unlink-workspace-from-automation-account"></a>Odłączanie obszaru roboczego od konta usługi Automation

Istnieją dwie opcje odłączania obszaru roboczego usługi Log Analytics od konta usługi Automation. Ten proces można wykonać z konta usługi Automation lub z połączonego obszaru roboczego.

Aby odłączyć się od konta usługi Automation, wykonaj następujące kroki.

1. W Azure Portal wybierz pozycję **Konta usługi Automation.**

2. Otwórz konto usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz pozycję Odłącz obszar **roboczy** i odpowiedz na monity.

   ![Strona odłączanie obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączyć obszar roboczy usługi Log Analytics możesz śledzić postęp w obszarze **Powiadomienia** z menu.

Aby odłączyć się od obszaru roboczego, wykonaj następujące kroki.

1. W obszarze Azure Portal **obszarów roboczych usługi Log Analytics.**

2. W obszarze roboczym wybierz pozycję **Konto usługi Automation w** obszarze Powiązane **zasoby.**

3. Na stronie Konto usługi Automation wybierz pozycję **Odłącz konto** i odpowiedz na monity.

Podczas próby odłączyć konto usługi Automation możesz śledzić postęp w obszarze **Powiadomienia** z menu.

### <a name="delete-automation-account"></a>Usuwanie konta usługi Automation

1. W Azure Portal wybierz pozycję **Konta usługi Automation.**

2. Otwórz konto usługi Automation i wybierz **pozycję Usuń** z menu.

Gdy informacje zostaną zweryfikowane i konto zostanie usunięte, możesz śledzić postęp w obszarze **Powiadomienia** wybranego z menu.

## <a name="delete-the-feature"></a>Usuwanie funkcji

Aby usunąć Start/Stop VMs during off-hours z konta usługi Automation, wykonaj następujące kroki. Konto usługi Automation i obszar roboczy usługi Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego usługi Log Analytics, musisz usunąć go ręcznie. Aby uzyskać więcej informacji na temat usuwania obszaru roboczego, zobacz [Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics.](../azure-monitor/logs/delete-workspace.md)

1. Przejdź do konta usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby.**

2. Wybierz **pozycję Przejdź do obszaru roboczego.**

3. Kliknij **pozycję Rozwiązania** w obszarze **Ogólne.**

4. Na stronie Rozwiązania wybierz pozycję **Start-Stop-VM[Workspace]**.

5. Na stronie **VMManagementSolution[Workspace]** wybierz **z** menu pozycję Usuń.

    ![Usuwanie funkcji zarządzania maszyną wirtualną](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. W oknie Usuwanie rozwiązania potwierdź, że chcesz usunąć tę funkcję.

7. Gdy informacje zostaną zweryfikowane i funkcja zostanie usunięta, możesz śledzić postęp w obszarze **Powiadomienia** wybranego z menu. Po zakończeniu procesu usuwania wrócisz do strony Rozwiązania.

8. Jeśli nie chcesz zachować zasobów [](automation-solution-vm-management.md#components) utworzonych przez funkcję lub przez Ciebie później (np. zmienne, harmonogramy itp.), musisz usunąć je ręcznie z konta.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć tę funkcję, zobacz [Włączanie/zatrzymywanie poza godzinami pracy.](automation-solution-vm-management-enable.md)