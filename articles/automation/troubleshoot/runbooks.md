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
ms.custom: has-adal-ref
ms.openlocfilehash: 70f3c52adc10556c358ed75a75fd023ffb21a813
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855093"
---
# <a name="troubleshoot-runbook-errors"></a>Rozwiązywanie problemów z błędami elementów Runbook

 W tym artykule opisano różne błędy elementów Runbook, które mogą wystąpić i sposoby ich rozwiązywania.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta Azure Automation można zaktualizować moduły do najnowszej wersji za pomocą [sposobu aktualizowania modułów Azure PowerShell w Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnose-runbook-issues"></a>Diagnozuj problemy z elementem Runbook

Po otrzymaniu błędów podczas wykonywania elementu Runbook w Azure Automation można wykonać następujące czynności, aby ułatwić zdiagnozowanie problemów:

1. Upewnij się, że skrypt elementu Runbook został pomyślnie wykonany na komputerze lokalnym.

    Aby uzyskać informacje dotyczące języka i modułów szkoleniowych, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview) lub [dokumenty języka Python](https://docs.python.org/3/). Uruchamianie skryptu lokalnie może wykrywać i rozwiązywać typowe błędy, takie jak:

      * Brakujące moduły
      * Błędy składniowe
      * Błędy logiki

1. Zbadaj [strumienie błędów](../automation-runbook-output-and-messages.md#runbook-output)elementu Runbook.

    Sprawdź te strumienie dla konkretnych komunikatów i porównaj je z błędami opisanymi w tym artykule.

1. Upewnij się, że węzły i obszar roboczy automatyzacji mają wymagane moduły.

    Jeśli element Runbook importuje dowolne moduły, sprawdź, czy są one dostępne dla konta usługi Automation, wykonując kroki z sekcji [Import modułów](../shared-resources/modules.md#import-modules). Zaktualizuj moduły programu PowerShell do najnowszej wersji, postępując zgodnie z instrukcjami w temacie [Update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md). Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, zobacz [Rozwiązywanie problemów modułów](shared-resources.md#modules).

1. Jeśli element Runbook jest zawieszony lub nieoczekiwanie nie powiedzie się:

    * [Odnów certyfikat,](../manage-runas-account.md#cert-renewal) Jeśli konto Uruchom jako wygasło.
    * [Odnów element webhook,](../automation-webhooks.md#renew-a-webhook) Jeśli próbujesz użyć wygasłego elementu webhook, aby uruchomić element Runbook.
    * [Sprawdź stan zadania](../automation-runbook-execution.md#job-statuses) , aby określić bieżące Stany elementu Runbook i niektóre możliwe przyczyny problemu.
    * [Dodaj dodatkowe dane wyjściowe](../automation-runbook-output-and-messages.md#message-streams) do elementu Runbook, aby określić, co się dzieje przed wstrzymaniem elementu Runbook.
    * [Obsługa wszelkich wyjątków](../automation-runbook-execution.md#exceptions) zgłaszanych przez zadanie.

1. Wykonaj ten krok, jeśli zadanie elementu Runbook lub środowisko w hybrydowym procesie roboczym elementu Runbook nie odpowiada.

    Jeśli używasz elementów Runbook w hybrydowym procesie roboczym elementu Runbook zamiast w Azure Automation, może być konieczne [rozwiązanie tego samego procesu](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenariusz: element Runbook kończy się niepowodzeniem z powodu braku uprawnień lub niedostępności 403

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu braku uprawnień lub niedostępności niedozwolonej 403 lub równoważnej.

### <a name="cause"></a>Przyczyna

Konta Uruchom jako mogą nie mieć tych samych uprawnień wobec zasobów platformy Azure jako bieżącego konta usługi Automation. 

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że konto Uruchom jako ma [uprawnienia dostępu do wszystkich zasobów](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) używanych w skrypcie.

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
1. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w Azure Automation poświadczenia w lokalnym edytorze ISE programu PowerShell. Uruchom następujące polecenia cmdlet w programie PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Jeśli uwierzytelnienie nie powiedzie się lokalnie, poświadczenia usługi Azure Active Directory (Azure AD) nie zostały prawidłowo skonfigurowane. Aby poprawnie skonfigurować konto usługi Azure AD, zapoznaj się z wpisem w blogu [uwierzytelnianie na platformie Azure przy użyciu Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).

1. Jeśli błąd pojawia się jako przejściowy, spróbuj dodać logikę ponowień do procedury uwierzytelniania, aby zapewnić bardziej niezawodne uwierzytelnianie.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenariusz: Uruchom logowanie-AzureRMAccount, aby się zalogować

### <a name="issue"></a>Problem

Podczas uruchamiania elementu Runbook pojawia się następujący błąd:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, gdy nie używasz konta Uruchom jako lub konto Uruchom jako wygasło. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami Azure Automation Uruchom jako](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ten błąd ma dwie przyczyny główne:

* Istnieją różne wersje AzureRM lub AZ module.
* Próbujesz uzyskać dostęp do zasobów w oddzielnej subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Jeśli ten błąd wystąpi po zaktualizowaniu jednego AzureRM lub AZ module, zaktualizuj wszystkie moduły do tej samej wersji.

Jeśli próbujesz uzyskać dostęp do zasobów w innej subskrypcji, wykonaj następujące kroki, aby skonfigurować uprawnienia:

1. Przejdź do konta Uruchom jako usługi Automation i skopiuj **Identyfikator aplikacji** i **odcisk palca**.

    ![Kopiowanie identyfikatora aplikacji i odcisku palca](../media/troubleshoot-runbooks/collect-app-id.png)

1. Przejdź do **kontroli dostępu** do subskrypcji, w której konto usługi Automation *nie* jest hostowane, i Dodaj nowe przypisanie roli.

    ![Kontrola dostępu](../media/troubleshoot-runbooks/access-control.png)

1. Dodaj wcześniej zebrane **identyfikatory aplikacji** . Wybierz pozycję uprawnienia **współautora** .

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
* Użytkownik usługi Azure AD próbujący uzyskać szczegółowe informacje o subskrypcji nie jest skonfigurowany jako administrator subskrypcji.
* Polecenie cmdlet jest niedostępne.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby określić, czy masz uwierzytelnienie na platformie Azure i czy masz dostęp do subskrypcji, którą próbujesz wybrać:

1. Aby upewnić się, że skrypt działa autonomicznie, przetestuj go poza Azure Automation.
1. Przed uruchomieniem `Select-*` polecenia cmdlet upewnij się, że skrypt uruchamia polecenie cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) .
1. Dodaj `Disable-AzContextAutosave –Scope Process` do początku elementu Runbook. To polecenie cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego elementu Runbook.
1. Jeśli nadal widzisz komunikat o błędzie, zmodyfikuj swój kod przez dodanie `AzContext` parametru do `Connect-AzAccount`, a następnie wykonaj kod.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenariusz: elementy Runbook kończą się niepowodzeniem podczas pracy z wieloma subskrypcjami

### <a name="issue"></a>Problem

Podczas wykonywania elementów Runbook element Runbook nie może zarządzać zasobami platformy Azure.

### <a name="cause"></a>Przyczyna

Element Runbook nie używa poprawnego kontekstu podczas uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Kontekst subskrypcji może zostać utracony, gdy element Runbook wywoła wiele elementów Runbook. Aby zapewnić, że kontekst subskrypcji zostanie przekazany do elementów Runbook, element Runbook klienta przekazuje kontekst do `Start-AzureRmAutomationRunbook` polecenia cmdlet w `AzureRmContext` parametrze. Użyj `Disable-AzureRmContextAutosave` polecenia cmdlet z `Scope` parametrem ustawionym `Process` na, aby upewnić się, że określone poświadczenia są używane tylko dla bieżącego elementu Runbook. Aby uzyskać więcej informacji, zobacz [subskrypcje](../automation-runbook-execution.md#subscriptions).

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

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenariusz: uwierzytelnianie na platformie Azure nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe

### <a name="issue"></a>Problem

Podczas uwierzytelniania na platformie Azure zostanie wyświetlony następujący błąd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Przyczyna

Jeśli masz uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz używać Azure Active Directory użytkownika do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub nazwy głównej usługi do uwierzytelnienia.

### <a name="resolution"></a>Rozwiązanie

Aby użyć certyfikatu z poleceniami cmdlet klasycznego modelu wdrażania platformy Azure, zobacz [Tworzenie i Dodawanie certyfikatu w celu zarządzania usługami platformy Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Aby użyć nazwy głównej usługi z poleceniami cmdlet Azure Resource Manager, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelnianie jednostki usługi przy użyciu Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenariusz: element Runbook kończy się niepowodzeniem z komunikatem o błędzie "zadanie zostało anulowane"

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany użyciem nieaktualnych modułów platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Możesz rozwiązać ten problem, aktualizując moduły platformy Azure do najnowszej wersji:

1. Na koncie usługi Automation wybierz pozycję **moduły**, a następnie wybierz pozycję **Aktualizuj moduły platformy Azure**.
1. Aktualizacja trwa około 15 minut. Po zakończeniu uruchom ponownie element Runbook, który się nie powiódł.

Aby dowiedzieć się więcej na temat aktualizowania modułów, zobacz temat [aktualizowanie modułów platformy Azure w Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenariusz: termin nie został rozpoznany jako nazwa polecenia cmdlet, funkcji lub skryptu

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Moduł zawierający polecenie cmdlet nie został zaimportowany do konta usługi Automation.
* Moduł, który zawiera polecenie cmdlet, został zaimportowany, ale jest nieaktualny.

### <a name="resolution"></a>Rozwiązanie

Wykonaj jedną z następujących czynności, aby rozwiązać ten problem:

* Aby dowiedzieć się, jak zaktualizować moduły na koncie usługi Automation, zobacz artykuł [jak zaktualizować moduły Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md) .
* W przypadku modułu spoza platformy Azure upewnij się, że moduł został zaimportowany do konta usługi Automation.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenariusz: polecenie cmdlet kończy się niepowodzeniem w elemencie Runbook programu PowerShell PnP na Azure Automation

### <a name="issue"></a>Problem

Gdy element Runbook zapisuje obiekt wygenerowany przez program PowerShell PnP do danych wyjściowych Azure Automation bezpośrednio, dane wyjściowe polecenia cmdlet nie mogą przesyłać strumieniowo do automatyzacji.

### <a name="cause"></a>Przyczyna

Ten problem najczęściej występuje, gdy Azure Automation przetwarza elementy Runbook, które wywołują polecenia cmdlet programu PowerShell PnP, `add-pnplistitem`na przykład bez przechwytywania obiektów zwracanych.

### <a name="resolution"></a>Rozwiązanie

Edytuj skrypty, aby przypisać wszelkie wartości zwracane do zmiennych, aby polecenia cmdlet nie powodowały próby zapisania całych obiektów w standardowym wyjściu. Skrypt może przekierować strumień wyjściowy do polecenia cmdlet, jak pokazano poniżej.

```azurecli
  $null = add-pnplistitem
```

Jeśli skrypt analizuje dane wyjściowe polecenia cmdlet, skrypt musi przechowywać dane wyjściowe w zmiennej i manipulować zmienną zamiast po prostu przesyła strumieniowo dane wyjściowe.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenariusz: nie rozpoznano polecenia cmdlet podczas wykonywania elementu Runbook

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że aparat programu PowerShell nie może znaleźć polecenia cmdlet używanego w elemencie Runbook. Istnieje możliwość, że na koncie nie ma modułu zawierającego polecenie cmdlet, występuje konflikt nazw z nazwą elementu Runbook lub polecenie cmdlet istnieje również w innym module, a Automatyzacja nie może rozpoznać nazwy.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Upewnij się, że poprawnie wprowadzono nazwę polecenia cmdlet.
* Upewnij się, że polecenie cmdlet istnieje na koncie usługi Automation i że nie występują żadne konflikty. Aby sprawdzić, czy polecenie cmdlet jest obecne, Otwórz element Runbook w trybie edycji i Wyszukaj polecenie cmdlet, które chcesz znaleźć w bibliotece, lub uruchom `Get-Command <CommandName>`. Po sprawdzeniu, czy polecenie cmdlet jest dostępne dla konta i że nie występują konflikty nazw z innymi poleceniami cmdlet lub elementami Runbook, Dodaj polecenie cmdlet do kanwy. Upewnij się, że używasz prawidłowego zestawu parametrów w elemencie Runbook.
* Jeśli występuje konflikt nazw, a polecenie cmdlet jest dostępne w dwóch różnych modułach, należy rozwiązać problem, używając w pełni kwalifikowanej nazwy polecenia cmdlet. Na przykład możesz użyć nazwy `ModuleName\CmdletName`.
* Jeśli element Runbook jest wykonywany lokalnie w grupie hybrydowych procesów roboczych, upewnij się, że moduł i polecenie cmdlet są zainstalowane na komputerze, który hostuje hybrydowy proces roboczy.

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

Podczas wywoływania podrzędnego elementu Runbook `Wait` jest wyświetlany następujący błąd, a strumień wyjściowy zawiera obiekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

Jeśli strumień zawiera obiekty, `Start-AzAutomationRunbook` nie obsługuje prawidłowo strumienia wyjściowego.

### <a name="resolution"></a>Rozwiązanie

Zaimplementuj logikę sondowania i użyj polecenia cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) , aby pobrać dane wyjściowe. Przykład tej logiki został zdefiniowany tutaj:

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

Aby rozwiązać ten problem, użyj jednego z następujących rozwiązań:

* Jeśli tworzysz złożone obiekty z jednego polecenia cmdlet do innego, zawiń te polecenia cmdlet w `InlineScript` działaniu.
* Przekaż nazwę lub wartość, która jest potrzebna z obiektu złożonego, zamiast przekazywać cały obiekt.
* Użyj elementu Runbook programu PowerShell zamiast elementu Runbook przepływu pracy programu PowerShell.



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenariusz: 400 nieprawidłowy stan żądania podczas wywoływania elementu webhook

### <a name="issue"></a>Problem

Gdy próbujesz wywołać element webhook dla elementu Runbook Azure Automation, zostanie wyświetlony następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Przyczyna

Element webhook, który próbujesz wywołać, jest wyłączony lub wygasł. 

### <a name="resolution"></a>Rozwiązanie

Jeśli element webhook jest wyłączony, możesz go włączyć ponownie za pomocą Azure Portal. Jeśli element webhook wygasł, należy go usunąć, a następnie utworzyć ponownie. Możesz [odnowić element webhook](../automation-webhooks.md#renew-a-webhook) , jeśli jeszcze go nie wygasł. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenariusz: 429: liczba żądań jest obecnie zbyt duża

### <a name="issue"></a>Problem

Podczas uruchamiania `Get-AzAutomationJobOutput` polecenia cmdlet zostanie wyświetlony następujący komunikat o błędzie:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z elementu Runbook, który ma wiele [strumieni pełnych](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj jedną z następujących czynności:

* Edytuj element Runbook i zmniejsz liczbę strumieni zadań, które emituje.
* Zmniejsz liczbę strumieni do pobrania podczas uruchamiania polecenia cmdlet. W tym celu można ustawić wartość `Stream` parametru polecenia cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) , aby pobrać tylko strumienie wyjściowe. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenariusz: zadanie elementu Runbook nie powiodło się, ponieważ przekroczono przydzieloną przydział

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wykonanie zadania przekracza 500-minutowy bezpłatny limit przydziału dla Twojego konta. Ten limit przydziału dotyczy wszystkich typów zadań wykonywania zadań. Niektóre z tych zadań są testowaniem zadania, uruchamiania zadania z portalu, wykonywania zadania przy użyciu elementów webhook lub planowania zadania do wykonania przy użyciu Azure Portal lub centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut przetwarzania miesięcznie, Zmień subskrypcję z warstwy Bezpłatna na warstwę podstawowa:

1. Zaloguj się do subskrypcji platformy Azure.
1. Wybierz konto usługi Automation, które ma zostać uaktualnione.
1. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Cennik**.
1. Wybierz pozycję **Włącz** na dole strony, aby uaktualnić konto do warstwy Podstawowa.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenariusz: rozpoczęto próbę uruchomienia zadania elementu Runbook trzy razy, ale uruchamianie nie powiedzie się za każdym razem

### <a name="issue"></a>Problem

Wystąpił błąd elementu Runbook z następującym błędem:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu jednego z następujących problemów:

* **Limit pamięci.** Zadanie może zakończyć się niepowodzeniem, jeśli używa więcej niż 400 MB pamięci. Udokumentowane limity dotyczące pamięci przydzieloną do piaskownicy znajdują się w [granicach usługi Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 
* **Gniazda sieciowe.** Piaskownice platformy Azure są ograniczone do 1 000 współbieżnych gniazd sieciowych. Aby uzyskać więcej informacji, zobacz [limity usługi Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).
* **Moduł jest niezgodny.** Zależności modułu mogą nie być poprawne. W takim przypadku element Runbook zwykle zwraca komunikat `Command not found` lub. `Cannot bind parameter`
* **Brak uwierzytelniania w Active Directory dla piaskownicy.** Element Runbook próbował wywołać plik wykonywalny lub podproces, który jest uruchamiany w piaskownicy platformy Azure. Konfigurowanie elementów Runbook do uwierzytelniania za pomocą usługi Azure AD przy użyciu biblioteki uwierzytelniania Azure Active Directory (ADAL) nie jest obsługiwane.
* **Zbyt dużo danych wyjątku.** Element Runbook podjął próbę zapisania zbyt dużej ilości danych wyjątku w strumieniu wyjściowym.

### <a name="resolution"></a>Rozwiązanie

* **Limit pamięci, gniazda sieciowe.** Sugerowane sposoby pracy w ramach limitów pamięci to dzielenie obciążenia między wiele elementów Runbook, przetwarzanie mniejszej ilości danych w pamięci, unikanie zapisywania niepotrzebnych danych wyjściowych z elementów Runbook i Dowiedz się, ile punktów kontrolnych jest zapisywanych w elementach Runbook przepływu pracy programu PowerShell. Użyj metody Clear, takiej jak `$myVar.clear`, aby wyczyścić zmienne i użyć `[GC]::Collect` do natychmiastowego uruchomienia wyrzucania elementów bezużytecznych. Te akcje zmniejszają rozmiar pamięci elementu Runbook w czasie wykonywania.
* **Moduł jest niezgodny.** Zaktualizuj moduły platformy Azure, wykonując kroki opisane w temacie [jak zaktualizować moduły Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).
* **Brak uwierzytelniania w Active Directory dla piaskownicy.** Podczas uwierzytelniania w usłudze Azure AD za pomocą elementu Runbook upewnij się, że moduł usługi Azure AD jest dostępny na Twoim koncie usługi Automation. Upewnij się, że konto Uruchom jako ma odpowiednie uprawnienia do wykonywania zadań, które są automatyzuje przez element Runbook.

  Jeśli element Runbook nie może wywołać pliku wykonywalnego lub podprocesu działającego w piaskownicy platformy Azure, użyj elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez limity pamięci i sieci dla piaskownic platformy Azure.

* **Zbyt dużo danych wyjątku.** W strumieniu wyjściowym zadania istnieje limit 1 MB. Upewnij się, że element Runbook ujmuje wywołania do pliku wykonywalnego lub podprocesu przy użyciu `try` bloków i `catch` . Jeśli operacje zgłaszają wyjątek, kod Zapisz komunikat z wyjątku do zmiennej automatyzacji. Ta technika uniemożliwia zapisanie komunikatu w strumieniu wyjściowym zadania.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenariusz: zadanie programu PowerShell kończy się niepowodzeniem z komunikatem o błędzie "nie można wywołać metody"

### <a name="issue"></a>Problem

Podczas uruchamiania zadania programu PowerShell w elemencie Runbook, który działa na platformie Azure, zostanie wyświetlony następujący komunikat o błędzie:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wskazywać, że elementy Runbook, które działają w piaskownicy platformy Azure, nie mogą być uruchamiane w [trybie pełnego języka](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu:

* Zamiast korzystać z [zadania startowego](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), należy użyć polecenia [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , aby uruchomić element Runbook.
* Spróbuj uruchomić element Runbook w hybrydowym procesie roboczym elementu Runbook.

Aby dowiedzieć się więcej na temat tego zachowania i innych zachowań Azure Automation elementów Runbook, zobacz [wykonywanie elementów Runbook w programie Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenariusz: nie można ukończyć długotrwałego elementu Runbook

### <a name="issue"></a>Problem

Stan elementu Runbook jest wyświetlany w stanie zatrzymanym przez trzy godziny. Ten błąd może również pojawić się w następujący sposób:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

To zachowanie jest zaprojektowana w piaskownicy platformy Azure ze względu na [rzetelne](../automation-runbook-execution.md#fair-share) monitorowanie procesów w ramach Azure Automation. Jeśli proces jest wykonywany dłużej niż trzy godziny, prawidłowy udział automatycznie zatrzyma element Runbook. Stan elementu Runbook, który przekracza limit czasu udostępniania, różni się od typu elementu Runbook. Elementy Runbook programu PowerShell i języka Python mają ustawiony stan zatrzymany. Elementy Runbook przepływu pracy programu PowerShell są ustawione na wartość niepowodzenie.

### <a name="cause"></a>Przyczyna

Element Runbook działał ponad 3-godzinny limit dozwolony przez sprawiedliwy udział w piaskownicy platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jednym z zalecanych rozwiązań jest uruchomienie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez 3-godzinne ograniczenia dotyczące udziału w usłudze Azure Portal. W przypadku nieoczekiwanych problemów z infrastrukturą lokalną należy opracować elementy Runbook działające na hybrydowych procesach roboczych elementów Runbook.

Innym rozwiązaniem jest zoptymalizowanie elementu Runbook przez utworzenie [podrzędnych elementów Runbook](../automation-child-runbooks.md). Jeśli element Runbook jest używany przez tę samą funkcję na kilku zasobach, na przykład w operacji bazy danych na kilku bazach danych można przenieść funkcję do podrzędnego elementu Runbook. Każdy podrzędny element Runbook jest wykonywany równolegle w osobnym procesie. To zachowanie zmniejsza łączny czas pracy nadrzędnego elementu Runbook.

Polecenia cmdlet programu PowerShell, które umożliwiają korzystanie z podrzędnego scenariusza elementu Runbook, to:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). To polecenie cmdlet pozwala uruchomić element runbook i przekazać do niego parametry.
* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Jeśli istnieją operacje, które należy wykonać po zakończeniu podrzędnego elementu Runbook, to polecenie cmdlet umożliwia sprawdzenie stanu zadania dla każdego elementu podrzędnego.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenariusz: błąd w strumieniach zadań dotyczących metody get_SerializationSettings

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

Ten błąd jest prawdopodobnie spowodowany użyciem niekompletnej migracji z AzureRM do AZ modules w elemencie Runbook. Ta sytuacja może spowodować, że Azure Automation uruchomić zadanie elementu Runbook za pomocą tylko modułów AzureRM, a następnie uruchomić inne zadanie przy użyciu tylko AZ module, który prowadzi do awarii piaskownicy.

### <a name="resolution"></a>Rozwiązanie

Nie zaleca się używania poleceń cmdlet AZ i AzureRM w tym samym elemencie Runbook. Aby dowiedzieć się więcej o prawidłowym użyciu tych modułów, zobacz [Migrowanie do AZ modules](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenariusz: odmowa dostępu podczas korzystania z piaskownicy platformy Azure dla elementu Runbook lub aplikacji

### <a name="issue"></a>Problem

Gdy element Runbook lub aplikacja próbuje działać w piaskownicy platformy Azure, środowisko odmówi dostępu.

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, ponieważ Piaskownice platformy Azure uniemożliwiają dostęp do wszystkich pozaprocesowych serwerów COM. Na przykład aplikacja w trybie piaskownicy lub element Runbook nie mogą wywoływać do Instrumentacja zarządzania Windows (WMI) ani do usługi Instalator Windows (MSIServer. exe). 

### <a name="resolution"></a>Rozwiązanie

Aby uzyskać szczegółowe informacje na temat korzystania z piaskownic platformy Azure, zobacz [środowisko wykonywania elementu Runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenariusz: nieprawidłowy kod stanu zabroniona podczas korzystania z Key Vault wewnątrz elementu Runbook

### <a name="issue"></a>Problem

Gdy próbujesz uzyskać dostęp do Azure Key Vault za pomocą Azure Automation elementu Runbook, zostanie wyświetlony następujący błąd:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Przyczyna

Możliwe przyczyny tego problemu:

* Nie używa konta Uruchom jako.
* Niewystarczające uprawnienia.

### <a name="resolution"></a>Rozwiązanie

#### <a name="not-using-a-run-as-account"></a>Nieużywanie konta Uruchom jako

Postępuj zgodnie z [krok 5. Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) w celu zapewnienia dostępu do Key Vault za pomocą konta Uruchom jako.

#### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia

[Dodaj uprawnienia do Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) , aby upewnić się, że konto Uruchom jako ma wystarczające uprawnienia dostępu do Key Vault.

## <a name="recommended-documents"></a>Zalecane dokumenty

* [Wykonywanie elementu runbook w usłudze Azure Automation](../automation-runbook-execution.md)
* [Uruchamianie elementu Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure nawiązuje połączenie z społecznością platformy Azure w celu uzyskania odpowiedzi, pomocy technicznej i ekspertów.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
