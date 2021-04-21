---
title: Rozwiązywanie Azure Automation problemów z Azure Automation runbook
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation Runbook.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831226"
---
# <a name="troubleshoot-runbook-issues"></a>Rozwiązywanie problemów z elementami runbook

 W tym artykule opisano problemy z runbookami, które mogą wystąpić, oraz sposób ich rozwiązywania. Aby uzyskać ogólne informacje, zobacz [Wykonywanie runbook w programie Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnozowanie problemów z runbook

Jeśli podczas wykonywania runbook w programie Azure Automation występują błędy, możesz użyć następujących kroków, aby zdiagnozować problemy:

1. Upewnij się, że skrypt runbook został pomyślnie wykonany na komputerze lokalnym.

    Aby uzyskać informacje o języku i moduły szkoleniowe, zobacz dokumenty [programu PowerShell](/powershell/scripting/overview) lub dokumenty [języka Python.](https://docs.python.org/3/) Uruchamianie skryptu lokalnie może odnajdywać i rozwiązywać typowe błędy, takie jak:

      * Brakujące moduły
      * Błędy składni
      * Błędy logiki

1. Zbadaj strumienie [błędów runbook.](../automation-runbook-output-and-messages.md#runbook-output)

    Przyjrzyj się tym strumieniom, aby znaleźć określone komunikaty, i porównaj je z błędami udokumentowane w tym artykule.

1. Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły.

    Jeśli twój moduł Runbook importuje jakiekolwiek moduły, sprawdź, czy są one dostępne dla Twojego konta usługi Automation, korzystając z kroków z [tematu Importowanie modułów.](../shared-resources/modules.md#import-modules) Zaktualizuj moduły programu PowerShell do najnowszej wersji, zgodnie z instrukcjami w te Azure PowerShell [update Azure Automation](../automation-update-azure-modules.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

1. Jeśli twój runbook zostanie wstrzymany lub nieoczekiwanie ulegnie awarii:

    * [Odnów certyfikat,](../manage-runas-account.md#cert-renewal) jeśli konto Uruchom jako wygasło.
    * [Odnów element webhook,](../automation-webhooks.md#renew-a-webhook) jeśli próbujesz uruchomić element runbook przy użyciu wygasłego.
    * [Sprawdź stany zadań,](../automation-runbook-execution.md#job-statuses) aby określić bieżące stany runbook i niektóre możliwe przyczyny problemu.
    * [Dodaj dodatkowe dane wyjściowe](../automation-runbook-output-and-messages.md#working-with-message-streams) do runbook, aby określić, co się dzieje przed wstrzymaniem tego runbook.
    * [Obsługa wszystkich wyjątków](../automation-runbook-execution.md#exceptions) zgłaszanych przez zadanie.

1. Wykonaj ten krok, jeśli zadanie runbook lub środowisko hybrydowego procesu roboczego runbook nie odpowiada.

    Jeśli uruchamiasz swoje podręczniki Runbook w hybrydowym procesie roboczy runbook, a nie w Azure Automation, może być konieczne rozwiązanie problemu z samym hybrydowym procesem [roboczy.](hybrid-runbook-worker.md)

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenariusz: błąd runbook z błędem Brak uprawnień lub Zabronione 403

### <a name="issue"></a>Problem

Twój runbook kończy się niepowodzeniem z błędem Brak uprawnień lub Zabronione 403 lub równoważnym.

### <a name="cause"></a>Przyczyna

Konta Uruchom jako mogą nie mieć takich samych uprawnień do zasobów platformy Azure, jak bieżące konto usługi Automation. 

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że konto Uruchom jako ma [uprawnienia dostępu do wszystkich zasobów używanych](../../role-based-access-control/role-assignments-portal.md) w skrypcie.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenariusz: Logowanie do konta platformy Azure nie powiodło się

### <a name="issue"></a>Problem

Podczas pracy z poleceniem cmdlet zostanie wyświetlony jeden z następujących `Connect-AzAccount` błędów:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Przyczyna

Te błędy występują, jeśli nazwa zasobu poświadczeń jest nieprawidłowy. Mogą one również wystąpić, jeśli nazwa użytkownika i hasło użyte do skonfigurowania zasobu poświadczeń usługi Automation są nieprawidłowe.

### <a name="resolution"></a>Rozwiązanie

Aby ustalić, co jest nie tak, wykonaj następujące kroki:

1. Upewnij się, że nie masz żadnych znaków specjalnych. Te znaki zawierają znak w nazwie zasobu poświadczeń usługi Automation, który jest używane do `\@` nawiązywania połączenia z platformą Azure.
1. Sprawdź, czy możesz użyć nazwy użytkownika i hasła przechowywanych w poświadczeniach usługi Azure Automation w lokalnym edytorze środowiska PowerShell ISE. Uruchom następujące polecenia cmdlet w programie PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Jeśli uwierzytelnianie nie powiedzie się lokalnie, poświadczenia usługi Azure Active Directory (Azure AD) nie zostały prawidłowo skonfigurowane. Aby prawidłowo skonfigurować konto usługi Azure AD, zobacz artykuł Authenticate to Azure using Azure Active Directory (Uwierzytelnianie na platformie [Azure przy użyciu Azure Active Directory).](../automation-use-azure-ad.md)

1. Jeśli błąd wydaje się przejściowy, spróbuj dodać logikę ponawiania do procedury uwierzytelniania, aby uwierzytelnianie było bardziej niezawodne.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenariusz: Uruchamianie Login-AzureRMAccount w celu zalogowania

### <a name="issue"></a>Problem

Podczas uruchamiania runbook jest wyświetlany następujący błąd:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, gdy nie używasz konta Uruchom jako lub konto Uruchom jako wygasło. Aby uzyskać więcej informacji, [zobacz Azure Automation Konta Uruchom jako — omówienie.](../automation-security-overview.md#run-as-accounts)

Ten błąd ma dwie podstawowe przyczyny:

* Istnieją różne wersje modułu AzureRM lub Az.
* Próbujesz uzyskać dostęp do zasobów w oddzielnej subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Jeśli ten błąd wystąpi po zaktualizowaniu jednego modułu AzureRM lub Az, zaktualizuj wszystkie moduły do tej samej wersji.

Jeśli próbujesz uzyskać dostęp do zasobów w innej subskrypcji, wykonaj następujące kroki, aby skonfigurować uprawnienia:

1. Przejdź do konta Uruchom jako usługi Automation, a następnie skopiuj identyfikator **aplikacji** i **odcisk palca**.

    ![Kopiowanie identyfikatora aplikacji i odcisku palca](../media/troubleshoot-runbooks/collect-app-id.png)

1. Przejdź do kontroli dostępu subskrypcji, w  **której** nie jest hostowane konto usługi Automation, i dodaj nowe przypisanie roli.

    ![Kontrola dostępu](../media/troubleshoot-runbooks/access-control.png)

1. Dodaj zebrane **wcześniej** identyfikator aplikacji. Wybierz **pozycję Uprawnienia** współautora.

    ![Dodaj przypisanie roli](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Skopiuj nazwę subskrypcji.

1. Teraz możesz użyć następującego kodu runbook, aby przetestować uprawnienia z konta usługi Automation do innej subskrypcji. Zastąp `<CertificateThumbprint>` wartość wartością skopiowaną w kroku 1. Zastąp `"<SubscriptionName>"` wartość wartością skopiowaną w kroku 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenariusz: Nie można odnaleźć subskrypcji platformy Azure

### <a name="issue"></a>Problem

Podczas pracy z poleceniem `Select-AzureSubscription` `Select-AzureRMSubscription` cmdlet , lub występuje następujący `Select-AzSubscription` błąd:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Błąd

Ten błąd może wystąpić, jeśli:

* Nazwa subskrypcji jest nieprawidłowy.
* Użytkownik usługi Azure AD, który próbuje uzyskać szczegóły subskrypcji, nie jest skonfigurowany jako administrator subskrypcji.
* Polecenie cmdlet jest niedostępne.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby określić, czy użytkownik został uwierzytelniony na platformie Azure i ma dostęp do subskrypcji, która próbujesz wybrać:

1. Aby upewnić się, że skrypt działa autonomicznie, przetestuj go poza Azure Automation.
1. Przed uruchomieniem polecenia cmdlet upewnij się, że skrypt uruchamia polecenie cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) `Select-*`
1. Dodaj `Disable-AzContextAutosave -Scope Process` do początku swojego runbook. To polecenie cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego runbook.
1. Jeśli nadal widzisz komunikat o błędzie, zmodyfikuj kod, dodając `AzContext` parametr dla , a następnie wykonaj `Connect-AzAccount` kod.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenariusz: Niepowodzenie elementów Runbook podczas pracy z wieloma subskrypcjami

### <a name="issue"></a>Problem

Podczas wykonywania runbook nie można zarządzać zasobami platformy Azure.

### <a name="cause"></a>Przyczyna

Podczas uruchamiania w programie Runbook nie jest uruchomiony prawidłowy kontekst. Może to być spowodowane przypadkowym próbą uzyskania dostępu do nieprawidłowej subskrypcji przez program Runbook.

Mogą pojawić się błędy podobne do tych:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Rozwiązanie

Kontekst subskrypcji może zostać utracony, gdy runbook wywołuje wiele elementów Runbook. Aby uniknąć przypadkowej próby uzyskania dostępu do nieprawidłowej subskrypcji, postępuj zgodnie z poniższymi wskazówkami.

* Aby uniknąć odwoływania się do niewłaściwej subskrypcji, wyłącz zapisywanie kontekstu w swoich elementy runbook usługi Automation, używając następującego kodu na początku każdego z nich.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* Polecenia cmdlet Azure PowerShell obsługują `-DefaultProfile` parametr . Zostało to dodane do wszystkich poleceń cmdlet Az i AzureRm w celu obsługi uruchamiania wielu skryptów programu PowerShell w tym samym procesie, co pozwala określić kontekst i subskrypcję do użycia dla każdego polecenia cmdlet. W przypadku twoich elementach Runbook należy zapisać obiekt kontekstu w elementach Runbook podczas jego tworzenia (czyli po każdym wzięcie pod uwagę konta) i za każdym razem, gdy jest on zmieniany, oraz odwoływać się do kontekstu podczas określania polecenia cmdlet Az.

   > [!NOTE]
   > Należy przekazać obiekt kontekstu nawet wtedy, gdy bezpośrednio manipuluje kontekstem przy użyciu takich polecenia cmdlet jak [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) lub [Select-AzSubscription.](/powershell/module/servicemanagement/azure.service/set-azuresubscription)

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenariusz: Uwierzytelnianie na platformie Azure kończy się niepowodzeniem, ponieważ włączono uwierzytelnianie wieloskładnikowe

### <a name="issue"></a>Problem

Podczas uwierzytelniania na platformie Azure przy użyciu nazwy użytkownika i hasła platformy Azure jest wyświetlany następujący błąd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Przyczyna

Jeśli masz uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz użyć konta użytkownika Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub jednostki usługi do uwierzytelniania.

### <a name="resolution"></a>Rozwiązanie

Aby użyć klasycznego konta Uruchom jako z poleceniami cmdlet klasycznego modelu wdrażania platformy Azure, zobacz Create a Classic Run As account to manage Azure services (Tworzenie klasycznego konta [Uruchom jako w celu zarządzania usługami platformy Azure).](../automation-create-standalone-account.md#create-a-classic-run-as-account) Aby użyć jednostki usługi z Azure Resource Manager cmdlet, zobacz Creating [service principal using Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) (Tworzenie jednostki usługi przy użyciu Azure Portal) i Authenticating a service principal with Azure Resource Manager (Uwierzytelnianie jednostki usługi za pomocą [Azure Resource Manager).](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenariusz: Runbook kończy się niepowodzeniem z komunikatem o błędzie "Zadanie zostało anulowane"

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z błędem podobnym do następującego przykładu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany użyciem nieaktualnych modułów platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Ten błąd można usunąć, aktualizując moduły platformy Azure do najnowszej wersji:

1. Na koncie usługi Automation wybierz pozycję **Moduły,** a następnie wybierz pozycję **Aktualizuj moduły platformy Azure.**
1. Aktualizacja trwa około 15 minut. Po zakończeniu uruchom ponownie podręcznik Runbook, który zakończył się niepowodzeniem.

Aby dowiedzieć się więcej na temat aktualizowania modułów, zobacz [Aktualizowanie modułów platformy Azure w Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenariusz: termin nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji lub skryptu

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z błędem podobnym do następującego przykładu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Moduł zawierający polecenie cmdlet nie jest importowany na konto usługi Automation.
* Moduł, który zawiera polecenie cmdlet, jest importowany, ale jest aktualny.

### <a name="resolution"></a>Rozwiązanie

Wykonaj jedno z następujących zadań, aby rozwiązać ten błąd:

* Aby uzyskać informacje na temat modułu platformy Azure, zobacz jak Azure PowerShell modułów w usłudze [Azure Automation,](../automation-update-azure-modules.md) aby dowiedzieć się, jak zaktualizować moduły na koncie usługi Automation.
* W przypadku modułu spoza platformy Azure upewnij się, że moduł został zaimportowany do Twojego konta usługi Automation.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenariusz: Polecenie cmdlet kończy się niepowodzeniem w podręczniku Runbook programu PowerShell PnP na Azure Automation

### <a name="issue"></a>Problem

Gdy w danych wyjściowych programu Azure Automation runbook jest zapisywany obiekt wygenerowany przez program PowerShell PnP, dane wyjściowe polecenia cmdlet nie mogą być przesyłane strumieniowo z powrotem do usługi Automation.

### <a name="cause"></a>Przyczyna

Ten problem najczęściej występuje, gdy Azure Automation runbook wywołujące polecenia cmdlet programu PowerShell PnP, na przykład , bez przechwytujące `add-pnplistitem` obiektów zwracanych.

### <a name="resolution"></a>Rozwiązanie

Edytuj skrypty, aby przypisać wszelkie wartości zwracane do zmiennych, aby polecenia cmdlet nie próbowały zapisywać całych obiektów do standardowych danych wyjściowych. Skrypt może przekierować strumień wyjściowy do polecenia cmdlet, jak pokazano poniżej.

```azurecli
  $null = add-pnplistitem
```

Jeśli skrypt analizuje dane wyjściowe polecenia cmdlet, skrypt musi przechowywać dane wyjściowe w zmiennej i manipulować zmienną, zamiast po prostu przesyłać strumieniowo dane wyjściowe.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenariusz: polecenie cmdlet nie jest rozpoznawane podczas wykonywania runbook

### <a name="issue"></a>Problem

Zadanie runbook kończy się niepowodzeniem z błędem:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że aparat programu PowerShell nie może znaleźć polecenia cmdlet, które jest w twoim runbooku. Istnieje możliwość, że w module zawierającym polecenie cmdlet brakuje konta, występuje konflikt nazw z nazwą runbook lub polecenie cmdlet również istnieje w innym module, a automatyzacja nie może rozpoznać nazwy.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z następujących rozwiązań:

* Upewnij się, że wprowadzono poprawnie nazwę polecenia cmdlet.
* Upewnij się, że polecenie cmdlet istnieje na Twoim koncie usługi Automation i że nie ma konfliktów. Aby sprawdzić, czy polecenie cmdlet jest obecne, otwórz go w trybie edycji i wyszukaj polecenie cmdlet, które chcesz znaleźć w bibliotece, lub uruchom polecenie `Get-Command <CommandName>` . Po weryfikacji, czy polecenie cmdlet jest dostępne dla konta i czy nie występują konflikty nazw z innymi poleceniami cmdlet lub runbook, dodaj polecenie cmdlet do kanwy. Upewnij się, że używasz prawidłowego zestawu parametrów w swoim runbook.
* Jeśli występuje konflikt nazw, a polecenie cmdlet jest dostępne w dwóch różnych modułach, rozwiąż problem, używając w pełni kwalifikowanej nazwy polecenia cmdlet. Na przykład możesz użyć nazwy `ModuleName\CmdletName`.
* Jeśli uruchamiasz lokalnie runbook w grupie hybrydowych procesów roboczych, upewnij się, że moduł i polecenie cmdlet są zainstalowane na maszynie hostujące hybrydowy proces roboczy.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenariusz: Nieprawidłowe odwołanie do obiektu w wywołaniu Add-AzAccount

### <a name="issue"></a>Problem

Ten błąd jest wyświetlany podczas pracy z `Add-AzAccount` poleceniem , który jest aliasem polecenia `Connect-AzAccount` cmdlet :

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, jeśli przed wywołaniem wywołania w celu dodania konta usługi Automation w programie Runbook nie są wymagane `Add-AzAccount` odpowiednie kroki. Przykładem jednego z niezbędnych kroków jest zalogowanie się przy użyciu konta Uruchom jako. Aby uzyskać informacje o prawidłowych operacjach do użycia w twoim programie Runbook, zobacz Runbook execution in Azure Automation (Wykonywanie [runbook w programie Azure Automation](../automation-runbook-execution.md)).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenariusz: Odwołanie do obiektu nie jest ustawione na wystąpienie obiektu

### <a name="issue"></a>Problem

Podczas wywoływania podrzędnego obiektu Runbook z parametrem występuje następujący błąd, a strumień wyjściowy `Wait` zawiera obiekt :

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

Jeśli strumień zawiera obiekty, `Start-AzAutomationRunbook` nie obsługuje prawidłowo strumienia wyjściowego.

### <a name="resolution"></a>Rozwiązanie

Zaimportuj logikę sondowania i pobierz dane wyjściowe za pomocą polecenia cmdlet [Get-AzAutomationJobOutput.](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) Przykład tej logiki jest zdefiniowany tutaj:

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
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenariusz: Runbook kończy się niepowodzeniem z powodu deserializowanego obiektu

### <a name="issue"></a>Problem

Twój runbook kończy się niepowodzeniem z błędem:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Przyczyna

Jeśli twój runbook jest przepływem pracy programu PowerShell, przechowuje złożone obiekty w formacie zdeserializizowanym, aby utrwalić stan elementu Runbook, jeśli przepływ pracy zostanie wstrzymany.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z następujących rozwiązań:

* W przypadku instalacji pipingu złożonych obiektów z jednego polecenia cmdlet do innego opakuj te polecenia cmdlet w `InlineScript` działaniu.
* Przekaż potrzebną nazwę lub wartość ze złożonego obiektu zamiast przekazywać cały obiekt.
* Użyj runbooka programu PowerShell zamiast runbook przepływu pracy programu PowerShell.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenariusz: 400 Zły stan żądania podczas wywoływania webhook

### <a name="issue"></a>Problem

Podczas próby wywołania element webhook dla Azure Automation Runbook zostanie wyświetlony następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Przyczyna

Webhook, który próbujesz wywołać, jest wyłączony lub wygasł. 

### <a name="resolution"></a>Rozwiązanie

Jeśli ten webhook jest wyłączony, możesz go ponownie włączyć za pośrednictwem Azure Portal. Jeśli ten webhook wygasł, musisz go usunąć, a następnie utworzyć ponownie. Możesz odnowić [webhook](../automation-webhooks.md#renew-a-webhook) tylko wtedy, gdy jeszcze nie wygasł. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenariusz: 429: Liczba żądań jest obecnie zbyt duża

### <a name="issue"></a>Problem

Podczas uruchamiania polecenia cmdlet zostanie wyświetlony `Get-AzAutomationJobOutput` następujący komunikat o błędzie:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z runbook, który ma wiele [pełnych strumieni.](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)

### <a name="resolution"></a>Rozwiązanie

Aby usunąć ten błąd, wykonaj jedną z następujących czynności:

* Edytuj pozycję runbook i zmniejsz liczbę strumieni zadań, które emituje.
* Zmniejsz liczbę strumieni do pobrania podczas uruchamiania polecenia cmdlet . W tym celu można ustawić wartość parametru dla polecenia `Stream` cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) w celu pobrania tylko strumieni wyjściowych. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenariusz: Zadanie runbook kończy się niepowodzeniem z powodu przekroczenia przydzielonego limitu przydziału

### <a name="issue"></a>Problem

Zadanie runbook kończy się niepowodzeniem z błędem:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wykonanie zadania przekracza bezpłatny limit przydziału 500 minut dla Twojego konta. Ten limit przydziału ma zastosowanie do wszystkich typów zadań wykonywania zadań. Niektóre z tych zadań to testowanie zadania, uruchamianie zadania z portalu, wykonywanie zadania przy użyciu webhook lub planowanie zadania do wykonania przy użyciu Azure Portal lub centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [Cennik usługi Automation.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz korzystać z ponad 500 minut przetwarzania miesięcznie, zmień swoją subskrypcję z warstwy Bezpłatna na warstwę Podstawowa:

1. Zaloguj się do subskrypcji platformy Azure.
1. Wybierz konto usługi Automation, które chcesz uaktualnić.
1. Wybierz **pozycję Ustawienia,** a następnie wybierz pozycję **Cennik.**
1. Wybierz **pozycję Włącz** w dolnej części strony, aby uaktualnić konto do warstwy Podstawowa.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scenariusz: strumień wyjściowy runbook większy niż 1 MB

### <a name="issue"></a>Problem

Działanie runbook uruchomionego w piaskownicy platformy Azure kończy się niepowodzeniem z następującym błędem:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, ponieważ twój runbook próbował zapisać zbyt dużo danych wyjątków do strumienia wyjściowego.

### <a name="resolution"></a>Rozwiązanie

Istnieje limit 1 MB dla strumienia wyjściowego zadania. Upewnij się, że element Runbook ujął wywołania do pliku wykonywalnego lub podprocesu przy użyciu `try` bloków `catch` i . Jeśli operacje zrzucą wyjątek, kod zapisze komunikat z wyjątku w zmiennej usługi Automation. Ta technika zapobiega zapisywaniu komunikatu w strumieniu wyjściowym zadania. W przypadku wykonanych zadań hybrydowych procesów roboczych runbook strumień wyjściowy obcięty do 1 MB jest wyświetlany bez komunikatu o błędzie.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenariusz: Trzy razy podjęto próbę uruchomienia zadania runbook, ale nie można uruchomić zadania za każdym razem

### <a name="issue"></a>Problem

Wystąpił błąd dla twojego runbook z następującym błędem:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu jednego z następujących problemów:

* **Limit pamięci.** Zadanie może się nie powieść, jeśli używa więcej niż 400 MB pamięci. Udokumentowane limity pamięci przydzielonej do piaskownicy znajdują się w tesłudze [Automation service limits (Limity usługi Automation).](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* **Gniazda sieciowe.** Piaskownice platformy Azure są ograniczone do 1000 współbieżnych gniazd sieciowych. Aby uzyskać więcej informacji, zobacz [Automation service limits (Limity usługi Automation).](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)

* **Niezgodny moduł.** Zależności modułów mogą nie być poprawne. W takim przypadku twój runbook zwykle zwraca `Command not found` komunikat lub `Cannot bind parameter` .

* **Brak uwierzytelniania w usłudze Active Directory dla piaskownicy.** Element Runbook próbował wywołać plik wykonywalny lub podproces uruchamiany w piaskownicy platformy Azure. Konfigurowanie do uwierzytelniania w usłudze Azure AD przy użyciu biblioteki Azure Active Directory Authentication Library (ADAL) nie jest obsługiwane.

### <a name="resolution"></a>Rozwiązanie

* **Limit pamięci, gniazda sieciowe.** Sugerowane sposoby pracy w ramach limitów pamięci to dzielenie obciążenia między wiele elementów Runbook, przetwarzanie mniejszej ilości danych w pamięci, unikanie zapisywania niepotrzebnych danych wyjściowych z elementów Runbook oraz uwzględnianie, ile punktów kontrolnych jest zapisywanych w ramach elementów Runbook przepływu pracy programu PowerShell. Użyj metody clear, takiej jak , aby wyczyścić zmienne i użyć metody w `$myVar.clear` `[GC]::Collect` celu natychmiastowego uruchomienia wyrzucania elementów bezużytecznych. Te akcje zmniejszają zużycie pamięci przez moduł Runbook podczas wykonywania.

* **Niezgodny moduł.** Zaktualizuj moduły platformy Azure, korzystając z kroków instrukcje [z tematu Jak Azure PowerShell modułów w Azure Automation](../automation-update-azure-modules.md).

* **Brak uwierzytelniania w usłudze Active Directory dla piaskownicy.** Podczas uwierzytelniania w usłudze Azure AD za pomocą runbook upewnij się, że moduł usługi Azure AD jest dostępny na Twoim koncie usługi Automation. Pamiętaj, aby przyznać kontu Uruchom jako uprawnienia niezbędne do wykonywania zadań automatyznych przez ten podręcznik.

  Jeśli element Runbook nie może wywołać pliku wykonywalnego lub podprocesu uruchomionego w piaskownicy platformy Azure, użyj go w hybrydowym procesu [roboczego runbook.](../automation-hrw-run-runbooks.md) Hybrydowe pracownicy nie są ograniczone limitami pamięci i sieci, które mają piaskownice platformy Azure.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenariusz: Zadanie programu PowerShell kończy się niepowodzeniem z komunikatem o błędzie "Nie można wywołać metody"

### <a name="issue"></a>Problem

Po uruchomieniu zadania programu PowerShell w ramach runbook uruchomionego na platformie Azure zostanie wyświetlony następujący komunikat o błędzie:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wskazywać, że nie można uruchomić elementów Runbook uruchamianych w piaskownicy platformy Azure w trybie [pełnego języka.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu:

* Zamiast używać [start-job,](/powershell/module/microsoft.powershell.core/start-job)użyj [start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) do uruchomienia runbook.
* Spróbuj uruchomić ten runbook w hybrydowym procesu roboczego runbook.

Aby dowiedzieć się więcej o tym zachowaniu i innych zachowaniach Azure Automation [Runbook,](../automation-runbook-execution.md)zobacz Wykonywanie Azure Automation .

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenariusz: Nie można ukończyć długotrwałego runbook

### <a name="issue"></a>Problem

Po uruchomieniu przez trzy godziny twój runbook jest w stanie Zatrzymany. Może również zostać wyświetlony ten błąd:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

To zachowanie jest zgodnie z projektem w piaskownicach platformy Azure ze względu na sprawiedliwe monitorowanie [udziałów](../automation-runbook-execution.md#fair-share) procesów w Azure Automation. Jeśli proces jest wykonywany dłużej niż trzy godziny, fair share automatycznie zatrzymuje runbook. Stan obiektu Runbook, który wykracza poza limit czasu sprawiedliwego udostępniania, różni się w zależności od typu. Program PowerShell i python runbook są ustawione na stan Zatrzymano. Dla zadań Runbook przepływu pracy programu PowerShell ustawiono wartość Niepowodzenie.

### <a name="cause"></a>Przyczyna

Element Runbook został uruchomiony w ramach trzygodzinnego limitu dozwolonego przez sprawiedliwe udostępnianie w piaskownicy platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jednym z zalecanych rozwiązań jest uruchomienie runbook w hybrydowym procesu [roboczego runbook.](../automation-hrw-run-runbooks.md) Hybrydowe pracownicy nie są ograniczani przez trzygodzinny limit fair share runbook, który mają piaskownice platformy Azure. W przypadku nieoczekiwanych problemów z infrastrukturą lokalną należy opracować podręczniki runbook uruchamiane w ramach hybrydowych pracowników runbook w celu obsługi zachowania ponownego uruchamiania.

Innym rozwiązaniem jest optymalizacja tego podręcznika przez utworzenie [podrzędnego.](../automation-child-runbooks.md) Jeśli twój runbook przechodzi w pętli przez tę samą funkcję w kilku zasobach, na przykład podczas operacji bazy danych na kilku bazach danych, możesz przenieść funkcję do podrzędnego podręcznika Runbook. Każdy podrzędny podręcznik Runbook jest wykonywany równolegle w osobnym procesie. To zachowanie zmniejsza całkowity czas ukończenia nadrzędnego elementu Runbook.

Polecenia cmdlet programu PowerShell, które umożliwiają scenariusz podrzędnego runbook:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). To polecenie cmdlet pozwala uruchomić element runbook i przekazać do niego parametry.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Jeśli istnieją operacje, które należy wykonać po zakończeniu działania podrzędnego podręcznika Runbook, to polecenie cmdlet umożliwia sprawdzenie stanu zadania dla każdego podrzędnego.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenariusz: błąd w strumieniach zadań get_SerializationSettings metody

### <a name="issue"></a>Problem

W strumieniach zadań dla runbook zostanie wyświetlony następujący błąd:

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

Ten błąd jest prawdopodobnie spowodowany użyciem niekompletnej migracji z modułów AzureRM do Az w elementach Runbook. Taka sytuacja może spowodować uruchomienie Azure Automation runbook przy użyciu tylko modułów AzureRM, a następnie uruchomienie innego zadania przy użyciu tylko modułów Az, co prowadzi do awarii piaskownicy.

### <a name="resolution"></a>Rozwiązanie

Nie zalecamy używania az i azureRM polecenia cmdlet w tym samym runbook. Aby dowiedzieć się więcej o prawidłowym użyciu modułów, zobacz [Migrowanie do modułów Az.](../shared-resources/modules.md#migrate-to-az-modules)

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenariusz: odmowa dostępu podczas korzystania z piaskownicy platformy Azure dla runbook lub aplikacji

### <a name="issue"></a>Problem

Gdy element Runbook lub aplikacja próbuje uruchomić się w piaskownicy platformy Azure, środowisko nie zezwala na dostęp.

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, ponieważ piaskownice platformy Azure uniemożliwiają dostęp do wszystkich serwerów COM poza procesem. Na przykład aplikacja lub runbook w trybie piaskownicy nie może wywołać usługi Instrumentacja zarządzania Windows (WMI) ani do usługi Instalator Windows (msiserver.exe).

### <a name="resolution"></a>Rozwiązanie

Aby uzyskać szczegółowe informacje na temat korzystania z piaskownic platformy Azure, zobacz [Runbook execution environment (Środowisko wykonywania runbook).](../automation-runbook-execution.md#runbook-execution-environment)

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenariusz: nieprawidłowy kod stanu Zabronione podczas używania Key Vault wewnątrz runbook

### <a name="issue"></a>Problem

Podczas próby uzyskania dostępu do Azure Key Vault za pośrednictwem Azure Automation Runbook występuje następujący błąd:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Przyczyna

Możliwe przyczyny tego problemu to:

* Nie korzystasz z konta Uruchom jako.
* Niewystarczające uprawnienia.

### <a name="resolution"></a>Rozwiązanie

#### <a name="not-using-a-run-as-account"></a>Nie korzystasz z konta Uruchom jako

Wykonaj [kroki 5 — dodawanie uwierzytelniania w](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) celu zarządzania zasobami platformy Azure w celu upewnienia się, że używasz konta Uruchom jako do uzyskiwania dostępu do Key Vault.

#### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia

[Dodaj uprawnienia do Key Vault,](../manage-runas-account.md#add-permissions-to-key-vault) aby upewnić się, że konto Uruchom jako ma wystarczające uprawnienia dostępu do Key Vault.

## <a name="recommended-documents"></a>Zalecane dokumenty

* [Wykonywanie runbook w programie Azure Automation](../automation-runbook-execution.md)
* [Uruchamianie runbook w programie Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz swojego problemu w tym miejscu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów z platformy Azure na [forach platformy Azure.](https://azure.microsoft.com/support/forums/)
* Połącz się z usługą , [@AzureSupport](https://twitter.com/azuresupport) oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta. pomoc techniczna platformy Azure łączy Cię ze społecznością platformy Azure, aby uzyskać odpowiedzi, pomoc techniczną i ekspertów.
* Jeśli potrzebujesz więcej pomocy, możesz złożyć zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
