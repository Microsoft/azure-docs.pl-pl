---
title: Uruchamianie elementu runbook w usłudze Azure Automation
description: Podsumowuje różne metody, które mogą służyć do uruchamiania uruchomieniu księgi rozkładu w usłudze Azure Automation i zawiera szczegółowe informacje na temat korzystania z witryny Azure portal i programu Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7f2c0dda952959db3bffba6016f48b986016c19e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679465"
---
# <a name="start-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook w usłudze Azure Automation

Poniższa tabela ułatwia określenie metody uruchamiania elementów runbook w usłudze Azure Automation, która jest najbardziej odpowiednia dla danego scenariusza. Ten artykuł zawiera szczegółowe informacje na temat uruchamiania systemu runbook z witryny Azure portal i programu Windows PowerShell. Szczegółowe informacje na temat innych metod znajdują się w innej dokumentacji, do której można uzyskać dostęp z poniższych łączy.

| **Metoda** | **Właściwości** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Najprostsza metoda z interaktywnym interfejsem użytkownika.<br> <li>Formularz, aby zapewnić proste wartości parametrów.<br> <li>Łatwo śledź stan zadania.<br> <li>Dostęp uwierzytelniony za pomocą logowania na platformie Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Wywołanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell.<br> <li>Może być dołączony do automatycznego rozwiązania z wieloma krokami.<br> <li>Żądanie jest uwierzytelnione za pomocą certyfikatu lub jednostki zabezpieczeń użytkownika OAuth / jednostki usługi.<br> <li>Podaj proste i złożone wartości parametrów.<br> <li>Śledź stan zadania.<br> <li>Klient wymagany do obsługi poleceń cmdlet programu PowerShell. |
| [Interfejs API automatyzacji platformy Azure](/rest/api/automation/) |<li>Najbardziej elastyczna metoda, ale także najbardziej złożona.<br> <li>Zadzwoń z dowolnego kodu niestandardowego, który może tworzyć żądania HTTP.<br> <li>Żądanie uwierzytelnione z certyfikatem lub zleceniodawcą/jednostką usługi Oauth.<br> <li>Podaj proste i złożone wartości parametrów. *Jeśli wywołujesz system runbook języka Python przy użyciu interfejsu API, ładunek JSON musi być serializowany.*<br> <li>Śledź stan zadania. |
| [Elementów webhook](automation-webhooks.md) |<li>Uruchom księgę uruchomię z pojedynczego żądania HTTP.<br> <li>Uwierzytelniony za pomocą tokenu zabezpieczającego w adresie URL.<br> <li>Klient nie może zastąpić wartości parametrów określonych podczas tworzenia elementu webhook. Runbook można zdefiniować pojedynczy parametr, który jest wypełniany szczegółami żądania HTTP.<br> <li>Brak możliwości śledzenia stanu zadania za pośrednictwem adresu URL elementu webhook. |
| [Odpowiadanie na alert platformy Azure](../log-analytics/log-analytics-alerts.md) |<li>Uruchom zestaw runbook w odpowiedzi na alert platformy Azure.<br> <li>Skonfiguruj element webhook dla elementu runbook i łącze do alertu.<br> <li>Uwierzytelniony za pomocą tokenu zabezpieczającego w adresie URL. |
| [Harmonogram](automation-schedules.md) |<li>Automatycznie uruchom program runbook zgodnie z harmonogramem godzinowym, dziennym, tygodniowym lub miesięcznym.<br> <li>Manipuluj harmonogramem za pośrednictwem witryny Azure portal, poleceń cmdlet programu PowerShell lub interfejsu API platformy Azure.<br> <li>Podaj wartości parametrów, które mają być używane z harmonogramem. |
| [Z innego ekscesu](automation-child-runbooks.md) |<li>Użyj elementa runbook jako działania w innym elementem runbook.<br> <li>Przydatne dla funkcji używanych przez wiele żyła.<br> <li>Podaj wartości parametrów podrzędnej żyjącej i użyj danych wyjściowych w nadrzędnym uruchomieniu. Dasz. |

Na poniższej ilustracji przedstawiono szczegółowy proces krok po kroku w cyklu życia elementów runbook. Zawiera różne sposoby uruchamiania systemu runbook w usłudze Azure Automation, które składniki wymagane dla procesu roboczego hybrydowego systemu runbook do wykonywania elementów runbook usługi Azure Automation i interakcji między różnymi składnikami. Aby dowiedzieć się więcej o wykonywaniu śliwa śliwiania aplikacji Automation w centrum danych, zobacz [hybrydowe elementy owebookowe](automation-hybrid-runbook-worker.md)

![Architektura ekscesu](media/automation-starting-runbook/runbooks-architecture.png)

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="runbook-parameters"></a>Parametry uruchomieniu

Po uruchomieniu systemu runbook z witryny Azure portal lub programu Windows PowerShell, instrukcja jest wysyłana za pośrednictwem usługi sieci web usługi Azure Automation. Ta usługa nie obsługuje parametrów ze złożonymi typami danych. Jeśli chcesz podać wartość złożonego parametru, należy wywołać go wwłakom z innego uruchomieniu, zgodnie z opisem w [podręcznikach podrzędnych w usłudze Azure Automation.](automation-child-runbooks.md)

Usługa sieci web usługi Azure Automation zapewnia specjalne funkcje dla parametrów przy użyciu niektórych typów danych, jak opisano w następujących sekcjach:

### <a name="named-values"></a>Nazwane wartości

Jeśli parametrem jest typ danych [object], można użyć następującego formatu JSON, aby wysłać mu listę nazwanych wartości: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Te wartości muszą być typu prostego. Elementu runbook odbiera parametr jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) z właściwości, które odpowiadają każdej nazwanej wartości.

Rozważmy następujący tekstowy element Runbook, który akceptuje parametr o nazwie user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Poniższego tekstu można użyć jako parametru user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Powoduje to następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice

Jeśli parametr jest tablicą taką jak [tablica] lub [string[]], można użyć następującego formatu JSON, aby wysłać mu listę wartości: *[Value1, Value2, Value3]*. Te wartości muszą być typu prostego.

Rozważmy następujący tekstowy element Runbook, który akceptuje parametr o nazwie *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Poniższego tekstu można użyć jako parametru user.

```input
["Joe","Smith",2,true]
```

Powoduje to następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia

Jeśli parametrem jest `PSCredential`typ danych, można podać nazwę [zasobu poświadczeń](automation-credentials.md)usługi Azure Automation . Grupa runbook pobiera poświadczenia o określonej nazwie. Poniższy testowy projekt runbook `credential`akceptuje parametr o nazwie .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Poniższy tekst może służyć dla parametru użytkownika, przy `My Credential`założeniu, że istnieje zasób poświadczeń o nazwie .

```input
My Credential
```

Zakładając, że nazwa użytkownika `jsmith`w poświadczeniu jest, następujące dane wyjściowe są wyświetlane.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Uruchamianie uruchomieniu książki za pomocą witryny Azure portal

1. W witrynie Azure portal wybierz pozycję **Automatyzacja,** a następnie kliknij nazwę konta automatyzacji.
2. W menu Centrum wybierz polecenie **Elementy runbook**.
3. Na stronie Runbooks wybierz projekt runbook, a następnie kliknij przycisk **Start**.
4. Jeśli projekt runbook ma parametry, zostanie wyświetlony monit o podanie wartości z polem tekstowym dla każdego parametru. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry uruchomieniu](#runbook-parameters).
5. W okienku Zadania można wyświetlić stan zadania runbooka.

## <a name="start-a-runbook-with-powershell"></a>Uruchamianie eksmisji za pomocą programu PowerShell

Za pomocą [książki Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) można uruchomić system runbook z programem Windows PowerShell. Poniższy przykładowy kod uruchamia projekt runbook o nazwie **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`zwraca obiekt zadania, którego można użyć do śledzenia stanu po uruchomieniu śmiwalka. Następnie można użyć tego obiektu zadania z [Get-AzAutomationOb](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) do określenia stanu zadania i [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0) do pobierania jego danych wyjściowych. W poniższym przykładzie rozpoczyna się projekt runbook o nazwie **Test-Runbook**, czeka, aż został ukończony, a następnie wyświetla jego dane wyjściowe.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Jeśli projekt runbook wymaga parametrów, należy podać je jako [hashtable](https://technet.microsoft.com/library/hh847780.aspx). Klucz tabeli mieszania musi być zgodny z nazwą parametru, a wartość jest wartością parametru. Poniższy przykład przedstawia sposób uruchamiania elementu Runbook z dwoma parametrami będącymi ciągami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry uruchomieniu](#runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wykonywaniu śliwa wiązek ceł automatyzacji w centrum danych, zobacz [Hybrydowe procesy owe workers.](automation-hybrid-runbook-worker.md)
* Aby dowiedzieć się więcej na temat tworzenia modułowych elementów runbook, które mają być używane przez inne programy runbook dla określonych lub typowych funkcji, zapoznaj się z [podręcznikami podrzędnymi](automation-child-runbooks.md).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
