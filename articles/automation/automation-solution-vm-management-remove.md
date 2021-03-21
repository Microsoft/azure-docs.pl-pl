---
title: Usuwanie Azure Automation Start/Stop VMs during off-hours — Omówienie
description: W tym artykule opisano sposób usuwania funkcji Start/Stop VMs during off-hours i rozłączania konta usługi Automation z obszaru roboczego Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122089"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Usuń Start/Stop VMs during off-hours z konta usługi Automation

Po włączeniu funkcji Start/Stop VMs during off-hours do zarządzania stanem działania maszyn wirtualnych platformy Azure Możesz zrezygnować z korzystania z niej. Usunięcie tej funkcji można wykonać przy użyciu jednej z następujących metod opartych na obsługiwanych modelach wdrażania:

* Usuń grupę zasobów zawierającą konto usługi Automation i połączone Azure Monitor Log Analytics obszar roboczy, każdy przeznaczony do obsługi tej funkcji.
* Odłącz obszar roboczy Log Analytics od konta usługi Automation i Usuń konto usługi Automation dedykowane dla tej funkcji.
* Usuń funkcję z konta usługi Automation i połączonego obszaru roboczego, który obsługuje inne cele zarządzania i monitorowania.

Usunięcie tej funkcji powoduje jedynie usunięcie skojarzonych elementów Runbook, nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas wdrażania, ani żadnych zdefiniowanych przez siebie niestandardowych.

## <a name="delete-the-dedicated-resource-group"></a>Usuń dedykowaną grupę zasobów

Aby usunąć grupę zasobów, wykonaj kroki opisane w artykule [Azure Resource Manager grupy zasobów i usunięcie zasobu](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Usuwanie konta usługi Automation

Aby usunąć konto usługi Automation przeznaczone do Start/Stop VMs during off-hours, wykonaj następujące czynności.

1. Zaloguj się do platformy Azure pod adresem [https://portal.azure.com](https://portal.azure.com) .

2. Przejdź do konta usługi Automation, a następnie wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

3. Wybierz pozycję **Przejdź do obszaru roboczego**.

4. W obszarze **Ogólne** kliknij pozycję **rozwiązania** .

5. Na stronie rozwiązania wybierz pozycję **Start-Stop-VM [obszar roboczy]**.

6. Na stronie **VMManagementSolution [obszar roboczy]** wybierz pozycję **Usuń** z menu.

7. Gdy informacje są weryfikowane i usunięto funkcję, można śledzić postęp w obszarze **powiadomienia**, z menu. Po zakończeniu procesu usuwania nastąpi powrót do strony rozwiązania.

### <a name="unlink-workspace-from-automation-account"></a>Odłączanie obszaru roboczego od konta usługi Automation

Istnieją dwie opcje odłączania obszaru roboczego Log Analytics od konta usługi Automation. Ten proces można wykonać na koncie usługi Automation lub w połączonym obszarze roboczym.

Aby odłączyć się od konta usługi Automation, wykonaj następujące czynności.

1. W Azure Portal wybierz pozycję **konta usługi Automation**.

2. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz opcję **Odłącz obszar roboczy** i odpowiadaj na instrukcje.

   ![Odłącz stronę obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączenia obszaru roboczego Log Analytics można śledzić postęp w obszarze **powiadomienia** z menu.

Aby odłączyć z obszaru roboczego, wykonaj następujące czynności.

1. W Azure Portal wybierz pozycję **log Analytics obszary robocze**.

2. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**.

3. Na stronie konto usługi Automation wybierz opcję **Odłącz konto** i odpowiadaj na nie.

Podczas próby odłączenia konta usługi Automation można śledzić postęp w obszarze **powiadomienia** z menu.

### <a name="delete-automation-account"></a>Usuwanie konta usługi Automation

1. W Azure Portal wybierz pozycję **konta usługi Automation**.

2. Otwórz konto usługi Automation i wybierz pozycję **Usuń** z menu.

Gdy informacje są weryfikowane i konto zostało usunięte, możesz śledzić postęp w obszarze **powiadomienia**, który został wybrany z menu.

## <a name="delete-the-feature"></a>Usuń funkcję

Aby usunąć Start/Stop VMs during off-hours z konta usługi Automation, wykonaj następujące czynności. Konto usługi Automation i obszar roboczy Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz przechowywać obszaru roboczego Log Analytics, musisz go usunąć ręcznie. Aby uzyskać więcej informacji na temat usuwania obszaru roboczego, zobacz temat [usuwanie i odzyskiwanie obszaru roboczego usługi Azure log Analytics](../azure-monitor/logs/delete-workspace.md).

1. Przejdź do konta usługi Automation, a następnie wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Wybierz pozycję **Przejdź do obszaru roboczego**.

3. W obszarze **Ogólne** kliknij pozycję **rozwiązania** .

4. Na stronie rozwiązania wybierz pozycję **Start-Stop-VM [obszar roboczy]**.

5. Na stronie **VMManagementSolution [obszar roboczy]** wybierz pozycję **Usuń** z menu.

    ![Usuwanie funkcji zarządzania maszyną wirtualną](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. W oknie Usuń rozwiązanie Potwierdź, że chcesz usunąć tę funkcję.

7. Gdy informacje są weryfikowane i usunięto funkcję, można śledzić postęp w obszarze **powiadomienia**, z menu. Po zakończeniu procesu usuwania nastąpi powrót do strony rozwiązania.

8. Jeśli nie chcesz przechowywać [zasobów](automation-solution-vm-management.md#components) utworzonych przez funkcję lub przez użytkownika (np. zmienne, harmonogramy itp.), musisz ręcznie usunąć je z konta.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć tę funkcję, zobacz [Włączanie uruchamiania/zatrzymywania poza godzinami pracy](automation-solution-vm-management-enable.md).