---
title: Przenoszenie konta Azure Automation do innej subskrypcji
description: W tym artykule opisano sposób przenoszenia konta usługi Automation do innej subskrypcji.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562ea5e0e9e4851ed59bd3ef917be2f9c48cd2a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185555"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Przenoszenie konta Azure Automation do innej subskrypcji

Azure Automation umożliwia przeniesienie niektórych zasobów do nowej grupy zasobów lub subskrypcji. Zasoby można przenosić za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej o tym procesie, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Konto usługi Automation jest jednym z zasobów, które można przenieść. Ten artykuł zawiera informacje na temat przenoszenia kont usługi Automation do innego zasobu lub subskrypcji. Poniżej przedstawiono ogólne kroki służące do przeniesienia konta usługi Automation:

1. Wyłącz funkcje.
2. Odłącz obszar roboczy.
3. Przenieś konto usługi Automation.
4. Usuń i ponownie Utwórz konta Uruchom jako.
5. Włącz ponownie funkcje.

## <a name="remove-features"></a>Usuń funkcje

Aby odłączyć obszar roboczy od konta usługi Automation, musisz usunąć zasoby funkcji w obszarze roboczym:

- Śledzenie zmian i spis
- Zarządzanie aktualizacjami
- Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

1. Znajdź grupę zasobów w witrynie Azure Portal.
2. Znajdź każdą funkcję i wybierz pozycję **Usuń** na stronie **usuwanie zasobów** .

    ![Zrzut ekranu przedstawiający usuwanie zasobów funkcji z Azure Portal](../media/move-account/delete-solutions.png)

Jeśli wolisz, możesz usunąć zasoby za pomocą polecenia cmdlet [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Usuń reguły alertów dla Start/Stop VMs during off-hours

W przypadku Start/Stop VMs during off-hours należy również usunąć reguły alertów utworzone przez funkcję.

1. W Azure Portal przejdź do grupy zasobów, a następnie wybierz pozycję **monitorowanie**  >  **alertów**  >  **Zarządzaj regułami alertów**.

   ![Zrzut ekranu przedstawiający stronę alertów z możliwością wyboru opcji Zarządzaj regułami alertów](../media/move-account/alert-rules.png)

2. Na stronie reguły powinna zostać wyświetlona lista alertów skonfigurowanych w tej grupie zasobów. Ta funkcja tworzy następujące reguły:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Zaznacz pojedyncze reguły, a następnie wybierz pozycję **Usuń** , aby je usunąć.

    ![Zrzut ekranu strony reguł, żądanie potwierdzenia usunięcia dla wybranych reguł](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Jeśli nie widzisz żadnych reguł alertów na stronie reguły, Zmień wartość pola **stan** na **wyłączone** , aby wyświetlić wyłączone alerty. 

4. Po usunięciu reguł alertów należy usunąć grupę akcji utworzoną dla Start/Stop VMs during off-hours powiadomień. W Azure Portal wybierz kolejno pozycje **Monitoruj**  >  **alerty**  >  **Zarządzaj grupami akcji**.

5. Wybierz **StartStop_VM_Notification**. 

6. Na stronie Grupa akcji wybierz pozycję **Usuń**.

    ![Zrzut ekranu strony grupy akcji](../media/move-account/delete-action-group.png)

Jeśli wolisz, możesz usunąć grupę akcji przy użyciu polecenia cmdlet [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Odłącz obszar roboczy

Teraz możesz odłączyć obszar roboczy:

1. W Azure Portal wybierz opcję **Automation account**  >  **Related Resources**  >  **połączony obszar roboczy**zasoby powiązane z kontem usługi Automation. 

2. Wybierz opcję **Odłącz obszar roboczy** , aby odłączyć obszar roboczy od konta usługi Automation.

    ![Zrzut ekranu przedstawiający odłączenie obszaru roboczego od konta usługi Automation](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Przenoszenie konta usługi Automation

Teraz można przenieść konto usługi Automation i jego elementy Runbook. 

1. W Azure Portal przejdź do grupy zasobów konta usługi Automation. Wybierz pozycję **Przenieś**  >  **Przenieś do innej subskrypcji**.

    ![Zrzut ekranu strony grupy zasobów, Przenieś do innej subskrypcji](../media/move-account/move-resources.png)

2. Wybierz zasoby w grupie zasobów, które chcesz przenieść. Upewnij się, że masz konto usługi Automation, elementy Runbook i zasoby obszaru roboczego Log Analytics.

## <a name="re-create-run-as-accounts"></a>Ponowne tworzenie kont Uruchom jako

[Konta Uruchom jako](../manage-runas-account.md) tworzą nazwę główną usługi w Azure Active Directory do uwierzytelniania w zasobach platformy Azure. Gdy zmienisz subskrypcje, konto usługi Automation nie używa już istniejącego konta Uruchom jako. Aby ponownie utworzyć konta Uruchom jako:

1. Przejdź do konta usługi Automation w nowej subskrypcji i wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**. Zobaczysz, że konta Uruchom jako są wyświetlane jako niekompletne.

    ![Zrzut ekranu przedstawiający konta Uruchom jako z niekompletnymi](../media/move-account/run-as-accounts.png)

2. Usuń konta Uruchom jako, po jednym naraz, wybierając pozycję **Usuń** na stronie **Właściwości** . 

    > [!NOTE]
    > Jeśli nie masz uprawnień do tworzenia lub wyświetlania kont Uruchom jako, zobaczysz następujący komunikat: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Aby uzyskać więcej informacji, zobacz [uprawnienia wymagane do skonfigurowania kont Uruchom jako](../manage-runas-account.md#permissions).

3. Po usunięciu kont Uruchom jako wybierz pozycję **Utwórz** w obszarze **konto Uruchom jako platformy Azure**. 

4. Na stronie Dodawanie konta Uruchom jako platformy Azure wybierz pozycję **Utwórz** , aby utworzyć konto Uruchom jako i nazwę główną usługi. 

5. Powtórz powyższe kroki przy użyciu klasycznego konta Uruchom jako platformy Azure.

## <a name="enable-features"></a>Włącz funkcje

Po ponownym utworzeniu konta Uruchom jako należy ponownie włączyć funkcje, które zostały usunięte przed przeniesieniem:

1. Aby włączyć Change Tracking i spis, wybierz pozycję **Change Tracking i spis** na koncie usługi Automation. Wybierz obszar roboczy Log Analytics przesunięty, a następnie wybierz pozycję **Włącz**.

2. Powtórz krok 1 dla Update Management.

    ![Zrzut ekranu przedstawiający funkcje ponownego włączania w przenoszonym koncie usługi Automation](../media/move-account/reenable-solutions.png)

3. Maszyny z włączonymi funkcjami są widoczne po połączeniu istniejącego obszaru roboczego Log Analytics. Aby włączyć funkcję Start/Stop VMs during off-hours, należy ją ponownie włączyć. W obszarze **pokrewne zasoby**wybierz kolejno pozycje **Start/zatrzymywanie maszyn wirtualnych**  >  **Dowiedz się więcej na temat i Włącz**  >  **Tworzenie** rozwiązania, aby rozpocząć wdrażanie.

4. Na stronie Dodawanie rozwiązania wybierz obszar roboczy Log Analytics i konto usługi Automation.

    ![Zrzut ekranu przedstawiający menu Dodaj rozwiązanie](../media/move-account/add-solution-vm.png)

5. Skonfiguruj funkcję zgodnie z opisem w [Start/Stop VMS during off-hours Omówienie](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Weryfikowanie przenoszenia

Po zakończeniu przenoszenia Sprawdź, czy wymienione poniżej możliwości są włączone. 

|Możliwość|Testy|Rozwiązywanie problemów|
|---|---|---|
|Elementy Runbook|Pomyślnie można uruchomić element Runbook i połączyć się z zasobami platformy Azure.|[Rozwiązywanie problemów z elementami runbook](../troubleshoot/runbooks.md)
|Kontrola źródła|Możesz uruchomić ręczną synchronizację w repozytorium kontroli źródła.|[Integracja kontroli źródła](../source-control-integration.md)|
|Śledzenie zmian i spis|Sprawdź, czy na maszynach są widoczne bieżące dane spisu.|[Rozwiązywanie problemów ze śledzeniem zmian](../troubleshoot/change-tracking.md)|
|Zarządzanie aktualizacjami|Sprawdź, czy Twoje maszyny są widoczne i są w dobrej kondycji.</br>Uruchom testowe wdrożenie aktualizacji oprogramowania.|[Rozwiązywanie problemów z zarządzaniem aktualizacjami](../troubleshoot/update-management.md)|
|Udostępnione zasoby|Sprawdź, czy są widoczne wszystkie zasoby udostępnione, takie jak [poświadczenia](../shared-resources/credentials.md) i [zmienne](../shared-resources/variables.md).|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przenoszeniu zasobów na platformie Azure, zobacz [przenoszenie zasobów na platformie Azure](../../azure-resource-manager/management/move-support-resources.md).
