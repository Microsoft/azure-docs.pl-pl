---
title: Uruchamianie elementu runbook w usłudze Azure Automation
description: W tym artykule opisano sposób uruchamiania runbook w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01b6e060fcab9c7dab4934aad3d1ab6047ec5236
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829858"
---
# <a name="start-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook w usłudze Azure Automation

W poniższej tabeli przedstawiono metodę uruchamiania w programie Azure Automation, która jest najbardziej odpowiednia dla danego scenariusza. Ten artykuł zawiera szczegółowe informacje na temat uruchamiania runbook za pomocą Azure Portal i Windows PowerShell. Szczegółowe informacje na temat innych metod znajdują się w innej dokumentacji, do których można uzyskać dostęp za pomocą poniższych linków.

| **Metoda** | **Właściwości** |
| --- | --- |
| [Witryna Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Najprostsza metoda z interaktywnym interfejsem użytkownika.<br> <li>Formularz, aby podać proste wartości parametrów.<br> <li>Łatwe śledzenie stanu zadania.<br> <li>Dostęp uwierzytelniony przy użyciu logowania na platformie Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Wywołaj polecenie z wiersza polecenia Windows PowerShell polecenia cmdlet.<br> <li>Może być uwzględniony w funkcji zautomatyzowanej z wieloma krokami.<br> <li>Żądanie jest uwierzytelniane przy użyciu certyfikatu lub jednostki użytkownika/jednostki usługi OAuth.<br> <li>Podaj proste i złożone wartości parametrów.<br> <li>Śledzenie stanu zadania.<br> <li>Klient wymagany do obsługi poleceń cmdlet programu PowerShell. |
| [Azure Automation API](/rest/api/automation/) |<li>Najbardziej elastyczna metoda, ale również najbardziej złożona.<br> <li>Wywołaj z dowolnego kodu niestandardowego, który może tworzyć żądania HTTP.<br> <li>Zażądaj uwierzytelnienia za pomocą certyfikatu lub jednostki użytkownika/jednostki usługi OAuth.<br> <li>Podaj proste i złożone wartości parametrów. *W przypadku wywoływania podręcznika Runbook języka Python przy użyciu interfejsu API ładunek JSON musi być serializowany.*<br> <li>Śledzenie stanu zadania. |
| [Elementy webhook](automation-webhooks.md) |<li>Uruchom runbook z pojedynczego żądania HTTP.<br> <li>Uwierzytelnione przy użyciu tokenu zabezpieczającego w adresie URL.<br> <li>Klient nie może zastąpić wartości parametrów określonych podczas tworzenia webhook. Runbook może definiować pojedynczy parametr, który jest wypełniany szczegółami żądania HTTP.<br> <li>Brak możliwości śledzenia stanu zadania za pomocą adresu URL webhook. |
| [Odpowiadanie na alert platformy Azure](../azure-monitor/alerts/alerts-overview.md) |<li>Uruchom runbook w odpowiedzi na alert platformy Azure.<br> <li>Konfigurowanie urządzenia webhook dla runbook i link do alertu.<br> <li>Uwierzytelnione przy użyciu tokenu zabezpieczającego w adresie URL. |
| [Zaplanuj](./shared-resources/schedules.md) |<li>Automatycznie uruchamiaj program Runbook zgodnie z harmonogramem godzinowym, dziennym, tygodniowym lub miesięcznym.<br> <li>Manipuluj harmonogramem za Azure Portal, poleceniami cmdlet programu PowerShell lub interfejsem API platformy Azure.<br> <li>Podaj wartości parametrów, które mają być używane z harmonogramem. |
| [Z innego runbook](automation-child-runbooks.md) |<li>Użyj runbook jako działania w innym elementie Runbook.<br> <li>Przydatne w przypadku funkcji używanych przez wiele elementów Runbook.<br> <li>Podaj wartości parametrów podrzędnego elementu Runbook i użyj danych wyjściowych w nadrzędnym elementie Runbook. |

Na poniższej ilustracji przedstawiono szczegółowy proces krok po kroku w cyklu życia runbook. Zawiera on różne sposoby uruchamiania elementów Runbook w programie Azure Automation, które składniki wymagane przez hybrydowy proces roboczy runbook do wykonywania Azure Automation runbook i interakcji między różnymi składnikami. Aby dowiedzieć się więcej na temat wykonywania runbook usługi Automation w centrum danych, zapoznaj się z [hybrydowymi procesami pracy runbook](automation-hybrid-runbook-worker.md)

![Architektura runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Praca z parametrami runbook

Po uruchomieniu runbook z Azure Portal lub Windows PowerShell instrukcja jest wysyłana za pośrednictwem Azure Automation internetowej. Ta usługa nie obsługuje parametrów ze złożonymi typami danych. Jeśli musisz podać wartość złożonego parametru, musisz wywołać ją w tekście z innego podręcznika Runbook, jak opisano w tece Podrzędne podręczniki [runbook](automation-child-runbooks.md)w Azure Automation .

Usługa Azure Automation udostępnia specjalne funkcje dla parametrów korzystających z niektórych typów danych, zgodnie z opisem w poniższych sekcjach:

### <a name="named-values"></a>Nazwane wartości

Jeśli parametr ma typ danych [object], możesz użyć następującego formatu JSON, aby wysłać do niego listę nazwanych wartości: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Te wartości muszą być typu prostego. Ten parametr jest odbierany jako parametr [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) z właściwościami, które odpowiadają każdej nazwanej wartości.

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

W wyniku tego zostaną uzyskane następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice

Jeśli parametr jest tablicą, taką jak [array] lub [string[]], możesz użyć następującego formatu JSON, aby wysłać do niego listę wartości: *[Value1, Value2, Value3]*. Te wartości muszą być typu prostego.

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

W wyniku tego zostaną uzyskane następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia

Jeśli parametr ma typ danych , możesz podać `PSCredential` nazwę zasobu Azure Automation [poświadczenia.](./shared-resources/credentials.md) Ten runbook pobiera poświadczenia o imieniu, które określisz. Poniższy testowy runbook akceptuje parametr o nazwie `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Dla parametru użytkownika można użyć następującego tekstu przy założeniu, że istnieje zasób poświadczeń o nazwie `My Credential` .

```input
My Credential
```

Przy założeniu, że nazwa użytkownika w poświadczeniu to `jsmith` , zostaną wyświetlone następujące dane wyjściowe.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Uruchamianie runbook przy użyciu Azure Portal

1. W Azure Portal wybierz pozycję **Automation,** a następnie kliknij nazwę konta usługi Automation.
2. W menu Centrum wybierz pozycję **Elementy Runbook.**
3. Na stronie Runbook wybierz pozycję runbook, a następnie kliknij przycisk **Uruchom.**
4. Jeśli parametr runbook ma parametry, zostanie wyświetlony monit o podanie wartości z polem tekstowym dla każdego parametru. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry runbook](#work-with-runbook-parameters).
5. W okienku Zadanie można wyświetlić stan zadania runbook.

## <a name="start-a-runbook-with-powershell"></a>Uruchamianie runbook przy użyciu programu PowerShell

Możesz użyć [start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) aby uruchomić runbook z Windows PowerShell. Poniższy przykładowy kod uruchamia elementy Runbook o **nazwie Test-Runbook.**

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` zwraca obiekt zadania, za pomocą których można śledzić stan po uruchomieniu runbook. Następnie można użyć tego obiektu zadania z [get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) do określenia stanu zadania i [Get-AzAutomationJobOutput,](/powershell/module/az.automation/get-azautomationjoboutput) aby pobrać jego dane wyjściowe. Poniższy przykład uruchamia runbook o nazwie **Test-Runbook,** czeka na jego ukończenie, a następnie wyświetla jego dane wyjściowe.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Jeśli runbook wymaga parametrów, należy podać je jako [tablicę skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Klucz tablicy skrótów musi odpowiadać nazwie parametru, a wartość jest wartością parametru. Poniższy przykład przedstawia sposób uruchamiania elementu Runbook z dwoma parametrami będącymi ciągami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry runbook](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat zarządzania runbook, zobacz Manage runbook in Azure Automation (Zarządzanie [zasobami Runbook w programie Azure Automation).](manage-runbooks.md)
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz [PowerShell Docs](/powershell/scripting/overview).
* Aby rozwiązać problemy z wykonywaniem runbook, zobacz [Rozwiązywanie problemów z runbookami.](troubleshoot/runbooks.md)
