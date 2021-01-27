---
title: Uruchamianie elementu runbook w usłudze Azure Automation
description: W tym artykule opisano sposób uruchamiania elementu Runbook w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b5c5166785ad8c82c114fb7193cd49716536b408
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896600"
---
# <a name="start-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook w usłudze Azure Automation

Poniższa tabela pomaga określić metodę uruchamiania elementu Runbook w Azure Automation, który jest najbardziej odpowiedni dla konkretnego scenariusza. Ten artykuł zawiera szczegółowe informacje na temat uruchamiania elementu Runbook za pomocą Azure Portal i programu Windows PowerShell. Szczegółowe informacje na temat innych metod znajdują się w innych dokumentach, do których można uzyskać dostęp z poniższych linków.

| **Metoda** | **Właściwości** |
| --- | --- |
| [Witryna Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Najprostsza metoda z interakcyjnym interfejsem użytkownika.<br> <li>Formularz, aby zapewnić proste wartości parametrów.<br> <li>Łatwe śledzenie stanu zadania.<br> <li>Dostęp uwierzytelniany przy użyciu logowania do platformy Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Wywoływanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell.<br> <li>Może zostać uwzględniony w funkcji automatycznej z wieloma krokami.<br> <li>Żądanie zostało uwierzytelnione z certyfikatem lub podmiotem zabezpieczeń użytkownika OAuth/jednostką usługi.<br> <li>Podaj proste i złożone wartości parametrów.<br> <li>Śledzenie stanu zadania.<br> <li>Klient wymagany do obsługi poleceń cmdlet programu PowerShell. |
| [Interfejs API Azure Automation](/rest/api/automation/) |<li>Najbardziej elastyczna metoda, ale również najbardziej złożona.<br> <li>Wywoływanie z dowolnego niestandardowego kodu, który może wykonywać żądania HTTP.<br> <li>Zażądaj uwierzytelnienia za pomocą certyfikatu lub podmiotu zabezpieczeń uwierzytelniania OAuth/nazwy głównej usługi.<br> <li>Podaj proste i złożone wartości parametrów. *Jeśli wywołujesz element Runbook języka Python przy użyciu interfejsu API, ładunek JSON musi być serializowany.*<br> <li>Śledzenie stanu zadania. |
| [Elementy webhook](automation-webhooks.md) |<li>Uruchom element Runbook z pojedynczego żądania HTTP.<br> <li>Uwierzytelniono z tokenem zabezpieczającym w adresie URL.<br> <li>Klient nie może przesłonić wartości parametrów określonych podczas tworzenia elementu webhook. Element Runbook może zdefiniować pojedynczy parametr, który jest wypełniany informacjami o żądaniu HTTP.<br> <li>Brak możliwości śledzenia stanu zadania przy użyciu adresu URL elementu webhook. |
| [Odpowiedz na alert platformy Azure](../azure-monitor/platform/alerts-overview.md) |<li>Uruchom element Runbook w odpowiedzi na alert platformy Azure.<br> <li>Skonfiguruj element webhook dla elementu Runbook i link do alertu.<br> <li>Uwierzytelniono z tokenem zabezpieczającym w adresie URL. |
| [Zaplanuj](./shared-resources/schedules.md) |<li>Automatyczne uruchamianie elementu Runbook w harmonogramie co godzinę, codziennie, co tydzień lub co miesiąc.<br> <li>Manipulowanie harmonogramem za pomocą Azure Portal, poleceń cmdlet programu PowerShell lub interfejsu API platformy Azure.<br> <li>Podaj wartości parametrów, które mają być używane z harmonogramem. |
| [Z innego elementu Runbook](automation-child-runbooks.md) |<li>Użyj elementu Runbook jako działania w innym elemencie Runbook.<br> <li>Przydatne w przypadku funkcji używanych przez wiele elementów Runbook.<br> <li>Podaj wartości parametrów do podrzędnego elementu Runbook i Użyj danych wyjściowych w nadrzędnym elemencie Runbook. |

Na poniższej ilustracji przedstawiono szczegółowy proces krok po kroku w cyklu życia elementu Runbook. Obejmuje różne sposoby uruchamiania elementu Runbook w Azure Automation, które składniki wymagane dla hybrydowego procesu roboczego elementu Runbook wykonują Azure Automation elementy Runbook i interakcje między różnymi składnikami. Aby dowiedzieć się więcej o wykonywaniu elementów Runbook usługi Automation w centrum danych, zobacz [hybrydowe procesy robocze elementów Runbook](automation-hybrid-runbook-worker.md) .

![Architektura elementu Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Współpraca z parametrami elementu Runbook

Po uruchomieniu elementu Runbook z Azure Portal lub Windows PowerShell instrukcja jest wysyłana za pośrednictwem usługi sieci Web Azure Automation. Ta usługa nie obsługuje parametrów o złożonych typach danych. Jeśli trzeba podać wartość dla parametru złożonego, należy wywołać go z innego elementu Runbook, zgodnie z opisem w [podrzędnych elementach Runbook w Azure Automation](automation-child-runbooks.md).

Usługa sieci Web Azure Automation oferuje specjalne funkcje parametrów przy użyciu określonych typów danych, zgodnie z opisem w poniższych sekcjach:

### <a name="named-values"></a>Nazwane wartości

Jeśli parametr jest typem danych [Object], wówczas można użyć następującego formatu JSON do wysłania listy nazwanych wartości: *{Name1: "wartość1", NAME2: "wartość2", nazwa3: "wartość3"}*. Te wartości muszą być typu prostego. Element Runbook odbiera parametr jako [parametr PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) z właściwościami odpowiadającymi każdej nazwanej wartości.

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

Wynikiem tego są następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice

Jeśli parametr jest tablicą, taką jak [Array] lub [String []], wówczas można użyć następującego formatu JSON do wysłania listy wartości: *[wartość1, wartość2, Wartość3]*. Te wartości muszą być typu prostego.

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

Wynikiem tego są następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia

Jeśli parametr jest typem danych `PSCredential` , można podać nazwę [zasobu poświadczenia](./shared-resources/credentials.md)Azure Automation. Element Runbook pobierze poświadczenie z określoną nazwą. Następujący testowy element Runbook akceptuje parametr o nazwie `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Następujący tekst może zostać użyty jako parametr użytkownika przy założeniu, że został wywołany zasób poświadczenia `My Credential` .

```input
My Credential
```

Przy założeniu, że nazwa użytkownika w poświadczeniu to `jsmith` , wyświetlane są następujące dane wyjściowe.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Uruchamianie elementu Runbook za pomocą Azure Portal

1. W Azure Portal wybierz pozycję **Automatyzacja** , a następnie kliknij nazwę konta usługi Automation.
2. W menu centrum wybierz pozycję **elementy Runbook**.
3. Na stronie elementy Runbook wybierz element Runbook, a następnie kliknij przycisk **Uruchom**.
4. Jeśli element Runbook ma parametry, zostanie wyświetlony monit o podanie wartości pola tekstowego dla każdego parametru. Aby uzyskać więcej informacji na temat parametrów, zobacz [Runbook Parameters](#work-with-runbook-parameters).
5. W okienku zadania można wyświetlić stan zadania elementu Runbook.

## <a name="start-a-runbook-with-powershell"></a>Uruchamianie elementu Runbook za pomocą programu PowerShell

Aby uruchomić element Runbook za pomocą programu Windows PowerShell, można użyć polecenia [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) . Następujący przykładowy kod uruchamia element Runbook o nazwie **test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` zwraca obiekt zadania, którego można użyć do śledzenia stanu po rozpoczęciu elementu Runbook. Następnie można użyć tego obiektu zadania z poleceniem [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) , aby określić stan zadania i [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) , aby pobrać dane wyjściowe. W poniższym przykładzie jest uruchamiany element Runbook o nazwie **test-Runbook**, czeka na zakończenie, a następnie wyświetla dane wyjściowe.

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

Jeśli element Runbook wymaga parametrów, należy podać je jako [tablicę skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Klucz tablicy skrótów musi być zgodny z nazwą parametru, a wartość jest wartością parametru. Poniższy przykład przedstawia sposób uruchamiania elementu Runbook z dwoma parametrami będącymi ciągami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji na temat parametrów, zobacz [Runbook Parameters](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat zarządzania elementami Runbook, zobacz [Zarządzanie elementami Runbook w programie Azure Automation](manage-runbooks.md).
* Aby uzyskać szczegółowe informacje dotyczące programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby rozwiązać problemy z wykonywaniem elementu Runbook, zobacz [Rozwiązywanie](troubleshoot/runbooks.md)problemów z elementem Runbook.
