---
title: Tworzenie modułowych Azure Automation
description: W tym artykule opisano sposób tworzenia runbook, który jest wywoływany przez inny.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 101ff9affe43dcc97de6cf5a535c82559aafeced
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834916"
---
# <a name="create-modular-runbooks"></a>Tworzenie modułowych elementów runbook

Zaleca się pisanie modułowych elementów Runbook wielokrotnego użytku Azure Automation z dyskretną funkcją wywoływaną przez inne elementy Runbook. Nadrzędny element Runbook często wywołuje co najmniej jeden podrzędny element Runbook w celu wykonania wymaganych funkcji. 

Istnieją dwa sposoby wywołania podrzędnego runbook i istnieją odrębne różnice, które należy zrozumieć, aby móc określić, która z nich jest najlepsza dla Twoich scenariuszy. W poniższej tabeli podsumowano różnice między tymi dwoma sposobami wywołania jednego z nich.

|  | Śródwierszowo | Polecenie cmdlet |
|:--- |:--- |:--- |
| **Zadanie** |Podrzędne elementy Runbook są uruchamiane w tym samym zadaniu co element nadrzędny. |Tworzone jest osobne zadanie dla podrzędnego elementu Runbook. |
| **Wykonanie** |Przed kontynuowaniem nadrzędny element Runbook czeka na ukończenie działania podrzędnego elementu Runbook. |Nadrzędny element Runbook jest kontynuowany  natychmiast po uruchomieniu podrzędnego elementu Runbook lub poczeka na zakończenie zadania podrzędnego. |
| **Dane wyjściowe** |Nadrzędny element Runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu Runbook. |Nadrzędny element Runbook musi pobierać dane wyjściowe z zadania podrzędnego elementu *Runbook,* a nadrzędny element Runbook może bezpośrednio pobierać dane wyjściowe z podrzędnego elementu Runbook. |
| **Parametry** |Wartości parametrów podrzędnego elementu Runbook są określane oddzielnie i mogą mieć dowolny typ danych. |Wartości parametrów podrzędnego runbook muszą być połączone w jedną tablicę skrótów. Ta tabela skrótów może zawierać tylko typy danych proste, tablicowe i obiektowe, które używają serializacji JSON. |
| **Konto usługi Automation** |Nadrzędny element Runbook może używać podrzędnego elementu Runbook tylko na tym samym koncie usługi Automation. |Nadrzędne element runbook może używać podrzędnego elementu Runbook z dowolnego konta usługi Automation, z tej samej subskrypcji platformy Azure, a nawet z innej subskrypcji, z którą masz połączenie. |
| **Publikowanie** |Podrzędny element Runbook należy opublikować przed opublikowaniem nadrzędnego elementu Runbook. |Podrzędny element Runbook jest publikowany w dowolnym momencie przed uruchomieniem nadrzędnego elementu Runbook. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Wywoływanie podrzędnego podręcznika Runbook przy użyciu wykonywania wbudowanego

Aby wywołać element Runbook w tekście z innego runbook, użyj nazwy i podaj wartości jego parametrów, tak jak w przypadku działania lub polecenia cmdlet. Wszystkie podręczniki Runbook na tym samym koncie usługi Automation są dostępne dla wszystkich innych, które mogą być używane w ten sposób. Nadrzędny element Runbook czeka na ukończenie podrzędnego elementu Runbook przed przejściem do następnego wiersza, a wszystkie dane wyjściowe są zwracane bezpośrednio do elementu nadrzędnego.

Element Runbook wywoływany śródwierszowo jest uruchamiany w tym samym zadaniu co nadrzędny element Runbook. Nie ma żadnego wskazania w historii zadania podrzędnego runbook. Wszelkie wyjątki i wszelkie dane wyjściowe strumienia z podrzędnego elementu Runbook są skojarzone z elementem nadrzędnym. Takie zachowanie powoduje mniejszą liczbę zadań i ułatwia ich śledzenie i rozwiązywanie problemów.

Po opublikowaniu podręcznika Runbook wszystkie podrzędne wywołania przez niego runbook muszą już zostać opublikowane. Przyczyną jest to, Azure Automation tworzy skojarzenie z dowolnymi podrzędnym elementami Runbook podczas kompilowania element Runbook. Jeśli podrzędne element Runbook nie zostały jeszcze opublikowane, nadrzędny element Runbook wydaje się publikować prawidłowo, ale generuje wyjątek podczas jego uruchamiania. W takim przypadku można ponownie opublikować nadrzędny element Runbook, aby prawidłowo odwoływać się do podrzędnego elementu Runbook. Nie trzeba ponownie opublikować nadrzędnego elementu Runbook, jeśli jakikolwiek podrzędny element Runbook został zmieniony, ponieważ skojarzenie zostało już utworzone.

Parametry podrzędnego obiektu Runbook wywoływanego w tekście mogą mieć dowolny typ danych, w tym obiekty złożone. Nie ma serializacji [JSON](start-runbooks.md#work-with-runbook-parameters), tak jak w przypadku uruchamiania runbook przy użyciu polecenia Azure Portal lub polecenia cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Typy elementów Runbook

Które typy elementów Runbook mogą się wzajemnie wywołać?

* Element [Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i graficzny element [Runbook](automation-runbook-types.md#graphical-runbooks) mogą wywołać siebie nawzajem w tekście, ponieważ oba elementy są oparte na programie PowerShell.
* Element [Runbook przepływu](automation-runbook-types.md#powershell-workflow-runbooks) pracy programu PowerShell i graficzny element Runbook przepływu pracy programu PowerShell mogą wywołać siebie nawzajem w tekście, ponieważ oba elementy są oparte na przepływie pracy programu PowerShell.
* Typy programu PowerShell i przepływy pracy programu PowerShell nie mogą wywołać siebie nawzajem i muszą używać polecenia `Start-AzAutomationRunbook` .

Kiedy kolejność publikowania ma znaczenie?

Kolejność publikowania elementów Runbook ma znaczenie tylko dla przepływu pracy programu PowerShell i graficznych elementów Runbook przepływu pracy programu PowerShell.

Gdy element Runbook wywołuje graficzny lub podrzędny element Runbook przepływu pracy programu PowerShell przy użyciu wykonywania wbudowanego, używa nazwy tego element runbook. Nazwa musi zaczynać się od `.\\` , aby określić, że skrypt znajduje się w katalogu lokalnym.

### <a name="example"></a>Przykład

Poniższy przykład uruchamia testowy podrzędny element Runbook, który akceptuje obiekt złożony, wartość całkowitą i wartość logiczną. Dane wyjściowe podrzędnego elementu Runbook są przypisywane do zmiennej. W takim przypadku podrzędny podręcznik Runbook jest słowem runbook przepływu pracy programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Oto ten sam przykład użycia jako podrzędnego podręcznika programu PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Uruchamianie podrzędnego podręcznika Runbook przy użyciu polecenia cmdlet

> [!IMPORTANT]
> Jeśli element Runbook wywołuje podrzędny element Runbook za pomocą polecenia cmdlet z parametrem , a podrzędny element Runbook generuje wynik obiektu, operacja może `Start-AzAutomationRunbook` `Wait` napotkać błąd. Aby ominąć ten błąd, zobacz Podrzędne podręczniki [Runbook](troubleshoot/runbooks.md#child-runbook-object) z wyjściowym obiektem, aby dowiedzieć się, jak zaimplementować logikę sondowania wyników przy użyciu polecenia cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Możesz użyć programu , aby uruchomić program Runbook zgodnie z opisem w tece `Start-AzAutomationRunbook` [To start a runbook with Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Istnieją dwa tryby użycia tego polecenia cmdlet. W jednym trybie polecenie cmdlet zwraca identyfikator zadania podczas tworzenia zadania dla podrzędnego runbook. W drugim trybie, który włącza skrypt przez określenie parametru *Wait,* polecenie cmdlet czeka, aż zadanie podrzędne zakończy pracę i zwróci dane wyjściowe z podrzędnego podręcznika Runbook.

Zadanie podrzędnego elementu Runbook uruchomione za pomocą polecenia cmdlet jest uruchamiane niezależnie od nadrzędnego zadania elementu Runbook. Takie zachowanie powoduje więcej zadań niż uruchamianie wbudowanego runbook i utrudnia śledzenie zadań. Element nadrzędny może uruchomić więcej niż jeden podrzędny element Runbook asynchronicznie bez oczekiwania na ukończenie każdego z nich. W przypadku tego równoległego wykonywania wywołującego podrzędne element runbook w tekście nadrzędny element Runbook musi używać [równoległego słowa kluczowego](automation-powershell-workflow.md#use-parallel-processing).

Z powodu chronometrażu dane wyjściowe podrzędnego elementu Runbook nie są niezawodnie zwracane do nadrzędnego elementu Runbook. Ponadto zmienne, takie jak `$VerbosePreference` `$WarningPreference` , i inne, mogą nie być propagowane do podrzędnych podręczników Runbook. Aby uniknąć tych problemów, możesz uruchomić podrzędne podręczniki Runbook jako oddzielne zadania automatyzacji przy użyciu `Start-AzAutomationRunbook` `Wait` parametru . Ta technika blokuje nadrzędny element Runbook do momentu ukończenia podrzędnego elementu Runbook.

Jeśli nie chcesz, aby nadrzędny element Runbook był blokowany podczas oczekiwania, możesz uruchomić podrzędny element Runbook bez `Start-AzAutomationRunbook` `Wait` parametru . W takim przypadku należy użyć [get-AzAutomationJob,](/powershell/module/az.automation/get-azautomationjob) aby zaczekać na ukończenie zadania. Do pobrania wyników należy również użyć plików [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) i [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Parametry podrzędnego podręcznika Runbook uruchomionego za pomocą polecenia cmdlet są udostępniane jako tablica skrótów, zgodnie z opisem w [tece Parametry.](start-runbooks.md#work-with-runbook-parameters) Można używać tylko prostych typów danych. Jeśli element Runbook ma parametr o złożonym typie danych, musi być wywoływany śródwierszowo.

Kontekst subskrypcji może zostać utracony podczas uruchamiania podrzędnych zadań Runbook jako oddzielnych zadań. Aby podrzędny element runbook wykonywał polecenia cmdlet modułu Az dla określonej subskrypcji platformy Azure, musi uwierzytelniać się w tej subskrypcji niezależnie od nadrzędnego elementu Runbook.

Jeśli zadania w ramach tego samego konta usługi Automation działają z więcej niż jedną subskrypcją, wybranie subskrypcji w jednym zadaniu może zmienić kontekst aktualnie wybranej subskrypcji dla innych zadań. Aby uniknąć tej sytuacji, użyj `Disable-AzContextAutosave -Scope Process` na początku każdego z nich. Ta akcja zapisuje tylko kontekst wykonywania tego runbook.

### <a name="example"></a>Przykład

Poniższy przykład uruchamia podrzędny podręcznik Runbook z parametrami, a następnie czeka na jego ukończenie przy użyciu `Start-AzAutomationRunbook` polecenia cmdlet z `Wait` parametrem . Po zakończeniu przykład zbiera dane wyjściowe polecenia cmdlet z podrzędnego podręcznika Runbook. Aby użyć `Start-AzAutomationRunbook` polecenia , skrypt musi uwierzytelnić się w subskrypcji platformy Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Następne kroki

* Aby uruchomić swój runbook, zobacz [Uruchamianie runbook w programie Azure Automation](start-runbooks.md).
* Aby uzyskać informacje na temat monitorowania operacji runbook, zobacz Runbook output and messages in Azure Automation (Dane wyjściowe i komunikaty dotyczące [runbook w programie Azure Automation).](automation-runbook-output-and-messages.md)
