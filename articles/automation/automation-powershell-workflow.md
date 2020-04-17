---
title: Proces uczenia się przepływu pracy programu PowerShell dla automatyzacji platformy Azure
description: Ten artykuł jest przeznaczony jako szybka lekcja dla autorów zaznajomionych z programem PowerShell, aby zrozumieć konkretne różnice między przepływem pracy programu PowerShell i przepływem pracy programu PowerShell oraz pojęciami dotyczącymi śmiób uruchomieniu do automatyzacji.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457539"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Nauka kluczowych koncepcji przepływu pracy programu Windows PowerShell dla uruchomieniu książek roboczej automatyzacji

Elementy runbook w usłudze Azure Automation są implementowane jako przepływy pracy programu Windows PowerShell.  Przepływ pracy programu Windows PowerShell jest podobny do skryptu programu Windows PowerShell, ale ma pewne istotne różnice, które mogą być mylące dla nowego użytkownika.  Ten artykuł ma na celu ułatwienie pisania śmiób przy użyciu przepływu pracy programu PowerShell, ale zaleca się pisanie śmiób przy użyciu programu PowerShell, chyba że potrzebne są punkty kontrolne.  Istnieje kilka różnic składni podczas tworzenia liksów pracy programu PowerShell i te różnice wymagają nieco więcej pracy, aby zapisać skuteczne przepływy pracy.

Przepływ pracy to sekwencja zaprogramowanych, połączonych ze sobą czynności służących do wykonywania długotrwałych zadań lub do zapewnienia koordynacji wielu czynności na wielu różnych urządzeniach albo w wielu węzłach zarządzanych. Korzyści ze stosowania przepływu pracy zamiast zwykłego skryptu obejmują możliwość jednoczesnego wykonywania akcji na różnych urządzeniach oraz zdolność usuwania skutków błędów. Przepływ pracy programu Windows PowerShell to skrypt programu Windows PowerShell, który używa programu Windows Workflow Foundation. Przepływ pracy ma składnię Windows PowerShell i jest uruchamiany przez środowisko Windows PowerShell, jednak jest przetwarzany przez program Windows Workflow Foundation.

Aby uzyskać pełne informacje na temat tematów w tym artykule, zobacz [Wprowadzenie do przepływu pracy programu Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="basic-structure-of-a-workflow"></a>Podstawowa struktura przepływu pracy

Pierwszym krokiem do konwersji skryptu programu PowerShell do przepływu pracy `Workflow` programu PowerShell jest załączanie go do słowa kluczowego.  Przepływ pracy rozpoczyna się `Workflow` od słowa kluczowego, po którym następuje treść skryptu ujęta w nawiasy klamrowe. Nazwa przepływu pracy jest `Workflow` zgodna ze słowem kluczowym, jak pokazano w następującej składni:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Nazwa przepływu pracy musi być zgodna z nazwą uruchomieniu automatyzacji. Jeśli projekt runbook jest importowany, nazwa pliku musi być zgodna z nazwą przepływu pracy i musi kończyć się na *ps1*.

Aby dodać parametry do przepływu `Param` pracy, użyj słowa kluczowego tak samo, jak w skrypcie.

## <a name="code-changes"></a>Zmiany kodu

Kod przepływu pracy programu PowerShell wygląda prawie identycznie jak kod skryptu programu PowerShell, z wyjątkiem kilku istotnych zmian.  W poniższych sekcjach opisano zmiany, które należy wprowadzić do skryptu programu PowerShell, aby był on uruchamiany w przepływie pracy.

### <a name="activities"></a>Działania

Działanie jest konkretnym zadaniem w przepływie pracy. Tak jak skrypt składa się z jednego polecenia lub kilku poleceń, tak przepływ pracy składa się z działania lub kilku działań wykonywanych w określonej kolejności. Podczas wykonywania przepływu pracy program Windows PowerShell Workflow automatycznie konwertuje wiele spośród poleceń cmdlet środowiska Windows PowerShell na działania. Po określeniu jednego z tych poleceń cmdlet w żyłaku odpowiednie działanie jest uruchamiane przez program Windows Workflow Foundation. W przypadku tych poleceń cmdlet bez odpowiedniego działania przepływ pracy programu Windows PowerShell automatycznie uruchamia polecenie cmdlet w ramach działania [InlineScript.](#inlinescript) Istnieje zestaw poleceń cmdlet, które są wykluczone i nie mogą być używane w przepływie pracy, chyba że jawnie dołączyć je do bloku InlineScript. Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Korzystanie z działań w przepływach pracy skryptów](https://technet.microsoft.com/library/jj574194.aspx).

Działania przepływów pracy dzielą zestaw wspólnych parametrów konfigurujących ich pracę. Aby uzyskać szczegółowe informacje na temat typowych parametrów przepływu pracy, zobacz [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parametry pozycyjne

Nie można używać parametrów pozycyjnych z działaniami i poleceniami cmdlet w przepływie pracy.  Wszystko to oznacza, że należy używać nazw parametrów.

Na przykład należy wziąć pod uwagę następujący kod, który pobiera wszystkie uruchomione usługi.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Jeśli spróbujesz uruchomić ten sam kod w przepływie pracy, zostanie wyświetlony komunikat w stylu "Nie można rozpoznać zestawu parametrów przy użyciu określonych nazwanych parametrów".  Aby to poprawić, podaj nazwę parametru, jak w poniższej.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Obiekty deserialne

Obiekty w przepływach pracy są deserializowane.  Oznacza to, że ich właściwości są nadal dostępne, ale nie ich metody.  Rozważmy na przykład następujący kod programu PowerShell, który zatrzymuje usługę przy użyciu Stop metody Service obiektu.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Jeśli spróbujesz uruchomić to w przepływie pracy, zostanie wyświetlony komunikat "Wywołanie metody nie jest obsługiwane w przepływie pracy programu Windows PowerShell".

Jedną z opcji jest zawijanie tych dwóch wierszy kodu w bloku [InlineScript,](#inlinescript) w którym to przypadku $Service będzie obiektem usługi w bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Inną opcją jest użycie innego polecenia cmdlet, który wykonuje taką samą funkcjonalność jak metoda, jeśli jest dostępna.  W naszym przykładzie polecenie cmdlet stop-service zapewnia taką samą funkcjonalność jak Stop metody i można użyć następujących dla przepływu pracy.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

Działanie`InlineScript` jest przydatne, gdy trzeba uruchomić jedno lub więcej poleceń jako tradycyjny skrypt programu PowerShell zamiast przepływu pracy programu PowerShell.  Podczas gdy polecenia zawarte w przepływie pracy są wysyłane do przetwarzania w programie Windows Workflow Foundation, polecenia umieszczone w bloku InlineScript są przetwarzane w środowisku Windows PowerShell.

InlineScript używa następującej składni pokazanej poniżej.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Dane wyjściowe można zwrócić z języka InlineScript, przypisując dane wyjściowe do zmiennej. Poniższy przykład zatrzymuje usługę, a następnie wyprowadza nazwę usługi.

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

Wartości można przekazywać do bloku InlineScript, ale należy użyć **modyfikatora $Using** zakresu.  Poniższy przykład jest identyczny z poprzednim przykładem, z tą różnicą, że nazwa usługi jest dostarczana przez zmienną.

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

Chociaż działania inlinescript mogą mieć kluczowe znaczenie w niektórych przepływach pracy, ale nie obsługują konstrukcji przepływu pracy i powinny być używane tylko wtedy, gdy jest to konieczne z następujących powodów:

* Nie można używać [punktów kontrolnych](#checkpoints) wewnątrz bloku InlineScript. Jeśli wystąpi błąd w bloku, należy wznowić od początku bloku.
* Nie można używać [wykonywania równoległego](#parallel-processing) wewnątrz inlinescriptblock.
* InlineScript wpływa na skalowalność przepływu pracy, ponieważ przechowuje sesję programu Windows PowerShell przez całą długość bloku InlineScript.

Aby uzyskać więcej informacji na temat korzystania z języka InlineScript, zobacz [Uruchamianie poleceń programu Windows PowerShell w przepływie pracy](https://technet.microsoft.com/library/jj574197.aspx) i [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Przetwarzanie równoległe

Jedną z korzyści wynikających z używania przepływów pracy w środowisku Windows PowerShell jest możliwość wykonywania zestawów poleceń równolegle, w przeciwieństwie do wykonywania sekwencyjnego, jak w typowym skrypcie.

Za pomocą `Parallel` słowa kluczowego można utworzyć blok skryptu z wieloma poleceniami, które są uruchamiane jednocześnie. Spowoduje to użycie następującej składni pokazanej poniżej. W takim przypadku Activity1 i Activity2 rozpoczyna się w tym samym czasie. Działanie3 rozpoczyna się dopiero po zakończeniu działania1 i activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Rozważmy na przykład następujące polecenia programu PowerShell, które kopiują wiele plików do miejsca docelowego sieci.  Polecenia te są uruchamiane sekwencyjnie, dzięki czemu jeden plik musi zakończyć kopiowanie przed rozpoczęciem następnego.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Następujący przepływ pracy uruchamia te same polecenia równolegle, dzięki czemu wszystkie one zaczynają kopiować w tym samym czasie.  Dopiero po skopiowaniu wszystkich jest wyświetlany komunikat zakończenia.

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

Konstruowanie `ForEach -Parallel` służy do przetwarzania poleceń dla każdego elementu w kolekcji jednocześnie. Elementy w kolekcji są przetwarzane współbieżnie, podczas gdy polecenia w bloku skryptu są wykonywane sekwencyjnie. Spowoduje to użycie następującej składni pokazanej poniżej. W takim przypadku Activity1 rozpoczyna się w tym samym czasie dla wszystkich elementów w kolekcji. Dla każdego elementu działanie2 rozpoczyna się po zakończeniu działania1. Działanie3 rozpoczyna się dopiero po zakończeniu działania1 i activity2 dla wszystkich elementów. Używamy parametru, `ThrottleLimit` aby ograniczyć równoległość. Zbyt wysoka `ThrottleLimit` może powodować problemy. Idealna wartość parametru `ThrottleLimit` zależy od wielu czynników w środowisku. Należy spróbować rozpocząć od niskiej wartości i spróbuj różnych wartości zwiększających, aż znajdziesz taki, który działa dla danej okoliczności.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Poniższy przykład jest podobny do poprzedniego przykładu kopiowania plików równolegle.  W takim przypadku komunikat jest wyświetlany dla każdego pliku po jego skopiowaniu.  Dopiero po ich całkowitym skopiowaniu wyświetlany jest komunikat o zakończeniu końcowego.

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
> Nie zaleca się uruchamiania obrażeń śunkowych podrzędnych równolegle, ponieważ wykazano, że daje to niewiarygodne wyniki. Dane wyjściowe z podrzędnego elementu runbook czasami nie są wyświetlane, a ustawienia w jednym podrzędnym niuacie podrzędnym mogą mieć wpływ na inne równoległe podrzędne elementu runbook. Zmienne, takie jak $VerbosePreference, $WarningPreference i inne nie mogą być propagowane do śmigiełców podrzędnych. A jeśli element runbook podrzędny zmieni te wartości, mogą nie zostać poprawnie przywrócone po wywołaniu.

## <a name="checkpoints"></a>Punkty kontrolne

Punkt *kontrolny* jest migawką bieżącego stanu przepływu pracy, który zawiera bieżącą wartość dla zmiennych i wszelkie dane wyjściowe generowane do tego punktu. Jeśli przepływ pracy kończy się błędem lub jest zawieszony, a następnie przy następnym uruchomieniu rozpocznie się od ostatniego punktu kontrolnego zamiast rozpoczęcia przepływu pracy.  Można ustawić punkt kontrolny w przepływie pracy z działaniem. `Checkpoint-Workflow` Usługa Azure Automation ma funkcję o nazwie [fair share](automation-runbook-execution.md#fair-share), gdzie każdy element runbook, który działa przez 3 godziny jest zwalniany, aby umożliwić uruchamianie innych elementów runbook. Ostatecznie niechładowany projekt runbook zostanie ponownie załadowany, a gdy jest, wznowi wykonywanie z ostatniego punktu kontrolnego podjęte w życiorysie. Aby zagwarantować, że projekt runbook zostanie ostatecznie ukończony, należy dodać punkty kontrolne w odstępach czasu, które są uruchamiane przez mniej niż 3 godziny. Jeśli podczas każdego uruchomienia zostanie dodany nowy punkt kontrolny, a element runbook zostanie eksmitowany po 3 godzinach z powodu błędu, element runbook zostanie wznowiony przez czas nieokreślony.

W poniższym przykładowym kodzie wyjątek występuje po Aktywność2 powodując przepływ pracy do końca. Gdy przepływ pracy jest uruchamiany ponownie, rozpoczyna się od uruchomienia Activity2, ponieważ było to tuż po ostatnim zestawie punktów kontrolnych.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Należy ustawić punkty kontrolne w przepływie pracy po działaniach, które mogą być podatne na wyjątek i nie powinny być powtarzane, jeśli przepływ pracy zostanie wznowiony. Na przykład przepływ pracy może utworzyć maszynę wirtualną. Punkt kontrolny można ustawić przed poleceniami tworzącymi maszynę wirtualną i po nich. Jeśli tworzenie zakończy się niepowodzeniem, polecenia zostaną powtórzone, jeśli przepływ pracy zostanie uruchomiony ponownie. Jeśli przepływ pracy zakończy się niepowodzeniem po pomyślnym utworzeniu, maszyna wirtualna nie zostanie utworzona ponownie po wznowieniu przepływu pracy.

Poniższy przykład kopiuje wiele plików do lokalizacji sieciowej i ustawia punkt kontrolny po każdym pliku.  Jeśli lokalizacja sieciowa zostanie utracona, przepływ pracy zakończy się błędem.  Po ponownym uruchomieniu zostanie wznowione w ostatnim punkcie kontrolnym, co oznacza, że tylko pliki, które zostały już skopiowane, zostaną pominięte.

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

Ponieważ poświadczenia nazwy użytkownika nie są zachowywane po wywołaniu działania [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) lub po ostatnim punkcie kontrolnym, należy ustawić `Suspend-Workflow` poświadczenia na wartość null, a następnie pobrać je ponownie z magazynu zasobów po lub punkt kontrolny jest wywoływany.  W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Poniższy ten sam kod pokazuje, jak radzić sobie z tym w elementów runbook przepływu pracy programu PowerShell.

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
> W przypadku niegraficzowych wiązków czągo programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` aliasów [connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.


Nie jest to wymagane, jeśli uwierzytelniasz się przy użyciu konta Uruchom jako skonfigurowanego z jednostką usługi.

Aby uzyskać więcej informacji na temat punktów kontrolnych, zobacz [Dodawanie punktów kontrolnych do przepływu pracy skryptu](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell workflow runbook](automation-first-runbook-textual.md)

