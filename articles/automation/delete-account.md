---
title: Usuwanie konta Azure Automation magazynu
description: W tym artykule opisano sposób usuwania konta usługi Automation w różnych scenariuszach konfiguracji.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: d088f3adc391068de5e337c10ab52dc3d3a2dd07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535543"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Jak usunąć konto Azure Automation użytkownika

Po włączeniu konta usługi Azure Automation w celu zautomatyzowania procesu IT lub biznesowego lub włączeniu jego innych funkcji w celu obsługi zarządzania operacjami maszyn platformy Azure i maszyn spoza platformy Azure, takich jak Update Management, można przestać korzystać z konta usługi Automation. Jeśli włączono funkcje, które są zależne od integracji z obszarem roboczym usługi Azure Monitor Log Analytics, do wykonania tej akcji jest wymaganych więcej kroków.

Usunięcie konta usługi Automation można wykonać przy użyciu jednej z następujących metod opartych na obsługiwanych modelach wdrażania:

* Usuń grupę zasobów zawierającą konto usługi Automation.
* Usuń grupę zasobów zawierającą konto usługi Automation i Azure Monitor obszaru roboczego usługi Log Analytics, jeśli:

    * Konto i obszar roboczy są przeznaczone do obsługi Update Management, Śledzenie zmian i spis i/lub Start/Stop VMs during off-hours.
    * Konto jest przeznaczone do automatyzacji procesów i zintegrowane z obszarem roboczym w celu wysyłania stanu zadania runbook i strumieni zadań.

* Odłącz obszar roboczy usługi Log Analytics od konta usługi Automation i usuń konto usługi Automation.
* Usuń tę funkcję z połączonego obszaru roboczego, odłącz konto od obszaru roboczego, a następnie usuń konto usługi Automation.

W tym artykule opisano, jak całkowicie usunąć konto usługi Automation za pośrednictwem witryny Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

> [!NOTE]
> Przed podjęciem dalszej czynności sprawdź, czy nie zastosowano żadnych [blokad Resource Manager](../azure-resource-manager/management/lock-resources.md) subskrypcji, grupy zasobów lub zasobu, co zapobiega przypadkowemu usunięciu lub modyfikacji krytycznych zasobów. Jeśli wdrożono rozwiązanie usługi Start/Stop VMs during off-hours, ustawia poziom blokady na **CanNotDelete** względem kilku zasobów zależnych na koncie usługi Automation (w szczególności jego podręczników Runbook i zmiennych). Aby można było usunąć konto usługi Automation, należy usunąć wszystkie blokady.

## <a name="delete-the-dedicated-resource-group"></a>Usuwanie dedykowanej grupy zasobów

Aby usunąć konto usługi [Automation, a także](../azure-resource-manager/management/delete-resource-group.md) obszar roboczy usługi Log Analytics, jeśli jest połączony z kontem utworzonym w tej samej grupie zasobów dedykowanej dla konta, wykonaj kroki opisane w artykule Azure Resource Manager resource group and resource deletion (Grupa zasobów usługi Azure Resource Manager i usuwanie zasobów).

## <a name="delete-a-standalone-automation-account"></a>Usuwanie autonomicznego konta usługi Automation

Jeśli konto usługi Automation nie jest połączone z obszarem roboczym usługi Log Analytics, wykonaj następujące kroki, aby go usunąć.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się do platformy Azure pod witrynie [https://portal.azure.com](https://portal.azure.com) .

2. W Azure Portal przejdź do konta **usługi Automation.**

3. Otwórz konto usługi Automation i wybierz **pozycję Usuń** z menu.

Gdy informacje zostaną zweryfikowane i konto zostanie usunięte, możesz śledzić postęp w obszarze **Powiadomienia** wybranego z menu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

To polecenie usuwa konto usługi Automation bez monitowania o weryfikację.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Usuwanie autonomicznego konta usługi Automation połączonego z obszarem roboczym

Jeśli konto usługi Automation jest połączone z obszarem roboczym usługi Log Analytics w celu zbierania strumieni zadań i dzienników zadań, wykonaj następujące kroki, aby usunąć konto.

Istnieją dwie opcje odłączania obszaru roboczego usługi Log Analytics od konta usługi Automation. Ten proces można wykonać z konta usługi Automation lub z połączonego obszaru roboczego.

Aby odłączyć się od konta usługi Automation, wykonaj następujące kroki.

1. W Azure Portal przejdź do konta **usługi Automation.**

2. Otwórz konto usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz pozycję Odłącz obszar **roboczy** i odpowiedz na monity.

   ![Strona odłączanie obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączyć obszar roboczy usługi Log Analytics możesz śledzić postęp w obszarze **Powiadomienia** z menu.

Aby odłączyć od obszaru roboczego, wykonaj następujące kroki.

1. W chmurze Azure Portal do obszaru **roboczego usługi Log Analytics.**

2. W obszarze roboczym wybierz pozycję **Konto usługi Automation w** obszarze Powiązane **zasoby.**

3. Na stronie Konto usługi Automation wybierz pozycję **Odłącz konto** i odpowiedz na monity.

Gdy próbuje odłączyć konto usługi Automation, możesz śledzić postęp w obszarze **Powiadomienia** z menu.

Po pomyślnym odłączyć konto usługi Automation od obszaru roboczego wykonaj kroki opisane w sekcji autonomicznego konta [usługi Automation,](#delete-a-standalone-automation-account) aby usunąć konto.

## <a name="delete-a-shared-capability-automation-account"></a>Usuwanie konta usługi Automation z udostępnioną możliwością

Aby usunąć konto usługi Automation połączone z obszarem roboczym usługi Log Analytics na Update Management, Śledzenie zmian i spis i/lub Start/Stop VMs during off-hours, wykonaj następujące kroki.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Krok 1. Usuwanie rozwiązania z połączonego obszaru roboczego

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się do platformy Azure na stronie [https://portal.azure.com](https://portal.azure.com) .

2. Przejdź do konta usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby.**

3. Wybierz **pozycję Przejdź do obszaru roboczego.**

4. Kliknij **pozycję Rozwiązania** w obszarze **Ogólne.**

5. Na stronie Rozwiązania wybierz jedną z następujących funkcji wdrożonych na koncie:

    * Aby Start/Stop VMs during off-hours, wybierz **pozycję Start-Stop-VM[nazwa obszaru roboczego]**.
    * Aby Update Management, wybierz **pozycję Aktualizacje (nazwa obszaru roboczego).**
    * W Śledzenie zmian i spis wybierz pozycję **ChangeTracking(nazwa obszaru roboczego).**

6. Na stronie **Rozwiązanie** wybierz pozycję **Usuń** z menu. Jeśli na koncie usługi Automation i połączonym obszarze roboczym wdrożono więcej niż jedną z wymienionych powyżej funkcji, przed podjęciem kolejnej czynności należy wybrać i usunąć każdą z nich.

7. Gdy informacje zostaną zweryfikowane i funkcja zostanie usunięta, możesz śledzić postęp w obszarze **Powiadomienia** wybranego z menu. Po zakończeniu procesu usuwania wrócisz do strony Rozwiązania.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć zainstalowane rozwiązanie przy użyciu Azure PowerShell, użyj polecenia cmdlet [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Krok 2. Odłączanie obszaru roboczego od konta usługi Automation

Istnieją dwie opcje odłączania obszaru roboczego usługi Log Analytics od konta usługi Automation. Ten proces można wykonać z konta usługi Automation lub z połączonego obszaru roboczego.

Aby odłączyć się od konta usługi Automation, wykonaj następujące kroki.

1. W Azure Portal przejdź do konta **usługi Automation.**

2. Otwórz konto usługi Automation i wybierz pozycję **Połączony obszar roboczy w** obszarze Powiązane **zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz pozycję Odłącz obszar **roboczy** i odpowiedz na monity.

   ![Strona odłączanie obszaru roboczego](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Podczas próby odłączyć obszar roboczy usługi Log Analytics możesz śledzić postęp w obszarze **Powiadomienia** z menu.

Aby odłączyć się od obszaru roboczego, wykonaj następujące kroki.

1. W Azure Portal przejdź do obszaru **roboczego usługi Log Analytics.**

2. W obszarze roboczym wybierz pozycję **Konto usługi Automation w** obszarze Powiązane **zasoby.**

3. Na stronie Konto usługi Automation wybierz pozycję **Odłącz konto** i odpowiedz na monity.

Podczas próby odłączyć konto usługi Automation możesz śledzić postęp w obszarze **Powiadomienia** z menu.

### <a name="step-3-delete-automation-account"></a>Krok 3. Usuwanie konta usługi Automation

Po pomyślnym odłączyć konto usługi Automation od obszaru roboczego wykonaj kroki opisane w sekcji autonomicznego konta usługi [Automation,](#delete-a-standalone-automation-account) aby usunąć konto.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć konto usługi Automation na Azure Portal, zobacz [Tworzenie autonomicznego Azure Automation usługi](automation-create-standalone-account.md). Jeśli wolisz utworzyć konto przy użyciu szablonu, zobacz Create an Automation account using an Azure Resource Manager template (Tworzenie konta usługi Automation przy [użyciu Azure Resource Manager szablonu).](quickstart-create-automation-account-template.md)