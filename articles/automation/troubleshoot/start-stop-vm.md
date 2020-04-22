---
title: Rozwiązywanie problemów z maszynami wirtualnymi start/stop w godzinach wolnych od pracy
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z rozwiązaniem Start/Stop VM.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679166"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Rozwiązywanie problemów z maszynami wirtualnymi start/stop w godzinach wolnych od pracy

Ten artykuł zawiera informacje na temat rozwiązywania problemów, które pojawiają się podczas pracy z start/stop maszyn wirtualnych w godzinach pracy rozwiązania.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenariusz: Rozwiązanie start/stop VM podczas godzin pracy nie można poprawnie wdrożyć

### <a name="issue"></a>Problem

Podczas wdrażania [maszyny wirtualne Start/Stop w godzinach wolnych od pracy](../automation-solution-vm-management.md)pojawia się jeden z następujących błędów:

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

Wdrożenia mogą zakończyć się niepowodzeniem z jednego z następujących powodów:

1. Istnieje już konto automatyzacji o tej samej nazwie w wybranym regionie.
2. Zasady nie zezwalają na wdrażanie maszyn wirtualnych Start/Stop w rozwiązaniu poza godzinami pracy.
3. `Microsoft.Insights`Typ `Microsoft.OperationsManagement`zasobu lub `Microsoft.Automation` typ zasobu nie jest zarejestrowany.
4. Obszar roboczy usługi Log Analytics jest zablokowany.
5. Masz przestarzałą wersję modułów AzureRM lub start/stop maszyn wirtualnych w ramach rozwiązania poza godzinami pracy.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z następującymi poprawkami, aby uzyskać potencjalne rozwiązania problemu:

* Konta automatyzacji muszą być unikatowe w regionie platformy Azure, nawet jeśli znajdują się w różnych grupach zasobów. Sprawdź istniejące konta automatyzacji w regionie docelowym.
* Istniejąca zasada uniemożliwia zasób, który jest wymagany dla start/stop maszyn wirtualnych w godzinach pracy rozwiązanie do wdrożenia. Przejdź do przypisań zasad w witrynie Azure portal i sprawdź, czy masz przypisanie zasad, które nie zezwala na wdrożenie tego zasobu. Aby dowiedzieć się więcej na ten temat, zobacz [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Aby wdrożyć rozwiązanie Start/Stop VMs, twoja subskrypcja musi być zarejestrowana w następujących obszarach nazw zasobów platformy Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zobacz [Rozwiązywanie błędów rejestracji dostawcy zasobów,](../../azure-resource-manager/templates/error-register-resource-provider.md) aby dowiedzieć się więcej o błędach podczas rejestrowania dostawców.
* Jeśli masz blokadę w obszarze roboczym usługi Log Analytics, przejdź do obszaru roboczego w witrynie Azure portal i usuń wszelkie blokady zasobu.
* Jeśli powyższe rozwiązania nie rozwiążą problemu, postępuj zgodnie z instrukcjami w obszarze [Aktualizuj rozwiązanie,](../automation-solution-vm-management.md#update-the-solution) aby ponownie wdrożyć rozwiązanie Start/Stop.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenariusz: Nie można uruchomić lub zatrzymać wszystkich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano maszyny wirtualne Start/Stop w godzinach poza godzinami pracy, ale nie uruchamia się ani nie zatrzymuje wszystkich maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. Harmonogram nie jest poprawnie skonfigurowany.
2. Konto Uruchom jako może nie być poprawnie skonfigurowane.
3. Może wystąpić w wyniku błędów.
4. Maszyny wirtualne mogły zostać wykluczone.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalnych rozwiązań problemu:

* Sprawdź, czy poprawnie skonfigurowano harmonogram dla maszyn wirtualnych Start/Stop w godzinach pracy rozwiązania. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz artykuł [Harmonogramy.](../automation-schedules.md)

* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. Poszukaj zadań z jednego z następujących elementów runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Sprawdź, czy [twoje konto Uruchom jako](../manage-runas-account.md) ma odpowiednie uprawnienia do maszyn wirtualnych, które próbujesz uruchomić lub zatrzymać. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta**i klikając odpowiednie konto Uruchom jako.

* Maszyny wirtualne nie mogą być uruchamiane lub zatrzymywane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne `External_ExcludeVMNames` są ustawiane w zmiennej na koncie automatyzacji, do którego rozwiązanie jest wdrażane. W poniższym przykładzie pokazano, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenariusz: nie można uruchomić lub zatrzymać niektórych moich maszyn wirtualnych

### <a name="issue"></a>Problem

Skonfigurowano maszyny wirtualne Start/Stop w godzinach poza godzinami pracy, ale nie uruchamia się ani nie zatrzymuje niektórych skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

1. W scenariuszu sekwencji tag może brakować lub niepoprawne.
2. Maszyna wirtualna może być wykluczona.
3. Konto Uruchom jako może nie mieć wystarczających uprawnień na maszynie Wirtualnej.
4. Maszyna wirtualna może mieć problem, który zatrzymał go od uruchamiania lub zatrzymywania.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj następującą listę potencjalnych rozwiązań problemu lub miejsc, w których warto szukać:

* Podczas korzystania ze [scenariusza sekwencji](../automation-solution-vm-management.md) start/stop maszyn wirtualnych w godzinach pracy rozwiązania, należy upewnić się, że każda maszyna wirtualna, którą chcesz uruchomić lub zatrzymać ma odpowiedni tag. Upewnij się, że maszyny wirtualne, `sequencestart` które chcesz uruchomić, mają tag i `sequencestop` maszyny wirtualne, które chcesz zatrzymać, mają tag. Oba znaczniki wymagają dodatniej wartości całkowitej. Można użyć kwerendy podobne do poniższego przykładu, aby wyszukać wszystkie maszyny wirtualne z tagów i ich wartości.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne `External_ExcludeVMNames` są ustawiane w zmiennej na koncie automatyzacji, do którego rozwiązanie jest wdrażane. W poniższym przykładzie pokazano, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby uruchomić i zatrzymać maszyny wirtualne, uruchom jako konto dla konta automatyzacji musi mieć odpowiednie uprawnienia do maszyny Wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Musisz podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta** i klikając odpowiednie konto Uruchom jako.

* Jeśli maszyna wirtualna ma problem z uruchamianiem lub alokacją, może to być problem na samej maszynie Wirtualnej. Na przykład aktualizacja jest stosowana, gdy maszyna wirtualna próbuje zamknąć, usługa zawiesza się i więcej. Przejdź do zasobu maszyny Wirtualnej i sprawdź **dzienniki aktywności,** aby sprawdzić, czy w dziennikach występują błędy. Można również spróbować zalogować się do maszyny Wirtualnej, aby sprawdzić, czy są jakieś błędy w dziennikach zdarzeń. Aby dowiedzieć się więcej na temat rozwiązywania problemów z maszyną wirtualną, zobacz [Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure](../../virtual-machines/troubleshooting/index.yml)

* Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. W portalu przejdź do konta automatyzacji i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenariusz: Uruchomienie lub zatrzymanie maszyn wirtualnych jest uruchamiane lub zatrzymywać mój niestandardowy eks-orp.

### <a name="issue"></a>Problem

Zostałeś autorem niestandardowego eksmisu lub pobrano go z galerii programu PowerShell i nie działa poprawnie.

### <a name="cause"></a>Przyczyna

Może istnieć wiele przyczyn awarii. Przejdź do swojego konta automatyzacji w witrynie Azure portal i wybierz pozycję **Zadania** w obszarze **Automatyzacja procesów**. Na stronie Zadania poszukaj zadań z ego księgi runbook, aby wyświetlić wszelkie błędy zadań.

### <a name="resolution"></a>Rozwiązanie

Zaleca się:

* Użyj [start/stop maszyny wirtualne w godzinach poza godzinami rozwiązania,](../automation-solution-vm-management.md) aby uruchomić i zatrzymać maszyny wirtualne w usłudze Azure Automation. To rozwiązanie jest autorstwa firmy Microsoft. 

* Należy pamiętać, że firma Microsoft nie obsługuje niestandardowych niestandardowych niestandardowych niestandardowych niestandardowych niestandardowych niestandardowych. Rozwiązanie dla niestandardowego wiązka uruchomieniu może znaleźć rozwiązanie problemu z [systemem runbook](runbooks.md). Sprawdź [strumienie zadań,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) aby wyszukać błędy. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenariusz: maszyny wirtualne nie uruchamiają się ani nie zatrzymują w odpowiedniej kolejności

### <a name="issue"></a>Problem

Maszyny wirtualne skonfigurowane w rozwiązaniu nie uruchamiają się ani nie zatrzymują w odpowiedniej kolejności.

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany nieprawidłowym tagowaniem na maszynach wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Należy wykonać następujące kroki, aby upewnić się, że rozwiązanie jest poprawnie skonfigurowane.

1. Upewnij się, że wszystkie maszyny wirtualne, które mają zostać uruchomione lub zatrzymane, mają `sequencestart` lub `sequencestop` tag, w zależności od sytuacji. Te tagi potrzebują dodatniej liczby całkowitej jako wartości. Maszyny wirtualne są przetwarzane w porządku rosnącym na podstawie tej wartości.
2. Upewnij się, że grupy zasobów dla maszyn wirtualnych, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` które mają zostać uruchomione lub zatrzymane, znajdują się w zmiennych lub w zależności od sytuacji.
3. Przetestuj zmiany, `SequencedStartStop_Parent` wykonując runbook z parametrem ustawionym `WHATIF` na True, aby wyświetlić podgląd zmian.
4. Aby uzyskać więcej informacji na temat używania rozwiązania do uruchamiania i zatrzymywania maszyn wirtualnych w sekwencji, zobacz [Uruchamianie/zatrzymywania maszyn wirtualnych w sekwencji](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenariusz: Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy kończy się niepowodzeniem z 403 zabronionym błędem

### <a name="issue"></a>Problem

Można znaleźć zadania, które `403 forbidden` nie powiodły się z powodu błędu dla maszyn wirtualnych Start/Stop podczas ysków rozwiązania poza godzinami.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany nieprawidłowo skonfigurowanym lub wygasłym kontem Uruchom jako. Może to być również z powodu niewystarczających uprawnień do zasobów maszyny Wirtualnej przez konto Uruchom jako.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, czy twoje konto Uruchom jako jest poprawnie skonfigurowane, przejdź do konta automatyzacji w witrynie Azure portal i wybierz pozycję **Uruchom jako konta** w obszarze Ustawienia **konta**. Jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane lub wygasło, stan pokazuje warunek.

Jeśli twoje konto Uruchom jako jest nieprawidłowo skonfigurowane, należy usunąć i ponownie utworzyć konto Uruchom jako. Zobacz [Zarządzanie programem Azure Automation Run jako kontami](../manage-runas-account.md).

Jeśli certyfikat wygasł dla twojego konta Uruchom jako, zobacz kroki w instrukcji [odnawiania certyfikatu z podpisem własnym,](../manage-runas-account.md#cert-renewal) aby odnowić certyfikat.

Jeśli brakuje uprawnień, zobacz [Szybki start: Wyświetlanie ról przypisanych do użytkownika za pomocą portalu Azure](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Tę wartość można pobrać, przechodząc do konta automatyzacji w witrynie Azure portal, wybierając **pozycję Uruchom jako konta** w obszarze Ustawienia **konta**i klikając odpowiednie konto Uruchom jako.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenariusz: Mój problem nie jest wymieniony powyżej

### <a name="issue"></a>Problem

Wystąpi problem lub nieoczekiwany wynik podczas korzystania z start/stop maszyn wirtualnych w godzinach poza godzinami rozwiązania, które nie jest wymienione na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele razy błędy mogą być spowodowane przy użyciu starej i przestarzałej wersji rozwiązania.

> [!NOTE]
> Rozwiązanie start/stop VMs poza godzinami pracy zostało przetestowane przy testach modułów platformy Azure, które są importowane do konta automatyzacji podczas wdrażania rozwiązania. Rozwiązanie obecnie nie działa z nowszych wersji modułu platformy Azure. Dotyczy to tylko konta automatyzacji, którego używasz do uruchamiania maszyn wirtualnych Start/Stop w rozwiązaniu poza godzinami pracy. Nadal można używać nowszych wersji modułu platformy Azure na innych kontach automatyzacji, zgodnie z opisem w [jak zaktualizować moduły usługi Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać wiele błędów, zaleca się usunięcie i [zaktualizowanie maszyny wirtualne Start/Stop podczas rozwiązania poza godzinami pracy.](../automation-solution-vm-management.md#update-the-solution) Ponadto można sprawdzić [strumienie zadań, aby wyszukać](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) wszelkie błędy. 

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.