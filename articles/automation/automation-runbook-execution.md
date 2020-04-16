---
title: Wykonanie uruchomieniu w usłudze Azure Automation
description: W tym artykule opisano szczegóły dotyczące sposobu przetwarzania uruchomieniu księgi uruchomieniu w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: a7dd9de1f2ae41b20d94cf31de48e92fbb71ca6a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405640"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonanie uruchomieniu w usłudze Azure Automation

Automatyzacja procesów w usłudze Azure Automation umożliwia tworzenie i zarządzanie programami PowerShell, przepływ pracy programu PowerShell i uruchomieniu elementami. Aby uzyskać szczegółowe informacje, zobacz [Elementy runbook usługi Azure Automation](automation-runbook-types.md). 

Automatyzacja wykonuje elementy runbook na podstawie logiki zdefiniowanej wewnątrz nich. Jeśli projekt runbook zostanie przerwany, zostanie ponownie uruchomiony na początku. To zachowanie wymaga zapisu elementów runbook, które obsługują są ponownie uruchamiane, jeśli wystąpią przejściowe problemy.

Uruchamianie uruchomieniu w usłudze Azure Automation tworzy zadanie, które jest wystąpieniem pojedynczego wykonywania ego księgi runbook. Każde zadanie uzyskuje dostęp do zasobów platformy Azure, nawiązując połączenie z subskrypcją platformy Azure. Zadanie może uzyskać dostęp do zasobów w centrum danych tylko wtedy, gdy te zasoby są dostępne z chmury publicznej.

Usługa Azure Automation przypisuje pracownika do uruchamiania każdego zadania podczas wykonywania uruchomieniu. wiązków. Podczas gdy pracownicy są współużytkowane przez wiele kont platformy Azure, zadania z różnych kont automatyzacji są odizolowane od siebie. Nie można kontrolować, który pracownik usługi żądania pracy.

Podczas wyświetlania listy śmięty w witrynie Azure portal, pokazuje stan każdego zadania, które zostało uruchomione dla każdego uruchomieniu.. . Usługa Azure Automation przechowuje dzienniki zadań przez maksymalnie 30 dni.

Na poniższym diagramie przedstawiono cykl życia zadania elementów runbook dla [elementów runbook programu PowerShell,](automation-runbook-types.md#powershell-runbooks) [elementów runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)i [elementów runbook graficznych.](automation-runbook-types.md#graphical-runbooks)

![Stany zadań — przepływ pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="where-to-run-your-runbooks"></a>Gdzie uruchomić runbooks

Elementy runbook w usłudze Azure Automation można uruchamiać w obszarze izolowanym platformy Azure lub [hybrydowym usłudze Runbook Worker.](automation-hybrid-runbook-worker.md) Gdy elementami runbook są przeznaczone do uwierzytelniania i uruchamiania z zasobami na platformie Azure, są one uruchamiane w obszarze izolowanym platformy Azure, który jest środowiskiem udostępnionym, którego można używać w wielu zadaniach. Zadania korzystające z tego samego piaskownicy są powiązane ograniczeniami zasobów w piaskownicy.

>[!NOTE]
>Środowisko piaskownicy platformy Azure nie obsługuje operacji interaktywnych. Wymaga również użycia lokalnych plików MOF dla śmigieł, które sprawiają, że wywołania Win32.

Hybrydowego procesu roboczego żyli można używać do uruchamiania śmigieł bezpośrednio na komputerze, na którym działa rola i przeciwko lokalnym zasobom w środowisku. Usługa Azure Automation przechowuje i zarządza systemami runbook, a następnie dostarcza je do jednego lub więcej przypisanych komputerów.

W poniższej tabeli wymieniono niektóre zadania wykonywania elementów runbook z zalecanym środowiskiem wykonywania wymienionym dla każdego.

|Zadanie|Zalecenie|Uwagi|
|---|---|---|
|Integracja z zasobami platformy Azure|Piaskownica platformy Azure|Uwierzytelnianie hostowane na platformie Azure jest prostsze. Jeśli używasz hybrydowego procesu roboczego życiornika na maszynie Wirtualnej platformy Azure, możesz użyć [tożsamości zarządzanych dla zasobów platformy Azure.](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Uzyskiwanie optymalnej wydajności w celu zarządzania zasobami platformy Azure|Piaskownica platformy Azure|Skrypt jest uruchamiany w tym samym środowisku, które ma mniejsze opóźnienia.|
|Minimalizowanie kosztów operacyjnych|Piaskownica platformy Azure|Nie ma żadnych narzutów obliczeniowych i nie ma potrzeby maszyny Wirtualnej.|
|Wykonywanie długotrwałego skryptu|Hybrydowy proces roboczy elementu Runbook|Piaskowce platformy Azure mają [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interakcja z usługami lokalnymi|Hybrydowy proces roboczy elementu Runbook|Może bezpośrednio uzyskiwać dostęp do komputera-hosta lub zasobów w innych środowiskach chmury lub w środowisku lokalnym. |
|Wymagaj oprogramowania i plików wykonywalnych innych firm|Hybrydowy proces roboczy elementu Runbook|Zarządzasz systemem operacyjnym i możesz zainstalować oprogramowanie.|
|Monitorowanie pliku lub folderu za pomocą systemu runbook|Hybrydowy proces roboczy elementu Runbook|Użyj [zadania czujki](automation-watchers-tutorial.md) w hybrydowym uzywce uruchomieniu. żytnie.|
|Uruchamianie skryptu intensywnie korzystającego z zasobów|Hybrydowy proces roboczy elementu Runbook| Piaskowce platformy Azure mają [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Używaj modułów o określonych wymaganiach| Hybrydowy proces roboczy elementu Runbook|Przykłady to:</br> WinSCP - zależność od pliku winscp.exe </br> Administracja iis — zależność od włączania iis lub zarządzania nimi.|
|Instalowanie modułu z instalatorem|Hybrydowy proces roboczy elementu Runbook|Moduły do piaskownicy muszą obsługiwać kopiowanie.|
|Użyj śmięty lub modułów, które wymagają wersji .NET Framework innej niż wersja 4.7.2|Hybrydowy proces roboczy elementu Runbook|Piaskownice automatyzacji obsługują platformę .NET Framework 4.7.2, a uaktualnienie do innej wersji nie jest obsługiwane.|
|Uruchamianie skryptów wymagających podniesienia uprawnień|Hybrydowy proces roboczy elementu Runbook|Piaskowce nie zezwalają na elewację. Za pomocą hybrydowego procesu roboczego żyjącego można wyłączyć funkcji Kontrola konta użytkownika i używać [polecenia Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) podczas uruchamiania polecenia wymagającego podniesienia uprawnień.|
|Uruchamianie skryptów wymagających dostępu do instrumentacji zarządzania windowsem (WMI)|Hybrydowy proces roboczy elementu Runbook|Zadania uruchomione w piaskownicach w chmurze nie mogą uzyskać dostępu do dostawcy usługi WMI. |

## <a name="runbook-behavior"></a>Zachowanie ekwidujnika

### <a name="creating-resources"></a>Tworzenie zasobów

Jeśli element runbook tworzy zasób, skrypt powinien sprawdzić, czy zasób już istnieje przed podjęciem próby jego utworzenia. Oto podstawowy przykład.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Obsługa skryptów zależnych od czasu

Twoje pliki runbook muszą być niezawodne i zdolne do obsługi błędów przejściowych, które mogą spowodować ich ponowne uruchomienie lub niepowodzenie. Jeśli system runbook nie powiedzie się, usługa Azure Automation ponawia jego ponowną prób.

Jeśli system runbook zwykle działa w ramach ograniczenia czasowego, należy logikę implementacji skryptu, aby sprawdzić czas wykonywania. Ten system sprawdzania gwarantuje uruchomienie operacji, takich jak uruchamianie, zamykanie systemu lub skalowanie w poziomie tylko w określonych godzinach.

> [!NOTE]
> Czas lokalny w procesie piaskownicy platformy Azure jest ustawiony na UTC. Obliczenia daty i godziny w yjsce muszą uwzględniać ten fakt.

### <a name="tracking-progress"></a>Śledzenie postępu

Dobrą praktyką jest tworzenie elementów runbook, które mają charakter modułowy, z logiką, którą można łatwo ponownie uruchomić i ponownie uruchomić. Śledzenie postępu w uruchomieniu jest dobrym sposobem, aby upewnić się, że logika runbook jest wykonywana poprawnie, jeśli występują problemy. Postęp y w żyłaku można śledzić przy użyciu zewnętrznego źródła, takiego jak konto magazynu, baza danych lub pliki udostępnione. Można utworzyć logikę w bieście runbook, aby najpierw sprawdzić stan ostatniej wykonanej akcji. Następnie, na podstawie wyniku kontroli, logika można pominąć lub kontynuować określone zadania w uruchomieniu. chyli.

### <a name="preventing-concurrent-jobs"></a>Zapobieganie równoczesnych zadań

Niektóre programy runbook zachowują się dziwnie, jeśli działają w wielu zadaniach w tym samym czasie. W takim przypadku ważne jest, aby system runbook zaimplementować logikę, aby ustalić, czy istnieje już uruchomione zadanie. Oto podstawowy przykład.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Praca z wieloma subskrypcjami

Aby poradzić sobie z wieloma subskrypcjami, element runbook musi używać polecenia cmdlet [Disable-AzContextAutosave.](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) To polecenie cmdlet zapewnia, że kontekst uwierzytelniania nie jest pobierany z innego zestawu runbook uruchomionego w tym samym piaskownicy. Projekt runbook używa`AzContext` również parametru na poleceniach cmdlet modułu Az i przekazuje go w odpowiednim kontekście.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Obsługa wyjątków

W tej sekcji opisano niektóre sposoby obsługi wyjątków lub sporadyczne problemy w ystawy runbook.

#### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) zmienna określa, jak program PowerShell reaguje na błąd nie kończący. Błędy kończenie zawsze kończą się i `ErrorActionPreference`nie mają na nie wpływu .

Gdy używa zestawu `ErrorActionPreference`runbook, zwykle nie kończący `PathNotFound` się błąd, takich jak z [get-ChildItem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) polecenia cmdlet zatrzymuje runbook z ukończenia. W poniższym przykładzie `ErrorActionPreference`przedstawiono użycie pliku . Końcowe polecenie [zapisu i wyjścia](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) nigdy nie jest wykonywane, ponieważ skrypt zatrzymuje się.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Spróbuj złapać wreszcie

[Spróbuj Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) jest używany w skryptach programu PowerShell do obsługi błędów zakończenia. Skrypt można użyć tego mechanizmu do połowu określonych wyjątków lub wyjątków ogólnych. Instrukcja `catch` powinna służyć do śledzenia lub próby obsługi błędów. Poniższy przykład próbuje pobrać plik, który nie istnieje. Przechwytuje wyjątek `System.Net.WebException` i zwraca ostatnią wartość dla każdego innego wyjątku.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) może służyć do generowania błędu zakończenia. Ten mechanizm może być przydatne podczas definiowania własnej logiki w uruchomieniu. chyli. Jeśli skrypt spełnia kryterium, które powinno go `throw` zatrzymać, można użyć instrukcji, aby zatrzymać. W poniższym przykładzie użyto tej instrukcji do wyświetlenia parametru wymaganej funkcji.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Korzystanie z plików wykonywalnych lub procesów wywołujących

Programy runbook uruchamiane w obszarze izolowanym platformy Azure nie obsługują procesów wywoływania, takich jak pliki wykonywalne (pliki**exe)** lub podprocesy. Powodem tego jest, że piaskownica platformy Azure jest procesem udostępnionym uruchamianym w kontenerze, który może nie być w stanie uzyskać dostępu do wszystkich podstawowych interfejsów API. W przypadku scenariuszy wymagających oprogramowania innych firm lub wywołań podprocesów należy wykonać system runbook w [hybrydowym uzyciu roboczym.](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Dostęp do urządzeń i charakterystyk aplikacji

Zadania likrządu uruchamiane w piaskownicach platformy Azure nie mogą uzyskać dostępu do żadnych cech urządzenia ani aplikacji. Najczęstszym interfejsem API używanym do wykonywania zapytań o metryki wydajności w systemie Windows jest usługa WMI, przy czym niektóre typowe metryki to użycie pamięci i procesora CPU. Nie ma jednak znaczenia, jaki interfejs API jest używany, ponieważ zadania uruchomione w chmurze nie mogą uzyskać dostępu do implementacji firmy Microsoft w zakresie zarządzania przedsiębiorstwem opartym na sieci Web (WBEM). Platforma ta jest oparta na modelu wspólnych informacji (CIM), zapewniając standardy branżowe dotyczące definiowania charakterystyki urządzeń i aplikacji.

## <a name="handling-errors"></a>Obsługa błędów

Twoje programy runbook muszą być zdolne do obsługi błędów. Program PowerShell ma dwa typy błędów, kończące się i nie kończące. Błędy zakończenia zatrzymać wykonanie uruchomieniu, gdy wystąpią. Projekt runbook zatrzymuje się ze stanem zadania Nie powiodło się.

Błędy nieokazyjące umożliwiają kontynuowanie skryptu nawet po ich wystąpieniu. Przykładem błędu nieubiegającego się jest taki, który `Get-ChildItem` występuje, gdy element runbook używa polecenia cmdlet ze ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje do następnego folderu. Błąd w tym przypadku nie ustawia stanu zadania zestawu runbook na Niepowodzenie, a zadanie może nawet zostać ukończone. Aby wymusić zatrzymanie przez system runbook błędu nieubiegającego się, można użyć `ErrorAction Stop` polecenia cmdlet.

## <a name="handling-jobs"></a>Obsługa zadań

Można ponownie użyć środowiska wykonywania dla zadań z tego samego konta automatyzacji. Pojedynczy śmiętnik runbook może mieć wiele zadań uruchomionych w tym czasie. Im więcej zadań zostanie uruchomionych w tym samym czasie, tym częściej mogą być wysyłane do tej samej piaskownicy.

Zadania uruchomione w tym samym procesie piaskownicy mogą mieć na siebie nawzajem wpływ. Jednym z przykładów jest uruchomienie polecenia cmdlet [Disconnect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) Wykonanie tego polecenia cmdlet rozłącza każde zadanie zestawu runbook w procesie współdzielonego piaskownicy.

Zadania programu PowerShell uruchomione z uruchomieniu, który działa w obszarze izolowanym platformy Azure, mogą nie działać w trybie języka pełnego [programu PowerShell.](/powershell/module/microsoft.powershell.core/about/about_language_modes) Aby uzyskać więcej informacji na temat interakcji z zadaniami w usłudze Azure Automation, zobacz [Pobieranie stanu zadania za pomocą programu PowerShell.](#retrieving-job-status-using-powershell)

### <a name="job-statuses"></a>Stany stanowisk

W poniższej tabeli opisano stany, które są możliwe dla zadania.

| Stan | Opis |
|:--- |:--- |
| Zakończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Nie można skompilować graficznego lub programu PowerShell Workflow. Nie można uruchomić śmiętu skryptu programu PowerShell lub zadanie miało wyjątek. Zobacz [typy elementów runbook usługi Azure Automation](automation-runbook-types.md).|
| Nie powiodło się, oczekiwanie na zasoby |Zadanie nie powiodło się, ponieważ trzykrotnie osiągnął limit [sprawiedliwego udziału](#fair-share) i rozpoczęło się od tego samego punktu kontrolnego lub od początku wiązanego za każdym razem. |
| W kolejce |Zadanie oczekuje na zasoby na automation pracownika, aby stać się dostępne, dzięki czemu można go uruchomić. |
| Uruchamianie |Zadanie zostało przypisane do pracownika, a system go uruchamia. |
| Wznawianie |System wznawia zadanie po jego zawieszeniu. |
| Działanie |Zadanie jest uruchomione. |
| Uruchamianie, oczekiwanie na zasoby |Zadanie zostało wyładowane, ponieważ osiągnęło limit sprawiedliwego udziału. Zostanie wznowiona wkrótce z ostatniego punktu kontrolnego. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika przed jego ukończeniem. |
| Zatrzymywanie |System zatrzymuje zadanie. |
| Suspended |Dotyczy tylko [śmięty graficznych i uruchomieniu przepływów pracy programu PowerShell.](automation-runbook-types.md) Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie Runbook. Jeśli system runbook nie ma punktu kontrolnego, rozpoczyna się od początku. Jeśli ma punkt kontrolny, można uruchomić ponownie i wznowić z ostatniego punktu kontrolnego. System zawiesza element runbook tylko wtedy, gdy wystąpi wyjątek. Domyślnie zmienna jest ustawiona `ErrorActionPreference` na Kontynuuj, co oznacza, że zadanie jest nadal uruchomione z powodu błędu. Jeśli zmienna uprzywilejowana jest ustawiona na Zatrzymaj, zadanie zostanie wstrzymane w przypadku błędu.  |
| Zawieszanie |Dotyczy tylko [śmięty graficznych i uruchomieniu przepływów pracy programu PowerShell.](automation-runbook-types.md) System próbuje zawiesić zadanie na żądanie użytkownika. Przez zawieszeniem element Runbook musi dotrzeć do swojego następnego punktu kontrolnego. Jeśli przeszedł już ostatni punkt kontrolny, kończy się, zanim może zostać zawieszony. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Wyświetlanie stanu zadania w witrynie Azure portal

Podsumowanie stanu dla wszystkich zadań uruchomieniu lub przechodzenie szczegółów dotyczących określonego zadania umienia w witrynie Azure portal. Można również skonfigurować integrację z obszarem roboczym usługi Log Analytics, aby przesłać dalej stan zadania egonika i strumienie zadań. Aby uzyskać więcej informacji na temat integracji z dziennikami usługi Azure Monitor, zobacz [Przekazywanie stanu zadania i strumieni zadań z automatyzacji do dzienników usługi Azure Monitor.](automation-manage-send-joblogs-log-analytics.md)

Po prawej stronie wybranego konta automatyzacji można wyświetlić podsumowanie wszystkich zadań elementu runbook w kafelka **Statystyki zadań.**

![Kafelek Statystyka oferty pracy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ten kafelek wyświetla liczbę i graficzną reprezentację stanu zadania dla każdego wykonanego zadania.

Kliknięcie kafelka przedstawia stronę Zadania, która zawiera podsumowaną listę wszystkich wykonanych zadań. Na tej stronie jest wyświetlany stan, nazwa uruchomieniu, godzina rozpoczęcia i czas ukończenia każdego zadania.

![Strona Zadania konta automatyzacji](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Listę zadań można filtrować, wybierając pozycję **Filtruj zadania**. Filtruj określony element runbook, stan zadania lub wybór z listy rozwijanej i podaj zakres czasu wyszukiwania.

![Stan zadania filtrowania](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternatywnie można wyświetlić szczegóły podsumowania zadania dla określonego żyłak, wybierając ten projekt runbook ze strony Elementy runbook na koncie automatyzacji, a następnie wybierając kafelek **Zadania.** Ta akcja przedstawia zadania strony. W tym miejscu można kliknąć rekord zadania, aby wyświetlić jego szczegóły i dane wyjściowe.

![Strona Zadania konta automatyzacji](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Wyświetlanie podsumowania zadania

Opisane powyżej podsumowanie zadania umożliwia przeglądanie listy wszystkich zadań utworzonych dla określonego wiązka umienia i ich najnowszych stanów. Aby wyświetlić szczegółowe informacje i dane wyjściowe zadania, kliknij jego nazwę na liście. Szczegółowy widok zadania zawiera wartości parametrów runbook, które zostały dostarczone do tego zadania.

Następujące kroki służą do wyświetlania zadań elementu Runbook.

1. W witrynie Azure portal wybierz pozycję **Automatyzacja,** a następnie wybierz nazwę konta automatyzacji.
2. W centrum wybierz pozycję **Elementy runbook w** obszarze **Automatyzacja procesów**.
3. Na stronie Runbooks wybierz runbook z listy.
3. Na stronie wybranego elementu runbook kliknij kafelek **Zadania.**
4. Kliknij jedno z zadań na liście i wyświetl jego szczegóły i dane wyjściowe na stronie szczegółów zadania likemi.

### <a name="retrieving-job-status-using-powershell"></a>Pobieranie stanu zadania przy użyciu programu PowerShell

Polecenie cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) służy do pobierania zadań utworzonych dla kreślenia kreślącego i szczegółów określonego zadania. Jeśli uruchomisz system runbook z `Start-AzAutomationRunbook`programem PowerShell przy użyciu programu , zwraca wynikowe zadanie. Użyj [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) do pobierania danych wyjściowych zadania.

Poniższy przykład pobiera ostatnie zadanie dla przykładowego elementów runbook i wyświetla jego stan, wartości podane dla parametrów runbook i dane wyjściowe zadania.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdy rekord. Jeśli istnieje wyjątek dla jednego z rekordów, skrypt zapisuje wyjątek zamiast wartości. To zachowanie jest przydatne, ponieważ wyjątki mogą dostarczyć dodatkowych informacji, które mogą nie być rejestrowane normalnie podczas danych wyjściowych.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="getting-details-from-the-activity-log"></a>Uzyskiwanie szczegółowych informacji z dziennika aktywności

Szczegóły księgi runbook, takie jak osoba lub konto, które uruchomiły runbook, z dziennika aktywności dla konta automatyzacji. Poniższy przykład programu PowerShell zawiera ostatniego użytkownika do uruchomienia określonego elementów runbook.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Udostępnianie zasobów między licznikami runbook

Aby udostępnić zasoby wszystkim żyło łakom żyła w chmurze, usługa Azure Automation tymczasowo zwalnia lub zatrzymuje każde zadanie, które zostało uruchomione przez ponad trzy godziny. Zadania dla [uruchomieniu programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [uruchomieniu python](automation-runbook-types.md#python-runbooks) są zatrzymywane i nie uruchamiane ponownie, a stan zadania staje się zatrzymany.

W przypadku długotrwałych zadań zaleca się użycie hybrydowego procesu roboczego żyjącego. Hybrydowe środowiska runbook Workers nie są ograniczone przez sprawiedliwy udział i nie mają ograniczenia co do tego, jak długo można wykonać program runbook. Inne [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zadań dotyczą zarówno piaskownicy platformy Azure, jak i hybrydowych workers y liksu runbook. Hybrydowe procesy pracy wiązki uruchomieniu nie są ograniczone limitem 3-godzinnego sprawiedliwego udziału, ale należy opracować programy runbook do uruchamiania na pracownikach obsługujących ponowne uruchamianie z powodu nieoczekiwanych problemów z infrastrukturą lokalną.

Inną opcją jest optymalizacja śmiętu żyli przy użyciu śmięty podrzędnych. Na przykład elementu runbook może pętli za pośrednictwem tej samej funkcji na kilka zasobów, takich jak operacja bazy danych w kilku bazach danych. Tę funkcję można przenieść do [podrzędnego elementu runbook](automation-child-runbooks.md) i wywołać ją w yskusu za pomocą [elementu Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Podrzędne elementu runbook są wykonywane równolegle w oddzielnych procesach.

Użycie obrażeń umowczych podrzędnych zmniejsza całkowity czas ukończenia nadrzędnego umienia. Element runbook może użyć polecenia cmdlet [Get-AzAutomationJob,](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) aby sprawdzić stan zadania dla śmięty podrzędnego, jeśli nadal ma więcej operacji po zakończeniu dojrzewania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak pracować z systemem runbook, zobacz [Zarządzanie podręcznikami runbook w usłudze Azure Automation](manage-runbooks.md).
* Aby dowiedzieć się więcej na temat metod, które mogą służyć do uruchamiania uruchomieniu w usłudze Azure Automation, zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation.](automation-starting-a-runbook.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów referencyjnych i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
