---
title: Usuwanie konta Azure Automation
description: W tym artykule opisano sposób usuwania konta usługi Automation w różnych scenariuszach konfiguracji.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735094"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Jak usunąć konto Azure Automation

Po włączeniu konta Azure Automation, aby ułatwić automatyzację lub proces biznesowy, lub włączyć inne funkcje do obsługi zarządzania operacjami na maszynach z platformą Azure i poza platformą Azure, takich jak Update Management, można zrezygnować z korzystania z konta usługi Automation. Jeśli włączono funkcje, które są zależne od integracji z obszarem roboczym Azure Monitor Log Analytics, wykonanie tej akcji wymaga wykonania większej liczby kroków.

Usuwanie konta usługi Automation można wykonać przy użyciu jednej z następujących metod opartych na obsługiwanych modelach wdrażania:

* Usuń grupę zasobów zawierającą konto usługi Automation.
* Usuń grupę zasobów zawierającą konto usługi Automation i połączone Azure Monitor Log Analytics obszaru roboczego, jeśli:

    * Konto i obszar roboczy są przeznaczone do obsługi Update Management, Change Tracking i spisu oraz/lub Start/Stop VMs during off-hours.
    * Konto jest przeznaczone do przetwarzania automatyzacji i zintegrowane z obszarem roboczym do wysyłania strumieni zadań i stanów zadań elementu Runbook.

* Odłącz obszar roboczy Log Analytics od konta usługi Automation i Usuń konto usługi Automation.
* Usuń funkcję z połączonego obszaru roboczego, Odłącz konto od obszaru roboczego, a następnie usuń konto usługi Automation.

W tym artykule opisano, jak całkowicie usunąć konto usługi Automation za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="delete-the-dedicated-resource-group"></a>Usuń dedykowaną grupę zasobów

Aby usunąć konto usługi Automation, a także obszar roboczy Log Analytics, jeśli jest połączony z kontem utworzonym w tej samej grupie zasobów, która jest przeznaczona dla konta, wykonaj kroki opisane w artykule [Azure Resource Manager grup zasobów i usuwania zasobów](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-a-standalone-automation-account"></a>Usuwanie autonomicznego konta usługi Automation

Jeśli konto usługi Automation nie jest połączone z obszarem roboczym Log Analytics, wykonaj następujące kroki, aby je usunąć.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się do platformy Azure pod adresem [https://portal.azure.com](https://portal.azure.com) .

2. W Azure Portal przejdź do **konta usługi Automation**.

3. Otwórz konto usługi Automation i wybierz pozycję **Usuń** z menu.

Gdy informacje są weryfikowane i konto zostało usunięte, możesz śledzić postęp w obszarze **powiadomienia**, który został wybrany z menu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

To polecenie usuwa konto usługi Automation bez monitowania o weryfikację.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Usuwanie autonomicznego konta usługi Automation połączonego z obszarem roboczym

Jeśli konto usługi Automation jest połączone z obszarem roboczym Log Analytics w celu zebrania strumieni zadań i dzienników zadań, wykonaj następujące kroki, aby usunąć konto.

Istnieją dwie opcje odłączania obszaru roboczego Log Analytics od konta usługi Automation. Ten proces można wykonać na koncie usługi Automation lub w połączonym obszarze roboczym.

Aby odłączyć się od konta usługi Automation, wykonaj następujące czynności.

1. W Azure Portal przejdź do **konta usługi Automation**.

2. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz opcję **Odłącz obszar roboczy** i odpowiadaj na polecenia.

   ![Odłącz stronę obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączenia obszaru roboczego Log Analytics można śledzić postęp w obszarze **powiadomienia** z menu.

Aby odłączyć z obszaru roboczego, wykonaj następujące czynności.

1. W Azure Portal przejdź do **obszaru log Analytics obszary robocze**.

2. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**.

3. Na stronie konto usługi Automation wybierz opcję **Odłącz konto** i odpowiadaj na instrukcje.

Podczas próby odłączenia konta usługi Automation można śledzić postęp w obszarze **powiadomienia** z menu.

Po pomyślnym odłączeniu konta usługi Automation od obszaru roboczego wykonaj kroki opisane w sekcji [autonomiczne konto usługi Automation](#delete-a-standalone-automation-account) , aby usunąć konto.

## <a name="delete-a-shared-capability-automation-account"></a>Usuń udostępnione konto automatyzacji możliwości

Aby usunąć konto usługi Automation połączone z obszarem roboczym Log Analytics w ramach obsługi Update Management, Change Tracking i spisu oraz/lub Start/Stop VMs during off-hours, wykonaj następujące czynności.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Krok 1. Usuwanie rozwiązania z połączonego obszaru roboczego

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się do platformy Azure pod adresem [https://portal.azure.com](https://portal.azure.com) .

2. Przejdź do konta usługi Automation, a następnie wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

3. Wybierz pozycję **Przejdź do obszaru roboczego**.

4. W obszarze **Ogólne** kliknij pozycję **rozwiązania** .

5. Na stronie rozwiązania wybierz jedną z następujących opcji na podstawie funkcji wdrożonych na koncie:

    * W obszarze Start/Stop VMs during off-hours wybierz pozycję **Start-Stop-VM [nazwa obszaru roboczego]**.
    * W obszarze Update Management wybierz pozycję **aktualizacje (nazwa obszaru roboczego)**.
    * W obszarze Change Tracking i Spis wybierz pozycję **śledzenia zmian (nazwa obszaru roboczego)**.

6. Na stronie **rozwiązanie** wybierz pozycję **Usuń** z menu. Jeśli na koncie usługi Automation i połączonym obszarze roboczym są wdrożone więcej niż jedna z powyższych funkcji, przed kontynuowaniem musisz wybrać i usunąć każdy z wymienionych powyżej.

7. Gdy informacje są weryfikowane i usunięto funkcję, można śledzić postęp w obszarze **powiadomienia**, z menu. Po zakończeniu procesu usuwania nastąpi powrót do strony rozwiązania.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć zainstalowane rozwiązanie przy użyciu Azure PowerShell, należy użyć polecenia cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Krok 2. Odłączanie obszaru roboczego od konta usługi Automation

Istnieją dwie opcje odłączania obszaru roboczego Log Analytics od konta usługi Automation. Ten proces można wykonać na koncie usługi Automation lub w połączonym obszarze roboczym.

Aby odłączyć się od konta usługi Automation, wykonaj następujące czynności.

1. W Azure Portal przejdź do **konta usługi Automation**.

2. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz opcję **Odłącz obszar roboczy** i odpowiadaj na polecenia.

   ![Odłącz stronę obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączenia obszaru roboczego Log Analytics można śledzić postęp w obszarze **powiadomienia** z menu.

Aby odłączyć z obszaru roboczego, wykonaj następujące czynności.

1. W Azure Portal przejdź do **obszaru log Analytics obszary robocze**.

2. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**.

3. Na stronie konto usługi Automation wybierz opcję **Odłącz konto** i odpowiadaj na instrukcje.

Podczas próby odłączenia konta usługi Automation można śledzić postęp w obszarze **powiadomienia** z menu.

### <a name="step-3-delete-automation-account"></a>Krok 3. Usuwanie konta usługi Automation

Po pomyślnym odłączeniu konta usługi Automation od obszaru roboczego wykonaj kroki opisane w sekcji [autonomiczne konto usługi Automation](#delete-a-standalone-automation-account) , aby usunąć konto.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć konto usługi Automation na podstawie Azure Portal, zobacz [Tworzenie autonomicznego konta Azure Automation](automation-create-standalone-account.md). Jeśli wolisz utworzyć konto przy użyciu szablonu, zobacz [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](quickstart-create-automation-account-template.md).