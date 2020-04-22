---
title: Zarządzanie harmonogramami w usłudze Azure Automation
description: Harmonogramy automatyzacji są używane do planowania uruchomieniu w usłudze Azure Automation, aby uruchomić automatycznie. W tym artykule opisano sposób tworzenia harmonogramu i zarządzania nim, tak aby można było automatycznie uruchomić księgę runbook w określonym czasie lub zgodnie z harmonogramem cyklicznym.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732794"
---
# <a name="manage-schedules-in-azure-automation"></a>Zarządzanie harmonogramami w usłudze Azure Automation

Aby zaplanować uruchomienie systemu Runbook w usłudze Azure Automation o określonej godzinie, należy połączyć go z co najmniej jednym harmonogramem. Harmonogram można skonfigurować tak, aby uruchamiał się raz lub w powtarzającym się harmonogramie godzinowym lub dziennym dla śmięty uruchomieniu w witrynie Azure portal. Można je również zaplanować na tygodniowe, miesięczne, określone dni tygodnia lub dni miesiąca lub określonego dnia miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Harmonogramy nie obsługują obecnie konfiguracji usługi Azure Automation DSC.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell

Polecenia cmdlet w poniższej tabeli są używane do tworzenia harmonogramów i zarządzania nimi za pomocą programu PowerShell w usłudze Azure Automation. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Pobiera harmonogram. |
| [Książka Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Pobiera zaplanowane runbooki. |
| [Nowy-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Tworzy nowy harmonogram. |
| [Zarejestruj się-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Kojarzy runbook z harmonogramem. |
| [Usuń-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Usuwa harmonogram. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Ustawia właściwości istniejącego harmonogramu. |
| [Wyrejestrowanie-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Odłącza runbook od harmonogramu. |

## <a name="creating-a-schedule"></a>Tworzenie harmonogramu

Można utworzyć nowy harmonogram dla uruchomieniu w witrynie Azure portal lub za pomocą programu PowerShell.

> [!NOTE]
> Usługa Azure Automation używa najnowszych modułów na koncie automatyzacji po uruchomieniu nowego zaplanowanego zadania.  Aby uniknąć wpływu na elementy runbook i procesy, które automatyzują, należy najpierw przetestować wszystkie elementy runbook, które mają połączone harmonogramy z kontem automatyzacji przeznaczonym do testowania.  To sprawdza, czy zaplanowane runbooki nadal działają poprawnie, a jeśli nie, można dalej rozwiązywać problemy i stosować wszelkie zmiany wymagane przed migracją zaktualizowanej wersji likścień likwierkacza do produkcji.
> Konto automatyzacji nie otrzymuje automatycznie żadnych nowych wersji modułów, chyba że zaktualizowano je ręcznie, wybierając opcję [Aktualizuj moduły platformy Azure](../automation-update-azure-modules.md) z **modułów**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Tworzenie nowego harmonogramu w witrynie Azure portal

1. W witrynie Azure portal z konta automatyzacji wybierz pozycję **Harmonogramy** w sekcji **Zasoby udostępnione** po lewej stronie.
2. Kliknij **pozycję Dodaj harmonogram** u góry strony.
3. W okienku Nowy harmonogram wpisz nazwę i opcjonalnie opis nowego harmonogramu.
4. O tym, czy harmonogram jest uruchamiany jeden raz, czy w harmonogramie powtarzającym się, wybierz opcję **Raz** czy **Cyklicznie**. Jeśli wybierzesz **once**, określ godzinę rozpoczęcia, a następnie kliknij przycisk **Utwórz**. Jeśli wybierzesz **opcję Cykliczne,** określ godzinę rozpoczęcia. W przypadku **powtarzania co**, wybierz, jak często ma się powtarzać program runbook — według godziny, dnia, tygodnia lub miesiąca.
    1. Jeśli wybierzesz **tydzień,** dni tygodnia są prezentowane do wyboru. Wybierz dowolną liczbę dni. Pierwszy bieg harmonogramu odbędzie się pierwszego dnia wybranego po godzinie rozpoczęcia. Na przykład, aby wybrać harmonogram weekendowy, wybierz sobotę i niedzielę. 
    
       ![Ustawianie harmonogramu cyklicznego weekendu](../media/schedules/week-end-weekly-recurrence.png)

    2. Jeśli wybierzesz **miesiąc,** masz różne opcje. W przypadku opcji **Wystąpienia miesięczne** wybierz opcję **Dni miesiąca** lub **Dni tygodnia**. Jeśli wybierzesz **Miesiąc dni,** zostanie wyświetlony kalendarz, który pozwoli wybrać dowolną liczbę dni. Jeśli wybierzesz datę, taką jak 31, która nie występuje w bieżącym miesiącu, harmonogram nie zostanie uruchomiony. Jeśli chcesz, aby harmonogram był uruchamiany ostatniego dnia, wybierz pozycję **Tak** w obszarze **Uruchom ostatni dzień miesiąca**. Jeśli wybierzesz **dni tygodnia,** zostanie wyświetlona opcja **Powtórz każdy.** Wybierz **pierwszy,** **drugi,** **trzeci,** **czwarty**lub **ostatni**. Na koniec wybierz dzień, aby powtórzyć.

       ![Miesięczny harmonogram pierwszego, piętnastego i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu kliknij przycisk **Utwórz**.

### <a name="create-a-new-schedule-with-powershell"></a>Tworzenie nowego harmonogramu za pomocą programu PowerShell

Użyj polecenia cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) do tworzenia harmonogramów. Należy określić godzinę rozpoczęcia dla harmonogramu i częstotliwość, którą powinien uruchomić. Poniższe przykłady pokazują, jak utworzyć wiele różnych scenariuszy harmonogramu.

#### <a name="create-a-one-time-schedule"></a>Tworzenie harmonogramu jednorazowego

Poniższe przykładowe polecenia tworzą harmonogram jednorazowy.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Tworzenie harmonogramu cyklicznego

W poniższym przykładzie pokazano, jak utworzyć harmonogram cykliczny, który jest uruchamiany codziennie o godzinie 13:00 przez rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie tygodniowego harmonogramu cyklicznego

W poniższym przykładzie pokazano, jak utworzyć harmonogram tygodniowy, który działa tylko w dni powszednie.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie tygodniowego harmonogramu cyklicznego dla weekendów

Poniższe przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy, który działa tylko w weekendy.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Tworzenie harmonogramu cyklicznego dla pierwszych, 15 i ostatnich dni miesiąca

W poniższym przykładzie pokazano, jak utworzyć harmonogram cykliczny, który działa na 1, 15 i ostatni dzień miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Łączenie harmonogramu z runbookiem

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli system runbook ma parametry, można podać dla nich wartości. Należy podać wartości dla wszystkich parametrów obowiązkowych i może zawierać wartości dla wszystkich parametrów opcjonalnych. Te wartości są używane za każdym razem, gdy projekt runbook jest uruchamiany przez ten harmonogram. Ten sam projekt runbook można dołączyć do innego harmonogramu i określić różne wartości parametrów.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Łączenie harmonogramu z zestawem runbook za pomocą witryny Azure portal

1. W witrynie Azure portal z konta automatyzacji wybierz pozycję **Runbooks** w obszarze **Automatyzacja procesów**.
2. Kliknij nazwę elementu Runbook do zaplanowania.
3. Jeśli element runbook nie jest obecnie połączony z harmonogramem, zostanie zaoferowana opcja utworzenia nowego harmonogramu lub łącza do istniejącego harmonogramu.
4. Jeśli element runbook ma parametry, można wybrać opcję **Modyfikuj ustawienia uruchamiania (domyślna:Azure)** i zostanie wyświetlone okienko Parametry. Informacje o parametrach można wprowadzić tutaj.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Łączenie harmonogramu z programem Runbook za pomocą programu PowerShell

Użyj polecenia cmdlet [Register-AzAutomationScheduledRunbook,](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) aby połączyć harmonogram. Można określić wartości parametrów elementu Runbook za pomocą parametru Parametry . Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [Uruchamianie uruchomieniu księgi uruchomieniu w usłudze Azure Automation](../automation-starting-a-runbook.md).
W poniższym przykładzie pokazano, jak połączyć harmonogram z elementem runbook przy użyciu polecenia cmdlet usługi Azure Resource Manager z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Planowanie biegania do częstszego uruchamiania

Najczęściej interwał harmonogram w usłudze Azure Automation można skonfigurować dla jest jedną godzinę. Jeśli harmonogramy wymagają wykonywania częściej niż to, istnieją dwie opcje:

* Utwórz element [webhook](../automation-webhooks.md) dla elementu runbook i użyj [usługi Azure Logic Apps,](../../logic-apps/logic-apps-overview.md) aby wywołać element webhook. Usługa Azure Logic Apps zapewnia bardziej szczegółowe ziarnistość podczas definiowania harmonogramu.

* Utwórz cztery harmonogramy, które rozpoczynają się w ciągu 15 minut od siebie uruchomionego raz na godzinę. W tym scenariuszu umożliwia uruchamianie egobooka co 15 minut z różnymi harmonogramami.

## <a name="disabling-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu każdy schemat runbook połączony z nim nie działa już zgodnie z tym harmonogramem. Harmonogram można ręcznie wyłączyć lub ustawić czas wygaśnięcia dla harmonogramów z częstotliwością podczas ich tworzenia. Po osiągnięciu czasu wygaśnięcia harmonogram jest wyłączony.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Wyłączanie harmonogramu z witryny Azure portal

1. Na koncie automatyzacji wybierz pozycję **Harmonogramy** w obszarze **Zasoby udostępnione**.
2. Kliknij nazwę harmonogramu, aby otworzyć okienko szczegółów.
3. Zmień **włączono** na **Nie**.

> [!NOTE]
> Jeśli chcesz wyłączyć harmonogram, który ma czas rozpoczęcia w przeszłości, należy zmienić datę rozpoczęcia na godzinę w przyszłości przed zapisaniem go.

### <a name="disable-a-schedule-with-powershell"></a>Wyłączanie harmonogramu za pomocą programu PowerShell

Użyj polecenia cmdlet [Set-AzAutomationSchedule,](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) aby zmienić właściwości istniejącego harmonogramu. Aby wyłączyć harmonogram, należy `IsEnabled` określić wartość False dla parametru.

W poniższym przykładzie pokazano, jak wyłączyć harmonogram dla zestawu runbook przy użyciu polecenia cmdlet usługi Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z podręcznikami runbook w usłudze Azure Automation, zobacz [Uruchamianie uruchomieniu księgi ą w usłudze Azure Automation.](../automation-starting-a-runbook.md)