---
title: Przenoszenie konta usługi Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta automatyzacji do innej subskrypcji.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681889"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta usługi Azure Automation do innej subskrypcji

Usługa Azure Automation umożliwia przeniesienie niektórych zasobów do nowej grupy zasobów lub subskrypcji. Można przenieść zasoby za pośrednictwem witryny Azure portal, PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej o tym procesie, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Konto usługi Azure Automation jest jednym z zasobów, które można przenieść. W tym artykule dowiesz się, aby przenieść konta automatyzacji do innego zasobu lub subskrypcji. Kroki wysokiego poziomu dotyczące przenoszenia konta automatyzacji to:

1. Usuń swoje rozwiązania.
2. Odłącz obszar roboczy.
3. Przenoszenie konta automatyzacji.
4. Usuń i ponownie stwórz konta Uruchom jako.
5. Włącz ponownie swoje rozwiązania.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="remove-solutions"></a>Usuń roztwory

Aby odłączyć obszar roboczy od konta automatyzacji, należy usunąć te rozwiązania z obszaru roboczego:

- Śledzenie zmian i spis
- Zarządzanie aktualizacjami
- Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

1. Znajdź grupę zasobów w witrynie Azure Portal.
2. Znajdź każde rozwiązanie i kliknij pozycję **Usuń** na stronie Usuń zasoby.

    ![Usuwanie rozwiązań z witryny Azure portal](../media/move-account/delete-solutions.png)

    Jeśli wolisz, możesz usunąć rozwiązania za pomocą polecenia cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Usuwanie reguł alertów dla maszyny wirtualne Start/Stop w godzinach pracy

Dla start/stop maszyn wirtualnych w godzinach pracy rozwiązania, należy również usunąć reguły alertów utworzone przez rozwiązanie.

1. W witrynie Azure portal przejdź do grupy zasobów i wybierz pozycję **Monitorowanie** > alertów > **Zarządzaj regułami****alertów**.

![Strona Alerty z wyborem reguł Zarządzania alertami](../media/move-account/alert-rules.png)

2. Na stronie Reguły powinna zostać wyświetlona lista alertów skonfigurowanych w tej grupie zasobów. Rozwiązanie tworzy następujące reguły:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Zaznacz reguły po jednym naraz i kliknij przycisk **Usuń,** aby je usunąć.

    ![Strona reguł z prośbą o potwierdzenie usunięcia dla wybranych reguł](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Jeśli nie widzisz żadnych reguł alertów na stronie Reguły, zmień pole **Stan** na Wyłączone, aby wyświetlić wyłączone alerty, ponieważ być może zostały one wyłączone.

4. Po usunięciu reguł alertów należy usunąć grupę akcji utworzoną dla maszyn wirtualnych Start/Stop podczas powiadomień o rozwiązaniach poza godzinami pracy. W witrynie Azure portal wybierz pozycję **Monitor alerty** > **Zarządzaj****grupami** > akcji .

5. Wybierz **StartStop_VM_Notification**. 

6. Na stronie grupy akcji wybierz pozycję **Usuń**.

    ![Strona grupy akcji](../media/move-account/delete-action-group.png)

    Jeśli wolisz, możesz usunąć grupę akcji za pomocą polecenia cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Odłączanie obszaru roboczego

Teraz możesz odłączyć obszar roboczy:

1. W witrynie Azure portal wybierz > **obszar roboczy Połączone****zasoby powiązane z kontem** >  **automatyzacji**. 

2. Wybierz **opcję Odłącz obszar roboczy,** aby odłączyć obszar roboczy od konta automatyzacji.

    ![Odłączanie obszaru roboczego od konta automatyzacji](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta automatyzacji

Teraz możesz przenieść swoje konto automatyzacji i jego elementy runbook. 

1. W witrynie Azure portal przejdź do grupy zasobów konta automatyzacji. Wybierz **pozycję Przenieś** > **przenieś do innej subskrypcji**.

    ![Strona grupy zasobów, przejście do innej subskrypcji](../media/move-account/move-resources.png)

2. Wybierz zasoby w grupie zasobów, które chcesz przenieść. Upewnij się, że dołączasz swoje konto automatyzacji, elementy runbook i zasoby obszaru roboczego usługi Log Analytics.

## <a name="recreate-run-as-accounts"></a>Odtwórz konto Uruchom jako

[Uruchom jako konta](../manage-runas-account.md) utworzyć jednostkę usługi w usłudze Azure Active Directory do uwierzytelniania przy użyciu zasobów platformy Azure. Po zmianie subskrypcji konto automatyzacji nie używa już istniejącego konta Uruchom jako. Aby ponownie utworzyć konta Uruchom jako:

1. Przejdź do swojego konta Automatyzacji w nowej subskrypcji i wybierz pozycję **Uruchom jako konta** w obszarze Ustawienia **konta**. Zobaczysz, że konta Uruchom jako są teraz wyświetlane jako niekompletne.

    ![Uruchom jako konta są niekompletne](../media/move-account/run-as-accounts.png)

2. Usuń konta Uruchom jako po jednym naraz za pomocą przycisku **Usuń** na stronie Właściwości. 

    > [!NOTE]
    > Jeśli nie masz uprawnień do tworzenia lub wyświetlania kont Uruchom jako, zostanie wyświetlony następujący komunikat: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Aby dowiedzieć się więcej o uprawnieniach wymaganych do skonfigurowania konta Uruchom jako, zobacz Uprawnienia wymagane do [skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

3. Po usunięciu kont Uruchom jako wybierz pozycję **Utwórz** w obszarze **Konto Azure Uruchom jako**. 

4. Na stronie Dodaj konto Azure Run jako wybierz pozycję **Utwórz,** aby utworzyć jednostkę konta i usługi Uruchom jako. 

5. Powtórz powyższe kroki za pomocą konta Azure Classic Run As.

## <a name="enable-solutions"></a>Włączanie rozwiązań

Po ponownym utworzyć konto Uruchom jako, należy ponownie włączyć rozwiązania, które zostały usunięte przed przeniesieniem: 

1. Aby włączyć rozwiązanie śledzenia zmian i zapasów, wybierz pozycję Śledzenie zmian i zasoby reklamowe na koncie Automatyzacja. Wybierz obszar roboczy usługi Log Analytics, który został przeniesiony, a następnie wybierz pozycję **Włącz**.

2. Powtórz krok 1 dla rozwiązania do zarządzania aktualizacjami.

    ![Ponowne włączanie rozwiązań na przeniesionym koncie automatyzacji](../media/move-account/reenable-solutions.png)

3. Maszyny, które są wbudowane w rozwiązania są widoczne po podłączeniu istniejącego obszaru roboczego usługi Log Analytics. Aby włączyć start/stop maszyn wirtualnych w godzinach pracy rozwiązania, należy ponownie wdrożyć rozwiązanie. W obszarze **Powiązane zasoby**wybierz **pozycję Start/Stop VMs** > **Dowiedz się więcej o rozwiązaniu** > **Tworzenie** i włączanie rozwiązania w celu uruchomienia wdrożenia.

4. Na stronie Dodawanie rozwiązania wybierz obszar roboczy usługi Log Analytics i konto automatyzacji.

    ![Menu Dodaj rozwiązanie](../media/move-account/add-solution-vm.png)

5. Skonfiguruj rozwiązanie zgodnie z opisem w [rozwiązaniu Start/Stop VMs w godzinach pracy w usłudze Azure Automation.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>Sprawdź ruch

Po zakończeniu przenoszenia sprawdź, czy możliwości wymienione poniżej są włączone. 

|Możliwości|Testy|Rozwiązywanie problemów|
|---|---|---|
|Elementy Runbook|Zestaw runbook można pomyślnie uruchomić i połączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z elementami runbook](../troubleshoot/runbooks.md)
|Kontrola źródła|Synchronizację ręczną można uruchomić w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i zapasy|Sprawdź, czy są widoczne bieżące dane magazynu z twoich komputerów.|[Rozwiązywanie problemów ze śledzeniem zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, czy widzisz swoje maszyny i czy są zdrowe.</br>Uruchom wdrożenie aktualizacji oprogramowania testowego.|[Rozwiązywanie problemów z zarządzaniem aktualizacjami](../troubleshoot/update-management.md)|
|Udostępnione zasoby|Sprawdź, czy widzisz wszystkie zasoby udostępnione, takie jak [poświadczenia,](../shared-resources/credentials.md) [zmienne](../shared-resources/variables.md)i tym podobne.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przenoszeniu zasobów na platformie Azure, zobacz [Przenoszenie zasobów na platformie Azure](../../azure-resource-manager/management/move-support-resources.md).
