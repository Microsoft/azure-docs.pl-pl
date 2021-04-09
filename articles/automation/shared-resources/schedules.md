---
title: Zarządzanie harmonogramami w Azure Automation
description: W tym artykule opisano sposób tworzenia harmonogramu i pracy z nim w Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: a829cd946f36fb5996405ba00945e9f9cb65d162
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544234"
---
# <a name="manage-schedules-in-azure-automation"></a>Zarządzanie harmonogramami w Azure Automation

Aby zaplanować uruchomienie elementu Runbook w Azure Automation o określonym czasie, należy połączyć go z co najmniej jednym harmonogramem. Harmonogram można skonfigurować do uruchamiania jednokrotnie lub cyklicznie lub codziennie harmonogramu dla elementów Runbook w Azure Portal. Można je również zaplanować co tydzień, co miesiąc, konkretne dni tygodnia lub dni miesiąca lub określony dzień miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Azure Automation obsługuje czas letni i harmonogramy odpowiednio dla operacji automatyzacji.

> [!NOTE]
> Harmonogramy obecnie nie są włączone dla Azure Automation konfiguracji DSC.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Polecenia cmdlet programu PowerShell służące do uzyskiwania dostępu do harmonogramów

Polecenia cmdlet w poniższej tabeli tworzą harmonogramy automatyzacji i zarządzaj nimi za pomocą programu PowerShell. Są one dostarczane jako część [AZ modules](modules.md#az-modules).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Pobiera harmonogram. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Pobiera zaplanowane elementy Runbook. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Tworzy nowy harmonogram. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Kojarzy element Runbook z harmonogramem. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Usuwa harmonogram. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Ustawia właściwości istniejącego harmonogramu. |
| [Unregister — AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Deskojarzenie elementu Runbook z harmonogramem. |

## <a name="create-a-schedule"></a>Tworzenie harmonogramu

Można utworzyć nowy harmonogram dla elementów Runbook z Azure Portal przy użyciu programu PowerShell lub szablonu Azure Resource Manager (ARM). Aby uniknąć wpływu na elementy Runbook i procesy, które automatyzują, należy najpierw przetestować wszystkie elementy Runbook, które mają połączone harmonogramy z kontem usługi Automation dedykowanym do testowania. Test sprawdza, czy zaplanowane elementy Runbook nadal działają poprawnie. Jeśli zobaczysz problem, możesz rozwiązać problemy i zastosować wszelkie zmiany wymagane przed przeprowadzeniem migracji zaktualizowanej wersji elementu Runbook do środowiska produkcyjnego.

> [!NOTE]
> Twoje konto usługi Automation nie otrzymuje automatycznie żadnych nowych wersji modułów, chyba że zostały zaktualizowane ręcznie, wybierając opcję [Aktualizuj moduły platformy Azure](../automation-update-azure-modules.md) z **modułów**. Azure Automation korzysta z najnowszych modułów na koncie usługi Automation, gdy zostanie uruchomione nowe zaplanowane zadanie. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Utwórz nowy harmonogram w Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **harmonogramy** w obszarze **zasoby udostępnione**.
2. Na stronie **harmonogramy** wybierz pozycję **Dodaj harmonogram**.
3. Na stronie **Nowy harmonogram** wprowadź nazwę i opcjonalnie wprowadź opis nowego harmonogramu.

    >[!NOTE]
    >Harmonogramy automatyzacji nie obsługują obecnie znaków specjalnych w nazwie harmonogramu.
    >

4. Określ, czy harmonogram ma być uruchamiany raz, czy w harmonogramie, wybierając **jeden raz** lub **cyklicznie**. Jeśli wybierzesz **jeden raz**, określ godzinę rozpoczęcia, a następnie wybierz pozycję **Utwórz**. W przypadku wybrania opcji **cyklicznie** określ godzinę rozpoczęcia. Dla opcji **Powtarzaj co** należy określić, jak często element Runbook ma być powtarzany. Wybierz według godziny, dnia, tygodnia lub miesiąca.

    * W przypadku wybrania wartości **tydzień** dni tygodnia są prezentowane w celu wybrania opcji. Wybierz dowolną liczbę dni. Pierwsze uruchomienie harmonogramu zostanie wykonane pierwszego dnia wybranego po czasie rozpoczęcia. Na przykład, aby wybrać harmonogram weekendowy, wybierz soboty i niedziela.

    ![Ustawianie cyklicznego harmonogramu weekendu](../media/schedules/week-end-weekly-recurrence.png)

    * W przypadku wybrania **miesiąca** są dostępne różne opcje. Dla opcji **miesięczne wystąpienia** wybierz opcję **miesiąc** lub dni **tygodnia**. Jeśli wybierzesz **miesiąc dni**, zostanie wyświetlony kalendarz, aby można było wybrać dowolną liczbę dni. Jeśli wybierzesz datę, taką jak 31, która nie występuje w bieżącym miesiącu, harmonogram nie zostanie uruchomiony. Jeśli chcesz, aby harmonogram był uruchamiany w ostatnim dniu miesiąca, wybierz pozycję **tak** w obszarze **Uruchom w ostatnim dniu**. Jeśli wybierzesz **tydzień dni**, zostanie wyświetlona opcja **Powtarzaj każdy** z nich. Wybierz **pierwszy**, **drugi**, **trzeci**, **czwarty** lub **ostatni**. Na koniec wybierz dzień do powtórzenia.

    ![Harmonogram miesięczny pierwszego, piętnastu i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu wybierz pozycję **Utwórz**.

### <a name="create-a-new-schedule-with-powershell"></a>Tworzenie nowego harmonogramu za pomocą programu PowerShell

Użyj polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) , aby utworzyć harmonogramy. Należy określić godzinę rozpoczęcia harmonogramu oraz częstotliwość, w której ma zostać uruchomiony. W poniższych przykładach pokazano, jak utworzyć wiele różnych scenariuszy harmonogramu.

>[!NOTE]
>Harmonogramy automatyzacji nie obsługują obecnie znaków specjalnych w nazwie harmonogramu.
>

#### <a name="create-a-one-time-schedule"></a>Tworzenie harmonogramu jednorazowego

Poniższy przykład tworzy harmonogram jednorazowy.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Tworzenie harmonogramu cyklicznego

Poniższy przykład pokazuje, jak utworzyć cykliczny harmonogram, który jest uruchamiany codziennie o godzinie 1:00 PM przez rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie tygodniowego harmonogramu cyklicznego

Poniższy przykład pokazuje, jak utworzyć harmonogram tygodniowy, który jest uruchamiany tylko w dni robocze.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie tygodniowego harmonogramu cyklicznego dla weekendów

Poniższy przykład pokazuje, jak utworzyć harmonogram tygodniowy, który jest uruchamiany tylko w weekendy.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Utwórz harmonogram cykliczny dla pierwszych, piętnastu i ostatnich dni miesiąca.

Poniższy przykład pokazuje, jak utworzyć cykliczny harmonogram, który jest uruchamiany pierwszy, piętnasty i ostatni dzień miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Tworzenie harmonogramu przy użyciu szablonu Menedżer zasobów

W tym przykładzie używamy szablonu usługi Automation Menedżer zasobów (ARM), który tworzy nowy harmonogram zadań. Aby uzyskać ogólne informacje na temat tego szablonu do zarządzania harmonogramami zadań usługi Automation, zobacz [Dokumentacja szablonu Microsoft. Automation automationAccounts/jobSchedules](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates).

Skopiuj ten plik szablonu do edytora tekstu:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Edytuj poniższe wartości parametrów i Zapisz szablon jako plik JSON:

* Nazwa obiektu harmonogramu zadań: identyfikator GUID (unikatowy identyfikator globalny) jest używany jako nazwa obiektu harmonogramu zadań.

   >[!IMPORTANT]
   > Dla każdego harmonogramu zadań wdrożonego za pomocą szablonu ARM identyfikator GUID musi być unikatowy. Nawet w przypadku ponownego planowania istniejącego harmonogramu należy zmienić identyfikator GUID. Dotyczy to nawet sytuacji, gdy wcześniej został usunięty istniejący harmonogram zadań, który został utworzony przy użyciu tego samego szablonu. Ponowne użycie tego samego identyfikatora GUID spowoduje niepowodzenie wdrożenia.</br></br>
   > Istnieją usługi online, które mogą generować nowy identyfikator GUID, na przykład ten [bezpłatny Generator identyfikatorów GUID w trybie online](https://guidgenerator.com/).

* Nazwa harmonogramu: reprezentuje nazwę harmonogramu zadań automatyzacji, który zostanie połączony z określonym elementem Runbook.
* Nazwa elementu Runbook: reprezentuje nazwę elementu Runbook usługi Automation, z którym ma zostać skojarzony harmonogram zadań.

Po zapisaniu pliku można utworzyć harmonogram zadań elementu Runbook za pomocą następującego polecenia programu PowerShell. Polecenie używa parametru, `TemplateFile` Aby określić ścieżkę i nazwę pliku szablonu.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Łączenie harmonogramu z elementem Runbook

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli element Runbook ma parametry, można podać dla nich wartości. Należy podać wartości parametrów obowiązkowych, a także podać wartości parametrów opcjonalnych. Te wartości są używane za każdym razem, gdy element Runbook jest uruchamiany zgodnie z harmonogramem. Możesz dołączyć ten sam element Runbook do innego harmonogramu i określić różne wartości parametrów.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Łączenie harmonogramu z elementem Runbook za pomocą Azure Portal

1. W Azure Portal z konta usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Wybierz nazwę elementu Runbook do zaplanowania.
1. Jeśli element Runbook nie jest obecnie połączony z harmonogramem, jest dostępna opcja utworzenia nowego harmonogramu lub połączenia z istniejącym harmonogramem.
1. Jeśli element Runbook ma parametry, możesz wybrać opcję **Modyfikuj Parametry uruchomieniowe (domyślnie: Azure)** i wyświetlenie okienka **Parametry** . Tutaj możesz wprowadzić informacje o parametrach.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Łączenie harmonogramu z elementem Runbook za pomocą programu PowerShell

Użyj polecenia cmdlet [register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) , aby połączyć harmonogram. Można określić wartości parametrów elementu Runbook za pomocą parametru Parametry . Aby uzyskać więcej informacji na temat sposobu określania wartości parametrów, zobacz [Uruchamianie elementu Runbook w Azure Automation](../start-runbooks.md).
Poniższy przykład pokazuje, jak połączyć harmonogram z elementem Runbook za pomocą polecenia cmdlet Azure Resource Manager z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Planowanie uruchamiania elementów Runbook częściej

Interwał, dla którego można skonfigurować harmonogram w Azure Automation, to jedna godzina. Aby harmonogramy były uruchamiane częściej niż to możliwe, dostępne są dwie opcje:

* Utwórz [element webhook](../automation-webhooks.md) dla elementu Runbook i Użyj [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) do wywołania elementu webhook. Azure Logic Apps zapewnia bardziej szczegółowy poziom szczegółowości, aby zdefiniować harmonogram.

* Utwórz cztery harmonogramy, które zaczynają się w ciągu 15 minut od siebie i są uruchamiane co godzinę. Ten scenariusz umożliwia uruchamianie elementu Runbook co 15 minut przy użyciu różnych harmonogramów.

## <a name="disable-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu wszystkie elementy Runbook połączone z nim nie będą już działać w tym harmonogramie. Harmonogram można wyłączyć ręcznie lub ustawić czas wygaśnięcia dla harmonogramów z częstotliwością podczas ich tworzenia. Po osiągnięciu czasu wygaśnięcia harmonogram jest wyłączony.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Wyłącz harmonogram z Azure Portal

1. W obszarze konto usługi Automation w okienku po lewej stronie wybierz pozycję **harmonogramy** w obszarze **zasoby udostępnione**.
1. Wybierz nazwę harmonogramu, aby otworzyć okienko szczegółów.
1. Zmień **włączone** na **nie**.

> [!NOTE]
> Aby wyłączyć harmonogram, który ma godzinę rozpoczęcia w przeszłości, należy zmienić datę początkową na godzinę w przyszłości przed jej zapisaniem.

### <a name="disable-a-schedule-with-powershell"></a>Wyłączanie harmonogramu przy użyciu programu PowerShell

Aby zmienić właściwości istniejącego harmonogramu, użyj polecenia cmdlet [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) . Aby wyłączyć harmonogram, należy określić wartość false dla `IsEnabled` parametru.

Poniższy przykład pokazuje, jak wyłączyć harmonogram dla elementu Runbook za pomocą polecenia cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Usuwanie harmonogramu

Gdy wszystko będzie gotowe do usunięcia harmonogramów, możesz użyć Azure Portal lub programu PowerShell. Należy pamiętać, że można usunąć tylko harmonogram, który został wyłączony zgodnie z opisem w poprzedniej sekcji.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Usuwanie harmonogramu przy użyciu Azure Portal

1. W obszarze konto usługi Automation w okienku po lewej stronie wybierz pozycję **harmonogramy** w obszarze **zasoby udostępnione**.
2. Wybierz nazwę harmonogramu, aby otworzyć okienko szczegółów.
3. Kliknij polecenie **Usuń**.

### <a name="remove-a-schedule-with-powershell"></a>Usuwanie harmonogramu za pomocą programu PowerShell

Możesz użyć `Remove-AzAutomationSchedule` polecenia cmdlet, jak pokazano poniżej, aby usunąć istniejący harmonogram.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet służących do uzyskiwania dostępu do harmonogramów, zobacz [Zarządzanie modułami w Azure Automation](modules.md).
* Aby uzyskać ogólne informacje o elementach Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](../automation-runbook-execution.md).