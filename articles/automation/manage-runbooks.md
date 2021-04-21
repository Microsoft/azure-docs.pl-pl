---
title: Zarządzanie zasobami Runbook w programie Azure Automation
description: W tym artykule opisano sposób zarządzania Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830092"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie zasobami Runbook w programie Azure Automation

Element Runbook można dodać do Azure Automation, tworząc nowy lub importując istniejący element z pliku lub [galerii runbook.](automation-runbook-gallery.md) Ten artykuł zawiera informacje dotyczące zarządzania runbook zaimportowane z pliku. Wszystkie szczegóły dotyczące uzyskiwania dostępu do community runbook i modułów można znaleźć w galeriach [runbook](automation-runbook-gallery.md)i modułów dla Azure Automation .

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Utwórz nowy podręcznik Runbook w Azure Automation przy użyciu Azure Portal lub Windows PowerShell. Po utworzeniu runbook można go edytować, korzystając z informacji w:

* [Edytowanie tekstowego runbook w programie Azure Automation](automation-edit-textual-runbook.md)
* [Poznaj kluczowe pojęcia Windows PowerShell przepływu pracy dla elementy Runbook automatyzacji](automation-powershell-workflow.md)
* [Zarządzanie pakietami języka Python 2 w programie Azure Automation](python-packages.md)
* [Zarządzanie pakietami języka Python 3 (wersja zapoznawcza) w Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Tworzenie runbook w Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz pozycję **Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.
3. Kliknij **pozycję Utwórz runbook.**
4. Wprowadź nazwę dla runbook i wybierz jego [typ](automation-runbook-types.md). Nazwa runbook musi zaczynać się literą i może zawierać litery, cyfry, podkreślenia i łączniki.
5. Kliknij **pozycję Utwórz,** aby utworzyć podręcznik Runbook i otworzyć edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie runbook za pomocą programu PowerShell

Użyj polecenia cmdlet [New-AzAutomationRunbook,](/powershell/module/az.automation/new-azautomationrunbook) aby utworzyć pusty element Runbook. Użyj `Type` parametru , aby określić jeden z typów elementów Runbook zdefiniowanych dla . `New-AzAutomationRunbook`

W poniższym przykładzie pokazano, jak utworzyć nowy pusty podręcznik Runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importowanie podręcznika Runbook

Aby utworzyć własny element Runbook, możesz zaimportować skrypt programu PowerShell lub przepływu pracy programu PowerShell **(ps1),** graficzny element Runbook **(.graphrunbook)** lub skrypt języka Python 2 lub Python 3 **(py).** Należy określić typ [runbook,](automation-runbook-types.md) który jest tworzony podczas importowania, biorąc pod uwagę następujące kwestie.

* Możesz zaimportować plik **ps1,** który nie zawiera przepływu pracy, do ani do [runbooka programu PowerShell,](automation-runbook-types.md#powershell-runbooks) ani do [runbooka przepływu pracy programu PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Jeśli zaimportujesz go do runbook przepływu pracy programu PowerShell, zostanie on przekonwertowany na przepływ pracy. W tym przypadku w tym przypadku w podręczniku Runbook są uwzględniane komentarze opisujące wprowadzone zmiany.

* Do runbook przepływu pracy programu PowerShell można zaimportować tylko plik **ps1** zawierający przepływ pracy programu [PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Jeśli plik zawiera wiele przepływów pracy programu PowerShell, importowanie nie powiedzie się. Każdy przepływ pracy należy zapisać do własnego pliku i zaimportować każdy z nich oddzielnie.

* Nie należy **importować pliku ps1** zawierającego przepływ pracy programu PowerShell do runbook programu [PowerShell,](automation-runbook-types.md#powershell-runbooks)ponieważ aparat skryptów programu PowerShell nie może go rozpoznać.

* Zaimportuj **tylko plik graphrunbook** do nowego graficznego element [runbook.](automation-runbook-types.md#graphical-runbooks)

### <a name="import-a-runbook-from-the-azure-portal"></a>Importowanie runbook z Azure Portal

Za pomocą poniższej procedury można zaimportować plik skryptu do Azure Automation.

> [!NOTE]
> Plik ps1 można **zaimportować** tylko do runbook przepływu pracy programu PowerShell przy użyciu portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **pozycję Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.
3. Kliknij **pozycję Importuj runbook.**
4. Kliknij **pozycję Plik runbook** i wybierz plik do zaimportowania.
5. Jeśli pole **Nazwa** jest włączone, możesz zmienić nazwę runbook. Nazwa musi zaczynać się od litery i może zawierać litery, cyfry, podkreślenia i łączniki.
6. Typ [runbook jest](automation-runbook-types.md) wybierany automatycznie, ale można go zmienić po uwzględnieniu odpowiednich ograniczeń.
7. Kliknij pozycję **Utwórz**. Nowy podręcznik Runbook zostanie wyświetlony na liście elementów Runbook dla konta usługi Automation.
8. Aby można [było go uruchomić,](#publish-a-runbook) należy opublikować go.

> [!NOTE]
> Po zaimportowaniu graficznego element runbook można przekonwertować go na inny typ. Nie można jednak przekonwertować graficznego runbook na tekstowy element Runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Importowanie runbook za pomocą Windows PowerShell

Użyj polecenia cmdlet [Import-AzAutomationRunbook,](/powershell/module/az.automation/import-azautomationrunbook) aby zaimportować plik skryptu jako roboczą pozycję Runbook. Jeśli runbook już istnieje, importowanie zakończy się niepowodzeniem, chyba że `Force` użyjemy parametru z poleceniem cmdlet .

W poniższym przykładzie pokazano, jak zaimportować plik skryptu do runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Obsługa zasobów

Jeśli twój zasób runbook tworzy [zasób](automation-runbook-execution.md#resources), przed podjęciem próby jego utworzenia skrypt powinien sprawdzić, czy zasób już istnieje. Oto podstawowy przykład.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Pobieranie szczegółów z dziennika aktywności

Możesz pobrać szczegóły dotyczące runbook, takie jak osoba lub konto, które uruchomiło go, z dziennika aktywności dla konta usługi Automation. [](automation-runbook-execution.md#activity-logging) Poniższy przykład programu PowerShell zawiera ostatniego użytkownika do uruchomienia określonego runbook.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Śledzenie postępu

Dobrym rozwiązaniem jest tworzenie swoich elementy Runbook w taki sposób, aby z natury działały modułowo, z logiką, która może być łatwo ponownie użyta i ponownie uruchomiona. Śledzenie postępu w programie Runbook gwarantuje, że logika runbook jest wykonywana poprawnie, jeśli występują problemy.

Postęp można śledzić za pomocą źródła zewnętrznego, takiego jak konto magazynu, baza danych lub pliki udostępnione. Utwórz logikę w swoim runbook, aby najpierw sprawdzić stan ostatniej wykonanej akcji. Następnie, na podstawie wyników sprawdzania, logika może pominąć lub kontynuować określone zadania w runbook.

## <a name="prevent-concurrent-jobs"></a>Zapobieganie współbieżnych zadaniach

Niektóre elementów Runbook zachowują się dziwne, jeśli są uruchamiane w wielu zadaniach w tym samym czasie. W takim przypadku ważne jest, aby element Runbook zaimplementował logikę w celu określenia, czy istnieje już uruchomione zadanie. Oto podstawowy przykład.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Obsługa błędów przejściowych w skrypcie zależnym od czasu

Twoje podręczniki Runbook muszą być niezawodne i mogą obsługiwały [błędy](automation-runbook-execution.md#errors), w tym błędy przejściowe, które mogą powodować ich ponowne uruchomienie lub niepowodzenie. Jeśli runbook ulegnie awarii, Azure Automation próbę jego ponownego uruchomienia.

Jeśli twój runbook zwykle działa w ramach ograniczenia czasowego, zaimekuj logikę implementacji skryptu, aby sprawdzić czas wykonywania. To sprawdzenie zapewnia uruchamianie operacji, takich jak uruchamianie, zamykanie lub skalowanie w zewnątrz tylko w określonych godzinach.

> [!NOTE]
> Czas lokalny w procesie piaskownicy platformy Azure jest ustawiony na czas UTC. W obliczeniach daty i godzin w twoich podręcznikach runbook należy wziąć pod uwagę ten fakt.

## <a name="work-with-multiple-subscriptions"></a>Praca z wieloma subskrypcjami

Twój podręcznik Runbook musi mieć możliwość pracy z [subskrypcjami](automation-runbook-execution.md#subscriptions). Aby na przykład obsłużyć wiele subskrypcji, ten runbook używa polecenia cmdlet [Disable-AzContextAutosave.](/powershell/module/Az.Accounts/Disable-AzContextAutosave) To polecenie cmdlet gwarantuje, że kontekst uwierzytelniania nie zostanie pobrany z innego runbook uruchomionego w tej samej piaskownicy. W ramach tego polecenia runbook jest również używane polecenie cmdlet do pobrania kontekstu bieżącej sesji i przypisania `Get-AzContext` go do zmiennej `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Praca ze skryptem niestandardowym

> [!NOTE]
> Zazwyczaj nie można uruchamiać skryptów niestandardowych i runbook na hoście z zainstalowanym agentem usługi Log Analytics.

Aby użyć skryptu niestandardowego:

1. Utwórz konto usługi Automation i uzyskaj rolę [Współautor.](automation-role-based-access-control.md)
2. [Połącz konto z obszarem roboczym platformy Azure.](../security-center/security-center-enable-data-collection.md)
3. Włącz [hybrydowy proces roboczy elementu Runbook,](automation-hybrid-runbook-worker.md) [Update Management](./update-management/overview.md)lub inną funkcję automatyzacji. 
4. Jeśli masz maszynę z systemem Linux, potrzebujesz dużych uprawnień. Zaloguj się, [aby wyłączyć sprawdzanie podpisu.](automation-linux-hrw-install.md#turn-off-signature-validation)

## <a name="test-a-runbook"></a>Testowanie elementu Runbook

Podczas testowania runbook wykonywana jest wersja [robocza](#publish-a-runbook) i wszystkie wykonywane przez niego akcje. Historia zadań nie jest tworzona, ale [strumienie](automation-runbook-output-and-messages.md#use-the-output-stream) danych wyjściowych oraz [ostrzeżeń](automation-runbook-output-and-messages.md#working-with-message-streams) i błędów są wyświetlane w okienku Dane wyjściowe testu. Komunikaty do [owego strumienia są wyświetlane](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) w okienku Dane wyjściowe tylko wtedy, gdy zmienna [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) jest ustawiona na `Continue` wartość .

Mimo że wersja robocza jest uruchamiana, nadal jest on wykonywany normalnie i wykonuje wszystkie akcje względem zasobów w środowisku. Z tego powodu należy testować tylko te zasoby, które nie są produkcyjne.

Procedura testowania każdego [typu runbook](automation-runbook-types.md) jest taka sama. Nie ma różnicy w testowaniu między edytorem tekstowym a edytorem graficznym w Azure Portal.

1. Otwórz wersję roboczą elementów Runbook w edytorze [tekstowym](automation-edit-textual-runbook.md) lub [edytorze graficznym](automation-graphical-authoring-intro.md).
1. Kliknij **przycisk Testuj,** aby otworzyć stronę Test.
1. Jeśli runbook ma parametry, są one wyświetlane w okienku po lewej stronie, w którym można podać wartości, które mają być używane podczas testu.
1. Jeśli chcesz uruchomić test dla hybrydowego procesu  roboczego  [runbook,](automation-hybrid-runbook-worker.md)zmień ustawienia uruchamiania na Hybrydowy proces roboczy i wybierz nazwę grupy docelowej.  W przeciwnym razie zachowaj domyślną **platformę Azure,** aby uruchomić test w chmurze.
1. Kliknij **przycisk Start,** aby rozpocząć test.
1. Możesz użyć przycisków w okienku Dane wyjściowe, aby zatrzymać lub wstrzymać przepływ pracy programu [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub graficzny element [Runbook](automation-runbook-types.md#graphical-runbooks) podczas jego testów. Gdy element Runbook zostanie wstrzymany, przed jego wstrzymaniem zostanie ukończone bieżące działanie. Po wstrzymaniu elementu Runbook można go zatrzymać lub uruchomić ponownie.
1. Sprawdź dane wyjściowe z runbook w okienku Dane wyjściowe.

## <a name="publish-a-runbook"></a>Publikowanie podręcznika Runbook

Podczas tworzenia lub importowania nowego runbook należy opublikować go przed jego uruchomieniem. Każdy runbook w Azure Automation ma wersję roboczą i opublikowaną. Tylko wersję opublikowaną można uruchomić i tylko wersję roboczą można zmienić. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Po opublikowaniu wersji roboczej należy ją opublikować, nadpisując bieżącą opublikowaną wersję wersją roboczą.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikowanie runbook w Azure Portal

1. Otwórz ten runbook w Azure Portal.
2. Kliknij pozycję **Edytuj**.
3. Kliknij **pozycję Publikuj,** a następnie pozycję **Tak** w odpowiedzi na komunikat weryfikacji.

### <a name="publish-a-runbook-using-powershell"></a>Publikowanie runbook przy użyciu programu PowerShell

Użyj polecenia cmdlet [Publish-AzAutomationRunbook,](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) aby opublikować swój runbook. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Planowanie użycia elementu Runbook w witrynie Azure Portal

Po opublikowaniu swojego runbook możesz zaplanować jego działanie:

1. Otwórz ten runbook w Azure Portal.
2. Wybierz **pozycję Harmonogramy w** obszarze **Zasoby.**
3. Wybierz **pozycję Dodaj harmonogram.**
4. W okienku Zaplanuj program Runbook wybierz **pozycję Połącz harmonogram z swoim programem Runbook.**
5. Wybierz **pozycję Utwórz nowy harmonogram** w okienku Harmonogram.
6. Wprowadź nazwę, opis i inne parametry w okienku Nowy harmonogram.
7. Po utworzeniu harmonogramu zaznacz go i kliknij przycisk **OK.** Powinien on być teraz połączony z twoim runbookem.
8. Poszukaj wiadomości e-mail w skrzynce pocztowej z powiadomieniem o stanie runbook.

## <a name="obtain-job-statuses"></a>Uzyskiwanie stanu zadania

### <a name="view-statuses-in-the-azure-portal"></a>Wyświetlanie stanów w Azure Portal

Szczegóły dotyczące obsługi zadań w Azure Automation znajdują się w te [tematach.](automation-runbook-execution.md#jobs) Gdy wszystko będzie gotowe do zobaczenia zadań runbook, użyj Azure Portal i uzyskaj dostęp do konta usługi Automation. Po prawej stronie w statystyce zadań można wyświetlić podsumowanie wszystkich zadań runbook.

![Kafelek Statystyka zadania](./media/manage-runbooks/automation-account-job-status-summary.png)

Podsumowanie zawiera liczbę i graficzną reprezentację stanu zadania dla każdego wykonanego zadania.

Kliknięcie kafelka przedstawia stronę Zadania, która zawiera podsumowaną listę wszystkich wykonanych zadań. Na tej stronie przedstawiono stan, nazwę runbook, czas rozpoczęcia i czas ukończenia każdego zadania.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Zrzut ekranu przedstawiający stronę Zadania.":::

Listę zadań można filtrować, wybierając pozycję **Filtruj zadania.** Odfiltruj określony elementów Runbook, stan zadania lub wybór z listy rozwijanej i podaj zakres czasu wyszukiwania.

![Filtrowanie stanu zadania](./media/manage-runbooks/automation-account-jobs-filter.png)

Możesz też wyświetlić szczegóły podsumowania zadania dla określonego podręcznika Runbook, wybierając go na stronie Runbook na koncie usługi Automation, a następnie wybierając pozycję **Zadania.** Ta akcja przedstawia stronę Zadania. W tym miejscu możesz kliknąć rekord zadania, aby wyświetlić jego szczegóły i dane wyjściowe.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Zrzut ekranu przedstawiający stronę Zadania z wyróżnionym przyciskiem Błędy.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Pobieranie stanów zadań przy użyciu programu PowerShell

Użyj polecenia cmdlet [Get-AzAutomationJob,](/powershell/module/Az.Automation/Get-AzAutomationJob) aby pobrać zadania utworzone dla danego zadania i szczegóły określonego zadania. Uruchomienie runbook przy użyciu `Start-AzAutomationRunbook` funkcji zwraca wynikowe zadanie. Użyj [get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) do pobrania danych wyjściowych zadania.

Poniższy przykład pobiera ostatnie zadanie dla przykładowego runbook i wyświetla jego stan, wartości podane dla parametrów i dane wyjściowe zadania.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdy rekord. Jeśli występuje [wyjątek dla](automation-runbook-execution.md#exceptions) jednego z rekordów, skrypt zapisuje wyjątek zamiast wartości . To zachowanie jest przydatne, ponieważ wyjątki mogą dostarczać dodatkowe informacje, które mogą nie być rejestrowane normalnie podczas danych wyjściowych.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat zarządzania runbook, zobacz Runbook execution in Azure Automation (Wykonywanie [runbook w programie Azure Automation).](automation-runbook-execution.md)
* Aby przygotować podręcznik Runbook programu PowerShell, zobacz [Edit textual runbook in Azure Automation](automation-edit-textual-runbook.md)(Edytowanie tekstowych Azure Automation ).
* Aby rozwiązać problemy z wykonywaniem runbook, zobacz [Rozwiązywanie problemów z runbookami.](troubleshoot/runbooks.md)
