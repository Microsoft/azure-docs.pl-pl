---
title: Rozwiązywanie Azure Automation Start/Stop VMs during off-hours problemów
description: W tym artykule opisano sposób rozwiązywania i rozwiązywania problemów, które pojawiają się podczas korzystania z Start/Stop VMs during off-hours funkcji.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b03481b41e879acac4c3b193d72594454f650525
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831190"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Rozwiązywanie Start/Stop VMs during off-hours problemów

Ten artykuł zawiera informacje na temat rozwiązywania problemów, które pojawiają się podczas wdrażania Azure Automation Start/Stop VMs during off-hours na swoich maszyn wirtualnych. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenariusz: Start/Stop VMs during off-hours nie można poprawnie wdrożyć

### <a name="issue"></a>Problem

Podczas wdrażania [Start/Stop VMs during off-hours](../automation-solution-vm-management.md)program otrzymuje jeden z następujących błędów:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Przyczyna

Wdrożenia mogą się nie powieść z jednego z następujących powodów:

- W wybranym regionie istnieje już konto usługi Automation o tej samej nazwie.
- Zasady nie zezwalają na wdrażanie Start/Stop VMs during off-hours.
- Typ `Microsoft.OperationsManagement` `Microsoft.Insights` zasobu , lub nie `Microsoft.Automation` jest zarejestrowany.
- Obszar roboczy usługi Log Analytics jest zablokowany.
- Masz nieaktualną wersję modułów AzureRM lub Start/Stop VMs during off-hours funkcji.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z następującymi poprawkami, aby zapoznać się z potencjalnymi rozwiązaniami:

* Konta usługi Automation muszą być unikatowe w obrębie regionu świadczenia usługi Azure, nawet jeśli są w różnych grupach zasobów. Sprawdź istniejące konta usługi Automation w regionie docelowym.
* Istniejące zasady uniemożliwiają wdrożenie zasobu wymaganego Start/Stop VMs during off-hours wdrożenia. Przejdź do przypisań zasad w Azure Portal i sprawdź, czy masz przypisanie zasad, które nie zezwala na wdrożenie tego zasobu. Aby dowiedzieć się więcej, zobacz [RequestDisallowedByPolicy error (Błąd RequestDisallowedByPolicy).](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)
* Aby wdrożyć Start/Stop VMs during off-hours, subskrypcja musi zostać zarejestrowana w następujących przestrzeniach nazw zasobów platformy Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Aby dowiedzieć się więcej o błędach podczas rejestrowania dostawców, zobacz Rozwiązywanie błędów [dotyczących rejestracji dostawcy zasobów.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Jeśli masz blokadę w obszarze roboczym usługi Log Analytics, przejdź do obszaru roboczego w Azure Portal i usuń wszelkie blokady zasobu.
* Jeśli te rozwiązania nie rozwiążą problemu, postępuj zgodnie z instrukcjami w obszarze [Aktualizowanie](../automation-solution-vm-management.md#update-the-feature) funkcji, aby ponownie Start/Stop VMs during off-hours.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenariusz: nie można uruchomić lub zatrzymać wszystkich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano konfigurację Start/Stop VMs during off-hours, ale nie uruchamia ani nie zatrzymuje wszystkich maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

- Harmonogram nie jest poprawnie skonfigurowany.
- Konto Uruchom jako może nie być poprawnie skonfigurowane.
- Być może wystąpiły błędy w runbook.
- Maszyny wirtualne mogły zostać wykluczone.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z następującą listą, aby zapoznać się z potencjalnymi rozwiązaniami:

* Sprawdź, czy prawidłowo skonfigurowano harmonogram dla Start/Stop VMs during off-hours. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz [Harmonogramy](../shared-resources/schedules.md).

* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#job-statuses) aby poszukać błędów. Poszukaj zadań z jednego z następujących elementy Runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Sprawdź, czy [konto Uruchom jako](../automation-security-overview.md#run-as-accounts) ma odpowiednie uprawnienia do maszyn wirtualnych, które chcesz uruchomić lub zatrzymać. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz Szybki [start:](../../role-based-access-control/check-access.md)wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal . Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w Azure Portal. Wybierz **pozycję Konta Uruchom jako** w obszarze Ustawienia **konta** i wybierz odpowiednie konto Uruchom jako.

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne są ustawiane w `External_ExcludeVMNames` zmiennej na koncie usługi Automation, na którym wdrożono funkcję. W poniższym przykładzie pokazano, jak można utworzyć zapytanie dotyczące tej wartości za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenariusz: Nie można uruchomić lub zatrzymać niektórych maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano Start/Stop VMs during off-hours, ale nie uruchamia ani nie zatrzymuje niektórych skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

- W scenariuszu sekwencji tag może być brakujący lub nieprawidłowy.
- Maszyna wirtualna może zostać wykluczona.
- Konto Uruchom jako może nie mieć wystarczających uprawnień do maszyny wirtualnej.
- Maszyna wirtualna może mieć problem, który zatrzymał ją przed uruchomieniem lub zatrzymaniem.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z następującą listą, aby zapoznać się z potencjalnymi rozwiązaniami:

* W przypadku korzystania ze scenariusza [sekwencji](../automation-solution-vm-management.md) Start/Stop VMs during off-hours upewnij się, że każda maszyna wirtualna, którą chcesz uruchomić lub zatrzymać, ma odpowiedni tag. Upewnij się, że maszyny wirtualne, które chcesz uruchomić, mają tag, a maszyny wirtualne, które chcesz zatrzymać, `sequencestart` mają `sequencestop` tag. Oba tagi wymagają dodatniej liczby całkowitej. Możesz użyć zapytania podobnego do poniższego przykładu, aby poszukać wszystkich maszyn wirtualnych z tagami i ich wartościami.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne są ustawiane w `External_ExcludeVMNames` zmiennej na koncie usługi Automation, na którym ta funkcja jest wdrażana. W poniższym przykładzie pokazano, jak można utworzyć zapytanie dotyczące tej wartości za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby uruchamiać i zatrzymywać maszyny wirtualne, konto Uruchom jako dla konta usługi Automation musi mieć odpowiednie uprawnienia do maszyny wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start:](../../role-based-access-control/check-access.md)wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal . Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w Azure Portal. Wybierz **pozycję Konta Uruchom jako** w obszarze Ustawienia **konta** i wybierz odpowiednie konto Uruchom jako.
* Jeśli maszyna wirtualna ma problem z uruchamianiem lub cofaniem alokacji, może to być problem na samej maszynie wirtualnej. Przykłady to aktualizacja, która jest stosowana, gdy maszyna wirtualna próbuje zamknąć maszynę wirtualną, usługa, która się zawiesza i nie tylko. Przejdź do zasobu maszyny wirtualnej i sprawdź **dzienniki** aktywności, aby sprawdzić, czy w dziennikach występują błędy. Możesz również spróbować zalogować się do maszyny wirtualnej, aby sprawdzić, czy w dziennikach zdarzeń występują błędy. Aby dowiedzieć się więcej na temat rozwiązywania problemów z maszyną wirtualną, zobacz [Troubleshooting Azure virtual machines (Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure).](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#job-statuses) aby poszukać błędów. W portalu przejdź do konta usługi Automation i wybierz pozycję **Zadania w** obszarze **Automatyzacja procesów.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenariusz: Nie można uruchomić lub zatrzymać moich maszyn wirtualnych w niestandardowym podręczniku Runbook

### <a name="issue"></a>Problem

Został on przez Ciebie autorem niestandardowego lub pobranego z Galeria programu PowerShell i nie działa prawidłowo.

### <a name="cause"></a>Przyczyna

Awaria może być przyczyną wielu przyczyn. Przejdź do konta usługi Automation w Azure Portal i wybierz pozycję **Zadania w** obszarze **Automatyzacja procesów.** Na stronie **Zadania** poszukaj zadań z twojego runbook, aby wyświetlić błędy zadań.

### <a name="resolution"></a>Rozwiązanie

Zalecamy wykonanie następujących czynności:

* Użyj [Start/Stop VMs during off-hours,](../automation-solution-vm-management.md) aby uruchamiać i zatrzymywać maszyny wirtualne w Azure Automation. 
* Należy pamiętać, że firma Microsoft nie obsługuje niestandardowych runbook. Rozwiązanie dla niestandardowego podręcznika Runbook można znaleźć w [tece Rozwiązywanie problemów z tym programem.](runbooks.md) Sprawdź [strumienie zadań,](../automation-runbook-execution.md#job-statuses) aby poszukać błędów. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenariusz: Maszyny wirtualne nie uruchamiają się ani nie zatrzymują w prawidłowej sekwencji

### <a name="issue"></a>Problem

Maszyny wirtualne włączone dla tej funkcji nie uruchamiają się ani nie zatrzymują w prawidłowej sekwencji.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu jest nieprawidłowe tagowanie maszyn wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby upewnić się, że funkcja jest prawidłowo włączona:

1. Upewnij się, że wszystkie maszyny wirtualne, które mają zostać uruchomione lub zatrzymane, mają `sequencestart` tag lub , w zależności od `sequencestop` sytuacji. Te tagi wymagają dodatniej liczby całkowitej jako wartości. Maszyny wirtualne są przetwarzane w kolejności rosnącej na podstawie tej wartości.
1. Upewnij się, że grupy zasobów dla maszyn wirtualnych, które mają zostać uruchomione lub zatrzymane, znajdują się w zmiennych lub , w `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` zależności od sytuacji.
1. Przetestuj zmiany, wykonując SequencedStartStop_Parent **runbook** z parametrem ustawionym na Wartość True, aby `WHATIF` wyświetlić podgląd zmian.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenariusz: Start/Stop VMs during off-hours kończy się niepowodzeniem z błędem 403 zabronione

### <a name="issue"></a>Problem

Znajdziesz zadania, które nie powiodły się `403 forbidden` z błędem dla Start/Stop VMs during off-hours Runbook.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez nieprawidłowo skonfigurowane lub wygasłe konto Uruchom jako. Może to być również spowodowane nieodpowiednimi uprawnieniami do zasobów maszyny wirtualnej przez konto Uruchom jako.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, czy konto Uruchom jako jest prawidłowo skonfigurowane, przejdź do  konta usługi Automation w menu Azure Portal wybierz pozycję Konta Uruchom jako w obszarze **Ustawienia konta.** Jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane lub wygasło, stan pokazuje warunek.

Jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane, usuń je i utwórz ponownie. Aby uzyskać więcej informacji, [zobacz Azure Automation Konta Uruchom jako.](../automation-security-overview.md#run-as-accounts)

Jeśli certyfikat wygasł dla konta Uruchom jako, wykonaj kroki opisane w tece [Odnawianie](../manage-runas-account.md#cert-renewal) certyfikatu z podpisem własnym, aby odnowić certyfikat.

Jeśli brakuje uprawnień, zobacz Szybki start: wyświetlanie ról [przypisanych do](../../role-based-access-control/check-access.md)użytkownika przy użyciu Azure Portal . Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta usługi Automation w Azure Portal. Wybierz **pozycję Konta Uruchom jako** w obszarze Ustawienia **konta** i wybierz odpowiednie konto Uruchom jako.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenariusz: Mojego problemu nie ma na tej liście

### <a name="issue"></a>Problem

Występuje problem lub nieoczekiwany wynik, gdy używasz Start/Stop VMs during off-hours których nie ma na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele błędów może być spowodowanych przez użycie starej i nieaktualnej wersji funkcji.

> [!NOTE]
> Ta Start/Stop VMs during off-hours została przetestowana z modułami platformy Azure, które są importowane do konta usługi Automation podczas wdrażania tej funkcji na maszynach wirtualnych. Funkcja obecnie nie działa z nowszą wersjami modułu platformy Azure. To ograniczenie dotyczy tylko konta usługi Automation służącego do uruchamiania Start/Stop VMs during off-hours. Nadal możesz używać nowszej wersji modułu platformy Azure na innych kontach usługi Automation, zgodnie z opisem w tece Update Azure PowerShell modules (Aktualizowanie Azure PowerShell [usługi .](../automation-update-azure-modules.md)

### <a name="resolution"></a>Rozwiązanie

Aby usunąć wiele błędów, usuń i [zaktualizuj Start/Stop VMs during off-hours](../automation-solution-vm-management.md#update-the-feature). Możesz również sprawdzić [strumienie zadań,](../automation-runbook-execution.md#job-statuses) aby znaleźć błędy. 

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj swojego problemu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów z platformy Azure na [forach platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się z usługą , [@AzureSupport](https://twitter.com/azuresupport) oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta. pomoc techniczna platformy Azure łączy społeczność platformy Azure z odpowiedziami, pomocą techniczną i ekspertami.
* Zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
