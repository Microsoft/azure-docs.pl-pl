---
title: Informacje o przepływie pracy programu PowerShell dla Azure Automation
description: W tym artykule przedstawiono różnice między przepływem pracy programu PowerShell a programem PowerShell i pojęciami dotyczącymi elementów Runbook usługi Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896922"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Informacje o przepływie pracy programu PowerShell dla Azure Automation

Elementy Runbook w Azure Automation są implementowane jako przepływy pracy środowiska Windows PowerShell, skrypty programu Windows PowerShell korzystające Windows Workflow Foundation. Przepływ pracy to sekwencja zaprogramowanych, połączonych ze sobą czynności służących do wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu czynności na wielu różnych urządzeniach albo w wielu węzłach zarządzanych. 

Podczas gdy przepływ pracy jest zapisywana przy użyciu składni środowiska Windows PowerShell i uruchamiany przez program Windows PowerShell, jest przetwarzany przez Windows Workflow Foundation. Korzyści płynące z przepływu pracy nad normalnym skryptem obejmują jednoczesne wykonywanie akcji na wielu urządzeniach i automatyczne odzyskiwanie z błędów. 

> [!NOTE]
> Skrypt przepływu pracy programu PowerShell jest bardzo podobny do skryptu programu Windows PowerShell, ale zawiera pewne znaczące różnice, które mogą być mylące dla nowego użytkownika. Dlatego zalecamy zapisanie elementów Runbook za pomocą przepływu pracy programu PowerShell tylko wtedy, gdy trzeba użyć [punktów kontrolnych](#use-checkpoints-in-a-workflow). 

Aby uzyskać szczegółowe informacje dotyczące tematów w tym artykule, zobacz [wprowadzenie with Windows PowerShell Workflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Użyj słowa kluczowego przepływu pracy

Pierwszym krokiem do przekonwertowania skryptu programu PowerShell na przepływ pracy programu PowerShell jest załączanie go za pomocą `Workflow` słowa kluczowego. Przepływ pracy rozpoczyna się od `Workflow` słowa kluczowego, po którym następuje treść skryptu ujęta w nawiasy klamrowe. Nazwa przepływu pracy jest zgodna ze `Workflow` słowem kluczowym, jak pokazano w następującej składni:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Nazwa przepływu pracy musi być zgodna z nazwą elementu Runbook usługi Automation. Jeśli element Runbook jest importowany, nazwa pliku musi być zgodna z nazwą przepływu pracy i musi kończyć się rozszerzeniem **. ps1**.

Aby dodać parametry do przepływu pracy, użyj `Param` słowa kluczowego w taki sam sposób jak w skrypcie.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Poznaj różnice między kodem przepływu pracy programu PowerShell i kodem skryptu programu PowerShell

Kod przepływu pracy programu PowerShell wygląda niemal identycznie z kodem skryptu programu PowerShell, z wyjątkiem kilku znaczących zmian. W poniższych sekcjach opisano zmiany, które należy wykonać w skrypcie programu PowerShell, aby można było go uruchomić w przepływie pracy.

### <a name="activities"></a>Działania

Działanie to określone zadanie w przepływie pracy, które jest wykonywane w sekwencji. Podczas wykonywania przepływu pracy program Windows PowerShell Workflow automatycznie konwertuje wiele spośród poleceń cmdlet środowiska Windows PowerShell na działania. Po określeniu jednego z tych poleceń cmdlet w elemencie Runbook odpowiednie działanie jest uruchamiane przez Windows Workflow Foundation. 

Jeśli polecenie cmdlet nie ma odpowiedniego działania, przepływ pracy programu Windows PowerShell automatycznie uruchamia polecenie cmdlet w działaniu [InlineScript](#use-inlinescript) . Niektóre polecenia cmdlet są wykluczone i nie mogą być używane w przepływie pracy, chyba że zostaną jawnie umieszczone w bloku InlineScript. Aby uzyskać więcej informacji, zobacz [Używanie działań w skryptowych przepływach pracy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Zobacz [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Parametry pozycyjne

W przepływie pracy nie można używać parametrów pozycyjnych z działaniami i poleceniami cmdlet. W związku z tym należy użyć nazw parametrów. Rozważmy następujący kod, który pobiera wszystkie uruchomione usługi:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Jeśli spróbujesz uruchomić ten kod w przepływie pracy, zostanie wyświetlony komunikat o błędzie, `Parameter set cannot be resolved using the specified named parameters.` jak w poniższym przykładzie:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Obiekty deserializowane

Obiekty w przepływach pracy są deserializowane, co oznacza, że ich właściwości są nadal dostępne, ale nie są ich metodami.  Rozważmy na przykład następujący kod programu PowerShell, który powoduje zatrzymanie usługi przy użyciu `Stop` metody `Service` obiektu.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Jeśli spróbujesz uruchomić ten element w przepływie pracy, zostanie wyświetlony komunikat o błędzie mówiący `Method invocation is not supported in a Windows PowerShell Workflow.`

Jedną z opcji jest zawinięcie tych dwóch wierszy kodu w bloku [InlineScript](#use-inlinescript) . W tym przypadku `Service` reprezentuje obiekt usługi w bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Innym rozwiązaniem jest użycie innego polecenia cmdlet, które ma takie same funkcje jak metoda, jeśli jest dostępna. W naszym przykładzie `Stop-Service` polecenie cmdlet zapewnia te same funkcje co `Stop` Metoda i można użyć następującego kodu dla przepływu pracy.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Użyj InlineScript

`InlineScript`Działanie jest przydatne, gdy trzeba uruchomić jedno lub więcej poleceń jako tradycyjnego skryptu programu PowerShell zamiast przepływu pracy programu PowerShell.  Podczas gdy polecenia zawarte w przepływie pracy są wysyłane do przetwarzania w programie Windows Workflow Foundation, polecenia umieszczone w bloku InlineScript są przetwarzane w środowisku Windows PowerShell.

InlineScript używa następującej składni poniżej.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Dane wyjściowe można zwrócić z InlineScript, przypisując dane wyjściowe do zmiennej. Poniższy przykład powoduje zatrzymanie usługi, a następnie wyprowadza nazwę usługi.

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

Można przekazać wartości do bloku InlineScript, ale należy użyć modyfikatora zakresu **$using** .  Poniższy przykład jest identyczny z poprzednim przykładem, z tą różnicą, że nazwa usługi jest udostępniana przez zmienną.

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

Chociaż działania InlineScript mogą być krytyczne w niektórych przepływach pracy, nie obsługują one konstrukcji przepływu pracy. Należy używać ich tylko w razie potrzeby z następujących powodów:

* [Punktów kontrolnych](#use-checkpoints-in-a-workflow) nie można używać wewnątrz bloku InlineScript. Jeśli wystąpi błąd w bloku, musi on zostać wznowiony od początku bloku.
* Nie można użyć [wykonywania równoległego](#use-parallel-processing) wewnątrz bloku InlineScript.
* InlineScript ma wpływ na skalowalność przepływu pracy, ponieważ zawiera sesję programu Windows PowerShell dla całej długości bloku InlineScript.

Aby uzyskać więcej informacji na temat korzystania z programu InlineScript, zobacz [Uruchamianie poleceń programu Windows PowerShell w przepływie pracy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) i [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Użyj przetwarzania równoległego

Jedną z korzyści wynikających z używania przepływów pracy w środowisku Windows PowerShell jest możliwość wykonywania zestawów poleceń równolegle, w przeciwieństwie do wykonywania sekwencyjnego, jak w typowym skrypcie.

Możesz użyć `Parallel` słowa kluczowego, aby utworzyć blok skryptu z wieloma poleceniami, które są uruchamiane współbieżnie. Ta funkcja używa następującej składni poniżej. W takim przypadku zakończeniu i Activity2 są uruchamiane w tym samym czasie. Działanie activity3 jest uruchamiany dopiero po zakończeniu obu zakończeniu i Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Rozważmy na przykład następujące polecenia programu PowerShell, które kopiują wiele plików do sieci docelowej. Te polecenia są uruchamiane sekwencyjnie, aby jeden plik musiał zakończyć kopiowanie przed rozpoczęciem następnego uruchomienia.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

W poniższym przepływie pracy te same polecenia są uruchamiane równolegle, tak aby wszystkie rozpoczęto kopiowanie w tym samym czasie.  Tylko wtedy, gdy są one kopiowane, zostanie wyświetlony komunikat o ukończeniu.

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

Można użyć `ForEach -Parallel` konstrukcji do przetwarzania poleceń dla każdego elementu w kolekcji współbieżnie. Elementy w kolekcji są przetwarzane współbieżnie, podczas gdy polecenia w bloku skryptu są wykonywane sekwencyjnie. Ten proces używa następującej składni poniżej. W takim przypadku zakończeniu rozpocznie się w tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu Activity2 rozpoczyna się po zakończeniu. Działanie activity3 jest uruchamiany dopiero po zakończeniu zakończeniu i Activity2 dla wszystkich elementów. Użyjemy `ThrottleLimit` parametru, aby ograniczyć równoległość. Zbyt wysoka z `ThrottleLimit` może spowodować problemy. Idealna wartość `ThrottleLimit` parametru zależy od wielu czynników w środowisku. Zacznij od niskiej wartości i wypróbuj różne wartości zwiększające się, aż znajdziesz je w konkretnym przypadku.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Poniższy przykład jest podobny do poprzedniego przykładu kopiowania plików równolegle.  W takim przypadku dla każdego pliku po jego skopiowaniu zostanie wyświetlony komunikat.  Tylko wtedy, gdy są one kopiowane, zostanie wyświetlony końcowy komunikat o zakończeniu.

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
> Nie zaleca się uruchamiania podrzędnych elementów Runbook równolegle, ponieważ został on wyświetlony w celu zapewnienia wiarygodnych wyników. Dane wyjściowe z podrzędnego elementu Runbook czasami nie są wyświetlane, a ustawienia w jednym podrzędnym elemencie Runbook mogą mieć wpływ na inne równoległe podrzędne elementy Runbook. Zmienne takie jak `VerbosePreference` , `WarningPreference` i inne mogą nie być propagowane do podrzędnych elementów Runbook. A jeśli podrzędny element Runbook zmieni te wartości, mogą one nie zostać prawidłowo przywrócone po wywołaniu.

## <a name="use-checkpoints-in-a-workflow"></a>Używanie punktów kontrolnych w przepływie pracy

Punkt kontrolny jest migawką bieżącego stanu przepływu pracy, który obejmuje bieżące wartości zmiennych i wszystkie dane wyjściowe wygenerowane do tego momentu. Jeśli przepływ pracy zostanie zakończony błędem lub jest zawieszony, zaczyna się od ostatniego punktu kontrolnego przy następnym uruchomieniu, zamiast rozpoczynać się na początku. 

Możesz ustawić punkt kontrolny w przepływie pracy przy użyciu `Checkpoint-Workflow` działania. Azure Automation ma funkcję o nazwie " [uczciwy udział](automation-runbook-execution.md#fair-share)", dla której każdy element Runbook, który działa przez trzy godziny, zostaje zwolniony, aby umożliwić uruchamianie innych elementów Runbook. Ostatecznie zwolniony element Runbook zostanie ponownie załadowany. Gdy tak jest, wznawia wykonywanie od ostatniego punktu kontrolnego wykonanego w elemencie Runbook.

Aby zagwarantować zakończenie działania elementu Runbook, należy dodać punkty kontrolne w odstępach czasu, które są uruchamiane krócej niż trzy godziny. Jeśli podczas każdego uruchomienia zostanie dodany nowy punkt kontrolny, a element Runbook zostanie wykluczony po trzech godzinach z powodu błędu, element Runbook zostanie wznowiony w nieskończoność.

W poniższym przykładzie wyjątek występuje po Activity2, co spowodowało zakończenie przepływu pracy. Po ponownym uruchomieniu przepływu pracy jest on uruchamiany przez uruchomienie Activity2, ponieważ to działanie było zaraz po ostatnim zestawie punktów kontrolnych.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Ustaw punkty kontrolne w przepływie pracy po działaniach, które mogą być podatne na wyjątek i nie powinny być powtarzane, jeśli przepływ pracy zostanie wznowiony. Na przykład przepływ pracy może utworzyć maszynę wirtualną. Aby utworzyć maszynę wirtualną, można ustawić punkt kontrolny zarówno przed, jak i po nim. Jeśli Tworzenie zakończy się niepowodzeniem, polecenia są powtarzane, jeśli przepływ pracy zostanie uruchomiony ponownie. Jeśli przepływ pracy zakończy się niepowodzeniem po pomyślnym utworzeniu, maszyna wirtualna nie zostanie ponownie utworzona po wznowieniu przepływu pracy.

Poniższy przykład kopiuje wiele plików do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  Jeśli lokalizacja sieciowa zostanie utracona, przepływ pracy zostanie zakończony błędem.  Po ponownym uruchomieniu zostanie on wznowiony przy ostatnim punkcie kontrolnym. Pominięto tylko te pliki, które zostały już skopiowane.

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

Ponieważ poświadczenia nazwy użytkownika nie są utrwalane po wywołaniu działania [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) lub po ostatnim punkcie kontrolnym, należy ustawić poświadczenia na wartość null, a następnie pobrać je ponownie z magazynu zasobów po wywołaniu lub wyjściu z `Suspend-Workflow` punktu kontrolnego.  W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Poniższy kod pokazuje, jak obsłużyć tę sytuację w elementach Runbook przepływu pracy programu PowerShell.

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
> Dla niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami dla polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation. Korzystanie z tych poleceń cmdlet nie jest wymagane w przypadku uwierzytelniania przy użyciu konta Uruchom jako skonfigurowanego za pomocą nazwy głównej usługi.

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do skryptu przepływu pracy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o elementach Runbook przepływu pracy programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook przepływu pracy programu PowerShell](learn/automation-tutorial-runbook-textual.md).
