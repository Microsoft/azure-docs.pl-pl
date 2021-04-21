---
title: Przenoszenie konta Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta usługi Automation do innej subskrypcji.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fd9350baf1805c0e6278b6210ad9818fa18c8d8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831496"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta Azure Automation do innej subskrypcji

Azure Automation umożliwia przeniesienie niektórych zasobów do nowej grupy zasobów lub subskrypcji. Zasoby można przenosić za pośrednictwem interfejsu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej na temat tego procesu, zobacz Przenoszenie zasobów do nowej [grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Konto usługi Automation jest jednym z zasobów, które można przenieść. Z tego artykułu dowiesz się, jak przenieść konta usługi Automation do innego zasobu lub subskrypcji. Kroki wysokiego poziomu dotyczące przenoszenia konta usługi Automation są następujące:

1. Wyłącz funkcje.
2. Odłącz obszar roboczy.
3. Przenieś konto usługi Automation.
4. Usuń i utwórz ponownie konta Uruchom jako.
5. Włącz ponownie funkcje.

## <a name="remove-features"></a>Usuwanie funkcji

Aby odłączyć obszar roboczy od konta usługi Automation, musisz usunąć zasoby funkcji w obszarze roboczym:

- Śledzenie zmian i spis
- Zarządzanie aktualizacjami
- Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

1. Znajdź grupę zasobów w witrynie Azure Portal.
2. Znajdź każdą funkcję i wybierz **pozycję Usuń** na **stronie Usuwanie** zasobów.

    ![Zrzut ekranu przedstawiający usuwanie zasobów funkcji z Azure Portal](../media/move-account/delete-solutions.png)

Jeśli wolisz, możesz usunąć zasoby za pomocą polecenia cmdlet [Remove-AzResource:](/powershell/module/Az.Resources/Remove-AzResource)

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Usuwanie reguł alertów dla Start/Stop VMs during off-hours

Na Start/Stop VMs during off-hours należy również usunąć reguły alertów utworzone przez funkcję.

1. W Azure Portal przejdź do grupy zasobów i wybierz pozycję **Monitorowanie**  >  **alertów** Zarządzaj  >  **regułami alertów.**

   ![Zrzut ekranu przedstawiający stronę Alerty z wyborem opcji Zarządzaj regułami alertów](../media/move-account/alert-rules.png)

2. Na stronie Reguły powinna zostać wyświetlona lista alertów skonfigurowanych w tej grupie zasobów. Funkcja tworzy następujące reguły:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Wybierz reguły po jednym na raz, a następnie wybierz pozycję **Usuń,** aby je usunąć.

    ![Zrzut ekranu przedstawiający stronę Reguły z żądaniem potwierdzenia usunięcia dla wybranych reguł](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Jeśli nie widzisz żadnych reguł alertów na stronie Reguły, zmień pole **Stan** na **Wyłączone,** aby wyświetlić wyłączone alerty. 

4. Po usunięciu reguł alertów należy usunąć grupę akcji utworzoną na Start/Stop VMs during off-hours powiadomień. W Azure Portal wybierz pozycję   >  **Monitoruj**  >  **alerty Zarządzaj grupami akcji.**

5. Wybierz **StartStop_VM_Notification**. 

6. Na stronie grupy akcji wybierz pozycję **Usuń.**

    ![Zrzut ekranu przedstawiający stronę Grupy akcji](../media/move-account/delete-action-group.png)

Jeśli wolisz, możesz usunąć grupę akcji za pomocą polecenia cmdlet [Remove-AzActionGroup:](/powershell/module/az.monitor/remove-azactiongroup)

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłączanie obszaru roboczego

Teraz możesz odłączyć obszar roboczy:

1. W obszarze Azure Portal wybierz pozycję Powiązane zasoby konta usługi **Automation**  >  **Połączony**  >  **obszar roboczy.** 

2. Wybierz **pozycję Odłącz obszar roboczy,** aby odłączyć obszar roboczy od konta usługi Automation.

    ![Zrzut ekranu przedstawiający odłączanie obszaru roboczego od konta usługi Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta usługi Automation

Teraz możesz przenieść konto usługi Automation i jego podręczniki Runbook. 

1. W Azure Portal przejdź do grupy zasobów konta usługi Automation. Wybierz **pozycję Przenieś** Przenieś do innej  >  **subskrypcji.**

    ![Zrzut ekranu przedstawiający stronę Grupa zasobów i przejście do innej subskrypcji](../media/move-account/move-resources.png)

2. Wybierz zasoby w grupie zasobów, którą chcesz przenieść. Upewnij się, że uwzględniasz zasoby konta usługi Automation, runbook i obszaru roboczego usługi Log Analytics.

## <a name="re-create-run-as-accounts"></a>Ponowne tworzenie kont Uruchom jako

[Konta Uruchom jako tworzą](../automation-security-overview.md#run-as-accounts) jednostkę usługi w usłudze Azure Active Directory do uwierzytelniania za pomocą zasobów platformy Azure. Po zmianie subskrypcji konto usługi Automation nie używa już istniejącego konta Uruchom jako. Aby ponownie utworzyć konta Uruchom jako:

1. Przejdź do konta usługi Automation w nowej subskrypcji, a następnie wybierz pozycję **Konta Uruchom jako w** obszarze Ustawienia **konta.** Zobaczysz, że konta Uruchom jako są teraz wyświetlane jako niekompletne.

    ![Zrzut ekranu przedstawiający niekompletne konta Uruchom jako](../media/move-account/run-as-accounts.png)

2. Usuń konta Uruchom jako po jednym na raz, wybierając pozycję **Usuń** na **stronie** Właściwości. 

    > [!NOTE]
    > Jeśli nie masz uprawnień do tworzenia lub wyświetlania kont Uruchom jako, zostanie wyświetlony następujący komunikat: Aby uzyskać więcej informacji, zobacz Uprawnienia wymagane do konfigurowania kont `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` [Uruchom jako.](../automation-security-overview.md#permissions)

3. Po usunięciu kont Uruchom jako  wybierz pozycję Utwórz w **obszarze Konto Uruchom jako platformy Azure.** 

4. Na stronie Dodawanie konta Uruchom jako platformy Azure wybierz pozycję **Utwórz,** aby utworzyć konto Uruchom jako i jednostkę usługi. 

5. Powtórz powyższe kroki przy użyciu klasycznego konta Uruchom jako platformy Azure.

## <a name="enable-features"></a>Włączanie funkcji

Po utworzeniu ponownie kont Uruchom jako należy ponownie włączyć usunięte funkcje przed przeniesieniem:

1. Aby włączyć Śledzenie zmian i spis, wybierz **pozycję Śledzenie zmian i spis** na koncie usługi Automation. Wybierz obszar roboczy usługi Log Analytics, który został przeniesiony, a następnie wybierz pozycję **Włącz.**

2. Powtórz krok 1 dla Update Management.

    ![Zrzut ekranu przedstawiający ponowne włączanie funkcji na przeniesionym koncie usługi Automation](../media/move-account/reenable-solutions.png)

3. Maszyny z włączonymi funkcjami są widoczne po połączeniu z istniejącym obszarem roboczym usługi Log Analytics. Aby włączyć Start/Stop VMs during off-hours, należy ją ponownie włączyć. W **obszarze Powiązane zasoby** wybierz pozycję **Uruchom/zatrzymaj maszyny wirtualne** Dowiedz się więcej i włącz tworzenie rozwiązania, aby  >    >   rozpocząć wdrażanie.

4. Na stronie Dodawanie rozwiązania wybierz obszar roboczy usługi Log Analytics i konto usługi Automation.

    ![Zrzut ekranu przedstawiający menu Dodaj rozwiązanie](../media/move-account/add-solution-vm.png)

5. Skonfiguruj funkcję zgodnie z opisem w [Start/Stop VMs during off-hours omówienie.](../automation-solution-vm-management.md)

## <a name="verify-the-move"></a>Weryfikowanie przeniesienia

Po zakończeniu przenoszenia sprawdź, czy możliwości wymienione poniżej są włączone. 

|Możliwość|Testy|Rozwiązywanie problemów|
|---|---|---|
|Elementy Runbook|Można pomyślnie uruchomić i połączyć się z zasobami platformy Azure za pomocą runbook.|[Rozwiązywanie problemów z elementami runbook](../troubleshoot/runbooks.md)
|Kontrola źródła|Ręczną synchronizację można uruchomić w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i spis|Sprawdź, czy widzisz bieżące dane spisu z maszyn.|[Rozwiązywanie problemów ze śledzeniem zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, czy widzisz maszyny i czy są one w dobrej kondycji.</br>Uruchamianie testowego wdrożenia aktualizacji oprogramowania.|[Rozwiązywanie problemów z zarządzaniem aktualizacjami](../troubleshoot/update-management.md)|
|Udostępnione zasoby|Sprawdź, czy widzisz wszystkie udostępnione zasoby, takie jak [poświadczenia](../shared-resources/credentials.md) i [zmienne.](../shared-resources/variables.md)|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przenoszeniu zasobów na platformę Azure, [zobacz Przenoszenie zasobów na platformie Azure.](../../azure-resource-manager/management/move-support-resources.md)
