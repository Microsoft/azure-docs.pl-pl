---
title: Poznaj przepływ pracy programu PowerShell dla Azure Automation
description: Ten artykuł zawiera informacje o różnicach między przepływem pracy programu PowerShell i programem PowerShell oraz pojęciach dotyczących automatyzacji runbook.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b3beb8b3eda4dfabf9240aa328a24d5f855689b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833512"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Poznaj przepływ pracy programu PowerShell dla Azure Automation

W programie Azure Automation są implementowane jako Windows PowerShell przepływy pracy, Windows PowerShell skrypty, które używają Windows Workflow Foundation. Przepływ pracy to sekwencja zaprogramowanych, połączonych ze sobą czynności służących do wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu czynności na wielu różnych urządzeniach albo w wielu węzłach zarządzanych. 

Przepływ pracy jest napisany przy użyciu Windows PowerShell składni i uruchomiony przez Windows PowerShell, ale jest przetwarzany przez Windows Workflow Foundation. Zalety przepływu pracy w stosunku do normalnego skryptu obejmują jednoczesną wydajność akcji dla wielu urządzeń i automatyczne odzyskiwanie po awarii. 

> [!NOTE]
> Skrypt przepływu pracy programu PowerShell jest bardzo podobny do skryptu Windows PowerShell, ale ma pewne istotne różnice, które mogą być mylące dla nowego użytkownika. W związku z tym zaleca się pisanie podręczników Runbook przy użyciu przepływu pracy programu PowerShell tylko wtedy, gdy konieczne jest użycie [punktów kontrolnych.](#use-checkpoints-in-a-workflow) 

Aby uzyskać szczegółowe informacje na temat tematów w tym artykule, zobacz Wprowadzenie with Windows PowerShell Workflow (Zarządzanie przepływem [pracy Windows PowerShell).](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))

## <a name="use-workflow-keyword"></a>Używanie słowa kluczowego przepływu pracy

Pierwszym krokiem konwersji skryptu programu PowerShell na przepływ pracy programu PowerShell jest dołączenie go do słowa `Workflow` kluczowego . Przepływ pracy rozpoczyna się od słowa `Workflow` kluczowego , po którym następuje treść skryptu ujęta w nawiasy klamrowe. Nazwa przepływu pracy jest zgodna ze słowem `Workflow` kluczowym , jak pokazano w następującej składni:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Nazwa przepływu pracy musi być dopasowana do nazwy runbook usługi Automation. Jeśli importowany jest podręcznik Runbook, nazwa pliku musi być dopasowana do nazwy przepływu pracy i kończyć się **na .ps1.**

Aby dodać parametry do przepływu pracy, użyj słowa kluczowego , tak jak w `Param` skrypcie.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Różnice między kodem przepływu pracy programu PowerShell i kodem skryptu programu PowerShell

Kod przepływu pracy programu PowerShell wygląda niemal tak samo jak kod skryptu programu PowerShell z wyjątkiem kilku znaczących zmian. W poniższych sekcjach opisano zmiany, które należy wprowadzić w skrypcie programu PowerShell, aby można było go uruchomić w przepływie pracy.

### <a name="activities"></a>Działania

Działanie to określone zadanie w przepływie pracy, które jest wykonywane w sekwencji. Podczas wykonywania przepływu pracy program Windows PowerShell Workflow automatycznie konwertuje wiele spośród poleceń cmdlet środowiska Windows PowerShell na działania. Po określeniu jednego z tych cmdlet w programie Runbook odpowiednie działanie jest uruchamiane przez Windows Workflow Foundation. 

Jeśli polecenie cmdlet nie ma odpowiadającego mu działania, program Windows PowerShell Workflow automatycznie uruchamia polecenie cmdlet w działaniu [InlineScript.](#use-inlinescript) Niektóre polecenia cmdlet są wykluczone i nie można ich używać w przepływie pracy, chyba że jawnie dołączysz je do bloku InlineScript. Aby uzyskać więcej informacji, zobacz Using Activities in Script Workflows (Używanie [działań w przepływach pracy skryptu).](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Zobacz [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Parametry pozyacyjne

Nie można używać parametrów pozyczkowych z działaniami i poleceniami cmdlet w przepływie pracy. W związku z tym należy użyć nazw parametrów. Rozważmy następujący kod, który pobiera wszystkie uruchomione usługi:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Jeśli spróbujesz uruchomić ten kod w przepływie pracy, zostanie wyświetlony komunikat, taki jak Aby rozwiązać ten problem, podaj nazwę parametru, jak w `Parameter set cannot be resolved using the specified named parameters.` poniższym przykładzie:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializowane obiekty

Obiekty w przepływach pracy są deserializowane, co oznacza, że ich właściwości są nadal dostępne, ale nie ich metody.  Rozważmy na przykład następujący kod programu PowerShell, który zatrzymuje usługę `Stop` przy użyciu metody obiektu `Service` .

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Jeśli spróbujesz uruchomić to w przepływie pracy, zostanie wyświetlony komunikat o błędzie `Method invocation is not supported in a Windows PowerShell Workflow.`

Jedną z opcji jest zawijanie tych dwóch wierszy kodu w [bloku InlineScript.](#use-inlinescript) W tym przypadku `Service` reprezentuje obiekt usługi w bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Inną opcją jest użycie innego polecenia cmdlet, które ma taką samą funkcjonalność jak metoda , jeśli jest dostępne. W naszym przykładzie polecenie cmdlet zapewnia taką samą funkcjonalność jak metoda i można użyć następującego `Stop-Service` `Stop` kodu dla przepływu pracy.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Korzystanie z inlineScript

To działanie jest przydatne, gdy trzeba uruchomić co najmniej jedno polecenie jako tradycyjny `InlineScript` skrypt programu PowerShell zamiast przepływu pracy programu PowerShell.  Podczas gdy polecenia zawarte w przepływie pracy są wysyłane do przetwarzania w programie Windows Workflow Foundation, polecenia umieszczone w bloku InlineScript są przetwarzane w środowisku Windows PowerShell.

W języku InlineScript jest używana następująca składnia pokazana poniżej.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Dane wyjściowe z inlineScript można zwrócić, przypisując dane wyjściowe do zmiennej. Poniższy przykład zatrzymuje usługę, a następnie wyprowadza nazwę usługi.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Wartości można przekazać do bloku InlineScript, ale należy użyć **$Using** zakresu.  Poniższy przykład jest identyczny z poprzednim przykładem, z tą różnicą, że nazwa usługi jest dostarczana przez zmienną.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Działania InlineScript mogą być krytyczne w niektórych przepływach pracy, ale nie obsługują konstrukcji przepływu pracy. Należy ich używać tylko w razie potrzeby z następujących powodów:

* Punktów kontrolnych nie [można używać wewnątrz](#use-checkpoints-in-a-workflow) bloku InlineScript. Jeśli w bloku wystąpi awaria, musi zostać wznowiona od początku bloku.
* Nie można używać wykonywania [równoległego wewnątrz](#use-parallel-processing) bloku InlineScript.
* Skrypt InlineScript wpływa na skalowalność przepływu pracy, ponieważ Windows PowerShell sesję przez całą długość bloku InlineScript.

Aby uzyskać więcej informacji na temat korzystania z języka InlineScript, zobacz [Uruchamianie poleceń Windows PowerShell w przepływie pracy i](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Korzystanie z przetwarzania równoległego

Jedną z korzyści wynikających z używania przepływów pracy w środowisku Windows PowerShell jest możliwość wykonywania zestawów poleceń równolegle, w przeciwieństwie do wykonywania sekwencyjnego, jak w typowym skrypcie.

Możesz użyć słowa `Parallel` kluczowego , aby utworzyć blok skryptu z wieloma poleceniami uruchamianymi jednocześnie. W tym celu jest używana następująca składnia pokazana poniżej. W takim przypadku działania Activity1 i Activity2 są rozpoczynane w tym samym czasie. Działanie Activity3 jest uruchamiane dopiero po zakończeniu działań Activity1 i Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Rozważmy na przykład następujące polecenia programu PowerShell, które kopiują wiele plików do lokalizacji docelowej sieci. Te polecenia są uruchamiane sekwencyjnie, tak aby jeden plik musiał zakończyć kopiowanie przed następnym uruchomieniem.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

W poniższym przepływie pracy te same polecenia są uruchamiane równolegle, tak aby wszystkie one rozpoczynały kopiowanie w tym samym czasie.  Dopiero po ich skopiowaniu zostanie wyświetlony komunikat o ukończeniu.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Za pomocą konstrukcji `ForEach -Parallel` można przetwarzać polecenia dla każdego elementu w kolekcji jednocześnie. Elementy w kolekcji są przetwarzane współbieżnie, podczas gdy polecenia w bloku skryptu są wykonywane sekwencyjnie. W tym procesie jest używana następująca składnia pokazana poniżej. W takim przypadku działanie Activity1 rozpoczyna się w tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu działanie Activity2 rozpoczyna się po zakończeniu działania Activity1. Działanie Activity3 jest uruchamiane dopiero po zakończeniu działań Activity1 i Activity2 dla wszystkich elementów. Używamy `ThrottleLimit` parametru , aby ograniczyć równoległość. Zbyt wysoki poziom może `ThrottleLimit` powodować problemy. Idealna wartość parametru `ThrottleLimit` zależy od wielu czynników w środowisku. Zacznij od niskiej wartości i wypróbuj różne wartości rosnące, aż znajdziesz taką, która będzie sprawdzać się w konkretnym przypadku.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Poniższy przykład jest podobny do poprzedniego przykładu równoległego kopiowania plików.  W takim przypadku dla każdego pliku po jego skopiowaniu jest wyświetlany komunikat.  Dopiero po ich skopiowaniu zostanie wyświetlony końcowy komunikat o ukończeniu.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Nie zalecamy równoległego uruchamiania podrzędnych runbook, ponieważ pokazano, że daje to zawodne wyniki. Dane wyjściowe podrzędnego podręcznika Runbook czasami nie są wyświetlane, a ustawienia w jednym podrzędnym runbook mogą mieć wpływ na inne równoległe podrzędne podręczniki Runbook. Zmienne, takie `VerbosePreference` jak , i `WarningPreference` inne, mogą nie być propagowane do podrzędnych podręczników Runbook. Jeśli podrzędny podręcznik Runbook zmieni te wartości, mogą one nie zostać prawidłowo przywrócone po wywołaniach.

## <a name="use-checkpoints-in-a-workflow"></a>Używanie punktów kontrolnych w przepływie pracy

Punkt kontrolny to migawka bieżącego stanu przepływu pracy, która zawiera bieżące wartości zmiennych i wszystkie wygenerowane do tego momentu dane wyjściowe. Jeśli przepływ pracy kończy się błędem lub został zawieszony, rozpoczyna się od ostatniego punktu kontrolnego przy następnym uruchomieniu, zamiast rozpoczynać się od początku. 

Możesz ustawić punkt kontrolny w przepływie pracy za pomocą `Checkpoint-Workflow` działania . Azure Automation ma funkcję o nazwie [fair share](automation-runbook-execution.md#fair-share), dla której każdy element Runbook uruchamiany przez trzy godziny jest zwalniany w celu umożliwienia uruchamiania innych elementach Runbook. Po pewnym czasie niezaładowany runbook zostanie ponownie załadowany. Gdy tak jest, wznawia wykonywanie od ostatniego punktu kontrolnego w tym runbook.

Aby zagwarantować, że ten runbook zostanie ukończony, należy dodać punkty kontrolne w odstępach czasu, które będą działać mniej niż trzy godziny. Jeśli podczas każdego uruchomienia zostanie dodany nowy punkt kontrolny, a element Runbook zostanie eksmitowany po trzech godzinach z powodu błędu, element Runbook zostanie wznowiony na czas nieokreślony.

W poniższym przykładzie po działaniu Activity2 występuje wyjątek, co powoduje zakończenie przepływu pracy. Gdy przepływ pracy zostanie uruchomiony ponownie, rozpocznie się od uruchomienia działania Activity2, ponieważ to działanie było tuż po ostatnim zestawie punktów kontrolnych.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Ustaw punkty kontrolne w przepływie pracy po działaniach, które mogą być podatne na wyjątek i nie powinny być powtarzane, jeśli przepływ pracy zostanie wznowiony. Na przykład przepływ pracy może utworzyć maszynę wirtualną. Punkt kontrolny można ustawić zarówno przed, jak i po poleceniach, aby utworzyć maszynę wirtualną. Jeśli tworzenie nie powiedzie się, polecenia zostaną powtórzone, jeśli przepływ pracy zostanie ponownie uruchomiony. Jeśli przepływ pracy zakończy się niepowodzeniem po zakończeniu tworzenia, maszyna wirtualna nie zostanie utworzona ponownie po wznowieniu przepływu pracy.

Poniższy przykład kopiuje wiele plików do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  Jeśli lokalizacja sieciowa zostanie utracona, przepływ pracy zakończy się błędem.  Po jego wznowieniu w ostatnim punkcie kontrolnym. Pomijane są tylko pliki, które zostały już skopiowane.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Ponieważ poświadczenia nazwy użytkownika nie są utrwalane po wywołaniu działania [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) lub po ostatnim punkcie kontrolnym, należy ustawić poświadczenia na wartość null, a następnie pobrać je ponownie z magazynu zasobów po wywołaniu punktu kontrolnego lub `Suspend-Workflow` .  W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Poniższy kod przedstawia sposób obsługi tej sytuacji w elementy Runbook przepływu pracy programu PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> W przypadku niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami polecenia [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Możesz użyć tych polecenia cmdlet lub zaktualizować [moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto usługi Automation. Korzystanie z tych polecenia cmdlet nie jest wymagane w przypadku uwierzytelniania przy użyciu konta Uruchom jako skonfigurowanego przy użyciu jednostki usługi.

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do przepływu pracy skryptu](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o runbookach przepływu pracy programu PowerShell, zobacz [Samouczek: tworzenie runbook przepływu pracy programu PowerShell.](learn/automation-tutorial-runbook-textual.md)
