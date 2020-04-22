---
title: Rozwiązywanie problemów z błędami w usłudze Azure Automation
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy, które mogą wystąpić w podręcznikach uruchomieniu usługi Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770918"
---
# <a name="troubleshoot-runbook-errors"></a>Rozwiązywanie problemów z błędami wiązki uruchomieniu

 W tym artykule opisano różne błędy żyła wiązki uruchomieniu, które mogą wystąpić i jak je rozwiązać.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="diagnosing-runbook-issues"></a>Diagnozowanie problemów z podręcznikiem runbook

Po otrzymaniu błędów podczas wykonywania elementów runbook w usłudze Azure Automation, można użyć następujących kroków, aby pomóc zdiagnozować problemy.

1. **Upewnij się, że skrypt egojnika jest pomyślnie wykonywany na komputerze lokalnym.** 

    Informacje na temat modułów referencyjnych i uczenia się można znaleźć w [dokumentów programu PowerShell](/powershell/scripting/overview) lub [Docs języka.](https://docs.python.org/3/) Lokalne wykonywanie skryptu może wykrywać i rozwiązywać typowe błędy, takie jak:

      * Brakujące moduły
      * Błędy składniowe
      * Błędy logiczne

2. **Zbadaj [strumienie błędów strumyka](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output).**

    Spójrz na te strumienie dla konkretnych komunikatów i porównaj je z błędami udokumentowanymi w tym artykule.

3. **Upewnij się, że węzły i obszar roboczy automatyzacji mają wymagane moduły.** 

    Jeśli program runbook importuje wszystkie moduły, sprawdź, czy są one dostępne dla twojego konta automatyzacji, wykonując kroki wymienione w module [Importuj](../shared-resources/modules.md#importing-modules). Zaktualizuj moduły do najnowszej wersji, postępując zgodnie z instrukcjami dotyczącymi [aktualizacji modułów platformy Azure w usłudze Azure Automation.](..//automation-update-azure-modules.md) Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, zobacz [Rozwiązywanie problemów z modułami](shared-resources.md#modules).

4. **Czy jeśli system runbook jest zawieszony lub nieoczekiwanie nie powiedzie się.**

    * [Sprawdź stan zadania](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definiuje stany uruchomieniu i niektóre możliwe przyczyny.
    * [Dodaj dodatkowe dane wyjściowe](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) do śmiwoja, aby zidentyfikować, co się dzieje, zanim projekt runbook zostanie zawieszony.
    * [Obsługa wszelkich wyjątków,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) które są generowane przez zadanie.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenariusz: Uruchom konto logowania AzureRMAccount, aby się zalogować

### <a name="issue"></a>Problem

Podczas wykonywania elementów runbook pojawia się następujący błąd:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, gdy nie używasz konta Uruchom jako lub konto Uruchom jako wygasło. Zobacz [Zarządzanie programem Azure Automation Run jako kontami](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ten błąd ma dwie podstawowe przyczyny:

* Istnieją różne wersje modułu AzureRM lub Az.
* Próbujesz uzyskać dostęp do zasobów w osobnej subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Jeśli ten błąd zostanie wyświetlony po zaktualizowaniu jednego modułu AzureRM lub Az, należy zaktualizować wszystkie moduły do tej samej wersji.

Jeśli próbujesz uzyskać dostęp do zasobów w innej subskrypcji, możesz wykonać poniższe czynności, aby skonfigurować uprawnienia.

1. Przejdź do konta Uruchamianie automatyzacji jako i skopiuj identyfikator aplikacji i odcisk palca.

    ![Identyfikator kopiowania i odcisk palca](../media/troubleshoot-runbooks/collect-app-id.png)

1. Przejdź do kontroli dostępu subskrypcji, gdzie konto automatyzacji NIE jest hostowane, i dodaj nowe przypisanie roli.

    ![Kontrola dostępu](../media/troubleshoot-runbooks/access-control.png)

1. Dodaj identyfikator aplikacji zebranych wcześniej. Wybierz pozycję Współautor uprawnień.

    ![Dodaj przypisanie roli](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Skopiuj nazwę subskrypcji.

1. Teraz można użyć następującego kodu systemu runbook, aby przetestować uprawnienia z konta automatyzacji do innej subskrypcji. Zamień `"\<CertificateThumbprint\>"` na wartość skopiowaną w kroku 1. Zamień `"\<SubscriptionName\>"` na wartość skopiowaną w kroku 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenariusz: Nie można znaleźć subskrypcji platformy Azure

### <a name="issue"></a>Problem

Podczas pracy z poleceniem `Select-AzureSubscription` `Select-AzureRMSubscription` `Select-AzSubscription` cmdlet lub poleceniem cmdlet pojawia się następujący błąd:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Błąd

Ten błąd może wystąpić, jeśli:

* Nazwa subskrypcji jest nieprawidłowa.
* Użytkownik usługi Azure Active Directory, który próbuje uzyskać szczegóły subskrypcji nie jest skonfigurowany jako administrator subskrypcji.
* Polecenie cmdlet jest niedostępne.

### <a name="resolution"></a>Rozwiązanie

Wykonaj poniższe czynności, aby ustalić, czy uwierzytelniłeś się na platformie Azure i masz dostęp do subskrypcji, którą próbujesz wybrać.

1. Aby upewnić się, że skrypt działa autonomicznie, przetestować go poza usługą Azure Automation.
2. Przed uruchomieniem polecenia cmdlet polecenia `Select-*` cmdlet upewnij się, że skrypt uruchamia polecenie cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)
3. Dodaj `Disable-AzContextAutosave –Scope Process` na początek ego księgi runbook. To wywołanie polecenia cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego zestawu runbook.
4. Jeśli ten komunikat o błędzie nadal jest `AzContext` wyświetlany, `Connect-AzAccount`zmodyfikuj kod, dodając parametr dla , a następnie wykonaj kod.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenariusz: uwierzytelnianie na platformie Azure nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe

### <a name="issue"></a>Problem

Podczas uwierzytelniania na platformie Azure przy użyciu nazwy użytkownika i hasła platformy Azure pojawia się następujący błąd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Przyczyna

Jeśli masz uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz użyć użytkownika usługi Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub jednostki usługi do uwierzytelniania.

### <a name="resolution"></a>Rozwiązanie

Aby użyć certyfikatu z poleceniami cmdlet modelu wdrażania klasycznego platformy Azure, zapoznaj się z [poleceniem Tworzenie i dodawanie certyfikatu do zarządzania usługami platformy Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć jednostki usługi z poleceniami cmdlet usługi Azure Resource Manager, zobacz [Tworzenie jednostki usługi przy użyciu witryny Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelnianie jednostki usługi za pomocą usługi Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenariusz: W strumieniach zadań pojawia się błąd dotyczący metody get_SerializationSettings

### <a name="issue"></a>Problem

W strumieniach zadań dla systemu runbook jest widoczny następujący błąd:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przy użyciu poleceń cmdlet modułu AzureRM i Az w życiorniku. Występuje podczas importowania modułu Az przed zaimportowanie modułu AzureRM.

### <a name="resolution"></a>Rozwiązanie

Nie można zaimportować i używać poleceń cmdlet Az i AzureRM w tym samym uruchomieniu. Rzesz. Aby dowiedzieć się więcej o poleceniach cmdlet AZ w usłudze Azure Automation, zobacz [Zarządzanie modułami w usłudze Azure Automation](../shared-resources/modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenariusz: Projekt runbook kończy się niepowodzeniem z powodu błędu: zadanie zostało anulowane

### <a name="issue"></a>Problem

Element runbook kończy się niepowodzeniem z błędem podobnym do następującego przykładu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przy użyciu przestarzałych modułów platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, aktualizując moduły platformy Azure do najnowszej wersji. 

1. Na koncie automatyzacji kliknij pozycję **Moduły**, a następnie **zaktualizuj moduły platformy Azure**. 
2. Aktualizacja trwa około 15 minut. Po jego zakończeniu uruchom ponownie projekt runbook, który nie powiódł się.

Aby dowiedzieć się więcej na temat aktualizowania modułów, zobacz [Aktualizowanie modułów platformy Azure w usłudze Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenariusz: Programy runbook nie powiodą się podczas wykonywania wielu subskrypcji

### <a name="issue"></a>Problem

Podczas wykonywania śmięty uruchomieniu, projekt runbook nie można zarządzać zasobami platformy Azure.

### <a name="cause"></a>Przyczyna

Program runbook nie używa poprawnego kontekstu podczas uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Kontekst subskrypcji może zostać utracony, gdy projekt runbook wywołuje wiele elementów runbook. Aby upewnić się, że kontekst subskrypcji jest przekazywany do elementów `Start-AzureRmAutomationRunbook` runbook, `AzureRmContext` niech runbook klienta przekazać kontekst do polecenia cmdlet w parametrze. Użyj `Disable-AzureRmContextAutosave` polecenia cmdlet `Scope` z `Process` parametrem ustawionym, aby upewnić się, że określone poświadczenia są używane tylko dla bieżącego zestawu runbook. Aby uzyskać więcej informacji, zobacz [Praca z wieloma subskrypcjami](../automation-runbook-execution.md#working-with-multiple-subscriptions).

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenariusz: Termin nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji, skryptu

### <a name="issue"></a>Problem

Element runbook kończy się niepowodzeniem z błędem podobnym do następującego przykładu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Przyczyna

Ten błąd może się zdarzyć z następujących powodów:

* Moduł zawierający polecenie cmdlet nie jest importowany do konta automatyzacji.
* Moduł zawierający polecenie cmdlet jest importowany, ale jest nieaktualny.

### <a name="resolution"></a>Rozwiązanie

Wykonaj jedno z następujących zadań, aby rozwiązać ten błąd. 

* Aby uzyskać moduł platformy Azure, zobacz [Jak zaktualizować moduły usługi Azure PowerShell w usłudze Azure Automation,](../automation-update-azure-modules.md) aby dowiedzieć się, jak zaktualizować moduły na koncie automatyzacji.

* W przypadku modułu nienawiązanego z platformą Azure upewnij się, że moduł zaimportowany do konta automatyzacji.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenariusz: Próbowano trzykrotnie uruchomić zadanie uruchomienia zadania, ale nie można było uruchomić za każdym razem

### <a name="issue"></a>Problem

Program runbook kończy się niepowodzeniem z następującym błędem:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu jednego z następujących problemów:

* Limit pamięci. Zadanie może zakończyć się niepowodzeniem, jeśli używa więcej niż 400 MB pamięci. Udokumentowane limity pamięci przydzielonej do piaskownicy znajdują się w [limitach usług automatyzacji.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* Gniazda sieciowe. Piaskowce platformy Azure są ograniczone do 1000 równoczesnych gniazd sieciowych. Zobacz [Limity usług automatyzacji](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Moduł niezgodny. Zależności modułu mogą być nieprawidłowe. W takim przypadku system runbook `Command not found` zazwyczaj `Cannot bind parameter` zwraca lub komunikat.

* Brak uwierzytelniania z usługą Active Directory dla piaskownicy. Element runbook próbował wywołać plik wykonywalny lub podproces, który jest uruchamiany w obszarze izolowanym platformy Azure. Konfigurowanie uwierzytelniania w usłudze Azure AD przy użyciu biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) nie jest obsługiwane.

* Za dużo danych wyjątków. Twój system runbook próbował zapisać zbyt wiele danych wyjątków do strumienia wyjściowego.

### <a name="resolution"></a>Rozwiązanie

* Limit pamięci, gniazda sieciowe. Sugerowane sposoby pracy w granicach pamięci to dzielenie obciążenia między wiele tekstów trakcyjnych, przetwarzanie mniejszej ilości danych w pamięci, unikanie zapisywania niepotrzebnych danych wyjściowych z tekstów runbook i rozważenie liczby punktów kontrolnych zapisanych w elementach runbook przepływu pracy programu PowerShell. Użyj clear metody, `$myVar.clear`takich jak , aby `[GC]::Collect` usunąć zmienne i użyć do uruchomienia wyrzucania elementów bezużytecznych natychmiast. Te akcje zmniejszają zużycie pamięci przez runbook podczas wykonywania.

* Moduł niezgodny. Zaktualizuj moduły platformy Azure, wykonując kroki opisane w [programie Jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

* Brak uwierzytelniania z usługą Active Directory dla piaskownicy. Podczas uwierzytelniania w usłudze Azure AD za pomocą systemu runbook upewnij się, że moduł usługi Azure AD jest dostępny na koncie automatyzacji. Pamiętaj, aby przyznać konto Uruchom jako niezbędne uprawnienia do wykonywania zadań automatyzuje element runbook.

  Jeśli element runbook nie może wywołać pliku wykonywalnego lub podprocesowego uruchomionego w obszarze izolowanym platformy Azure, użyj go w hybrydowym [usłudze Runbook Worker](../automation-hrw-run-runbooks.md). Hybrydowe procesy pracy nie są ograniczone przez ograniczenia pamięci i sieci, które mają piaskownice platformy Azure.

* Za dużo danych wyjątków. Istnieje limit 1MB na strumień wyjściowy zadania. Upewnij się, że element runbook zawiera wywołania do `try` wykonywalnego lub podprocesu przy użyciu i `catch` blokuje. Jeśli operacje zgłosić wyjątek, niech kod napisać komunikat z wyjątku do zmiennej automatyzacji. Ta technika zapobiega wiadomości jest zapisywany w strumieniu danych wyjściowych zadania.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenariusz: zalogowanie się do konta platformy Azure nie powiodło się

### <a name="issue"></a>Problem

Podczas pracy z poleceniem `Connect-AzAccount` cmdlet pojawia się jeden z następujących błędów:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Przyczyna

Te błędy występują, jeśli nazwa zasobu poświadczeń jest nieprawidłowa. Mogą one również wystąpić, jeśli nazwa użytkownika i hasło użyte do skonfigurowania zasobu poświadczeń automatyzacji nie są prawidłowe.

### <a name="resolution"></a>Rozwiązanie

Aby ustalić, co jest nie tak, należy wykonać następujące czynności:

1. Upewnij się, że nie masz żadnych znaków specjalnych. Znaki te `\@` zawierają znak w nazwie zasobu poświadczeń automatyzacji, którego używasz do łączenia się z platformą Azure.
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w poświadczeniu usługi Azure Automation w lokalnym edytorze środowiska POWERSHELL ISE. Uruchom następujące polecenia cmdlet w programie PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Jeśli uwierzytelnianie nie powiedzie się lokalnie, poświadczenia usługi Azure Active Directory nie zostały poprawnie skonfigurowane. Zapoznaj się z wpisem blogu [usługi Azure Active Directory za pomocą usługi Azure Active Directory,](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) aby poprawnie skonfigurować konto usługi Azure Active Directory.

4. Jeśli błąd wydaje się być przejściowy, spróbuj dodać logikę ponawiania do procedury uwierzytelniania, aby uwierzytelnianie było bardziej niezawodne.

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

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenariusz: Odwołanie do obiektu nie jest ustawione na wystąpienie obiektu

### <a name="issue"></a>Problem

Podczas wywoływania podrzędnego obiektu runbook z `Wait` parametrem pojawia się następujący błąd, a strumień wyjściowy zawiera obiekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

`Start-AzAutomationRunbook`nie obsługuje strumień output poprawnie, jeśli strumień zawiera obiekty.

### <a name="resolution"></a>Rozwiązanie

Zaleca się zaimplementowanie logiki sondowania i użyj polecenia cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) do pobierania danych wyjściowych. Przykład tej logiki jest zdefiniowany poniżej.

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenariusz: Program Runbook kończy się niepowodzeniem z powodu obiektu deseriii

### <a name="issue"></a>Problem

Twój program runbook kończy się niepowodzeniem z powodu błędu:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Przyczyna

Jeśli projekt runbook jest przepływ pracy programu PowerShell, przechowuje złożone obiekty w formacie deserialized do utrwalenia stanu umienia, jeśli przepływ pracy jest zawieszony.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z poniższych rozwiązań.

* Jeśli łączysz złożone obiekty z jednego polecenia cmdlet do drugiego, `InlineScript` zawiń te polecenia cmdlet w działaniu.
* Przekaż nazwę lub wartość, które są potrzebne z obiektu złożonego zamiast przekazywania całego obiektu.
* Zamiast uruchomieniu przepływu pracy programu PowerShell należy używać roboczej programu PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenariusz: Zadanie uruchomieniu kończy się niepowodzeniem, ponieważ przydział przydzielony został przekroczony

### <a name="issue"></a>Problem

Zadanie uruchomieniu kończy się niepowodzeniem z powodu błędu:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wykonanie zadania przekracza 500-minutowy przydział wolny dla konta. Ten przydział ma zastosowanie do wszystkich typów zadań wykonywania zadań. Niektóre z tych zadań testują zadanie, uruchamiają zadanie z portalu, wykonują zadanie przy użyciu elementów webhook lub planują zadanie do wykonania przy użyciu portalu Azure lub centrum danych. Aby dowiedzieć się więcej o cenach automatyzacji, zobacz [Ustalanie cen automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut przetwarzania miesięcznie, zmień subskrypcję z warstwy Bezpłatna na podstawową.

1. Zaloguj się do subskrypcji platformy Azure.
2. Wybierz konto automatyzacji do uaktualnienia.
3. Kliknij **pozycję Ustawienia**, a następnie pozycję **Cennik**.
4. Kliknij **pozycję Włącz** na dole strony, aby uaktualnić konto do warstwy Podstawowa.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenariusz: polecenie cmdlet nie jest rozpoznawane podczas wykonywania śmiwalnika

### <a name="issue"></a>Problem

Zadanie uruchomieniu kończy się niepowodzeniem z powodu błędu:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany, gdy aparat programu PowerShell nie może znaleźć polecenia cmdlet, którego używasz w życiorniku. Jest możliwe, że moduł zawierający polecenie cmdlet brakuje na koncie, istnieje konflikt nazw z nazwą uruchomieniu lub polecenie cmdlet istnieje również w innym module i automatyzacji nie można rozpoznać nazwę.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj dowolnego z poniższych rozwiązań.

* Upewnij się, że nazwa polecenia cmdlet została wprowadzona poprawnie.
* Upewnij się, że polecenie cmdlet istnieje na koncie automatyzacji i że nie ma żadnych konfliktów. Aby sprawdzić, czy polecenie cmdlet jest obecne, otwórz program runbook w trybie edycji i `Get-Command <CommandName>`wyszukaj polecenie cmdlet, które chcesz znaleźć w bibliotece, lub uruchom program . Po sprawdzeniu, czy polecenie cmdlet jest dostępne dla konta i że nie ma konfliktów nazw z innymi poleceniami cmdlet lub kreśleniami, dodaj polecenie cmdlet do kanwy i upewnij się, że używasz prawidłowego parametru ustawionego w zestawie runbook.
* Jeśli masz konflikt nazw i polecenie cmdlet jest dostępne w dwóch różnych modułach, rozwiąż problem przy użyciu w pełni kwalifikowanej nazwy polecenia cmdlet. Na przykład możesz użyć nazwy `ModuleName\CmdletName`.
* Jeśli wykonujesz system runbook lokalnie w hybrydowej grupie procesów roboczych, upewnij się, że moduł i polecenie cmdlet są zainstalowane na komputerze, na którym znajduje się proces roboczy hybrydowy.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenariusz: Nie można ukończyć długotrwałego eksletu

### <a name="issue"></a>Problem

Element runbook jest wyświetlany w stanie Zatrzymany po uruchomieniu przez 3 godziny. Może również pojawić się ten błąd:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

To zachowanie jest zgodne z projektem w piaskownicach platformy Azure ze względu na [monitorowanie sprawiedliwego udziału](../automation-runbook-execution.md#fair-share) procesów w ramach usługi Azure Automation. Jeśli proces jest wykonywany dłużej niż trzy godziny, udział sprawiedliwy automatycznie zatrzymuje system runbook. Stan śruty, który przekracza limit czasu udziału sprawiedliwego, różni się w zależności od typu śrutu. Runbooki programu PowerShell i Python są ustawione na stan Zatrzymane. Runicie przepływu pracy programu PowerShell są ustawione na niepowodzenie.

### <a name="cause"></a>Przyczyna

Element runbook działał na 3-godzinny limit dozwolony przez udział sprawiedliwy w obszarze izolowanym platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jednym z zalecanych rozwiązań jest uruchomienie śmiętu w [hybrydowym uzywce uruchomieniu. twórców](../automation-hrw-run-runbooks.md). Hybrydowe procesy pracy nie są ograniczone przez limit 3-godzinnego śmięty udziału targowego, który ma piaskownice platformy Azure. W przypadku nieoczekiwanych problemów z infrastrukturą lokalną należy opracować zestawy runbook uruchamiane w hybrydowych środowiskach ceń czągo.

Innym rozwiązaniem jest optymalizacja żyjącego śmiwoju poprzez tworzenie [śmięty podrzędnych](../automation-child-runbooks.md). Jeśli elementu runbook pętli za pośrednictwem tej samej funkcji na kilka zasobów, na przykład w operacji bazy danych na kilka baz danych, można przenieść funkcję do elementu runbook podrzędny. Każdy podrzędny elementu runbook wykonuje równolegle w oddzielnym procesie. To zachowanie zmniejsza całkowity czas dla nadrzędnego umienia do wykonania.

Polecenia cmdlet programu PowerShell, które umożliwiają scenariusz bieśrodowiska podrzędnej są następujące:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). To polecenie cmdlet pozwala uruchomić element runbook i przekazać do niego parametry.

* [Get-AzAutomationOb](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Jeśli istnieją operacje, które muszą być wykonywane po zakończeniu śmiętu podrzędnego, to polecenie cmdlet umożliwia sprawdzenie stanu zadania dla każdego dziecka.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenariusz: Stan: 400 złe żądanie podczas wywoływania elementu webhook

### <a name="issue"></a>Problem

Podczas próby wywołania elementu webhook dla elementu runbook usługi Azure Automation pojawia się następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Przyczyna

Element webhook, który próbujesz wywołać, jest wyłączony lub wygasł.

### <a name="resolution"></a>Rozwiązanie

Jeśli element webhook jest wyłączona, można ponownie włączyć element webhook za pośrednictwem witryny Azure portal. Jeśli element webhook wygasł, należy go usunąć, a następnie ponownie utworzyć. Element [webhook](../automation-webhooks.md#renew-webhook) można odnowić tylko wtedy, gdy jeszcze nie wygasł.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenariusz: 429: Stawka żądania jest obecnie zbyt duża...

### <a name="issue"></a>Problem

Podczas uruchamiania `Get-AzAutomationJobOutput` polecenia cmdlet pojawia się następujący komunikat o błędzie:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z uruchomieniu, który ma wiele [strumieni pełnych.](../automation-runbook-output-and-messages.md#verbose-stream)

### <a name="resolution"></a>Rozwiązanie

Wykonaj jedną z następujących czynności, aby rozwiązać ten błąd.

* Edytuj runbook i zmniejsz liczbę strumieni zadań, które emituje.

* Zmniejsz liczbę strumieni, które mają zostać pobrane podczas uruchamiania polecenia cmdlet. Aby to zrobić, można ustawić `Stream` wartość parametru dla polecenia cmdlet [Get-AzAutomationJobOutput,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) aby pobrać tylko strumienie wyjściowe. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenariusz: Zadanie programu PowerShell kończy się niepowodzeniem z powodu błędu: nie można wywołać metody

### <a name="issue"></a>Problem

Podczas uruchamiania zadania programu PowerShell w elemście runbooka uruchomionego na platformie Azure pojawia się następujący komunikat o błędzie:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wskazywać, że programy runbook wykonujące w obszarze izolowanym platformy Azure nie mogą działać w [trybie pełnego języka.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu.

* Zamiast używać [zadania startowego,](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)użyj [start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) aby uruchomić runbook.
* Spróbuj uruchomić system runbook w hybrydowym uzywce procesowej.

Aby dowiedzieć się więcej o tym zachowaniu i innych zachowaniach uruchomieniu usługę Azure Automation, zobacz [Zachowanie systemu Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenariusz: Proces roboczy hybrydowego systemu Linux odbiera monit o podanie hasła podczas podpisywania ego księgi runbook

### <a name="issue"></a>Problem

Uruchomienie `sudo` polecenia dla procesu roboczego hybrydowego systemu Linux pobiera nieoczekiwany monit o hasło.

### <a name="cause"></a>Przyczyna

Konto **nxautomationuser** dla agenta usługi Log Analytics dla systemu Linux nie jest poprawnie skonfigurowane w pliku **sudoers.** Hybrydowy proces roboczy systemu runbook wymaga odpowiedniej konfiguracji uprawnień konta i innych danych, aby można było podpisać elementy runbook w uzywniu systemu Linux Runbook.

### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że hybrydowy proces roboczy uruchomieniu ma wykonywalny gnuPG (GPG) na komputerze.

* Sprawdź konfigurację konta **nxautomationuser** w pliku **sudoers.** Zobacz [Uruchamianie uruchomieniu w hybrydowym uzywce uruchomieniu](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenariusz: niepowodzenie polecenia cmdlet w niuacie programu PnP PowerShell w usłudze Azure Automation

### <a name="issue"></a>Problem

Gdy projekt runbook zapisuje obiekt generowany przez program PnP PowerShell bezpośrednio do danych wyjściowych usługi Azure Automation, dane wyjściowe polecenia cmdlet nie można przesyłać strumieniowo z powrotem do automatyzacji.

### <a name="cause"></a>Przyczyna

Ten problem najczęściej występuje, gdy usługa Azure Automation przetwarza elementy runbook, które wywołują polecenia cmdlet programu PnP PowerShell, na przykład `add-pnplistitem`, bez przechwytywania zwracanych obiektów.

### <a name="resolution"></a>Rozwiązanie

Edytuj skrypty, aby przypisać do zmiennych wszelkie wartości zwracane, aby polecenia cmdlet nie próbowały zapisywać całych obiektów na standardowym wyjściu. Skrypt może przekierować strumień wyjściowy do polecenia cmdlet, jak pokazano poniżej.

```azurecli
  $null = add-pnplistitem
```

Jeśli skrypt analizuje dane wyjściowe polecenia cmdlet, skrypt musi przechowywać dane wyjściowe w zmiennej i manipulować zmienną zamiast po prostu przesyłania strumieniowego danych wyjściowych.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scenariusz: Nieprawidłowy kod stanu "Zabronione" podczas korzystania z usługi Key Vault wewnątrz egobooka

### <a name="issue"></a>Problem

Podczas próby uzyskania dostępu do usługi Key Vault za pośrednictwem elementów runbook usługi Azure Automation pojawia się następujący błąd:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Przyczyna

Możliwe przyczyny tego problemu:

* Nie używanie konta Uruchom jako.
* Niewystarczające uprawnienia.

### <a name="resolution"></a>Rozwiązanie

#### <a name="not-using-run-as-account"></a>Nie używanie konta Uruchom jako

Wykonaj kroki opisane w [kroku 5 — Dodaj uwierzytelnianie, aby zarządzać zasobami platformy Azure,](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) aby upewnić się, że używasz konta Uruchom jako, aby uzyskać dostęp do usługi Key Vault. 

#### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia

Wykonaj czynności opisane w [sekcji Dodaj uprawnienia do usługi Key Vault,](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) aby upewnić się, że twoje konto Uruchom jako ma wystarczające uprawnienia dostępu do usługi Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mój problem nie jest wymieniony powyżej

W poniższych sekcjach znajdziesz listę innych typowych błędów i dokumentację pomocniczą ułatwiającą rozwiązanie problemu.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrydowy proces roboczy runbook nie uruchamia zadań lub nie odpowiada

Jeśli zadania są uruchomione w hybrydowym usłudze Runbook Worker, a nie w usłudze Azure Automation, może być konieczne [rozwiązanie problemu z samym hybrydowym pracownikiem.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook kończy się niepowodzeniem bez uprawnień lub niektórych odmian

Konta Uruchom jako mogą nie mieć takich samych uprawnień względem zasobów platformy Azure, jak bieżące konto. Upewnij się, że twoje konto Uruchom jako ma [uprawnienia dostępu do zasobów używanych](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) w skrypcie.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemy z przekazywaniem parametrów do elementów webhook

Aby uzyskać pomoc dotyczącą przekazywania parametrów do elementów webhook, zobacz [Uruchamianie elementu runbook z elementu webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemy z używaniem modułów Az

Korzystanie z modułów Az i modułów AzureRM na tym samym koncie automatyzacji nie jest obsługiwane. Zobacz [moduły Az w podręcznikach runbook, aby](https://docs.microsoft.com/azure/automation/az-modules) uzyskać więcej informacji.

### <a name="inconsistent-behavior-in-runbooks"></a>Niespójne zachowanie w elementach runbook

Postępuj zgodnie ze wskazówkami w [wykonywaniu żyłajnika,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) aby uniknąć problemów z równoczesnych zadań, zasoby są tworzone wiele razy lub innych logiki zależne od czasu w uruchomieniu. śliwą.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook kończy się niepowodzeniem z błędem Brak uprawnień, Zabronione (403) lub niektóre odmiany

Konta Uruchom jako mogą nie mieć takich samych uprawnień względem zasobów platformy Azure, jak bieżące konto. Upewnij się, że twoje konto Uruchom jako ma [uprawnienia dostępu do zasobów używanych](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) w skrypcie.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Elementy runbook działały, ale nagle przestały

* Upewnij się, że konto Uruchom jako nie wygasło. Zobacz [odnowienie certyfikatów](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Jeśli używasz elementu [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) do uruchomienia elementu runbook, upewnij się, że element webhook nie wygasł.

### <a name="passing-parameters-into-webhooks"></a>Przekazywanie parametrów do elementów webhook

Aby uzyskać pomoc dotyczącą przekazywania parametrów do elementów webhook, zobacz [Uruchamianie elementu runbook z elementu webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Korzystanie z modułów Az

Korzystanie z modułów Az i modułów AzureRM na tym samym koncie automatyzacji nie jest obsługiwane. Zobacz [moduły Az w podręcznikach runbook .](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="using-self-signed-certificates"></a>Korzystanie z certyfikatów z podpisem własnym

Aby używać certyfikatów z podpisem własnym, zobacz [Tworzenie nowego certyfikatu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Odmowa dostępu podczas korzystania z piaskownicy platformy Azure dla ego księgi runbook

Piaskownica platformy Azure uniemożliwia dostęp do wszystkich serwerów COM poza procesem. Na przykład aplikacja piaskownicy lub projekt runbook nie może wywołać instrumentacji zarządzania windowsem (WMI) ani do usługi Instalatora Windows (msiserver.exe). Aby uzyskać szczegółowe informacje na temat korzystania z piaskownicy, zobacz [Wykonanie programu Runbook w usłudze Azure Automation.](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="recommended-documents"></a>Zalecane dokumenty

* [Uruchamianie uruchomieniu w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Wykonywanie elementu runbook w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
