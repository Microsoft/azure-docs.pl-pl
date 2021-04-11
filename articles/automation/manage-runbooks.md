---
title: Zarządzanie elementami Runbook w Azure Automation
description: W tym artykule opisano sposób zarządzania elementami Runbook w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: 7eb576a3d084630ebe6020b57814f12687dc9bd9
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168624"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie elementami Runbook w Azure Automation

Element Runbook można dodać do Azure Automation, tworząc nowy lub importując istniejący z pliku lub z [galerii elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje dotyczące zarządzania elementem Runbook zaimportowanym z pliku. Możesz znaleźć wszystkie szczegóły dotyczące uzyskiwania dostępu do elementów Runbook i modułów z społeczności w [elementach Runbook i galeriach modułów dla Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Tworzenie elementu runbook

Utwórz nowy element Runbook w Azure Automation przy użyciu Azure Portal lub środowiska Windows PowerShell. Po utworzeniu elementu Runbook można go edytować przy użyciu informacji w temacie:

* [Edytuj tekstowy element Runbook w Azure Automation](automation-edit-textual-runbook.md)
* [Poznaj kluczowe koncepcje przepływu pracy programu Windows PowerShell dotyczące elementów Runbook usługi Automation](automation-powershell-workflow.md)
* [Zarządzanie pakietami Python 2 w Azure Automation](python-packages.md)
* [Zarządzanie pakietami Python 3 (wersja zapoznawcza) w Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Tworzenie elementu Runbook w Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Kliknij pozycję **Utwórz element Runbook**.
4. Wprowadź nazwę elementu Runbook i wybierz jego [Typ](automation-runbook-types.md). Nazwa elementu Runbook musi rozpoczynać się od litery i może zawierać litery, cyfry, podkreślenia i łączniki.
5. Kliknij przycisk **Utwórz** , aby utworzyć element Runbook i otworzyć Edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie elementu Runbook za pomocą programu PowerShell

Użyj polecenia cmdlet [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) , aby utworzyć pusty element Runbook. Użyj `Type` parametru, aby określić jeden z typów elementów Runbook zdefiniowanych dla `New-AzAutomationRunbook` .

Poniższy przykład przedstawia sposób tworzenia nowego pustego elementu Runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importowanie elementu Runbook

Możesz zaimportować skrypt programu PowerShell lub przepływu pracy programu PowerShell (**. ps1**), graficzny element Runbook (**. graphrunbook**) lub skrypt python 2 lub Python 3 (**. PR**), aby utworzyć własny element Runbook. Należy określić [Typ elementu Runbook](automation-runbook-types.md) , który jest tworzony podczas importowania, biorąc pod uwagę poniższe zagadnienia.

* Plik **. ps1** , który nie zawiera przepływu pracy, można zaimportować do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Jeśli zaimportujesz go do elementu Runbook przepływu pracy programu PowerShell, zostanie on przekonwertowany na przepływ pracy. W takim przypadku Komentarze są zawarte w elemencie Runbook w celu opisania wprowadzonych zmian.

* Można zaimportować tylko plik **. ps1** zawierający przepływ pracy programu PowerShell do [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Jeśli plik zawiera wiele przepływów pracy programu PowerShell, importowanie nie powiedzie się. Każdy przepływ pracy należy zapisać do własnego pliku i zaimportować osobno.

* Nie należy importować pliku **. ps1** zawierającego przepływ pracy programu PowerShell do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), ponieważ aparat skryptu programu PowerShell nie może go rozpoznać.

* Zaimportuj plik **. graphrunbook** do nowego [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importuj element Runbook z Azure Portal

Aby zaimportować plik skryptu do Azure Automation, można użyć poniższej procedury.

> [!NOTE]
> Plik **. ps1** można zaimportować tylko do elementu Runbook przepływu pracy programu PowerShell przy użyciu portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Kliknij pozycję **Importuj element Runbook**.
4. Kliknij pozycję **plik Runbook** i wybierz plik do zaimportowania.
5. Jeśli pole **Nazwa** jest włączone, istnieje możliwość zmiany nazwy elementu Runbook. Nazwa musi zaczynać się od litery i może zawierać litery, cyfry, podkreślenia i kreski.
6. [Typ elementu Runbook](automation-runbook-types.md) jest wybierany automatycznie, ale można zmienić typ po wprowadzeniu odpowiednich ograniczeń do konta.
7. Kliknij pozycję **Utwórz**. Nowy element Runbook zostanie wyświetlony na liście elementów Runbook dla konta usługi Automation.
8. Aby można było uruchomić [element Runbook,](#publish-a-runbook) należy go opublikować.

> [!NOTE]
> Po zaimportowaniu graficznego elementu Runbook można go przekonwertować na inny typ. Nie można jednak skonwertować graficznego elementu Runbook do tekstowego elementu Runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Importowanie elementu Runbook za pomocą programu Windows PowerShell

Użyj polecenia cmdlet [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) , aby zaimportować plik skryptu jako roboczy element Runbook. Jeśli element Runbook już istnieje, importowanie nie powiedzie się, o ile nie zostanie użyty `Force` parametr z poleceniem cmdlet.

Poniższy przykład pokazuje, jak zaimportować plik skryptu do elementu Runbook.

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

Jeśli element Runbook tworzy [zasób](automation-runbook-execution.md#resources), skrypt powinien sprawdzić, czy zasób już istnieje, przed podjęciem próby jego utworzenia. Oto przykład podstawowy.

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

## <a name="retrieve-details-from-activity-log"></a>Pobierz szczegóły z dziennika aktywności

Możesz pobrać szczegóły elementu Runbook, takie jak osoba lub konto, które uruchomiło element Runbook, z [dziennika aktywności](automation-runbook-execution.md#activity-logging) dla konta usługi Automation. Poniższy przykład programu PowerShell zawiera ostatniego użytkownika do uruchomienia określonego elementu Runbook.

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

Dobrym sposobem jest samodzielne tworzenie elementów Runbook w charakterze modularnym, z logiką, którą można ponownie wykorzystać i ponownie uruchomić. Śledzenie postępu w elemencie Runbook zapewnia, że logika elementu Runbook jest wykonywana prawidłowo w przypadku wystąpienia problemów.

Postęp elementu Runbook można śledzić przy użyciu zewnętrznego źródła, takiego jak konto magazynu, baza danych lub udostępnione pliki. Utwórz logikę w elemencie Runbook, aby najpierw sprawdzić stan ostatniej podjętej akcji. Następnie w oparciu o wyniki kontroli logika może pominąć lub kontynuować określone zadania w elemencie Runbook.

## <a name="prevent-concurrent-jobs"></a>Uniemożliwiaj współbieżne zadania

Niektóre elementy Runbook działają dziwnie, jeśli działają w wielu zadaniach w tym samym czasie. W takim przypadku ważne jest, aby element Runbook zaimplementował logikę w celu ustalenia, czy istnieje już uruchomione zadanie. Oto przykład podstawowy.

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

Elementy Runbook muszą być niezawodne i mogą obsługiwać [Błędy](automation-runbook-execution.md#errors), w tym błędy przejściowe, które mogą spowodować ponowne uruchomienie lub niepowodzenie. Jeśli element Runbook ulegnie awarii, Azure Automation ponowienia próby.

Jeśli element Runbook jest zwykle uruchamiany w ramach ograniczenia czasu, należy wdrożyć logikę implementacji skryptu, aby sprawdzić czas wykonywania. To sprawdzenie gwarantuje uruchomienie operacji, takich jak uruchamianie, zamykanie lub skalowanie w poziomie tylko w określonych godzinach.

> [!NOTE]
> Czas lokalny w procesie piaskownicy platformy Azure jest ustawiony na czas UTC. Obliczenia dla daty i godziny w elementach Runbook muszą uwzględniać ten fakt.

## <a name="work-with-multiple-subscriptions"></a>Praca z wieloma subskrypcjami

Element Runbook musi mieć możliwość pracy z [subskrypcjami](automation-runbook-execution.md#subscriptions). Aby na przykład obsłużyć wiele subskrypcji, element Runbook używa polecenia cmdlet [disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) . To polecenie cmdlet zapewnia, że kontekst uwierzytelniania nie zostanie pobrany z innego elementu Runbook działającego w tej samej piaskownicy. Element Runbook używa również `Get-AzContext` polecenia cmdlet w celu pobrania kontekstu bieżącej sesji i przypisania go do zmiennej `$AzureContext` .

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

## <a name="work-with-a-custom-script"></a>Pracuj ze skryptem niestandardowym

> [!NOTE]
> Zazwyczaj nie można uruchamiać skryptów niestandardowych i elementów Runbook na hoście z zainstalowanym agentem Log Analytics.

Aby użyć skryptu niestandardowego:

1. Utwórz konto usługi Automation i uzyskaj [rolę współautor](automation-role-based-access-control.md).
2. [Połącz konto z obszarem roboczym platformy Azure](../security-center/security-center-enable-data-collection.md).
3. Włącz [hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md), [Update Management](./update-management/overview.md)lub inną funkcję automatyzacji. 
4. Jeśli na komputerze z systemem Linux potrzebujesz wysokiego poziomu uprawnień. Zaloguj się, aby wyłączyć [sprawdzanie podpisu](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testowanie elementu Runbook

Podczas testowania elementu Runbook jest wykonywana [wersja robocza](#publish-a-runbook) i wszystkie wykonywane działania. Nie utworzono historii zadań, ale strumienie [wyjściowe](automation-runbook-output-and-messages.md#use-the-output-stream) i [ostrzegawcze i błędów](automation-runbook-output-and-messages.md#working-with-message-streams) są wyświetlane w okienku danych wyjściowych testu. Komunikaty do [pełnego strumienia](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) są wyświetlane w okienku danych wyjściowych tylko wtedy, gdy zmienna [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) jest ustawiona na `Continue` .

Mimo że wersja robocza jest uruchamiana, element Runbook nadal wykonuje się normalnie i wykonuje wszelkie akcje dotyczące zasobów w środowisku. Z tego powodu należy przetestować elementy Runbook wyłącznie dla zasobów nieprodukcyjnych.

Procedura testowania każdego [typu elementu Runbook](automation-runbook-types.md) jest taka sama. Nie ma różnicy w testowaniu między edytorem tekstu i edytorem graficznym w Azure Portal.

1. Otwórz wersję roboczą elementu Runbook w [Edytorze](automation-edit-textual-runbook.md) tekstu lub [edytorze graficznym](automation-graphical-authoring-intro.md).
1. Kliknij przycisk **Testuj** , aby otworzyć stronę test.
1. Jeśli element Runbook ma parametry, są one wyświetlane w okienku po lewej stronie, gdzie można podać wartości do użycia w teście.
1. Jeśli chcesz uruchomić test w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md), Zmień **Parametry uruchomieniowe** na **hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie Zachowaj domyślną **platformę Azure** , aby uruchomić test w chmurze.
1. Kliknij przycisk **Rozpocznij** , aby rozpocząć test.
1. Za pomocą przycisków w okienku danych wyjściowych można zatrzymać lub wstrzymać [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficzny](automation-runbook-types.md#graphical-runbooks) element Runbook podczas testowania. Gdy element Runbook zostanie wstrzymany, przed jego wstrzymaniem zostanie ukończone bieżące działanie. Po wstrzymaniu elementu Runbook można go zatrzymać lub uruchomić ponownie.
1. Sprawdź dane wyjściowe z elementu Runbook w okienku danych wyjściowych.

## <a name="publish-a-runbook"></a>Publikowanie elementu Runbook

Podczas tworzenia lub importowania nowego elementu Runbook należy go opublikować przed jego uruchomieniem. Każdy element Runbook w Azure Automation ma wersję roboczą i opublikowaną wersję. Tylko wersję opublikowaną można uruchomić i tylko wersję roboczą można zmienić. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinna zostać udostępniona, należy ją opublikować, zastępując bieżącą opublikowaną wersję wersją roboczą.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikowanie elementu Runbook w Azure Portal

1. Otwórz element Runbook w Azure Portal.
2. Kliknij pozycję **Edytuj**.
3. Kliknij przycisk **Opublikuj** , a następnie pozycję **tak** w odpowiedzi na komunikat weryfikacyjny.

### <a name="publish-a-runbook-using-powershell"></a>Publikowanie elementu Runbook przy użyciu programu PowerShell

Użyj polecenia cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) , aby opublikować element Runbook. 

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

Po opublikowaniu elementu Runbook można zaplanować jego działanie:

1. Otwórz element Runbook w Azure Portal.
2. Wybierz pozycję **harmonogramy** w obszarze **zasoby**.
3. Wybierz pozycję **Dodaj harmonogram**.
4. W okienku harmonogram elementu Runbook wybierz pozycję **Połącz harmonogram z elementem Runbook**.
5. Wybierz pozycję **Utwórz nowy harmonogram** w okienku harmonogram.
6. Wprowadź nazwę, opis i inne parametry w okienku Nowy harmonogram.
7. Po utworzeniu harmonogramu zaznacz go i kliknij przycisk **OK**. Powinien być teraz połączony z elementem Runbook.
8. Poszukaj wiadomości e-mail w skrzynce pocztowej, aby powiadomić Cię o stanie elementu Runbook.

## <a name="obtain-job-statuses"></a>Uzyskaj Stany zadań

### <a name="view-statuses-in-the-azure-portal"></a>Wyświetlanie stanów w Azure Portal

Szczegółowe informacje o obsłudze zadań w Azure Automation są dostępne w obszarze [zadania](automation-runbook-execution.md#jobs). Gdy wszystko będzie gotowe do wyświetlenia zadań elementu Runbook, użyj Azure Portal i uzyskaj dostęp do konta usługi Automation. Po prawej stronie można zobaczyć podsumowanie wszystkich zadań elementu Runbook w **statystykach zadań**.

![Kafelek statystyk zadania](./media/manage-runbooks/automation-account-job-status-summary.png)

Podsumowanie wyświetla liczbę i graficzną reprezentację stanu zadania dla każdego wykonanego zadania.

Kliknięcie kafelka przedstawia stronę zadania, która zawiera skróconą listę wszystkich wykonanych zadań. Na tej stronie jest wyświetlany stan, nazwa elementu Runbook, czas rozpoczęcia i czas zakończenia każdego zadania.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Zrzut ekranu strony zadania.":::

Listę zadań można filtrować, wybierając pozycję **Filtruj zadania**. Odfiltruj określony element Runbook, stan zadania lub wybór z listy rozwijanej i podaj zakres czasu dla wyszukiwania.

![Stan zadania filtrowania](./media/manage-runbooks/automation-account-jobs-filter.png)

Możesz też wyświetlić szczegóły podsumowania zadania dla określonego elementu Runbook, wybierając ten element Runbook ze strony elementów Runbook na koncie usługi Automation, a następnie wybierając pozycję **zadania**. Ta akcja przedstawia stronę zadania. W tym miejscu możesz kliknąć rekord zadania, aby wyświetlić jego szczegóły i dane wyjściowe.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Zrzut ekranu strony zadania z wyróżnionym przyciskiem błędy.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Pobieranie stanów zadań przy użyciu programu PowerShell

Użyj polecenia cmdlet [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) , aby pobrać zadania utworzone dla elementu Runbook i szczegółowe informacje o konkretnym zadaniu. Po uruchomieniu elementu Runbook za pomocą usługi `Start-AzAutomationRunbook` zwraca ono wynikowe zadanie. Użyj polecenia [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) , aby pobrać dane wyjściowe zadania.

Poniższy przykład pobiera ostatnie zadanie dla przykładowego elementu Runbook i wyświetla jego stan, wartości podane dla parametrów elementu Runbook oraz dane wyjściowe zadania.

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

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdy rekord. Jeśli istnieje [wyjątek](automation-runbook-execution.md#exceptions) dla jednego z rekordów, skrypt zapisuje wyjątek zamiast wartości. To zachowanie jest przydatne, ponieważ wyjątki mogą zawierać dodatkowe informacje, które mogą nie być rejestrowane normalnie podczas danych wyjściowych.

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

* Aby dowiedzieć się więcej o zarządzaniu elementami Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).
* Aby przygotować element Runbook programu PowerShell, zobacz [Edytowanie tekstowych elementów Runbook w Azure Automation](automation-edit-textual-runbook.md).
* Aby rozwiązać problemy z wykonywaniem elementu Runbook, zobacz [Rozwiązywanie](troubleshoot/runbooks.md)problemów z elementem Runbook.
