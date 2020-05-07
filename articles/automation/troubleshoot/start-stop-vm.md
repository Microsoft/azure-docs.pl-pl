---
title: Rozwiązywanie problemów dotyczących uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z uruchamianiem/zatrzymywaniem maszyny wirtualnej w rozwiązaniu czasowym.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855064"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Rozwiązywanie problemów dotyczących uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów występujących podczas pracy z Azure Automation uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta Azure Automation można zaktualizować moduły do najnowszej wersji za pomocą [sposobu aktualizowania modułów Azure PowerShell w Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenariusz: rozwiązanie uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami nie powiodło się.

### <a name="issue"></a>Problem

Podczas wdrażania [maszyny wirtualnej uruchamiania/zatrzymywania w trakcie pracy](../automation-solution-vm-management.md)w trybie offline występuje jeden z następujących błędów:

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

- W wybranym regionie istnieje już konto usługi Automation o tej samej nazwie.
- Zasady nie zezwalają na wdrożenie maszyn wirtualnych Start/Stop w trakcie pracy.
- Typ `Microsoft.OperationsManagement`zasobu `Microsoft.Insights`, lub `Microsoft.Automation` nie jest zarejestrowany.
- Obszar roboczy Log Analytics jest zablokowany.
- Istnieje nieaktualna wersja modułów AzureRM lub uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z następującymi poprawkami, aby zapoznać się z potencjalnymi rozwiązaniami problemu:

* Konta usługi Automation muszą być unikatowe w obrębie regionu świadczenia usługi Azure, nawet jeśli znajdują się w różnych grupach zasobów. Sprawdź istniejące konta usługi Automation w regionie docelowym.
* Istniejące zasady uniemożliwiają wdrożenie zasobu, który jest wymagany dla maszyn wirtualnych Start/Stop poza godziną. Przejdź do przypisań zasad w Azure Portal i sprawdź, czy masz przypisanie zasad, które uniemożliwia wdrożenie tego zasobu. Aby dowiedzieć się więcej, zobacz [błąd RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Aby wdrożyć rozwiązanie do uruchamiania/zatrzymywania maszyn wirtualnych, należy zarejestrować subskrypcję w następujących przestrzeniach nazw zasobów platformy Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Aby dowiedzieć się więcej o błędach podczas rejestrowania dostawców, zobacz [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Jeśli masz blokadę obszaru roboczego Log Analytics, przejdź do obszaru roboczego w obszarze Azure Portal i Usuń wszystkie blokady zasobu.
* Jeśli te rozwiązania nie rozwiążą problemu, postępuj zgodnie z instrukcjami w sekcji [Aktualizowanie rozwiązania](../automation-solution-vm-management.md#update-the-solution) , aby ponownie wdrożyć maszynę wirtualną uruchamiania/zatrzymywania w trakcie pracy.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenariusz: uruchamianie lub zatrzymywanie wszystkich maszyn wirtualnych nie powiodło się

### <a name="issue"></a>Problem

Skonfigurowano Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami pracy, ale nie uruchamia ani nie zatrzymuje wszystkich maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

- Harmonogram nie jest prawidłowo skonfigurowany.
- Konto Uruchom jako może nie być poprawnie skonfigurowane.
- W elemencie Runbook mogły wystąpić błędy.
- Maszyny wirtualne mogły zostać wykluczone.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z poniższą listą, aby uzyskać potencjalne rozwiązania problemu:

* Sprawdź, czy masz prawidłowo skonfigurowany harmonogram dla maszyny wirtualnej uruchamiania/zatrzymywania w trakcie pracy. Aby dowiedzieć się, jak skonfigurować harmonogram, zobacz [harmonogramy](../automation-schedules.md).

* Sprawdź [strumienie zadań](../automation-runbook-execution.md#job-statuses) , aby wyszukać błędy. Wyszukaj zadania z jednego z następujących elementów Runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Sprawdź, czy [konto Uruchom jako](../manage-runas-account.md) ma odpowiednie uprawnienia do maszyn wirtualnych, które próbujesz uruchomić lub zatrzymać. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal. Wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**, a następnie wybierz odpowiednie konto Uruchom jako.

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne są ustawiane w `External_ExcludeVMNames` zmiennej na koncie usługi Automation, na którym wdrożono rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenariusz: uruchamianie lub zatrzymywanie niektórych maszyn wirtualnych nie powiodło się

### <a name="issue"></a>Problem

Skonfigurowano Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami pracy, ale nie uruchamia ani nie zatrzymuje niektórych skonfigurowanych maszyn wirtualnych.

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany jedną z następujących przyczyn:

- W scenariuszu sekwencji może brakować tagu lub jest on nieprawidłowy.
- Maszyna wirtualna może zostać wykluczona.
- Konto Uruchom jako może nie mieć wystarczających uprawnień na maszynie wirtualnej.
- Maszyna wirtualna może mieć problem, który zatrzymał się z uruchamiania lub zatrzymywania.

### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z poniższą listą, aby zapoznać się z potencjalnymi rozwiązaniami problemu lub miejscami, aby

* W przypadku użycia [scenariusza sekwencji](../automation-solution-vm-management.md) dla maszyn wirtualnych Start/Stop w trakcie pracy z rozwiązaniem należy upewnić się, że każda maszyna wirtualna, która ma zostać uruchomiona lub zatrzymana, ma odpowiedni tag. Upewnij się, że maszyny wirtualne, które chcesz uruchomić, `sequencestart` mają tag i maszyny wirtualne, które mają zostać zatrzymane, `sequencestop` mają tag. Oba Tagi wymagają dodatniej wartości całkowitej. Można użyć zapytania podobnego do poniższego przykładu, aby wyszukać wszystkie maszyny wirtualne ze znacznikami i ich wartościami.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Maszyny wirtualne mogą nie zostać uruchomione lub zatrzymane, jeśli są jawnie wykluczone. Wykluczone maszyny wirtualne są ustawiane w `External_ExcludeVMNames` zmiennej na koncie usługi Automation, na którym wdrożono rozwiązanie. Poniższy przykład pokazuje, jak można zbadać tę wartość za pomocą programu PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby można było uruchamiać i zatrzymywać maszyny wirtualne, konto Uruchom jako dla konta usługi Automation musi mieć odpowiednie uprawnienia do maszyny wirtualnej. Aby dowiedzieć się, jak sprawdzić uprawnienia do zasobu, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal. Wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta** i wybierz odpowiednie konto Uruchom jako.
* Jeśli maszyna wirtualna ma problem z uruchamianiem lub cofaniem przydziału, może wystąpić problem z maszyną wirtualną. Przykłady to aktualizacja stosowana podczas próby zamknięcia maszyny wirtualnej, usługa, która zawiesza się i nie tylko. Przejdź do zasobu maszyny wirtualnej i sprawdź **dzienniki aktywności** , aby sprawdzić, czy w dziennikach znajdują się błędy. Możesz także próbować zalogować się do maszyny wirtualnej, aby sprawdzić, czy w dziennikach zdarzeń występują błędy. Aby dowiedzieć się więcej o rozwiązywaniu problemów z maszyną wirtualną, zobacz [Rozwiązywanie problemów dotyczących maszyn wirtualnych platformy Azure](../../virtual-machines/troubleshooting/index.yml)
* Sprawdź [strumienie zadań](../automation-runbook-execution.md#job-statuses) , aby wyszukać błędy. W portalu przejdź do konta usługi Automation i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenariusz: mój niestandardowy element Runbook nie może uruchomić lub zatrzymać moich maszyn wirtualnych

### <a name="issue"></a>Problem

Utworzono niestandardowy element Runbook lub został on pobrany z Galeria programu PowerShell i nie działa prawidłowo.

### <a name="cause"></a>Przyczyna

Przyczyną błędu może być wiele przyczyn. Przejdź do konta usługi Automation w Azure Portal i wybierz pozycję **zadania** w obszarze **Automatyzacja procesów**. Na stronie **zadania** Znajdź zadania z elementu Runbook, aby wyświetlić wszystkie błędy zadań.

### <a name="resolution"></a>Rozwiązanie

Zalecamy wykonanie następujących czynności:

* Aby uruchamiać i zatrzymywać maszyny wirtualne w Azure Automation, użyj [rozwiązania Start/zatrzymywanie maszyn wirtualnych poza godzinami](../automation-solution-vm-management.md) . To rozwiązanie jest tworzone przez firmę Microsoft. 
* Należy pamiętać, że firma Microsoft nie obsługuje niestandardowych elementów Runbook. Rozwiązanie dla niestandardowego elementu Runbook można znaleźć na ekranie [Rozwiązywanie problemów](runbooks.md)z elementem Runbook. Sprawdź [strumienie zadań](../automation-runbook-execution.md#job-statuses) , aby wyszukać błędy. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenariusz: maszyny wirtualne nie uruchamiają się lub nie zatrzymują w poprawnej kolejności

### <a name="issue"></a>Problem

Maszyny wirtualne, które zostały skonfigurowane w rozwiązaniu, nie zaczynają się ani nie są zatrzymane w poprawnej kolejności.

### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez nieprawidłowe znakowanie na maszynach wirtualnych.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby upewnić się, że rozwiązanie jest prawidłowo skonfigurowane.

1. Upewnij się, że wszystkie maszyny wirtualne, które mają `sequencestart` zostać `sequencestop` uruchomione lub zatrzymane, mają tag lub, w zależności od sytuacji. Tagi te wymagają dodatniej liczby całkowitej jako wartości. Maszyny wirtualne są przetwarzane w kolejności rosnącej na podstawie tej wartości.
1. Upewnij się, że grupy zasobów dla maszyn wirtualnych, które mają być uruchomione lub zatrzymane, znajdują się w zmiennych `External_Start_ResourceGroupNames` lub `External_Stop_ResourceGroupNames` , w zależności od sytuacji.
1. Przetestuj zmiany, wykonując `SequencedStartStop_Parent` element Runbook z `WHATIF` parametrem ustawionym na wartość true, aby wyświetlić podgląd zmian.

Aby uzyskać więcej informacji na temat używania rozwiązania do uruchamiania i zatrzymywania maszyn wirtualnych w sekwencji, zobacz [Uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenariusz: zadanie uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami kończy się niepowodzeniem z powodu błędu niedozwolonego 403

### <a name="issue"></a>Problem

Możesz znaleźć zadania, które nie powiodły `403 forbidden` się z powodu błędu dotyczącego uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami elementów Runbook rozwiązania.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być nieprawidłowo skonfigurowane lub wygasłe konto Uruchom jako. Przyczyną może być nieodpowiednie uprawnienia do zasobów maszyny wirtualnej za pomocą konta Uruchom jako.

### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić, czy konto Uruchom jako jest prawidłowo skonfigurowane, przejdź do konta usługi Automation w Azure Portal i wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**. Jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane lub wygasło, stan pokazuje warunek.

Jeśli konto Uruchom jako jest nieprawidłowo skonfigurowane, Usuń i ponownie utwórz konto Uruchom jako. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami Azure Automation Uruchom jako](../manage-runas-account.md).

Jeśli certyfikat wygasł dla konta Uruchom jako, postępuj zgodnie z instrukcjami w sekcji [odnowienie certyfikatu](../manage-runas-account.md#cert-renewal) z podpisem własnym, aby odnowić certyfikat.

Jeśli brakuje uprawnień, zobacz [Szybki Start: Wyświetlanie ról przypisanych do użytkownika przy użyciu Azure Portal](../../role-based-access-control/check-access.md). Należy podać identyfikator aplikacji dla jednostki usługi używanej przez konto Uruchom jako. Możesz pobrać tę wartość, przechodząc do konta usługi Automation w Azure Portal. Wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**, a następnie wybierz odpowiednie konto Uruchom jako.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenariusz: mój problem nie jest tutaj wymieniony

### <a name="issue"></a>Problem

Wystąpił problem lub nieoczekiwany wynik w przypadku korzystania z maszyn wirtualnych Start/Stop w nieprawidłowym czasie, które nie znajdują się na tej stronie.

### <a name="cause"></a>Przyczyna

Wiele błędów może być spowodowanych przez użycie starej i nieaktualnej wersji rozwiązania.

> [!NOTE]
> Rozwiązanie do uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami zostało przetestowane z modułami platformy Azure, które zostały zaimportowane do konta usługi Automation podczas wdrażania rozwiązania. Obecnie rozwiązanie nie działa w przypadku nowszych wersji modułu platformy Azure. To ograniczenie ma wpływ tylko na konto usługi Automation, którego używasz do uruchamiania maszyn wirtualnych Start/Stop w niegodzinach. Nadal można używać nowszych wersji modułu platformy Azure na innych kontach usługi Automation, zgodnie z opisem w artykule [jak zaktualizować moduły Azure PowerShell w Azure Automation](../automation-update-azure-modules.md).

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać wiele błędów, Usuń i [zaktualizuj maszyny wirtualne uruchamiania/zatrzymywania podczas pracy z rozwiązaniem](../automation-solution-vm-management.md#update-the-solution). Możesz również sprawdzić [strumienie zadań](../automation-runbook-execution.md#job-statuses) , aby wyszukać błędy. 

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.