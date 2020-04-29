---
title: Uczenie przepływu pracy programu PowerShell dla Azure Automation
description: Ten artykuł jest przeznaczony do szybkiej lekcji dla autorów znanych z programem PowerShell w celu zrozumienia określonych różnic między środowiskiem Workflow i przepływem pracy programu PowerShell oraz pojęciami dotyczącymi elementów Runbook usługi Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457539"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Najważniejsze pojęcia dotyczące przepływu pracy programu Windows PowerShell dla elementów Runbook usługi Automation

Elementy Runbook w Azure Automation są implementowane jako przepływy pracy środowiska Windows PowerShell.  Przepływ pracy programu Windows PowerShell jest podobny do skryptu programu Windows PowerShell, ale zawiera pewne znaczące różnice, które mogą być mylące dla nowego użytkownika.  Ten artykuł ma na celu ułatwienie pisania elementów Runbook za pomocą przepływu pracy programu PowerShell, dlatego zalecamy pisanie elementów Runbook przy użyciu programu PowerShell, chyba że potrzebne są punkty kontrolne.  Istnieje kilka różnic składni podczas tworzenia elementów Runbook przepływu pracy programu PowerShell, a różnice te wymagają nieco większej pracy w celu zapisywania efektywnych przepływów pracy.

Przepływ pracy to sekwencja zaprogramowanych, połączonych ze sobą czynności służących do wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu czynności na wielu różnych urządzeniach albo w wielu węzłach zarządzanych. Korzyści ze stosowania przepływu pracy zamiast zwykłego skryptu obejmują możliwość jednoczesnego wykonywania akcji na różnych urządzeniach oraz zdolność usuwania skutków błędów. Przepływ pracy programu Windows PowerShell to skrypt programu Windows PowerShell, który używa Windows Workflow Foundation. Przepływ pracy ma składnię Windows PowerShell i jest uruchamiany przez środowisko Windows PowerShell, jednak jest przetwarzany przez program Windows Workflow Foundation.

Aby uzyskać szczegółowe informacje dotyczące tematów w tym artykule, zobacz [wprowadzenie with Windows PowerShell Workflow](https://technet.microsoft.com/library/jj134242.aspx).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="basic-structure-of-a-workflow"></a>Podstawowa struktura przepływu pracy

Pierwszym krokiem do przekonwertowania skryptu programu PowerShell na przepływ pracy programu PowerShell jest załączanie go `Workflow` za pomocą słowa kluczowego.  Przepływ pracy rozpoczyna się od `Workflow` słowa kluczowego, po którym następuje treść skryptu ujęta w nawiasy klamrowe. Nazwa przepływu pracy jest zgodna ze `Workflow` słowem kluczowym, jak pokazano w następującej składni:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Nazwa przepływu pracy musi być zgodna z nazwą elementu Runbook usługi Automation. Jeśli element Runbook jest importowany, nazwa pliku musi być zgodna z nazwą przepływu pracy i musi kończyć się na *. ps1*.

Aby dodać parametry do przepływu pracy, użyj `Param` słowa kluczowego w taki sam sposób jak w skrypcie.

## <a name="code-changes"></a>Zmiany kodu

Kod przepływu pracy programu PowerShell wygląda niemal identycznie z kodem skryptu programu PowerShell, z wyjątkiem kilku znaczących zmian.  W poniższych sekcjach opisano zmiany, które należy wykonać w skrypcie programu PowerShell, aby można było go uruchomić w przepływie pracy.

### <a name="activities"></a>Działania

Działanie jest konkretnym zadaniem w przepływie pracy. Tak jak skrypt składa się z jednego polecenia lub kilku poleceń, tak przepływ pracy składa się z działania lub kilku działań wykonywanych w określonej kolejności. Podczas wykonywania przepływu pracy program Windows PowerShell Workflow automatycznie konwertuje wiele spośród poleceń cmdlet środowiska Windows PowerShell na działania. Po określeniu jednego z tych poleceń cmdlet w elemencie Runbook odpowiednie działanie jest uruchamiane przez Windows Workflow Foundation. W przypadku tych poleceń cmdlet, które nie odpowiadają działaniu, przepływ pracy programu Windows PowerShell automatycznie uruchamia polecenie cmdlet w ramach działania [InlineScript](#inlinescript) . Istnieje zestaw poleceń cmdlet, które są wykluczone i nie mogą być używane w przepływie pracy, chyba że jawnie dołączysz je w bloku InlineScript. Aby uzyskać więcej szczegółowych informacji dotyczących tych pojęć, zobacz [Używanie działań w skryptowych przepływach pracy](https://technet.microsoft.com/library/jj574194.aspx).

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Aby uzyskać szczegółowe informacje o typowych parametrach przepływu pracy, zobacz [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametry pozycyjne

W przepływie pracy nie można używać parametrów pozycyjnych z działaniami i poleceniami cmdlet.  Oznacza to, że należy użyć nazw parametrów.

Rozważmy na przykład poniższy kod, który pobiera wszystkie uruchomione usługi.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Jeśli spróbujesz uruchomić ten sam kod w przepływie pracy, zostanie wyświetlony komunikat "nie można rozpoznać zestawu parametrów przy użyciu określonych parametrów nazwanych".  Aby rozwiązać ten konieczność, podaj nazwę parametru w następujący sposób.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Obiekty deserializowane

Obiekty w przepływach pracy są deserializowane.  Oznacza to, że ich właściwości są nadal dostępne, ale nie ich metodami.  Rozważmy na przykład następujący kod programu PowerShell, który zatrzymuje usługę przy użyciu metody Stop obiektu usługi.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Jeśli spróbujesz uruchomić ten element w przepływie pracy, zostanie wyświetlony komunikat o błędzie informujący, że wywołanie metody nie jest obsługiwane w przepływie pracy programu Windows PowerShell.

Jedną z opcji jest zawinięcie tych dwóch wierszy kodu w bloku [InlineScript](#inlinescript) , w którym przypadek $Service będzie obiektem usługi w bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Innym rozwiązaniem jest użycie innego polecenia cmdlet, które wykonuje te same funkcje co metoda, jeśli jest ona dostępna.  W naszym przykładzie polecenie cmdlet Stop-Service zapewnia te same funkcje co Metoda stop i można użyć poniższych danych dla przepływu pracy.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

`InlineScript` Działanie jest przydatne, gdy trzeba uruchomić jedno lub więcej poleceń jako tradycyjnego skryptu programu PowerShell zamiast przepływu pracy programu PowerShell.  Podczas gdy polecenia zawarte w przepływie pracy są wysyłane do przetwarzania w programie Windows Workflow Foundation, polecenia umieszczone w bloku InlineScript są przetwarzane w środowisku Windows PowerShell.

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

Chociaż działania InlineScript mogą być krytyczne w niektórych przepływach pracy, nie obsługują konstrukcji przepływu pracy i powinny być używane tylko w razie potrzeby z następujących powodów:

* [Punktów kontrolnych](#checkpoints) nie można używać wewnątrz bloku InlineScript. Jeśli wystąpi błąd w bloku, musi zostać wznowiony od początku bloku.
* Nie można użyć [wykonania równoległego](#parallel-processing) wewnątrz elementu InlineScriptBlock.
* InlineScript ma wpływ na skalowalność przepływu pracy, ponieważ zawiera sesję programu Windows PowerShell dla całej długości bloku InlineScript.

Aby uzyskać więcej informacji na temat korzystania z programu InlineScript, zobacz [Uruchamianie poleceń programu Windows PowerShell w przepływie pracy](https://technet.microsoft.com/library/jj574197.aspx) i [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Przetwarzanie równoległe

Jedną z korzyści wynikających z używania przepływów pracy w środowisku Windows PowerShell jest możliwość wykonywania zestawów poleceń równolegle, w przeciwieństwie do wykonywania sekwencyjnego, jak w typowym skrypcie.

Możesz użyć słowa kluczowego, `Parallel` aby utworzyć blok skryptu z wieloma poleceniami, które są uruchamiane współbieżnie. Ta funkcja używa następującej składni poniżej. W takim przypadku zakończeniu i Activity2 są uruchamiane w tym samym czasie. Działanie activity3 jest uruchamiany dopiero po zakończeniu obu zakończeniu i Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Rozważmy na przykład następujące polecenia programu PowerShell, które kopiują wiele plików do sieci docelowej.  Te polecenia są uruchamiane sekwencyjnie, aby jeden plik musiał zakończyć kopiowanie przed rozpoczęciem następnego uruchomienia.

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

Można użyć `ForEach -Parallel` konstrukcji do przetwarzania poleceń dla każdego elementu w kolekcji współbieżnie. Elementy w kolekcji są przetwarzane współbieżnie, podczas gdy polecenia w bloku skryptu są wykonywane sekwencyjnie. Ta funkcja używa następującej składni poniżej. W takim przypadku zakończeniu rozpocznie się w tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu Activity2 rozpoczyna się po zakończeniu. Działanie activity3 jest uruchamiany dopiero po zakończeniu zakończeniu i Activity2 dla wszystkich elementów. Użyjemy `ThrottleLimit` parametru, aby ograniczyć równoległość. Zbyt wysoka z `ThrottleLimit` może spowodować problemy. Idealna wartość `ThrottleLimit` parametru zależy od wielu czynników w środowisku. Należy spróbować uruchomić z niską wartością i wypróbować różne wartości zwiększające się, dopóki nie znajdziesz tego, który działa w konkretnym przypadku.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Poniższy przykład jest podobny do poprzedniego przykładu kopiowania plików równolegle.  W takim przypadku dla każdego pliku po jego skopiowaniu zostanie wyświetlony komunikat.  Tylko po całkowitym skopiowaniu jest wyświetlany końcowy komunikat uzupełniający.

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
> Nie zaleca się uruchamiania podrzędnych elementów Runbook równolegle, ponieważ został on wyświetlony w celu zapewnienia wiarygodnych wyników. Dane wyjściowe z podrzędnego elementu Runbook czasami nie są wyświetlane, a ustawienia w jednym podrzędnym elemencie Runbook mogą mieć wpływ na inne równoległe podrzędne elementy Runbook. Zmienne, takie jak $VerbosePreference, $WarningPreference i inne, nie mogą być propagowane do podrzędnych elementów Runbook. A jeśli podrzędny element Runbook zmieni te wartości, mogą one nie zostać prawidłowo przywrócone po wywołaniu.

## <a name="checkpoints"></a>Punkty kontrolne

*Punkt kontrolny* jest migawką bieżącego stanu przepływu pracy, który zawiera bieżącą wartość dla zmiennych i wszystkie dane wyjściowe wygenerowane do tego momentu. Jeśli przepływ pracy zakończy się błędem lub jest zawieszony, przy następnym uruchomieniu zostanie uruchomiony od ostatniego punktu kontrolnego zamiast od początku przepływu pracy.  Możesz ustawić punkt kontrolny w przepływie pracy przy `Checkpoint-Workflow` użyciu działania. Azure Automation ma funkcję o nazwie " [uczciwy udział](automation-runbook-execution.md#fair-share)", w której każdy element Runbook, który działa przez 3 godziny, zostaje zwolniony, aby umożliwić uruchomienie innych elementów Runbook. Po pewnym czasie zostanie ponownie załadowany zwolniony element Runbook, a w jego przypadku zostanie wznowione wykonywanie z ostatniego punktu kontrolnego wykonanego w elemencie Runbook. W celu zagwarantowania, że element Runbook zostanie ostatecznie zakończony, należy dodać punkty kontrolne w odstępach czasu, które są uruchamiane krócej niż 3 godziny. Jeśli podczas każdego uruchomienia zostanie dodany nowy punkt kontrolny, a element Runbook zostanie wykluczony po 3 godzinach z powodu błędu, element Runbook zostanie wznowiony w nieskończoność.

W poniższym przykładowym kodzie wyjątek występuje po Activity2, co spowodowało zakończenie przepływu pracy. Po ponownym uruchomieniu przepływu pracy jest on uruchamiany przez uruchomienie Activity2, ponieważ był tuż po ostatnim zestawie punktów kontrolnych.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Punkty kontrolne powinny być ustawiane w przepływie pracy po działaniach, które mogą być podatne na wyjątek i nie powinny być powtarzane, jeśli przepływ pracy zostanie wznowiony. Na przykład przepływ pracy może utworzyć maszynę wirtualną. Punkt kontrolny można ustawić przed poleceniami tworzącymi maszynę wirtualną i po nich. Jeśli Tworzenie zakończy się niepowodzeniem, polecenia zostałyby powtórzone, jeśli przepływ pracy zostanie uruchomiony ponownie. Jeśli przepływ pracy zakończy się niepowodzeniem po pomyślnym utworzeniu, maszyna wirtualna nie zostanie ponownie utworzona po wznowieniu przepływu pracy.

Poniższy przykład kopiuje wiele plików do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  Jeśli lokalizacja sieciowa zostanie utracona, przepływ pracy zostanie zakończony błędem.  Po ponownym uruchomieniu zostanie on wznowiony przez ostatni punkt kontrolny, co oznacza, że tylko te pliki, które zostały już skopiowane, zostaną pominięte.

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

Ponieważ poświadczenia nazwy użytkownika nie są utrwalane po wywołaniu działania [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) lub po ostatnim punkcie kontrolnym, należy ustawić poświadczenia na wartość null, a następnie pobrać je ponownie z magazynu zasobów po `Suspend-Workflow` wywołaniu lub wyjściu z punktu kontrolnego.  W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Poniższy kod ilustruje sposób obsługi tego w elementach Runbook przepływu pracy programu PowerShell.

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
> Dla niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami dla polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.


Nie jest to wymagane w przypadku uwierzytelniania przy użyciu konta Uruchom jako skonfigurowanego za pomocą nazwy głównej usługi.

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do skryptu przepływu pracy](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz [mój pierwszy element Runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md)

