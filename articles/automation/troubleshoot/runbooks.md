---
title: Rozwiązywanie problemów dotyczących błędów elementów Runbook Azure Automation
description: Dowiedz się, jak rozwiązywać problemy, które mogą wystąpić w przypadku Azure Automation elementów Runbook.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ef3f50e79f4458845ec2e3df73e1e87766bfd0f4
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508789"
---
# <a name="troubleshoot-runbook-errors"></a>Rozwiązywanie problemów z błędami elementów Runbook

 W tym artykule opisano różne błędy elementów Runbook, które mogą wystąpić i sposoby ich rozwiązywania.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnozowanie problemów z elementami Runbook

Po otrzymaniu błędów podczas wykonywania elementu Runbook w Azure Automation można wykonać następujące czynności, aby ułatwić zdiagnozowanie problemów.

1. **Upewnij się, że skrypt elementu Runbook został pomyślnie wykonany na komputerze lokalnym.** 

    Zapoznaj się z dokumentacją dla modułów dokumentacji [programu PowerShell](/powershell/scripting/overview) lub języka [Python](https://docs.python.org/3/) . Wykonywanie skryptu lokalnie może wykrywać i rozwiązywać typowe błędy, takie jak:

      * Brakujące moduły
      * Błędy składniowe
      * Błędy logiki

2. **Zbadaj [strumienie błędów](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)elementu Runbook.**

    Zapoznaj się z tymi strumieniami pod kątem określonych komunikatów i porównaj je z błędami opisanymi w tym artykule.

3. **Upewnij się, że węzły i obszar roboczy automatyzacji mają wymagane moduły.** 

    Jeśli element Runbook importuje dowolne moduły, sprawdź, czy są one dostępne dla konta usługi Automation, korzystając z procedury wymienionej w temacie [Import modules](../shared-resources/modules.md#importing-modules). Zaktualizuj moduły do najnowszej wersji, postępując zgodnie z instrukcjami w temacie [aktualizowanie modułów platformy Azure w Azure Automation](..//automation-update-azure-modules.md). Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, zobacz [Rozwiązywanie problemów modułów](shared-resources.md#modules).

4. **Wykonaj, jeśli element Runbook jest zawieszony lub nieoczekiwanie nie powiedzie się.**

    * [Sprawdź stan zadania](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) określa Stany elementów Runbook i niektóre możliwe przyczyny.
    * [Dodaj dodatkowe dane wyjściowe](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) do elementu Runbook, aby określić, co się dzieje przed wstrzymaniem elementu Runbook.
    * [Obsługa wszelkich wyjątków](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) zgłaszanych przez zadanie.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenariusz: Uruchom logowanie-AzureRMAccount, aby się zalogować

### <a name="issue"></a>Problem

Podczas wykonywania elementu Runbook pojawia się następujący błąd:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, gdy nie używasz konta Uruchom jako lub konto Uruchom jako wygasło. Zobacz [Zarządzanie kontami Azure Automation Uruchom jako](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ten błąd ma dwie przyczyny główne:

* Istnieją różne wersje AzureRM lub AZ module.
* Próbujesz uzyskać dostęp do zasobów w oddzielnej subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Jeśli ten błąd wystąpi po zaktualizowaniu jednego AzureRM lub AZ module, należy zaktualizować wszystkie moduły do tej samej wersji.

Jeśli próbujesz uzyskać dostęp do zasobów w innej subskrypcji, możesz wykonać poniższe kroki, aby skonfigurować uprawnienia.

1. Przejdź do konta Uruchom jako usługi Automation i skopiuj identyfikator aplikacji i odcisk palca.

    ![Identyfikator kopii i odcisk palca](../media/troubleshoot-runbooks/collect-app-id.png)

1. Przejdź do Access Control subskrypcji, w której konto usługi Automation nie jest hostowane, a następnie Dodaj nowe przypisanie roli.

    ![Kontrola dostępu](../media/troubleshoot-runbooks/access-control.png)

1. Dodaj wcześniej zebrane identyfikatory aplikacji. Wybierz pozycję uprawnienia współautora.

    ![Dodaj przypisanie roli](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Skopiuj nazwę subskrypcji.

1. Teraz można użyć poniższego kodu elementu Runbook do przetestowania uprawnień z konta usługi Automation do innej subskrypcji. Zamień `"\<CertificateThumbprint\>"` na wartość skopiowaną w kroku 1. Zamień `"\<SubscriptionName\>"` na wartość skopiowaną w kroku 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenariusz: nie można odnaleźć subskrypcji platformy Azure

### <a name="issue"></a>Problem

Podczas pracy z poleceniem `Select-AzureSubscription`, `Select-AzureRMSubscription`lub `Select-AzSubscription` zostanie wyświetlony następujący błąd:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

Ten błąd może wystąpić, jeśli:

* Nazwa subskrypcji jest nieprawidłowa.
* Użytkownik Azure Active Directory, który próbuje uzyskać szczegóły subskrypcji, nie jest skonfigurowany jako administrator subskrypcji.
* Polecenie cmdlet jest niedostępne.

### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z poniższymi instrukcjami, aby określić, czy masz uwierzytelnienie na platformie Azure i czy masz dostęp do subskrypcji, którą próbujesz wybrać.

1. Aby upewnić się, że skrypt działa autonomicznie, przetestuj go poza Azure Automation.
2. Przed uruchomieniem `Select-*` polecenia cmdlet upewnij się, że skrypt uruchamia polecenie cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) .
3. Dodaj `Disable-AzContextAutosave –Scope Process` do początku elementu Runbook. To wywołanie polecenia cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego elementu Runbook.
4. Jeśli ten komunikat o błędzie jest nadal wyświetlany, zmodyfikuj swój kod przez dodanie `AzContext` parametru do `Connect-AzAccount`, a następnie wykonaj kod.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenariusz: uwierzytelnianie na platformie Azure nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe

### <a name="issue"></a>Problem

Podczas uwierzytelniania na platformie Azure przy użyciu nazwy użytkownika i hasła platformy Azure zostanie wyświetlony następujący błąd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Przyczyna

Jeśli masz uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz używać Azure Active Directory użytkownika do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub nazwy głównej usługi do uwierzytelnienia.

### <a name="resolution"></a>Rozwiązanie

Aby użyć certyfikatu z poleceniami cmdlet klasycznego modelu wdrażania platformy Azure, zapoznaj się z tematem [Tworzenie i Dodawanie certyfikatu w celu zarządzania usługami platformy Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Aby użyć nazwy głównej usługi z poleceniami cmdlet Azure Resource Manager, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelnianie jednostki usługi przy użyciu Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenariusz: w strumieniach zadań pojawia się błąd dotyczący metody get_SerializationSettings

### <a name="issue"></a>Problem

W strumieniach zadań dla elementu Runbook zostanie wyświetlony następujący błąd:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Przyczyna

Ten błąd jest prawdopodobnie spowodowany użyciem niekompletnej migracji z AzureRM do AZ modules w elemencie Runbook. Może to spowodować, że Azure Automation uruchomić zadanie elementu Runbook przy użyciu tylko modułów AzureRM, a następnie uruchomić inne zadanie przy użyciu tylko AZ modułów, co prowadzi do awarii piaskownicy. 

### <a name="resolution"></a>Rozwiązanie

Nie zaleca się używania poleceń cmdlet AZ i AzureRM w tym samym elemencie Runbook. Aby dowiedzieć się więcej o prawidłowym użyciu tych modułów, zobacz [Migrowanie do AZ modules](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenariusz: element Runbook kończy się niepowodzeniem z powodu błędu: zadanie zostało anulowane

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany użyciem nieaktualnych modułów platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Możesz rozwiązać ten problem, aktualizując moduły platformy Azure do najnowszej wersji. 

1. Na koncie usługi Automation kliknij pozycję **moduły**, a następnie **zaktualizuj moduły platformy Azure**. 
2. Aktualizacja trwa około 15 minut. Po zakończeniu uruchom ponownie element Runbook, który się nie powiódł.

Aby dowiedzieć się więcej na temat aktualizowania modułów, zobacz temat [aktualizowanie modułów platformy Azure w Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenariusz: elementy Runbook kończą się niepowodzeniem podczas pracy z wieloma subskrypcjami

### <a name="issue"></a>Problem

Podczas wykonywania elementów Runbook element Runbook nie może zarządzać zasobami platformy Azure.

### <a name="cause"></a>Przyczyna

Element Runbook nie używa poprawnego kontekstu podczas uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Kontekst subskrypcji może zostać utracony, gdy element Runbook wywoła wiele elementów Runbook. Aby zapewnić, że kontekst subskrypcji zostanie przekazany do elementów Runbook, element Runbook klienta przekazuje kontekst do `Start-AzureRmAutomationRunbook` polecenia cmdlet w `AzureRmContext` parametrze. Użyj `Disable-AzureRmContextAutosave` polecenia cmdlet z `Scope` parametrem ustawionym `Process` na, aby upewnić się, że określone poświadczenia są używane tylko dla bieżącego elementu Runbook. Aby uzyskać więcej informacji, zobacz [Praca z wieloma subskrypcjami](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenariusz: termin nie został rozpoznany jako nazwa polecenia cmdlet, funkcji, skryptu

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Moduł zawierający polecenie cmdlet nie został zaimportowany do konta usługi Automation.
* Moduł zawierający polecenie cmdlet zostanie zaimportowany, ale jest nieaktualny.

### <a name="resolution"></a>Rozwiązanie

Wykonaj jedną z następujących czynności, aby rozwiązać ten problem. 

* Aby dowiedzieć się, jak zaktualizować moduły na koncie usługi Automation, zobacz artykuł [jak zaktualizować moduły Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md) .

* W przypadku modułu spoza platformy Azure upewnij się, że moduł został zaimportowany do konta usługi Automation.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenariusz: rozpoczęto próbę zadania elementu Runbook trzy razy, ale nie udało się uruchomić za każdym razem

### <a name="issue"></a>Problem

Wystąpił błąd elementu Runbook z następującym błędem:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu jednego z następujących problemów:

* Limit pamięci. Zadanie może zakończyć się niepowodzeniem, jeśli używa więcej niż 400 MB pamięci. Udokumentowane limity dotyczące pamięci przydzieloną do piaskownicy znajdują się w [granicach usługi Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Gniazda sieciowe. Piaskownice platformy Azure są ograniczone do 1000 współbieżnych gniazd sieciowych. Zobacz [limity usługi Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Moduł jest niezgodny. Zależności modułu mogą nie być poprawne. W takim przypadku element Runbook zwykle zwraca komunikat `Command not found` lub. `Cannot bind parameter`

* Brak uwierzytelniania w Active Directory dla piaskownicy. Element Runbook próbował wywołać plik wykonywalny lub podproces, który jest uruchamiany w piaskownicy platformy Azure. Konfigurowanie elementów Runbook do uwierzytelniania za pomocą usługi Azure AD przy użyciu biblioteki uwierzytelniania Azure Active Directory (ADAL) nie jest obsługiwane.

* Zbyt dużo danych wyjątku. Element Runbook podjął próbę zapisania zbyt dużej ilości danych wyjątku w strumieniu wyjściowym.

### <a name="resolution"></a>Rozwiązanie

* Limit pamięci, gniazda sieciowe. Sugerowane sposoby pracy w ramach limitów pamięci to dzielenie obciążenia między wiele elementów Runbook, przetwarzanie mniejszej ilości danych w pamięci, unikanie zapisywania niepotrzebnych danych wyjściowych z elementów Runbook i Dowiedz się, ile punktów kontrolnych jest zapisywanych w elementach Runbook przepływu pracy programu PowerShell. Użyj metody Clear, takiej jak `$myVar.clear`, aby wyczyścić zmienne i użyć `[GC]::Collect` do natychmiastowego uruchomienia wyrzucania elementów bezużytecznych. Te akcje zmniejszają rozmiar pamięci elementu Runbook w czasie wykonywania.

* Moduł jest niezgodny. Zaktualizuj moduły platformy Azure, wykonując kroki opisane w temacie [jak zaktualizować moduły Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

* Brak uwierzytelniania w Active Directory dla piaskownicy. Podczas uwierzytelniania w usłudze Azure AD za pomocą elementu Runbook upewnij się, że moduł usługi Azure AD jest dostępny na Twoim koncie usługi Automation. Upewnij się, że konto Uruchom jako ma odpowiednie uprawnienia do wykonywania zadań, które są automatyzuje przez element Runbook.

  Jeśli element Runbook nie może wywołać pliku wykonywalnego lub podprocesu działającego w piaskownicy platformy Azure, użyj elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez limity pamięci i sieci dla piaskownic platformy Azure.

* Zbyt dużo danych wyjątku. W strumieniu wyjściowym zadania istnieje limit 1 MB. Upewnij się, że element Runbook ujmuje wywołania do pliku wykonywalnego lub podprocesu przy użyciu `try` bloków i. `catch` Jeśli operacje zgłaszają wyjątek, kod Zapisz komunikat z wyjątku do zmiennej automatyzacji. Ta technika uniemożliwia zapisanie komunikatu w strumieniu wyjściowym zadania.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenariusz: nie można zalogować się do konta platformy Azure

### <a name="issue"></a>Problem

Podczas pracy z `Connect-AzAccount` poleceniem cmdlet zostanie wyświetlony jeden z następujących błędów:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Przyczyna

Te błędy występują, jeśli nazwa zasobu poświadczeń jest nieprawidłowa. Mogą również wystąpić, jeśli nazwa użytkownika i hasło użyte do skonfigurowania zasobu poświadczenia usługi Automation są nieprawidłowe.

### <a name="resolution"></a>Rozwiązanie

Aby określić, co się stało, wykonaj następujące czynności:

1. Upewnij się, że nie masz żadnych znaków specjalnych. Te znaki zawierają `\@` znak w nazwie zasobu poświadczenia usługi Automation używany do nawiązywania połączenia z platformą Azure.
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w Azure Automation poświadczenia w lokalnym edytorze ISE programu PowerShell. Uruchom następujące polecenia cmdlet w programie PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Jeśli uwierzytelnienie nie powiedzie się lokalnie, poświadczenia Azure Active Directory nie zostały prawidłowo skonfigurowane. Zapoznaj się z blogiem dotyczącym [uwierzytelniania na platformie Azure przy użyciu Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) wpis w blogu, aby pomyślnie skonfigurować konto Azure Active Directory.

4. Jeśli błąd pojawia się jako przejściowy, spróbuj dodać logikę ponowień do procedury uwierzytelniania, aby zapewnić bardziej niezawodne uwierzytelnianie.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenariusz: nieprawidłowe odwołanie do obiektu w wywołaniu metody Add-AzAccount

### <a name="issue"></a>Problem

Ten błąd występuje podczas pracy z `Add-AzAccount`, który jest aliasem dla `Connect-AzAccount` polecenia cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, jeśli element Runbook nie wykona odpowiednich czynności przed wywołaniem `Add-AzAccount` , aby dodać konto usługi Automation. Przykładem jednego z niezbędnych czynności jest zalogowanie się przy użyciu konta Uruchom jako. Aby uzyskać prawidłowe operacje do użycia w elemencie Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenariusz: odwołanie do obiektu nie jest ustawione na wystąpienie obiektu

### <a name="issue"></a>Problem

Wystąpi następujący błąd podczas wywoływania podrzędnego elementu Runbook z `Wait` parametrem, a strumień wyjściowy zawiera obiekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

`Start-AzAutomationRunbook`nie obsługuje prawidłowo strumienia wyjściowego, jeśli strumień zawiera obiekty.

### <a name="resolution"></a>Rozwiązanie

Zaleca się zaimplementowanie logiki sondowania i użycie polecenia cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) w celu pobrania danych wyjściowych. Przykład tej logiki został zdefiniowany poniżej.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenariusz: element Runbook kończy się niepowodzeniem z powodu deserializacji obiektu

### <a name="issue"></a>Problem

Wystąpił błąd elementu Runbook:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Przyczyna

Jeśli element Runbook jest przepływem pracy programu PowerShell, przechowuje złożone obiekty w deserializowanym formacie, aby zachować stan elementu Runbook, jeśli przepływ pracy zostanie zawieszony.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań.

* Jeśli tworzysz złożone obiekty z jednego polecenia cmdlet do innego, zawiń te polecenia cmdlet w `InlineScript` działaniu.
* Przekaż nazwę lub wartość, która jest potrzebna z obiektu złożonego, zamiast przekazywać cały obiekt.
* Użyj elementu Runbook programu PowerShell zamiast elementu Runbook przepływu pracy programu PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenariusz: zadanie elementu Runbook nie powiodło się, ponieważ przekroczono przydział przydziału

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wykonanie zadania przekracza 500-minutowy bezpłatny limit przydziału dla Twojego konta. Ten limit przydziału dotyczy wszystkich typów zadań wykonywania zadań. Niektóre z tych zadań są testowaniem zadania, uruchamiania zadania z portalu, wykonywania zadania przy użyciu elementów webhook lub planowania zadania do wykonania przy użyciu Azure Portal lub centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut przetwarzania miesięcznie, Zmień subskrypcję z warstwy Bezpłatna na warstwę podstawowa.

1. Zaloguj się do subskrypcji platformy Azure.
2. Wybierz konto usługi Automation, które ma zostać uaktualnione.
3. Kliknij pozycję **Ustawienia**, a następnie **Cennik**.
4. Kliknij pozycję **Włącz** na dole strony, aby uaktualnić konto do warstwy Podstawowa.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenariusz: nie rozpoznano polecenia cmdlet podczas wykonywania elementu Runbook

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że aparat programu PowerShell nie może znaleźć polecenia cmdlet używanego w elemencie Runbook. Istnieje możliwość, że na koncie nie ma modułu zawierającego polecenie cmdlet, występuje konflikt nazw z nazwą elementu Runbook lub polecenie cmdlet istnieje również w innym module, a Automatyzacja nie może rozpoznać nazwy.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań.

* Upewnij się, że poprawnie wprowadzono nazwę polecenia cmdlet.
* Upewnij się, że polecenie cmdlet istnieje na koncie usługi Automation i że nie występują żadne konflikty. Aby sprawdzić, czy polecenie cmdlet jest obecne, Otwórz element Runbook w trybie edycji i Wyszukaj polecenie cmdlet, które chcesz znaleźć w bibliotece, lub uruchom `Get-Command <CommandName>`. Po sprawdzeniu, czy polecenie cmdlet jest dostępne dla konta, i że nie występują konflikty nazw z innymi poleceniami cmdlet lub elementami Runbook, Dodaj polecenie cmdlet do kanwy i upewnij się, że używasz prawidłowego zestawu parametrów w elemencie Runbook.
* Jeśli występuje konflikt nazw, a polecenie cmdlet jest dostępne w dwóch różnych modułach, należy rozwiązać problem, używając w pełni kwalifikowanej nazwy polecenia cmdlet. Na przykład możesz użyć nazwy `ModuleName\CmdletName`.
* Jeśli element Runbook jest wykonywany lokalnie w grupie hybrydowych procesów roboczych, upewnij się, że moduł i polecenie cmdlet są zainstalowane na komputerze, który hostuje hybrydowy proces roboczy.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenariusz: nie można ukończyć długotrwałego elementu Runbook

### <a name="issue"></a>Problem

Element Runbook jest wyświetlany w stanie zatrzymanym po uruchomieniu przez 3 godziny. Ten błąd może również pojawić się w następujący sposób:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

To zachowanie jest zaprojektowana w piaskownicy platformy Azure ze względu na [rzetelne](../automation-runbook-execution.md#fair-share) monitorowanie procesów w ramach Azure Automation. Jeśli proces jest wykonywany dłużej niż trzy godziny, prawidłowy udział automatycznie zatrzyma element Runbook. Stan elementu Runbook, który przekracza limit czasu udostępniania, różni się od typu elementu Runbook. Elementy Runbook programu PowerShell i języka Python mają ustawiony stan zatrzymany. Elementy Runbook przepływu pracy programu PowerShell są ustawione na wartość niepowodzenie.

### <a name="cause"></a>Przyczyna

Element Runbook działał ponad 3-godzinny limit dozwolony przez sprawiedliwy udział w piaskownicy systemu Azure.

### <a name="resolution"></a>Rozwiązanie

Jednym z zalecanych rozwiązań jest uruchomienie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez 3-godzinne ograniczenia dotyczące udziału w usłudze Azure Portal. Elementy Runbook działające na hybrydowych procesach roboczych elementów Runbook należy opracować do obsługi zachowań ponownego uruchamiania w przypadku wystąpienia nieoczekiwanych problemów z infrastrukturą lokalną.

Innym rozwiązaniem jest zoptymalizowanie elementu Runbook przez utworzenie [podrzędnych elementów Runbook](../automation-child-runbooks.md). Jeśli element Runbook jest używany przez tę samą funkcję na kilku zasobach, na przykład w operacji bazy danych na kilku bazach danych można przenieść funkcję do podrzędnego elementu Runbook. Każdy podrzędny element Runbook jest wykonywany równolegle w osobnym procesie. To zachowanie zmniejsza łączny czas pracy nadrzędnego elementu Runbook.

Polecenia cmdlet programu PowerShell, które umożliwiają korzystanie z podrzędnego scenariusza elementu Runbook, to:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). To polecenie cmdlet pozwala uruchomić element runbook i przekazać do niego parametry.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Jeśli istnieją operacje, które należy wykonać po zakończeniu podrzędnego elementu Runbook, to polecenie cmdlet umożliwia sprawdzenie stanu zadania dla każdego elementu podrzędnego.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenariusz: stan: 400 Nieprawidłowe żądanie podczas wywoływania elementu webhook

### <a name="issue"></a>Problem

Gdy próbujesz wywołać element webhook dla elementu Runbook Azure Automation, zostanie wyświetlony następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Przyczyna

Element webhook, który próbujesz wywołać, jest wyłączony lub wygasł.

### <a name="resolution"></a>Rozwiązanie

Jeśli element webhook jest wyłączony, można ponownie włączyć element webhook za pomocą Azure Portal. Jeśli element webhook wygasł, należy go usunąć, a następnie utworzyć ponownie. Możesz [odnowić element webhook](../automation-webhooks.md#renew-webhook) , jeśli jeszcze go nie wygasł.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenariusz: 429: liczba żądań jest obecnie zbyt duża...

### <a name="issue"></a>Problem

Podczas uruchamiania `Get-AzAutomationJobOutput` polecenia cmdlet zostanie wyświetlony następujący komunikat o błędzie:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z elementu Runbook, który ma wiele [strumieni pełnych](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj jedną z następujących czynności.

* Edytuj element Runbook i zmniejsz liczbę strumieni zadań, które emituje.

* Zmniejsz liczbę strumieni do pobrania podczas uruchamiania polecenia cmdlet. W tym celu można ustawić wartość `Stream` parametru polecenia cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) , aby pobrać tylko strumienie wyjściowe. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenariusz: zadanie programu PowerShell nie powiodło się z powodu błędu: nie można wywołać metody

### <a name="issue"></a>Problem

Podczas uruchamiania zadania programu PowerShell w elemencie Runbook działającym na platformie Azure zostanie wyświetlony następujący komunikat o błędzie:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wskazywać, że elementy Runbook wykonywane w piaskownicy platformy Azure nie mogą być uruchamiane w [trybie pełnego języka](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu.

* Zamiast korzystać z [zadania startowego](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), należy użyć polecenia [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , aby uruchomić element Runbook.
* Spróbuj uruchomić element Runbook w hybrydowym procesie roboczym elementu Runbook.

Aby dowiedzieć się więcej na temat tego zachowania i innych zachowań Azure Automation elementów Runbook, zobacz [wykonywanie elementów Runbook w programie Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenariusz: hybrydowy proces roboczy elementu Runbook systemu Linux odbiera monit o podanie hasła podczas podpisywania elementu Runbook

### <a name="issue"></a>Problem

Uruchomienie `sudo` polecenia dla hybrydowego procesu roboczego elementu Runbook systemu Linux powoduje pobranie nieoczekiwanego monitu o podanie hasła.

### <a name="cause"></a>Przyczyna

Konto **nxautomationuser** agenta log Analytics dla systemu Linux nie zostało poprawnie skonfigurowane w pliku **sudo** . Hybrydowy proces roboczy elementu Runbook wymaga odpowiedniej konfiguracji uprawnień konta i innych danych, aby można było podpisywać elementy Runbook w ramach procesu roboczego elementu Runbook systemu Linux.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że hybrydowy proces roboczy elementu Runbook ma plik wykonywalny GnuPG (GPG) na komputerze.

* Sprawdź konfigurację konta **nxautomationuser** w pliku **sudo** . Zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenariusz: nie powiodło się polecenie cmdlet w elemencie Runbook programu PowerShell PnP na Azure Automation

### <a name="issue"></a>Problem

Gdy element Runbook zapisuje obiekt wygenerowany przez program PowerShell PnP do danych wyjściowych Azure Automation bezpośrednio, dane wyjściowe polecenia cmdlet nie mogą przesyłać strumieniowo do automatyzacji.

### <a name="cause"></a>Przyczyna

Ten problem najczęściej występuje, gdy Azure Automation przetwarza elementy Runbook, które wywołują polecenia cmdlet programu PowerShell PnP, `add-pnplistitem`na przykład bez przechwytywania obiektów zwracanych.

### <a name="resolution"></a>Rozwiązanie

Edytuj skrypty, aby przypisać wszelkie wartości zwracane do zmiennych, aby polecenia cmdlet nie powodowały zapisywania całych obiektów w standardowym wyjściu. Skrypt może przekierować strumień wyjściowy do polecenia cmdlet, jak pokazano poniżej.

```azurecli
  $null = add-pnplistitem
```

Jeśli skrypt analizuje dane wyjściowe polecenia cmdlet, skrypt musi przechowywać dane wyjściowe w zmiennej i manipulować zmienną zamiast po prostu przesyła strumieniowo dane wyjściowe.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scenariusz: nieprawidłowy kod stanu "zabroniony" podczas korzystania z Key Vault wewnątrz elementu Runbook

### <a name="issue"></a>Problem

Podczas próby dostępu do Key Vault za pomocą Azure Automation elementu Runbook zostanie wyświetlony następujący błąd:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Przyczyna

Możliwe przyczyny tego problemu:

* Nie używa konta Uruchom jako.
* Niewystarczające uprawnienia.

### <a name="resolution"></a>Rozwiązanie

#### <a name="not-using-run-as-account"></a>Nieużywanie konta Uruchom jako

Postępuj zgodnie z [krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) w celu zapewnienia dostępu do Key Vault za pomocą konta Uruchom jako. 

#### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia

[Dodaj uprawnienia do Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) , aby upewnić się, że konto Uruchom jako ma wystarczające uprawnienia dostępu do Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mój problem nie jest wymieniony powyżej

W poniższych sekcjach wymieniono inne typowe błędy i zamieszczono dokumentację pomocniczą ułatwiającą rozwiązanie problemu.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrydowy proces roboczy runbook nie uruchamia zadań lub nie odpowiada

Jeśli uruchamiasz zadania w hybrydowym procesie roboczym elementu Runbook, a nie w Azure Automation, może być konieczne [rozwiązanie tego samego procesu](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Działanie elementu Runbook nie powiodło się i nie ma uprawnień lub niektórych różnic

Konta Uruchom jako mogą nie mieć tych samych uprawnień wobec zasobów platformy Azure jako bieżącego konta. Upewnij się, że konto Uruchom jako ma [uprawnienia dostępu do wszystkich zasobów](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) używanych w skrypcie.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemy z przekazywaniem parametrów do elementów webhook

Aby uzyskać pomoc dotyczącą przekazywania parametrów do elementów webhook, zobacz [Uruchamianie elementu Runbook z elementu webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemy przy użyciu polecenia AZ modules

Użycie niekompletnej migracji modułów Runbook z programu AzureRM do AZ może spowodować awarie awarii i elementów Runbook piaskownicy. Zobacz [używanie modułów w elementach Runbook](../automation-runbook-execution.md#using-modules-in-your-runbooks).

### <a name="inconsistent-behavior-in-runbooks"></a>Niespójne zachowanie w elementach runbook

Postępuj zgodnie ze wskazówkami w obszarze [wykonywanie elementu Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) , aby uniknąć problemów z jednoczesnymi zadaniami, zasobami tworzonymi wielokrotnie lub z inną logiką zależną od czasu w elementach Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Element Runbook kończy się niepowodzeniem z powodu błędu Brak uprawnienia, dostęp zabroniony (403) lub odmiana

Konta Uruchom jako mogą nie mieć tych samych uprawnień wobec zasobów platformy Azure jako bieżącego konta. Upewnij się, że konto Uruchom jako ma [uprawnienia dostępu do wszystkich zasobów](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) używanych w skrypcie.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Elementy runbook działały, ale nagle przestały

* Upewnij się, że konto Uruchom jako nie wygasło. Zobacz [odnowienie certyfikacji](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Jeśli do uruchomienia elementu Runbook używasz elementu [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) , upewnij się, że element webhook nie wygasł.

### <a name="passing-parameters-into-webhooks"></a>Przekazywanie parametrów do elementów webhook

Aby uzyskać pomoc dotyczącą przekazywania parametrów do elementów webhook, zobacz [Uruchamianie elementu Runbook z elementu webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-self-signed-certificates"></a>Korzystanie z certyfikatów z podpisem własnym

Aby korzystać z certyfikatów z podpisem własnym, zobacz [Tworzenie nowego certyfikatu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Odmowa dostępu podczas korzystania z piaskownicy platformy Azure dla elementu Runbook

Piaskownica systemu Azure uniemożliwia dostęp do wszystkich pozaprocesowych serwerów COM. Na przykład aplikacja w trybie piaskownicy lub element Runbook nie mogą wywoływać do Instrumentacja zarządzania Windows (WMI) ani do usługi Instalator Windows (MSIServer. exe). Aby uzyskać szczegółowe informacje na temat korzystania z piaskownicy, zobacz [wykonywanie elementów Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Zalecane dokumenty

* [Wykonywanie elementu runbook w usłudze Azure Automation](../automation-runbook-execution.md)
* [Uruchamianie elementu Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Wykonywanie elementu runbook w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Connect with [@AzureSupport](https://twitter.com/azuresupport) — oficjalne konto Microsoft Azure, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
