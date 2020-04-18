---
title: Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy
description: To rozwiązanie do zarządzania maszynami wirtualnymi uruchamia i zatrzymuje maszyny wirtualne platformy Azure zgodnie z harmonogramem i proaktywnie monitoruje dzienniki usługi Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 968e609772e08814a9943734d30c16bf6f5972e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604707"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy w usłudze Azure Automation

Maszyny **wirtualne start/stop podczas rozwiązania poza godzinami pracy** uruchamia lub zatrzymuje maszyny wirtualne platformy Azure. Uruchamia lub zatrzymuje maszyny zgodnie z harmonogramami zdefiniowanymi przez użytkownika, zapewnia szczegółowe informacje za pośrednictwem dzienników usługi Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji.](../azure-monitor/platform/action-groups.md) Rozwiązanie obsługuje zarówno usługi Azure Resource Manager, jak i klasyczne maszyny wirtualne dla większości scenariuszy. 

To rozwiązanie zapewnia zdecentralizowaną opcję automatyzacji o niskich kosztach dla użytkowników, którzy chcą zoptymalizować swoje koszty maszyny Wirtualnej. Dzięki temu rozwiązaniu możesz:

- [Zaplanuj uruchamianie i zatrzymywać maszyny wirtualne](automation-solution-vm-management-config.md#schedule).
- Zaplanuj maszyny wirtualne, aby rozpocząć i zatrzymać w kolejności rosnącej [przy użyciu tagów platformy Azure](automation-solution-vm-management-config.md#tags) (nie są obsługiwane dla klasycznych maszyn wirtualnych).
- Autostop maszyn wirtualnych w oparciu o [niskie zużycie procesora](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> Maszyny **wirtualne start/stop podczas** rozwiązania poza godzinami pracy zostały zaktualizowane w celu obsługi najnowszych wersji modułów platformy Azure, które są dostępne.

Poniżej przedstawiono ograniczenia dotyczące bieżącego rozwiązania:

- Zarządza maszynami wirtualnymi w dowolnym regionie, ale może być używany tylko w tej samej subskrypcji co konto usługi Azure Automation.
- Jest on dostępny na platformie Azure i azure dla instytucji rządowych do dowolnego regionu, który obsługuje obszar roboczy usługi Log Analytics, konto usługi Azure Automation i alerty. Regiony platformy Azure Dla Instytucji Rządowych obecnie nie obsługują funkcji poczty e-mail.

## <a name="solution-prerequisites"></a>Wymagania wstępne rozwiązania

Podręczniki dla tego rozwiązania działają z [kontem Azure Run As](automation-create-runas-account.md). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa uwierzytelniania certyfikatu zamiast hasła, które może często wygasać lub zmieniać.

Zaleca się użycie oddzielnego konta automatyzacji dla **maszyn wirtualnych Start/stop podczas rozwiązania poza godzinami pracy.** Wersje modułów platformy Azure są często uaktualniane, a ich parametry mogą ulec zmianie. Rozwiązanie nie jest uaktualniane w tym samym rytmie i może nie działać z nowszymi wersjami poleceń cmdlet, których używa. Zaleca się przetestowanie aktualizacji modułu na koncie automatyzacji testu przed zaimportowanie ich na kontach automatyzacji produkcji.

## <a name="solution-permissions"></a>Uprawnienia rozwiązania

Musisz mieć pewne uprawnienia do wdrażania **maszyn wirtualnych Start/stop podczas rozwiązania poza godzinami pracy.** Uprawnienia są różne, jeśli rozwiązanie używa wstępnie utworzonego konta automatyzacji i obszaru roboczego usługi Log Analytics z uprawnień wymaganych, jeśli rozwiązanie tworzy nowe konto i obszar roboczy podczas wdrażania. 

Nie musisz konfigurować uprawnień, jeśli jesteś współautorem subskrypcji i administratorem globalnym w dzierżawie usługi Azure Active Directory. Jeśli nie masz tych praw lub musisz skonfigurować rolę niestandardową, upewnij się, że masz uprawnienia opisane poniżej.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Uprawnienia do istniejącego konta automatyzacji i obszaru roboczego usługi Log Analytics

Aby wdrożyć **maszyny wirtualne Start/stop podczas** poza godzinami pracy w istniejącym obszarze roboczym usługi Automation i usługi Log Analytics, użytkownik wdrażający rozwiązanie wymaga następujących uprawnień w zakresie grupy zasobów. Aby dowiedzieć się więcej o rolach, zobacz [Role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

| Uprawnienie | Zakres|
| --- | --- |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/variables/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/połączenia/zapis | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/certificates/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/modules/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/moduły/odczyt | Grupa zasobów |
| Microsoft.automation/automationKonta/zadaniaSześci/zapis | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read | Grupa zasobów |
| Microsoft.OperationsManagement/solutions/write | Grupa zasobów |
| Microsoft.OperationalInsights/obszary robocze/* | Grupa zasobów |
| Microsoft.Insights/diagnosticSettings/write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/Write | Grupa zasobów |
| Microsoft.Insights/ActionGroups/read | Grupa zasobów |
| Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Grupa zasobów |
| Zasoby/wdrożenia firmy Microsoft/* | Grupa zasobów |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Uprawnienia dla nowego konta automatyzacji i nowego obszaru roboczego usługi Log Analytics

Maszyny **wirtualne Start/stop podczas** poza godzinami pracy można wdrożyć w nowym obszarze roboczym usługi Automation i usługi Log Analytics. W takim przypadku użytkownik wdrażający rozwiązanie potrzebuje uprawnień zdefiniowanych w poprzedniej sekcji, a także uprawnień zdefiniowanych w tej sekcji. 

Użytkownik wdrażający rozwiązanie potrzebuje następujących ról:

- Coadministrator w subskrypcji. Ta rola jest wymagana do utworzenia konta Klasyczne uruchamianie jako, jeśli zamierzasz zarządzać klasycznymi maszynami wirtualnymi. [Konta Classic Run As](automation-create-standalone-account.md#create-a-classic-run-as-account) nie są już domyślnie tworzone.
- Członkostwo w roli dewelopera aplikacji [usługi Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Aby uzyskać więcej informacji na temat konfigurowania funkcji Uruchom jako konta, zobacz [Uprawnienia do konfigurowania kont Uruchom jako](manage-runas-account.md#permissions).
- Współautor w ramach subskrypcji lub następujących uprawnień.

| Uprawnienie |Zakres|
| --- | --- |
| Microsoft.Autoryzacja/Operacje/odczyt | Subskrypcja|
| Microsoft.Autoryzacja/uprawnienia/odczyt |Subskrypcja|
| Microsoft.Authorization/roleAssignments/read | Subskrypcja |
| Microsoft.Authorization/roleAssignments/write | Subskrypcja |
| Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft. | Subskrypcja |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/połączenia/odczyt | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationAccounts/certificates/read | Grupa zasobów |
| Automatyzacja/automatyzacja Microsoft.AutomationKonta/zapis | Grupa zasobów |
| Microsoft.OperationalInsights/workspaces/write | Grupa zasobów |

## <a name="solution-components"></a>Składniki rozwiązania

Maszyny **wirtualne Start/stop podczas rozwiązania poza godzinami pracy** zawiera wstępnie skonfigurowane wzorce, harmonogramy i integrację z dziennikami usługi Azure Monitor. Za pomocą tych elementów można dostosować uruchamianie i zamykanie maszyn wirtualnych do potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy runbook, które rozwiązanie wdraża na koncie automatyzacji. NIE należy wprowadzać zmian w kodzie uruchomieniu. . Zamiast tego napisz własny podręcznik runbook dla nowych funkcji.

> [!IMPORTANT]
> Nie uruchamiaj bezpośrednio żadnego umnienia z **nianią** z dodaniem podrzędnego do jego nazwy.

Wszystkie nadrzędne elementy `WhatIf` runbook zawierają parametr. Po ustawieniu true, parametr obsługuje szczegółowo dokładne zachowanie runbook trwa podczas uruchamiania bez parametru i sprawdza, czy poprawne maszyny wirtualne są ukierunkowane. Projekt runbook wykonuje tylko zdefiniowane `WhatIf` akcje, gdy parametr jest ustawiony na False.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (100) <br> Powiadomienie <br> Identyfikator elementów WebHookURI | Wywoływana z nadrzędnego egonatu. Ten element runbook tworzy alerty na podstawie dla dla scenariusza auto-stop.|
|AutoStop_CreateAlert_Parent | Lista maszyn wirtualnych<br> CoIf: Prawda lub Fałsz  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszynach wirtualnych w grupach subskrypcji lub zasobów docelowych. <br> `VMList`jest oddzieloną przecinkami listą maszyn wirtualnych. Na przykład `vm1, vm2, vm3`.<br> `WhatIf`umożliwia sprawdzanie poprawności logiki uruchomieniu bez wykonywania.|
|AutoStop_Disable | Brak | Wyłącza alerty auto-stop i domyślny harmonogram.|
|AutoStop_VM_Child | Element WebHookData | Wywoływana z nadrzędnego egonatu. Reguły alertów wywołać ten system runbook, aby zatrzymać klasycznej maszyny Wirtualnej.|
|AutoStop_VM_Child_ARM | Element WebHookData |Wywoływana z nadrzędnego egonatu. Reguły alertów wywołać ten projekt uruchamiany, aby zatrzymać maszynę wirtualną.  |
|ScheduledStartStop_Base_Classic | Nazwa usługi w chmurze<br> Działanie: Uruchamianie lub zatrzymywać<br> Lista maszyn wirtualnych  | Wykonuje akcję start lub stop w klasycznej grupie maszyn wirtualnych przez usługi w chmurze. |
|ScheduledStartStop_Child | VMName <br> Działanie: Uruchamianie lub zatrzymywać <br> ResourceGroupName | Wywoływana z nadrzędnego egonatu. Wykonuje akcję rozpoczęcia lub zatrzymania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Child_Classic | VMName<br> Działanie: Uruchamianie lub zatrzymywać<br> ResourceGroupName | Wywoływana z nadrzędnego egonatu. Wykonuje akcję start lub stop dla zaplanowanego zatrzymania dla klasycznych maszyn wirtualnych. |
|ScheduledStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br>Lista maszyn wirtualnych <br> CoIf: Prawda lub Fałsz | Uruchamia lub zatrzymuje wszystkie maszyny wirtualne w ramach subskrypcji. Edytuj zmienne `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` i wykonywać tylko w tych docelowych grupach zasobów. Można również wykluczyć określonych maszyn wirtualnych, aktualizując zmienną. `External_ExcludeVMNames`|
|SequencedStartStop_Parent | Działanie: Uruchamianie lub zatrzymywać <br> CoIf: Prawda lub Fałsz<br>Lista maszyn wirtualnych| Tworzy tagi o nazwie **sequencestart** i **sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencjonować działanie start/stop. W tych nazwach tagów rozróżniana jest wielkość liter. Wartość znacznika powinna być dodatnią liczą całkowitą (1, 2, 3), która odpowiada kolejności, w jakiej chcesz rozpocząć lub zatrzymać. <br>**Uwaga:** Maszyny wirtualne muszą znajdować `External_Start_ResourceGroupNames`się `External_Stop_ResourceGroupNames`w `External_ExcludeVMNames` grupach zasobów zdefiniowanych w programie i zmiennych. Muszą mieć odpowiednie tagi, aby akcje zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

W poniższej tabeli wymieniono zmienne utworzone na koncie automatyzacji. Modyfikuj tylko zmienne poprzedzone . `External` Modyfikowanie zmiennych poprzedzonych `Internal` powoduje niepożądane efekty.

> [!NOTE]
> Ograniczenia dotyczące nazwy maszyny Wirtualnej i grupy zasobów są w dużej mierze wynikiem zmiennego rozmiaru.

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne `GreaterThan`wartości `GreaterThanOrEqual` `LessThan`to `LessThanOrEqual`, , i .|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę wirtualną, jeśli procent procesora CPU przekracza próg.|
|External_AutoStop_Frequency | Częstotliwość oceny dla reguły. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin. |
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma być skonfigurowana reguła usługi Azure Alert.|
|External_AutoStop_Severity | Ważność alertu metryki, który może wynosić od 0 do 4. |
|External_AutoStop_Threshold | Próg dla reguły alertu azure `External_AutoStop_MetricName`określony w zmiennej . Wartości procentowe wahają się od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu zastosowany do wybranego rozmiaru okna w celu oceny warunku. Dopuszczalne `Average`wartości `Minimum` `Maximum`to `Total`, `Last`, , i .|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym platforma Azure analizuje wybrane metryki do wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Wartość określająca, czy klasyczne maszyny wirtualne są kierowane przez rozwiązanie. Wartością domyślną jest True. Ustaw tę zmienną na False dla subskrypcji dostawcy rozwiązań w chmurze (CSP) platformy Azure. Klasyczne maszyny wirtualne wymagają [klasycznego konta uruchom jako.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Oddzielona przecinkami lista nazw maszyn wirtualnych do wykluczenia, ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do listy, maszyny wirtualne, które są ustawione do wykluczenia, mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Oddzielona przecinkami lista jednej lub więcej grup zasobów, które są przeznaczone do uruchamiania akcji.|
|External_Stop_ResourceGroupNames | Oddzielona przecinkami lista jednej lub więcej grup zasobów, które są przeznaczone dla akcji zatrzymania.|
|External_WaitTimeForVMRetrySeconds |Czas oczekiwania w sekundach dla akcji, które mają być wykonywane na maszynach wirtualnych dla **SequencedStartStop_Parent** runbook. Ta zmienna umożliwia runbook czekać na operacje podrzędne przez określoną liczbę sekund przed kontynuowaniem następnej akcji. Maksymalny czas oczekiwania wynosi 10800 lub trzy godziny. Wartość domyślna to 2100 sekund.|
|Internal_AutomationAccountName | Określa nazwę konta automatyzacji.|
|Internal_AutoSnooze_ARM_WebhookURI | Identyfikator URI elementu webhook wywołany scenariuszem autostopu dla maszyn wirtualnych.|
|Internal_AutoSnooze_WebhookUri | Identyfikator URI elementu webhook wywołany scenariuszem Autostop dla klasycznych maszyn wirtualnych.|
|Internal_AzureSubscriptionId | Identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Nazwa grupy zasobów konta automatyzacji.|

>[!NOTE]
>Dla zmiennej `External_WaitTimeForVMRetryInSeconds`wartość domyślna została zaktualizowana z 600 do 2100. 

We wszystkich `External_Start_ResourceGroupNames`scenariuszach zmienne `External_Stop_ResourceGroupNames`i `External_ExcludeVMNames` są niezbędne do kierowania na maszyny wirtualne, z wyjątkiem list maszyn wirtualnych oddzielonych przecinkami dla **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**i **ScheduledStartStop_Parent** likemi. Oznacza to, że maszyny wirtualne muszą należeć do grup zasobów docelowych, aby akcje uruchamiania i zatrzymywania się miały miejsce. Logika działa podobnie do zasad platformy Azure, w tym można kierować subskrypcji lub grupy zasobów i akcje dziedziczone przez nowo utworzonych maszyn wirtualnych. Takie podejście pozwala uniknąć konieczności utrzymywania oddzielnego harmonogramu dla każdej maszyny Wirtualnej i zarządzania uruchomieniami i zatrzymaniami w skali.

### <a name="schedules"></a>Harmonogramy

W poniższej tabeli wymieniono każdy z domyślnych harmonogramów utworzonych na koncie automatyzacji.Można je zmodyfikować lub utworzyć własne harmonogramy niestandardowe.Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem **harmonogramów Scheduled_StartVM** i **Scheduled_StopVM.**

Nie włączaj wszystkich harmonogramów, ponieważ może to spowodować utworzenie nakładających się akcji harmonogramu. Najlepiej określić, które optymalizacje chcesz wykonać i odpowiednio je zmodyfikować. Zobacz przykładowe scenariusze w sekcji przeglądowej, aby uzyskać dalsze wyjaśnienia.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia **AutoStop_CreateAlert_Parent** runbook co 8 godzin, co z kolei zatrzymuje wartości `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`oparte `External_ExcludeVMNames` na maszynie Wirtualnej w programach i zmiennych. Alternatywnie można określić listę maszyn wirtualnych oddzielonych przecinkami przy użyciu parametru. `VMList`|
|Scheduled_StopVM | Definiowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** runbook z parametrem `Stop` każdego dnia o określonej godzinie.Automatycznie zatrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmiennych.Włącz powiązany harmonogram **Scheduled-StartVM**.|
|Scheduled_StartVM | Definiowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** runbook z wartością parametru `Start` każdego dnia o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmiennych.Włącz powiązany harmonogram **Scheduled-StopVM**.|
|Sekwencyjny-StopVM | 1:00 (UTC), w każdy piątek | Uruchamia Sequenced_Parent runbook z wartością parametru `Stop` w każdy piątek o określonej godzinie.Sekwencyjnie (rosnąco) zatrzymuje wszystkie maszyny wirtualne z tagiem **SequenceStop** zdefiniowane przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości znaczników i zmiennych zasobów, zobacz sekcję Księgi ekscjowe.Włącz powiązany **harmonogram, Sequenced-StartVM**.|
|Sekwencjonowane-StartVM | 13:00 (UTC), w każdy poniedziałek | Uruchamia **SequencedStopStart_Parent** runbook z wartością parametru `Start` każdego poniedziałku o określonej godzinie. Sekwencyjnie (malejąco) uruchamia wszystkie maszyny wirtualne z tagiem **SequenceStart** zdefiniowane przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości znaczników i zasobów zmiennych, zobacz [Programy runbook .](#runbooks) Włącz powiązany **harmonogram, Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Zastosowanie rozwiązania z klasycznymi maszynami wirtualnymi

Jeśli używasz **start/stop maszyn wirtualnych podczas poza godzinami pracy** rozwiązanie dla klasycznych maszyn wirtualnych, a następnie automatyzacji przetwarza wszystkie maszyny wirtualne sekwencyjnie na usługę w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze. 

Do korzystania z rozwiązania z klasycznych maszyn wirtualnych, trzeba klasyczne uruchom jako konto, które nie jest tworzony domyślnie. Aby uzyskać instrukcje dotyczące tworzenia konta Klasycznego uruchamiania jako, zobacz [Tworzenie konta klasycznego uruchamiania jako](automation-create-standalone-account.md#create-a-classic-run-as-account).

Jeśli masz więcej niż 20 maszyn wirtualnych na usługę w chmurze, oto kilka zaleceń:

* Utwórz wiele harmonogramów z **nadrzędnym elementem** runbook ScheduledStartStop_Parent i określaniem 20 maszyn wirtualnych na harmonogram. 
* We właściwościach harmonogramu `VMList` użyj parametru, aby określić nazwy maszyn wirtualnych jako listę oddzieloną przecinkami. 

W przeciwnym razie jeśli zadanie automatyzacji dla tego rozwiązania działa dłużej niż trzy godziny, jest tymczasowo zwalniane lub zatrzymane na limit [sprawiedliwego udziału.](automation-runbook-execution.md#fair-share)

Subskrypcje usługi Azure CSP obsługują tylko model usługi Azure Resource Manager. Usługi usługi Usługi Resource Manager inne niż Azure nie są dostępne w programie. Po uruchomieniu **maszyny wirtualnej Start/stop podczas pracy rozwiązania poza godzinami pracy** mogą pojawić się błędy, ponieważ ma polecenia cmdlet do zarządzania zasobami klasycznymi. Aby dowiedzieć się więcej o CSP, zobacz [Dostępne usługi w subskrypcjach CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Jeśli używasz subskrypcji CSP, należy ustawić zmienną [External_EnableClassicVMs](#variables) na False po wdrożeniu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Włączanie rozwiązania

Aby rozpocząć korzystanie z rozwiązania, wykonaj kroki opisane w [włącz rozwiązanie Start/stop VMs](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Wyświetlanie rozwiązania

Użyj jednego z następujących mechanizmów, aby uzyskać dostęp do rozwiązania po jego włączeniu:

* Na koncie automatyzacji wybierz **pozycję Start/Stop VM** w obszarze **Zasoby pokrewne**. Na stronie Start/Stop VM wybierz pozycję **Zarządzaj rozwiązaniem** z prawej strony w obszarze **Zarządzanie rozwiązaniami startowymi/stopowymi**maszyn wirtualnych .

* Przejdź do obszaru roboczego usługi Log Analytics połączonego z kontem automatyzacji. Po wybraniu obszaru roboczego wybierz pozycję **Rozwiązania** z lewego okienka. Na stronie Rozwiązania wybierz z listy rozwiązanie **Start-Stop-Stop-VM[workspace].**  

Wybranie rozwiązania powoduje wyświetlenie strony rozwiązania **Start-Stop-Stop-VM[obszar roboczy].** W tym miejscu można przejrzeć ważne szczegóły, takie jak informacje na kafelku **StartStopVM.** Podobnie jak w obszarze roboczym usługi Log Analytics, ten kafelek wyświetla liczbę i graficzną reprezentację zadań runbook dla rozwiązania, które zostały uruchomione i zakończone pomyślnie.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Dalszą analizę rekordów zadań można przeprowadzić, klikając kafelek pierścienia. Pulpit nawigacyjny rozwiązania pokazuje historię zadań i wstępnie zdefiniowane zapytania wyszukiwania dziennika. Przełącz się do zaawansowanego portalu analizy dzienników, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="update-the-solution"></a>Aktualizowanie rozwiązania

Jeśli wdrożono poprzednią wersję tego rozwiązania, usuń go z konta przed wdrożeniem zaktualizowanej wersji. Wykonaj kroki, aby [usunąć rozwiązanie,](#remove-the-solution) a następnie wykonaj kroki, aby [wdrożyć rozwiązanie](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Usuwanie rozwiązania

Jeśli nie trzeba już używać rozwiązania, można go usunąć z konta automatyzacji. Usunięcie rozwiązania usuwa tylko runbooki. Nie powoduje usunięcia harmonogramów ani zmiennych, które zostały utworzone podczas dodawania rozwiązania. Usuń te zasoby ręcznie, jeśli nie używasz ich z innymi systemami runbook.

Aby usunąć rozwiązanie:

1. Na koncie automatyzacji wybierz pozycję **Połączony obszar roboczy** w obszarze **Powiązane zasoby**.

2. Wybierz **pozycję Przejdź do obszaru roboczego**.

3. Kliknij **pozycję Rozwiązania** w obszarze **Ogólne**. 

4. Na stronie Rozwiązania wybierz rozwiązanie **Start-Stop-VM[Obszar roboczy].** 

5. Na stronie **VMManagementSolution[Workspace]** wybierz polecenie **Usuń** z menu.<br><br> ![Usuń rozwiązanie VM Mgmt](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. W oknie **Usuń rozwiązanie** upewnij się, że chcesz usunąć rozwiązanie.

7. Gdy informacje są weryfikowane, a rozwiązanie jest usuwane, można śledzić postęp w **obszarze Powiadomienia**, wybrane z menu. Po uruchomieniu procesu usuwania rozwiązania zostanie przywrócony do strony Rozwiązania.

Konto automatyzacji i obszar roboczy usługi Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego usługi Log Analytics, musisz ręcznie usunąć go z witryny Azure portal:

1. Wyszukaj i wybierz **obszary robocze usługi Log Analytics**.

2. Na stronie Obszaru roboczego usługi Log Analytics wybierz obszar roboczy.

3. Z menu na stronie ustawienia obszaru roboczego wybierz **polecenie Usuń.**

4. Jeśli nie chcesz zachować [składników rozwiązania](#solution-components)konta usługi Azure Automation, możesz ręcznie usunąć wszystkie.

## <a name="next-steps"></a>Następne kroki

[Włącz](automation-solution-vm-management-enable.md) **maszyny wirtualne Start/stop podczas** rozwiązania poza godzinami pracy dla maszyn wirtualnych platformy Azure.
