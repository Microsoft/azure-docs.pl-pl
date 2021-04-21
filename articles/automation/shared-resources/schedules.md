---
title: Zarządzanie harmonogramami w Azure Automation
description: W tym artykule opisano sposób tworzenia harmonogramu i pracy z nim w Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f119c15cbbfd9586bdb06fdffad0b12c9a441eea
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832648"
---
# <a name="manage-schedules-in-azure-automation"></a>Zarządzanie harmonogramami w Azure Automation

Aby zaplanować uruchamianie Azure Automation w określonym czasie, należy połączyć go z co najmniej jednym harmonogramem. Harmonogram można skonfigurować do uruchamiania raz lub zgodnie z cyklicznym harmonogramem godzinnym lub dziennym dla Azure Portal. Można je również zaplanować na cotygodniowe, miesięczne, określone dni tygodnia lub dni miesiąca albo na konkretny dzień miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Azure Automation obsługuje czas letni i odpowiednio planuje go dla operacji automatyzacji.

> [!NOTE]
> Harmonogramy nie są obecnie włączone dla Azure Automation DSC.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Polecenia cmdlet programu PowerShell używane do uzyskiwania dostępu do harmonogramów

Polecenia cmdlet w poniższej tabeli tworzą harmonogramy usługi Automation i zarządzają nimi za pomocą programu PowerShell. Są one dostępne w ramach modułów [Az](modules.md#az-modules).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Pobiera harmonogram. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Pobiera zaplanowane podręczniki Runbook. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Tworzy nowy harmonogram. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Kojarzy runbook z harmonogramem. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Usuwa harmonogram. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Ustawia właściwości dla istniejącego harmonogramu. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Skojarzeń runbook z harmonogramem. |

## <a name="create-a-schedule"></a>Tworzenie harmonogramu

Możesz utworzyć nowy harmonogram dla swoich elementach Runbook z witryny Azure Portal, programu PowerShell lub przy użyciu szablonu Azure Resource Manager (ARM). Aby uniknąć wpływu na twoje element runbook i zautomatyzowane procesy, najpierw przetestuj wszystkie element runbook, które mają połączone harmonogramy z kontem usługi Automation przeznaczonym do testowania. Test sprawdza, czy zaplanowane podręczniki Runbook nadal działają prawidłowo. Jeśli widzisz problem, możesz rozwiązać problem i zastosować wszelkie zmiany wymagane przed migracją zaktualizowanej wersji runbook do produkcji.

> [!NOTE]
> Twoje konto usługi Automation nie pobiera automatycznie żadnych nowych wersji modułów, chyba że zostały zaktualizowane ręcznie, wybierając opcję Aktualizuj moduły platformy [Azure](../automation-update-azure-modules.md) w **witrynie Moduły.** Azure Automation używa najnowszych modułów na koncie usługi Automation po uruchomieniu nowego zaplanowanego zadania. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Utwórz nowy harmonogram w Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Harmonogramy w** obszarze **Udostępnione zasoby.**
2. Na **stronie Harmonogramy** wybierz pozycję **Dodaj harmonogram.**
3. Na **stronie Nowy harmonogram** wprowadź nazwę i opcjonalnie wprowadź opis nowego harmonogramu.

    >[!NOTE]
    >Harmonogramy automatyzacji nie obsługują obecnie używania znaków specjalnych w nazwie harmonogramu.
    >

4. Wybierz, czy harmonogram jest uruchamiany raz, czy zgodnie z cyklicznym harmonogramem, wybierając pozycję **Raz** lub **Cyklicznie.** W przypadku wybrania **opcji Raz** określ czas rozpoczęcia, a następnie wybierz pozycję **Utwórz.** W przypadku wybrania **opcji Cykliczne** określ czas rozpoczęcia. W **przypadku opcji Powtarzaj co** wybierz, jak często ma być powtarzany ten typ runbook. Wybierz według godziny, dnia, tygodnia lub miesiąca.

    * W przypadku **wybrania opcji Tydzień** zostaną przedstawione dni tygodnia do wyboru. Wybierz tyle dni, ile chcesz. Pierwszy przebieg harmonogramu nastąpi pierwszego dnia wybranego po godzinie rozpoczęcia. Aby na przykład wybrać harmonogram weekendowy, wybierz sobotę i niedzielę.

    ![Ustawianie harmonogramu cyklicznego weekendu](../media/schedules/week-end-weekly-recurrence.png)

    * W przypadku wybrania **opcji Month**(Miesiąc) dostępne są różne opcje. Dla opcji **Wystąpienia miesięczne wybierz** pozycję Month days **(Dni miesiąca)** **lub Week days (Dni tygodnia).** W przypadku wybrania **opcji Month days (Dni** miesiąca) zostanie wyświetlony kalendarz, dzięki czemu możesz wybrać tyle dni, ile chcesz. Jeśli wybierzesz datę, taką jak 31., która nie występuje w bieżącym miesiącu, harmonogram nie zostanie uruchomiony. Jeśli chcesz, aby harmonogram był uruchamiany ostatniego dnia, wybierz pozycję **Tak** w **obszarze Uruchom w ostatnim dniu miesiąca.** W przypadku wybrania **opcji Dni tygodnia** zostanie **wyświetlona opcja Powtarzaj** co. Wybierz **pozycję Pierwszy,** **Drugi,** **Trzeci,** **Czwarty** lub **Ostatni.** Na koniec wybierz dzień, który ma być powtarzany.

    ![Harmonogram miesięczny pierwszego, 15. i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu wybierz pozycję **Utwórz**.

### <a name="create-a-new-schedule-with-powershell"></a>Tworzenie nowego harmonogramu przy użyciu programu PowerShell

Użyj polecenia cmdlet [New-AzAutomationSchedule,](/powershell/module/Az.Automation/New-AzAutomationSchedule) aby utworzyć harmonogramy. Należy określić czas rozpoczęcia harmonogramu i częstotliwość jego uruchamiania. Poniższe przykłady pokazują, jak utworzyć wiele różnych scenariuszy harmonogramu.

>[!NOTE]
>Harmonogramy automatyzacji nie obsługują obecnie używania znaków specjalnych w nazwie harmonogramu.
>

#### <a name="create-a-one-time-schedule"></a>Tworzenie harmonogramu czasowego

Poniższy przykład tworzy harmonogram godzinowy.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Tworzenie harmonogramu cyklicznego

W poniższym przykładzie pokazano, jak utworzyć cykliczny harmonogram uruchamiany codziennie o 13:00 przez rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie cotygodniowego harmonogramu cyklicznego

W poniższym przykładzie pokazano, jak utworzyć harmonogram tygodniowy uruchamiany tylko w dni robocze.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie cotygodniowego harmonogramu cyklicznego na weekendy

W poniższym przykładzie pokazano, jak utworzyć harmonogram tygodniowy uruchamiany tylko w weekendy.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Tworzenie harmonogramu cyklicznego dla pierwszego, 15. i ostatnich dni miesiąca

W poniższym przykładzie pokazano, jak utworzyć harmonogram cykliczny uruchamiany pierwszego, 15. i ostatniego dnia miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Tworzenie harmonogramu przy użyciu Resource Manager szablonu

W tym przykładzie używamy szablonu usługi Automation Resource Manager (ARM), który tworzy nowy harmonogram zadań. Aby uzyskać ogólne informacje na temat tego szablonu do zarządzania harmonogramami zadań usługi Automation, zobacz [Microsoft.Automation automationAccounts/jobSchedules template reference (Informacje o szablonie Microsoft.Automation automationAccounts/jobSchedules).](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)

Skopiuj ten plik szablonu do edytora tekstów:

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

Edytuj następujące wartości parametrów i zapisz szablon jako plik JSON:

* Nazwa obiektu harmonogramu zadań: identyfikator GUID (globalnie unikatowy identyfikator) jest używany jako nazwa obiektu harmonogramu zadań.

   >[!IMPORTANT]
   > Dla każdego harmonogramu zadań wdrożonego za pomocą szablonu usługi ARM identyfikator GUID musi być unikatowy. Nawet jeśli planujesz ponownie istniejący harmonogram, musisz zmienić identyfikator GUID. Ma to zastosowanie nawet wtedy, gdy wcześniej usunięto istniejący harmonogram zadań, który został utworzony przy użyciu tego samego szablonu. Ponowne ponowne wdrożenie tego samego identyfikatora GUID powoduje niepowodzenie wdrożenia.</br></br>
   > Istnieją usługi online, które mogą generować nowy identyfikator GUID, na przykład bezpłatny generator [identyfikatorów GUID online.](https://guidgenerator.com/)

* Nazwa harmonogramu: reprezentuje nazwę harmonogramu zadań usługi Automation, który zostanie połączony z określonym zadaniem Runbook.
* Nazwa runbook: reprezentuje nazwę runbook usługi Automation, z który ma zostać skojarzony harmonogram zadań.

Po zapisaniu pliku można utworzyć harmonogram zadań runbook za pomocą następującego polecenia programu PowerShell. Polecenie używa `TemplateFile` parametru , aby określić ścieżkę i nazwę pliku szablonu.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Łączenie harmonogramu z programem Runbook

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli runbook ma parametry, możesz podać dla nich wartości. Należy podać wartości dla wszystkich obowiązkowych parametrów, a także podać wartości dla dowolnych parametrów opcjonalnych. Te wartości są używane za każdym razem, gdy program Runbook zostanie uruchomiony zgodnie z tym harmonogramem. Ten sam podręcznik Runbook można dołączyć do innego harmonogramu i określić różne wartości parametrów.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Łączenie harmonogramu z programem Runbook za pomocą Azure Portal

1. Na stronie Azure Portal na koncie automatyzacji wybierz pozycję **Runbook** w obszarze **Automatyzacja procesów.**
1. Wybierz nazwę podręcznika Runbook do zaplanowania.
1. Jeśli element Runbook nie jest obecnie połączony z harmonogramem, możesz utworzyć nowy harmonogram lub połączyć go z istniejącym harmonogramem.
1. Jeśli runbook ma parametry, możesz wybrać opcję Modyfikuj ustawienia uruchamiania **(Default:Azure)** i zostanie **wyświetlone okienko** Parametry. Tutaj możesz wprowadzić informacje o parametrach.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Łączenie harmonogramu z programem Runbook przy użyciu programu PowerShell

Użyj polecenia cmdlet [Register-AzAutomationScheduledRunbook,](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) aby połączyć harmonogram. Można określić wartości parametrów elementu Runbook za pomocą parametru Parametry . Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [Starting a Runbook in Azure Automation](../start-runbooks.md).
W poniższym przykładzie pokazano, jak połączyć harmonogram z elementem Runbook przy użyciu Azure Resource Manager cmdlet z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Planowanie częstego uruchamiania podręczników Runbook

Najczęstszym interwałem, dla którego można skonfigurować Azure Automation jest jedna godzina. Jeśli harmonogramy mają być uruchamiane częściej, dostępne są dwie opcje:

* Utwórz [webhook dla](../automation-webhooks.md) tego runbook i użyj Azure Logic Apps [do](../../logic-apps/logic-apps-overview.md) wywołania tego. Azure Logic Apps zapewnia bardziej szczegółowy poziom szczegółowości w celu zdefiniowania harmonogramu.

* Utwórz cztery harmonogramy, które będą uruchamiane co godzinę w ciągu 15 minut od siebie. Ten scenariusz umożliwia uruchamianie runbook co 15 minut z różnymi harmonogramami.

## <a name="disable-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu połączony z nim podręcznik Runbook nie będzie już uruchamiany zgodnie z tym harmonogramem. Harmonogram można ręcznie wyłączyć lub ustawić czas wygaśnięcia dla harmonogramów z częstotliwością podczas ich tworzenia. Po osiągnięciu czasu wygaśnięcia harmonogram jest wyłączony.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Wyłącz harmonogram z Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Harmonogramy w** obszarze **Udostępnione zasoby.**
1. Wybierz nazwę harmonogramu, aby otworzyć okienko szczegółów.
1. Zmień **włączone na** **Nie**.

> [!NOTE]
> Jeśli chcesz wyłączyć harmonogram z czasem rozpoczęcia w przeszłości, przed jego zapisaniem musisz zmienić datę rozpoczęcia na datę w przyszłości.

### <a name="disable-a-schedule-with-powershell"></a>Wyłączanie harmonogramu przy użyciu programu PowerShell

Użyj polecenia cmdlet [Set-AzAutomationSchedule,](/powershell/module/Az.Automation/Set-AzAutomationSchedule) aby zmienić właściwości istniejącego harmonogramu. Aby wyłączyć harmonogram, określ wartość False dla `IsEnabled` parametru .

W poniższym przykładzie pokazano, jak wyłączyć harmonogram dla element Runbook przy użyciu Azure Resource Manager cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Usuwanie harmonogramu

Gdy wszystko będzie gotowe do usunięcia harmonogramów, możesz użyć Azure Portal programu PowerShell. Pamiętaj, że możesz usunąć tylko harmonogram, który został wyłączony zgodnie z opisem w poprzedniej sekcji.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Usuwanie harmonogramu przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Harmonogramy w** obszarze **Udostępnione zasoby.**
2. Wybierz nazwę harmonogramu, aby otworzyć okienko szczegółów.
3. Kliknij polecenie **Usuń**.

### <a name="remove-a-schedule-with-powershell"></a>Usuwanie harmonogramu za pomocą programu PowerShell

Możesz użyć polecenia `Remove-AzAutomationSchedule` cmdlet , jak pokazano poniżej, aby usunąć istniejący harmonogram.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o poleceniach cmdlet używanych do uzyskiwania dostępu do harmonogramów, zobacz Zarządzanie modułami w [programie Azure Automation](modules.md).
* Aby uzyskać ogólne informacje na temat runbook, zobacz [Runbook execution in Azure Automation (Wykonywanie runbook w programie Azure Automation](../automation-runbook-execution.md)).
