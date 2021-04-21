---
title: Azure Automation Start/Stop VMs during off-hours omówienie
description: W tym artykule opisano Start/Stop VMs during off-hours maszyn wirtualnych, która uruchamia lub zatrzymuje maszyny wirtualne zgodnie z harmonogramem i proaktywnie je Azure Monitor dzienników.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b28367aa242d5fab71dc5046ff6188c634883f03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834520"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Start/Stop VMs during off-hours omówienie

Ta Start/Stop VMs during off-hours uruchamia lub zatrzymuje włączone maszyny wirtualne platformy Azure. Uruchamia lub zatrzymuje maszyny zgodnie z harmonogramami zdefiniowanymi przez użytkownika, zapewnia szczegółowe informacje za pośrednictwem dzienników Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji.](../azure-monitor/alerts/action-groups.md) Tę funkcję można włączyć zarówno na Azure Resource Manager, jak i klasycznych maszyn wirtualnych w większości scenariuszy.

Ta funkcja używa polecenia cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) do uruchamiania maszyn wirtualnych. Do zatrzymywania maszyn wirtualnych jest używany program [Stop-AzVM.](/powershell/module/az.compute/stop-azvm)

> [!NOTE]
> Chociaż zaktualizowano je w celu używania nowych polecenia cmdlet modułu Azure Az, używają aliasu prefiksu modułu AzureRM.

> [!NOTE]
> Start/Stop VMs during off-hours do obsługi najnowszych dostępnych wersji modułów platformy Azure. Zaktualizowana wersja tej funkcji, dostępna w witrynie Marketplace, nie obsługuje modułów AzureRM, ponieważ zostały zmigrowane z modułów AzureRM do Az.

Ta funkcja zapewnia zdecentralizowaną, niekońcową opcję automatyzacji dla użytkowników, którzy chcą zoptymalizować koszty maszyn wirtualnych. Za pomocą tej funkcji można:

- [Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych.](automation-solution-vm-management-config.md#schedule)
- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w kolejności rosnącej przy [użyciu tagów platformy Azure.](automation-solution-vm-management-config.md#tags) To działanie nie jest obsługiwane w przypadku klasycznych maszyn wirtualnych.
- Automatyczne wstrzymywanie maszyn wirtualnych na podstawie [niskiego użycia procesora CPU.](automation-solution-vm-management-config.md#cpuutil)

Poniżej przedstawiono ograniczenia dotyczące bieżącej funkcji:

- Zarządza ona maszynami wirtualnych w dowolnym regionie, ale może być używana tylko w tej samej subskrypcji co Azure Automation magazynu.
- Jest ona dostępna na platformie Azure Azure Government dla dowolnego regionu, który obsługuje obszar roboczy usługi Log Analytics, konto Azure Automation i alerty. Azure Government obecnie nie obsługują funkcji poczty e-mail.

> [!NOTE]
> Przed zainstalowaniem tej wersji chcemy poznać następną wersję [,](https://github.com/microsoft/startstopv2-deployments)która jest obecnie dostępna w wersji zapoznawczej.  Ta nowa wersja (V2) oferuje te same funkcje co ta, ale została zaprojektowana tak, aby korzystać z nowszej technologii na platformie Azure. Dodaje ona niektóre często żądane funkcje od klientów, takie jak obsługa wielu subskrypcji z pojedynczego wystąpienia uruchamiania/zatrzymania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcje runbook uruchamiania/zatrzymywania maszyn wirtualnych poza godzinami pracy działają z kontem [Uruchom jako platformy Azure.](./automation-security-overview.md#run-as-accounts) Preferowaną metodą uwierzytelniania jest konto Uruchom jako, ponieważ używa ono uwierzytelniania certyfikatu zamiast hasła, które może wygasać lub często się zmieniać.

- Obszar [Azure Monitor usługi Log Analytics,](../azure-monitor/logs/design-logs-deployment.md) który przechowuje dzienniki zadań runbook i strumień zadań, powoduje tworzenie zapytań i analizowanie w obszarze roboczym. Konto usługi Automation można połączyć z nowym lub istniejącym obszarem roboczym usługi Log Analytics, a oba zasoby muszą być w tej samej grupie zasobów.

Zalecamy używanie oddzielnego konta usługi Automation do pracy z maszynami wirtualnych włączonymi dla Start/Stop VMs during off-hours usługi. Wersje modułów platformy Azure są często uaktualniane, a ich parametry mogą ulec zmianie. Funkcja nie jest uaktualniana w tym samym okresie i może nie działać z nowszą wersjami polecenia cmdlet, których używa. Przed zaimportowanie zaktualizowanych modułów do produkcyjnych kont usługi Automation zalecamy zaimportowanie ich do testowego konta usługi Automation, aby sprawdzić, czy nie ma żadnych problemów ze zgodnością.

## <a name="permissions"></a>Uprawnienia

Musisz mieć pewne uprawnienia, aby włączyć maszyny wirtualne dla Start/Stop VMs during off-hours wirtualnej. Uprawnienia różnią się w zależności od tego, czy funkcja używa wstępnie utworzonego konta usługi Automation i obszaru roboczego usługi Log Analytics, czy też tworzy nowe konto i obszar roboczy.

Nie musisz konfigurować uprawnień, jeśli jesteś współautorem subskrypcji i administratorem globalnym w dzierżawie usługi Azure Active Directory (AD). Jeśli nie masz tych praw lub musisz skonfigurować rolę niestandardową, upewnij się, że masz uprawnienia opisane poniżej.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Uprawnienia dla istniejącego konta usługi Automation i obszaru roboczego usługi Log Analytics

Aby włączyć maszyny wirtualne dla funkcji Start/Stop VMs during off-hours przy użyciu istniejącego konta usługi Automation i obszaru roboczego usługi Log Analytics, potrzebne są następujące uprawnienia w zakresie grupy zasobów. Aby dowiedzieć się więcej o rolach, zobacz [Role niestandardowe platformy Azure.](../role-based-access-control/custom-roles.md)

| Uprawnienie | Zakres|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupa zasobów |
| Microsoft.Automation/automationAccounts/variables/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/schedules/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/runbooks/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/connections/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/certificates/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/modules/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/modules/read | Grupa zasobów |
| Microsoft.automation/automationAccounts/jobSchedules/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/jobs/write | Grupa zasobów |
| Microsoft.Automation/automationAccounts/jobs/read | Grupa zasobów |
| Microsoft.OperationsManagement/solutions/write | Grupa zasobów |
| Microsoft.OperationalInsights/workspaces/* | Grupa zasobów |
| Microsoft.Insights/diagnosticSettings/write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/Write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/read | Grupa zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupa zasobów |
| Microsoft.Resources/deployments/* | Grupa zasobów |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Uprawnienia dla nowego konta usługi Automation i nowego obszaru roboczego usługi Log Analytics

Maszyny wirtualne dla funkcji Start/Stop VMs during off-hours przy użyciu nowego konta usługi Automation i obszaru roboczego usługi Log Analytics. W takim przypadku potrzebne są uprawnienia zdefiniowane w poprzedniej sekcji, a także uprawnienia zdefiniowane w tej sekcji. Wymagane są również następujące role:

- Co-Administrator subskrypcji. Ta rola jest wymagana do utworzenia klasycznego konta Uruchom jako, jeśli zamierzasz zarządzać klasycznymi maszynami wirtualnych. [Klasyczne konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account) nie są już domyślnie tworzone.
- Członkostwo w roli dewelopera aplikacji usługi [Azure AD.](../active-directory/roles/permissions-reference.md) Aby uzyskać więcej informacji na temat konfigurowania kont Uruchom jako, zobacz [Uprawnienia do konfigurowania kont Uruchom jako.](automation-security-overview.md#permissions)
- Współautor subskrypcji lub następujące uprawnienia.

| Uprawnienie |Zakres|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subskrypcja|
| Microsoft.Authorization/permissions/read |Subskrypcja|
| Microsoft.Authorization/roleAssignments/read | Subskrypcja |
| Microsoft.Authorization/roleAssignments/write | Subskrypcja |
| Microsoft.Authorization/roleAssignments/delete | Subskrypcja |
| Microsoft.Automation/automationAccounts/connections/read | Grupa zasobów |
| Microsoft.Automation/automationAccounts/certificates/read | Grupa zasobów |
| Microsoft.Automation/automationAccounts/write | Grupa zasobów |
| Microsoft.OperationalInsights/workspaces/write | Grupa zasobów |

## <a name="components"></a>Składniki

Ta Start/Stop VMs during off-hours obejmuje wstępnie skonfigurowane element runbook, harmonogramy i integrację z Azure Monitor dziennikami. Możesz użyć tych elementów, aby dostosować uruchamianie i zamykanie maszyn wirtualnych do własnych potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy Runbook wdrażane przez funkcję na koncie usługi Automation. NIE należy wprowadzać zmian w kodzie runbook. Zamiast tego napisz własny podręcznik Runbook, aby uzyskać nowe funkcje.

> [!IMPORTANT]
> Nie uruchamiaj bezpośrednio żadnego runbook z **podrzędnym** dołączonym do jego nazwy.

Wszystkie nadrzędne element runbook zawierają `WhatIf` parametr . W przypadku ustawienia wartości True parametr obsługuje szczegółowe zachowanie, które element Runbook ma w przypadku uruchomienia bez parametru, i sprawdza, czy właściwe maszyny wirtualne są celem. Runbook wykonuje zdefiniowane akcje tylko wtedy, gdy `WhatIf` parametr ma wartość False.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VmObject <br> AlertAction <br> WebHookURI | Wywoływana z nadrzędnego elementu Runbook. Ten podręcznik Runbook tworzy alerty dla każdego zasobu dla scenariusza automatycznego zatrzymania.|
|AutoStop_CreateAlert_Parent | Lista maszyn wirtualnych<br> WhatIf: True lub False  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszyny wirtualne w docelowej subskrypcji lub grupach zasobów. <br> `VMList` to rozdzielana przecinkami lista maszyn wirtualnych (bez białych znaków), na przykład `vm1,vm2,vm3` .<br> `WhatIf` umożliwia walidację logiki runbook bez wykonywania.|
|AutoStop_Disable | Brak | Wyłącza alerty automatycznego zatrzymania i domyślny harmonogram.|
|AutoStop_VM_Child | WebHookData | Wywoływana z nadrzędnego elementu Runbook. Reguły alertów wywołują ten runbook, aby zatrzymać klasyczną maszynę wirtualną.|
|AutoStop_VM_Child_ARM | WebHookData |Wywoływana z nadrzędnego elementu Runbook. Reguły alertów wywołują ten runbook, aby zatrzymać maszynę wirtualną.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akcja: Uruchamianie lub zatrzymywanie<br> Lista maszyn wirtualnych  | Wykonuje akcję uruchamiania lub zatrzymywania w klasycznej grupie maszyn wirtualnych przez Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Akcja: Uruchamianie lub zatrzymywanie <br> ResourceGroupName | Wywoływana z nadrzędnego elementu Runbook. Wykonuje akcję uruchamiania lub zatrzymania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Child_Classic | VMName<br> Akcja: Uruchamianie lub zatrzymywanie<br> ResourceGroupName | Wywoływana z nadrzędnego elementu Runbook. Wykonuje akcję uruchamiania lub zatrzymania dla zaplanowanego zatrzymania dla klasycznych maszyn wirtualnych. |
|ScheduledStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br>Lista maszyn wirtualnych <br> WhatIf: True lub False | Uruchamia lub zatrzymuje wszystkie maszyny wirtualne w subskrypcji. Edytuj zmienne i , `External_Start_ResourceGroupNames` aby wykonać je tylko w tych `External_Stop_ResourceGroupNames` docelowych grupach zasobów. Możesz również wykluczyć określone maszyny wirtualne, aktualizując `External_ExcludeVMNames` zmienną.|
|SequencedStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br> WhatIf: True lub False<br>Lista maszyn wirtualnych| Tworzy tagi o **nazwach sequencestart** **i sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencji działań uruchamiania/zatrzymania. W tych nazwach tagów jest wielkość liter. Wartość tagu powinna być listą dodatnich liczb całkowitych, na przykład , która odpowiada kolejności uruchamiania `1,2,3` lub zatrzymania. <br>**Uwaga:** Maszyny wirtualne muszą znajdować się w grupach zasobów zdefiniowanych w `External_Start_ResourceGroupNames` zmiennych , i `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` . Aby akcje zostały wprowadzone, muszą mieć odpowiednie tagi.|

### <a name="variables"></a>Zmienne

W poniższej tabeli wymieniono zmienne utworzone na koncie usługi Automation. Zmodyfikuj tylko zmienne z prefiksem `External` . Modyfikowanie zmiennych poprzedzonych prefiksem `Internal` powoduje niepożądane skutki.

> [!NOTE]
> Ograniczenia dotyczące nazwy maszyny wirtualnej i grupy zasobów są w dużej mierze wynikiem rozmiaru zmiennej. Zobacz [Zasoby zmiennych w Azure Automation](./shared-resources/variables.md).

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne wartości to `GreaterThan` , `GreaterThanOrEqual` , i `LessThan` `LessThanOrEqual` .|
|External_AutoStop_Description | Alert o zatrzymaniu maszyny wirtualnej w przypadku przekroczenia progu procentowego użycia procesora CPU.|
|External_AutoStop_Frequency | Częstotliwość oceny reguły. Ten parametr akceptuje dane wejściowe w formacie czasu. Możliwe wartości to od 5 minut do 6 godzin. |
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma zostać skonfigurowana reguła alertu platformy Azure.|
|External_AutoStop_Severity | Ważność alertu metryki, która może mieć zakres od 0 do 4. |
|External_AutoStop_Threshold | Próg dla reguły alertu platformy Azure określonej w zmiennej `External_AutoStop_MetricName` . Wartości procentowe wahają się od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu zastosowany do wybranego rozmiaru okna w celu oceny warunku. Dopuszczalne wartości to `Average` , , , i `Minimum` `Maximum` `Total` `Last` .|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym platforma Azure analizuje wybrane metryki w celu wyzwolenia alertu. Ten parametr akceptuje dane wejściowe w formacie czasu. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Wartość określająca, czy funkcja ma być obiektem docelowym klasycznych maszyn wirtualnych. Wartość domyślna to True. Ustaw tę zmienną na wartość False Azure Cloud Solution Provider subskrypcji usługi (CSP). Klasyczne maszyny wirtualne wymagają [klasycznego konta Uruchom jako.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Rozdzielana przecinkami lista nazw maszyn wirtualnych do wykluczenia ograniczona do 140 maszyn wirtualnych. Jeśli dodasz do listy więcej niż 140 maszyn wirtualnych, maszyny wirtualne określone do wykluczenia mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Rozdzielana przecinkami lista co najmniej jednej grupy zasobów, która jest ukierunkowana na akcje uruchamiania.|
|External_Stop_ResourceGroupNames | Rozdzielana przecinkami lista co najmniej jednej grupy zasobów, która jest ukierunkowana na akcje zatrzymania.|
|External_WaitTimeForVMRetrySeconds |Czas oczekiwania w sekundach na akcje, które mają  być wykonywane na maszyn wirtualnych dla SequencedStartStop_Parent Runbook. Ta zmienna umożliwia użytkownikowi oczekiwanie na operacje podrzędne przez określoną liczbę sekund przed przystąpieniem do następnej akcji. Maksymalny czas oczekiwania wynosi 10800 lub trzy godziny. Wartość domyślna to 2100 sekund.|
|Internal_AutomationAccountName | Określa nazwę konta usługi Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | W scenariuszu automatycznego zatrzymania dla maszyn wirtualnych jest wywoływany adres URI webhook.|
|Internal_AutoSnooze_WebhookUri | W scenariuszu automatycznego zatrzymania dla klasycznych maszyn wirtualnych jest wywoływany adres URI typu webhook.|
|Internal_AzureSubscriptionId | Identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Nazwa grupy zasobów konta usługi Automation.|

>[!NOTE]
>Dla zmiennej `External_WaitTimeForVMRetryInSeconds` wartość domyślna została zaktualizowana z 600 do 2100.

We wszystkich scenariuszach zmienne , i są niezbędne do określania maszyn wirtualnych, z wyjątkiem rozdzielonych przecinkami list maszyn wirtualnych dla `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` AutoStop_CreateAlert_Parent, **SequencedStartStop_Parent** i **ScheduledStartStop_Parent** runbook.  Oznacza to, że maszyny wirtualne muszą należeć do docelowych grup zasobów, aby można było wykonać akcje uruchamiania i zatrzymania. Logika działa podobnie do Azure Policy tym, że można określić subskrypcję lub grupę zasobów jako docelową i mieć akcje dziedziczone przez nowo utworzone maszyny wirtualne. Takie podejście pozwala uniknąć konieczności utrzymywania oddzielnego harmonogramu dla każdej maszyny wirtualnej oraz zarządzania startami i zatrzymaniami w skali.

### <a name="schedules"></a>Harmonogramy

W poniższej tabeli wymieniono wszystkie domyślne harmonogramy utworzone na koncie usługi Automation. Można je modyfikować lub tworzyć własne harmonogramy niestandardowe. Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem harmonogramów **Scheduled_StartVM** i **Scheduled_StopVM** harmonogramów.

Nie włączaj wszystkich harmonogramów, ponieważ może to spowodować nakładające się akcje harmonogramu. Najlepiej jest określić, które optymalizacje chcesz wykonać, i odpowiednio je zmodyfikować. Zobacz przykładowe scenariusze w sekcji przeglądu, aby uzyskać więcej informacji.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia ten **AutoStop_CreateAlert_Parent** runbook co 8 godzin, co z kolei zatrzymuje wartości oparte na maszynie wirtualnej w `External_Start_ResourceGroupNames` zmiennych , i `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` . Alternatywnie można określić listę maszyn wirtualnych rozdzielonych przecinkami przy użyciu `VMList` parametru .|
|Scheduled_StopVM | Zdefiniowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** Runbook z parametrem `Stop` codziennie o określonej godzinie. Automatycznie zatrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmiennych. Włącz powiązany harmonogram **Scheduled-StartVM.**|
|Scheduled_StartVM | Zdefiniowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** Runbook z wartością parametru `Start` codziennie o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmiennych. Włącz powiązany harmonogram **Scheduled-StopVM.**|
|Sequenced-StopVM | 1:00 (UTC), co piątek | Uruchamia **Sequenced_StopStop_Parent** Runbook z wartością parametru w `Stop` każdy piątek o określonej godzinie. Sekwencyjnie (rosnąco) zatrzymuje wszystkie maszyny wirtualne z tagiem **SequenceStop** zdefiniowanym przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zmiennych zasobów, zobacz [Runbook](#runbooks). Włącz powiązany harmonogram **Sequenced-StartVM.**|
|Sequenced-StartVM | 13:00 (UTC), co poniedziałek | Uruchamia **SequencedStopStart_Parent** Runbook z wartością parametru w `Start` każdy poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) uruchamia wszystkie maszyny wirtualne z tagiem **SequenceStart zdefiniowanym** przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zasobów zmiennych, zobacz [Runbook](#runbooks). Włącz powiązany harmonogram **Sequenced-StopVM.**|

## <a name="use-the-feature-with-classic-vms"></a>Używanie tej funkcji z klasycznymi maszynami wirtualnych

Jeśli używasz funkcji zarządzania Start/Stop VMs during off-hours klasycznych maszyn wirtualnych, usługa Automation przetwarza wszystkie maszyny wirtualne sekwencyjnie na usługę w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze. 

Do korzystania z tej funkcji w przypadku klasycznych maszyn wirtualnych potrzebne jest klasyczne konto Uruchom jako, które nie jest domyślnie tworzone. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta Uruchom jako, zobacz Tworzenie klasycznego konta [Uruchom jako.](automation-create-standalone-account.md#create-a-classic-run-as-account)

Jeśli masz więcej niż 20 maszyn wirtualnych na usługę w chmurze, oto kilka zaleceń:

* Utwórz wiele harmonogramów za pomocą nadrzędnego elementu runbook **ScheduledStartStop_Parent** określając 20 maszyn wirtualnych na harmonogram.
* We właściwościach harmonogramu użyj parametru , aby określić nazwy maszyn wirtualnych jako listę rozdzieloną `VMList` przecinkami (bez odstępów).

W przeciwnym razie, jeśli zadanie usługi Automation dla tej funkcji będzie działać dłużej niż trzy godziny, zostanie ono tymczasowo zwolniony lub zatrzymane według limitu [sprawiedliwego udostępniania.](automation-runbook-execution.md#fair-share)

Azure CSP subskrypcje obsługują tylko Azure Resource Manager modelu. Usługi inne Azure Resource Manager nie są dostępne w programie. Po Start/Stop VMs during off-hours funkcji mogą wystąpić błędy, ponieważ zawiera ona polecenia cmdlet do zarządzania zasobami klasycznymi. Aby dowiedzieć się więcej o programie CSP, zobacz [Dostępne usługi w subskrypcjach CSP.](/azure/cloud-solution-provider/overview/azure-csp-available-services) Jeśli używasz subskrypcji CSP, po wdrożeniu należy ustawić [zmienną External_EnableClassicVMs](#variables) na wartość False.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Wyświetlanie funkcji

Aby uzyskać dostęp do włączonej funkcji, użyj jednego z następujących mechanizmów:

* Na koncie usługi Automation wybierz pozycję **Uruchom/zatrzymaj maszynę wirtualną w** obszarze **Powiązane zasoby.** Na stronie Uruchamianie/zatrzymywanie maszyny wirtualnej wybierz pozycję Zarządzaj **rozwiązaniem w** obszarze Zarządzanie rozwiązaniami do **uruchamiania/zatrzymywania maszyn wirtualnych.**

* Przejdź do obszaru roboczego usługi Log Analytics połączonego z kontem usługi Automation. Po wybraniu obszaru roboczego wybierz pozycję **Rozwiązania w** okienku po lewej stronie. Na stronie Rozwiązania wybierz z listy pozycję **Start-Stop-VM[workspace].**  

Wybranie tej funkcji powoduje wyświetlenie **strony Start-Stop-VM[workspace].** W tym miejscu możesz przejrzeć ważne informacje, takie jak informacje na **kafelku StartStopVM.** Podobnie jak w obszarze roboczym usługi Log Analytics, ten kafelek wyświetla liczbę i graficzną reprezentację zadań elementów Runbook dla funkcji, która została uruchomiona i zakończyła pracę pomyślnie.

![Strona Update Management automatyzacji](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Możesz przeprowadzić dalszą analizę rekordów zadań, klikając kafelek pierścieniowy. Pulpit nawigacyjny przedstawia historię zadań i wstępnie zdefiniowane zapytania przeszukiwania dzienników. Przejdź do zaawansowanego portalu analizy dzienników, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="update-the-feature"></a>Aktualizowanie funkcji

Jeśli wdrożono poprzednią wersję programu Start/Stop VMs during off-hours, usuń ją ze swojego konta przed wdrożeniem zaktualizowanej wersji. Wykonaj kroki, [aby usunąć funkcję, a](automation-solution-vm-management-remove.md#delete-the-feature) następnie wykonaj kroki, aby ją [włączyć.](automation-solution-vm-management-enable.md)

## <a name="next-steps"></a>Następne kroki

Aby włączyć tę funkcję na komputerach wirtualnych w środowisku, zobacz [Włączanie Start/Stop VMs during off-hours](automation-solution-vm-management-enable.md).
