---
title: Omówienie Start/Stop VMs during off-hours Azure Automation
description: W tym artykule opisano funkcję Start/Stop VMs during off-hours, która uruchamia lub wstrzymuje maszyny wirtualne zgodnie z harmonogramem i aktywnie monitoruje je z dzienników Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: b71e5b1a8ba5f3ee8f883c71a7221e01d4af4fb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597712"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Przegląd Start/Stop VMs during off-hours

Funkcja Start/Stop VMs during off-hours uruchamiania lub zatrzymywania włączonych maszyn wirtualnych platformy Azure. Uruchamia lub kończy maszyny w harmonogramach zdefiniowanych przez użytkownika, udostępnia szczegółowe informacje za pośrednictwem dzienników Azure Monitor i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji](../azure-monitor/alerts/action-groups.md). Tę funkcję można włączyć na Azure Resource Manager i klasycznych maszynach wirtualnych w większości scenariuszy.

Ta funkcja używa polecenia cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) do uruchamiania maszyn wirtualnych. Do zatrzymywania maszyn wirtualnych jest wykorzystywany [komunikat STOP-AzVM](/powershell/module/az.compute/stop-azvm) .

> [!NOTE]
> Mimo że elementy Runbook zostały zaktualizowane do korzystania z nowych poleceń cmdlet platformy Azure AZ module, używają aliasu prefiksu AzureRM.

> [!NOTE]
> Zaktualizowano Start/Stop VMs during off-hours, aby obsługiwały najnowsze wersje modułów platformy Azure, które są dostępne. Zaktualizowana wersja tej funkcji dostępna w portalu Marketplace nie obsługuje modułów AzureRM, ponieważ przeprowadzono migrację z AzureRM do AZ modules.

Ta funkcja udostępnia zdecentralizowaną opcję automatyzacji o niskich kosztach dla użytkowników, którzy chcą zoptymalizować koszty maszyn wirtualnych. Za pomocą tej funkcji można:

- [Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych](automation-solution-vm-management-config.md#schedule).
- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w porządku rosnącym przy [użyciu tagów platformy Azure](automation-solution-vm-management-config.md#tags). To działanie nie jest obsługiwane w przypadku klasycznych maszyn wirtualnych.
- Zatrzymaj zatrzymywanie maszyn wirtualnych na podstawie [niskiego użycia procesora CPU](automation-solution-vm-management-config.md#cpuutil).

Poniżej przedstawiono ograniczenia związane z bieżącą funkcją:

- Zarządza maszynami wirtualnymi w dowolnym regionie, ale mogą być używane tylko w tej samej subskrypcji co konto Azure Automation.
- Jest ona dostępna na platformie Azure i Azure Government dla każdego regionu, który obsługuje Log Analytics obszar roboczy, konto Azure Automation i alerty. Regiony Azure Government obecnie nie obsługują funkcji poczty e-mail.

> [!NOTE]
> Przed zainstalowaniem tej wersji będziemy wiedzieć o [następnej wersji](https://github.com/microsoft/startstopv2-deployments), która jest teraz w wersji zapoznawczej.  Ta nowa wersja (wersja 2) oferuje wszystkie funkcje, które są takie same, ale zaprojektowano w celu wykorzystania nowszej technologii platformy Azure. Dodaje niektóre z często zażądanych funkcji od klientów, takich jak obsługa wielosubskrypcji z jednego wystąpienia uruchamiania/zatrzymywania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcja elementów Runbook dla maszyn wirtualnych uruchamiania/zatrzymywania w trakcie godzin pracy działa z [kontem Uruchom jako platformy Azure](./automation-security-overview.md#run-as-accounts). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa uwierzytelniania certyfikatu zamiast hasła, które może wygasnąć lub zmienić.

- [Azure Monitor Log Analytics obszar roboczy](../azure-monitor/logs/design-logs-deployment.md) , w którym są przechowywane dzienniki zadań elementu Runbook i wyniki strumienia zadań w obszarze roboczym w celu zbadania i przeanalizowania. Konto usługi Automation może być połączone z nowym lub istniejącym obszarem roboczym Log Analytics, a oba zasoby muszą znajdować się w tej samej grupie zasobów.

Zalecamy używanie oddzielnego konta usługi Automation do pracy z maszynami wirtualnymi z włączoną funkcją Start/Stop VMs during off-hours. Wersje modułów platformy Azure są często uaktualniane i ich parametry mogą ulec zmianie. Funkcja nie została uaktualniona na tym samym erze i może nie współpracować z nowszymi wersjami poleceń cmdlet, których używa. Przed zaimportowaniem zaktualizowanych modułów do kont automatyzacji produkcji zalecamy zaimportowanie ich do konta automatyzacji testów w celu sprawdzenia, czy nie występują żadne problemy ze zgodnością.

## <a name="permissions"></a>Uprawnienia

Aby włączyć maszyny wirtualne dla funkcji Start/Stop VMs during off-hours, musisz mieć pewne uprawnienia. Uprawnienia są różne w zależności od tego, czy funkcja używa wstępnie utworzonego konta usługi Automation i obszaru roboczego Log Analytics, czy tworzy nowe konto i obszar roboczy.

Nie musisz konfigurować uprawnień, jeśli jesteś współautorem subskrypcji i administratorem globalnym w dzierżawie usługi Azure Active Directory (AD). Jeśli nie masz tych praw lub musisz skonfigurować rolę niestandardową, upewnij się, że masz uprawnienia opisane poniżej.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Uprawnienia do istniejącego już konta usługi Automation i obszaru roboczego Log Analytics

Aby włączyć maszyny wirtualne dla funkcji Start/Stop VMs during off-hours przy użyciu istniejącego konta usługi Automation i Log Analytics obszaru roboczego, wymagane są następujące uprawnienia do zakresu grupy zasobów. Aby dowiedzieć się więcej na temat ról, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

| Uprawnienie | Zakres|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/zmienne/zapis | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Schedules/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Runbooks/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Connections/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Certificates/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/moduły/zapis | Grupa zasobów |
| Microsoft. Automation/automationAccounts/moduły/odczyt | Grupa zasobów |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Jobs/Write | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Jobs/Read | Grupa zasobów |
| Microsoft. OperationsManagement/Solutions/Write | Grupa zasobów |
| Microsoft. OperationalInsights/obszary robocze/* | Grupa zasobów |
| Microsoft. Insights/diagnosticSettings/Write | Grupa zasobów |
| Microsoft. Insights/ActionGroups/Write | Grupa zasobów |
| Microsoft. Insights/ActionGroups/Read | Grupa zasobów |
| Microsoft. resources/subscriptions/resourceGroups/Read | Grupa zasobów |
| Microsoft. resources/Deployments/* | Grupa zasobów |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Uprawnienia dla nowego konta usługi Automation i nowego obszaru roboczego Log Analytics

Maszyny wirtualne można włączyć dla funkcji Start/Stop VMs during off-hours przy użyciu nowego konta usługi Automation i Log Analytics obszaru roboczego. W takim przypadku wymagane są uprawnienia zdefiniowane w poprzedniej sekcji oraz uprawnienia zdefiniowane w tej sekcji. Wymagane są również następujące role:

- Co-Administrator subskrypcji. Ta rola jest wymagana do utworzenia klasycznego konta Uruchom jako, jeśli zamierzasz zarządzać klasycznymi maszynami wirtualnymi. [Klasyczne konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account) nie są już domyślnie tworzone.
- Członkostwo w roli Deweloper aplikacji [usługi Azure AD](../active-directory/roles/permissions-reference.md) . Aby uzyskać więcej informacji na temat konfigurowania kont Uruchom jako, zobacz [uprawnienia do konfigurowania kont Uruchom jako](automation-security-overview.md#permissions).
- Współautorem subskrypcji lub następujących uprawnień.

| Uprawnienie |Zakres|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Subskrypcja|
| Microsoft. Autoryzacja/uprawnienia/Odczyt |Subskrypcja|
| Microsoft. Authorization/roleAssignments/Read | Subskrypcja |
| Microsoft.Authorization/roleAssignments/write | Subskrypcja |
| Microsoft. Authorization/roleAssignments/Delete | Subskrypcja |
| Microsoft. Automation/automationAccounts/Connections/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Certificates/Read | Grupa zasobów |
| Microsoft. Automation/automationAccounts/Write | Grupa zasobów |
| Microsoft. OperationalInsights/obszary robocze/zapis | Grupa zasobów |

## <a name="components"></a>Składniki

Funkcja Start/Stop VMs during off-hours obejmuje wstępnie skonfigurowane elementy Runbook, harmonogramy i integrację z dziennikami Azure Monitor. Za pomocą tych elementów można dostosować uruchamianie i zamykanie maszyn wirtualnych zgodnie z potrzebami firmy.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy Runbook, które ta funkcja wdraża na koncie usługi Automation. NIE wprowadzaj zmian w kodzie elementu Runbook. Zamiast tego napisz własny element Runbook pod kątem nowych funkcji.

> [!IMPORTANT]
> Nie uruchamiaj bezpośrednio żadnego elementu Runbook z dołączonym **elementem podrzędnym** do jego nazwy.

Wszystkie nadrzędne elementy Runbook zawierają `WhatIf` parametr. Po ustawieniu na wartość true, parametr obsługuje szczegółowo dokładne zachowanie wykonywane przez element Runbook, gdy jest uruchamiany bez parametru i sprawdza, czy są wskazane poprawne maszyny wirtualne. Element Runbook wykonuje tylko zdefiniowane akcje, gdy `WhatIf` parametr ma wartość false.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Wywoływana z nadrzędnego elementu Runbook. Ten element Runbook tworzy alerty dotyczące poszczególnych zasobów dla scenariusza automatycznie zatrzymywania.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true lub false  | Tworzy lub aktualizuje reguły alertów platformy Azure na maszynach wirtualnych w ramach dostosowanej subskrypcji lub grup zasobów. <br> `VMList` jest rozdzielaną przecinkami listą maszyn wirtualnych (bez odstępów), na przykład `vm1,vm2,vm3` .<br> `WhatIf` Włącza weryfikację logiki elementu Runbook bez wykonywania operacji.|
|AutoStop_Disable | Brak | Wyłącza alerty autozatrzymaj i domyślny harmonogram.|
|AutoStop_VM_Child | WebHookData | Wywoływana z nadrzędnego elementu Runbook. Reguły alertów wywołują ten element Runbook, aby zatrzymać klasyczną maszynę wirtualną.|
|AutoStop_VM_Child_ARM | WebHookData |Wywoływana z nadrzędnego elementu Runbook. Reguły alertów wywołują ten element Runbook, aby zatrzymać maszynę wirtualną.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Akcja: uruchamianie lub zatrzymywanie<br> VMList  | Wykonuje akcję uruchamiania lub zatrzymywania w klasycznej grupie maszyn wirtualnych Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Akcja: uruchamianie lub zatrzymywanie <br> ResourceGroupName | Wywoływana z nadrzędnego elementu Runbook. Wykonuje akcję uruchamiania lub zatrzymywania dla zaplanowanego zatrzymania.|
|ScheduledStartStop_Child_Classic | VMName<br> Akcja: uruchamianie lub zatrzymywanie<br> ResourceGroupName | Wywoływana z nadrzędnego elementu Runbook. Wykonuje akcję uruchamiania lub zatrzymywania dla zaplanowanego zatrzymania dla klasycznych maszyn wirtualnych. |
|ScheduledStartStop_Parent | Akcja: uruchamianie lub zatrzymywanie <br>VMList <br> WhatIf: true lub false | Uruchamia lub kończy wszystkie maszyny wirtualne w subskrypcji. Edytuj zmienne `External_Start_ResourceGroupNames` i `External_Stop_ResourceGroupNames` Wykonaj tylko te grupy zasobów przeznaczonych do wykonania. Można również wykluczyć określone maszyny wirtualne, aktualizując `External_ExcludeVMNames` zmienną.|
|SequencedStartStop_Parent | Akcja: uruchamianie lub zatrzymywanie <br> WhatIf: true lub false<br>VMList| Tworzy Tagi o nazwie **sequencestart** i **sequencestop** na każdej maszynie wirtualnej, dla której chcesz sekwencjonować działanie uruchamiania/zatrzymywania. W tych nazwach tagów jest rozróżniana wielkość liter. Wartość znacznika powinna być listą dodatnich liczb całkowitych, na przykład, `1,2,3` odpowiadającą kolejności, w której chcesz uruchomić lub zatrzymać. <br>**Uwaga**: maszyny wirtualne muszą znajdować się w grupach zasobów zdefiniowanych w `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` zmiennych, i `External_ExcludeVMNames` . Muszą mieć odpowiednie Tagi, aby akcje zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

Poniższa tabela zawiera listę zmiennych utworzonych na koncie usługi Automation. Modyfikuj tylko zmienne poprzedzone prefiksem `External` . Modyfikowanie zmiennych poprzedzonych prefiksem `Internal` powoduje niepożądane skutki.

> [!NOTE]
> Ograniczenia dotyczące nazwy maszyny wirtualnej i grupy zasobów są znacznie wynikiem zmiennej wielkości. Zobacz [zmienne zasoby w Azure Automation](./shared-resources/variables.md).

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagany do skonfigurowania warunku przed wyzwoleniem alertu. Dopuszczalne wartości to `GreaterThan` , `GreaterThanOrEqual` , `LessThan` , i `LessThanOrEqual` .|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę wirtualną, jeśli procent procesora CPU przekroczy wartość progową.|
|External_AutoStop_Frequency | Częstotliwość oceny dla reguły. Ten parametr akceptuje dane wejściowe w formacie TimeSpan. Możliwe wartości to od 5 minut do 6 godzin. |
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla której ma zostać skonfigurowana reguła alertu platformy Azure.|
|External_AutoStop_Severity | Ważność alertu metryki, która może mieć wartość z zakresu od 0 do 4. |
|External_AutoStop_Threshold | Próg reguły alertu platformy Azure określony w zmiennej `External_AutoStop_MetricName` . Wartości procentowe mieszczą się w zakresie od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu stosowany do wybranego rozmiaru okna, aby oszacować warunek. Dopuszczalne wartości to `Average` , `Minimum` , `Maximum` , `Total` i `Last` .|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym platforma Azure analizuje wybrane metryki na potrzeby wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie TimeSpan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EnableClassicVMs| Wartość określająca, czy klasyczne maszyny wirtualne są objęte funkcją. Wartość domyślna to true. Ustaw dla tej zmiennej wartość false dla subskrypcji dostawcy rozwiązań w chmurze Azure (CSP). Klasyczne maszyny wirtualne wymagają [klasycznego konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Rozdzielana przecinkami lista nazw maszyn wirtualnych do wykluczenia, ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do listy, maszyny wirtualne określone do wykluczenia mogą zostać przypadkowo uruchomione lub zatrzymane.|
|External_Start_ResourceGroupNames | Rozdzielana przecinkami lista jednej lub większej liczby grup zasobów przeznaczonych do uruchamiania akcji.|
|External_Stop_ResourceGroupNames | Rozdzielana przecinkami lista co najmniej jednej grupy zasobów, która jest przeznaczona dla akcji zatrzymania.|
|External_WaitTimeForVMRetrySeconds |Czas oczekiwania (w sekundach) wykonywania akcji na maszynach wirtualnych dla **SequencedStartStop_Parent** elementu Runbook. Ta zmienna umożliwia elementowi Runbook oczekiwanie na wykonanie operacji podrzędnych przez określoną liczbę sekund przed przejściem do następnej akcji. Maksymalny czas oczekiwania wynosi 10800 lub trzy godziny. Wartość domyślna to 2100 sekund.|
|Internal_AutomationAccountName | Określa nazwę konta usługi Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | Identyfikator URI elementu webhook został wywołany dla scenariusza autostop dla maszyn wirtualnych.|
|Internal_AutoSnooze_WebhookUri | Identyfikator URI elementu webhook został wywołany dla scenariusza autostop dla klasycznych maszyn wirtualnych.|
|Internal_AzureSubscriptionId | Identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Nazwa grupy zasobów konta usługi Automation.|

>[!NOTE]
>Dla zmiennej `External_WaitTimeForVMRetryInSeconds` wartość domyślna została zaktualizowana z 600 do 2100.

We wszystkich scenariuszach zmienne `External_Start_ResourceGroupNames` ,  `External_Stop_ResourceGroupNames` i `External_ExcludeVMNames` są niezbędne do określania docelowych maszyn wirtualnych, z wyjątkiem list maszyn wirtualnych rozdzielonych przecinkami dla elementów runbook **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** i **ScheduledStartStop_Parent** . Oznacza to, że maszyny wirtualne muszą należeć do docelowych grup zasobów, aby akcje uruchamiania i zatrzymywania zostały wykonane. Logika działa podobnie jak Azure Policy, w którym można kierować do subskrypcji lub grupy zasobów, a akcje są dziedziczone przez nowo utworzone maszyny wirtualne. Takie podejście pozwala uniknąć konieczności utrzymania oddzielnego harmonogramu dla każdej maszyny wirtualnej i zarządzanie rozpoczęciem i zatrzymaniem w skali.

### <a name="schedules"></a>Harmonogramy

Poniższa tabela zawiera listę domyślnych harmonogramów utworzonych w ramach konta usługi Automation. Można je modyfikować lub tworzyć własne harmonogramy niestandardowe. Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem harmonogramów **Scheduled_StartVM** i **Scheduled_StopVM** .

Nie włączaj wszystkich harmonogramów, ponieważ może to spowodować utworzenie nakładających się akcji harmonogramu. Najlepiej określić optymalizacje, które chcesz wykonać, i odpowiednio je zmodyfikować. Zapoznaj się z przykładowymi scenariuszami w sekcji Przegląd w celu uzyskania dalszych wyjaśnień.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia **AutoStop_CreateAlert_Parent** elementu Runbook co 8 godzin, co z kolei powoduje zatrzymanie wartości w `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` zmiennych, i `External_ExcludeVMNames` . Alternatywnie można określić rozdzieloną przecinkami listę maszyn wirtualnych przy użyciu `VMList` parametru.|
|Scheduled_StopVM | Definiowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** elementu Runbook z parametrem `Stop` każdego dnia o określonej godzinie. Automatycznie wstrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmienne. Włącz powiązany harmonogram **zaplanowany — StartVM**.|
|Scheduled_StartVM | Definiowane przez użytkownika, codziennie | Uruchamia **ScheduledStopStart_Parent** elementu Runbook z wartością parametru `Start` każdego dnia o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zasoby zmienne. Włącz powiązany harmonogram **zaplanowany — StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), każdy piątek | Uruchamia **Sequenced_StopStop_Parent** elementu Runbook z wartością parametru `Stop` każdego piątku o określonej godzinie. Sekwencyjnie (rosnąco) przerywa wszystkie maszyny wirtualne ze znacznikiem **SequenceStop** zdefiniowanym przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zmiennych zasobów, zobacz [elementy Runbook](#runbooks). Włącz harmonogram związany z **sekwencją-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), co poniedziałek | Uruchamia **SequencedStopStart_Parent** elementu Runbook z wartością parametru `Start` każdego poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) uruchamia wszystkie maszyny wirtualne z tagiem **SequenceStart** zdefiniowanym przez odpowiednie zmienne. Aby uzyskać więcej informacji na temat wartości tagów i zasobów zmiennych, zobacz [elementy Runbook](#runbooks). Włącz harmonogram związany z **sekwencją-StopVM**.|

## <a name="use-the-feature-with-classic-vms"></a>Korzystanie z funkcji z klasycznymi maszynami wirtualnymi

Jeśli używasz funkcji Start/Stop VMs during off-hours dla klasycznych maszyn wirtualnych, usługa Automation przetwarza wszystkie maszyny wirtualne sekwencyjnie dla każdej usługi w chmurze. Maszyny wirtualne są nadal przetwarzane równolegle w różnych usługach w chmurze. 

Do korzystania z funkcji z klasycznymi maszynami wirtualnymi wymagane jest klasyczne konto Uruchom jako, które nie jest domyślnie tworzone. Aby uzyskać instrukcje dotyczące tworzenia klasycznego konta Uruchom jako, zobacz [Tworzenie klasycznego konta Uruchom jako](automation-create-standalone-account.md#create-a-classic-run-as-account).

Jeśli masz więcej niż 20 maszyn wirtualnych na usługę w chmurze, poniżej przedstawiono niektóre zalecenia:

* Tworzenie wielu harmonogramów z nadrzędnym elementem Runbook **ScheduledStartStop_Parent** i określanie 20 maszyn wirtualnych na harmonogram.
* We właściwościach harmonogramu Użyj parametru, `VMList` Aby określić nazwy maszyn wirtualnych jako listę rozdzieloną przecinkami (bez odstępów).

W przeciwnym razie, jeśli zadanie automatyzacji dla tej funkcji działa dłużej niż trzy godziny, jest tymczasowo zwolnione lub zatrzymane według ograniczonego limitu [udostępniania](automation-runbook-execution.md#fair-share) .

Subskrypcje dostawcy CSP platformy Azure obsługują tylko model Azure Resource Manager. Usługi inne niż Azure Resource Manager nie są dostępne w programie. Po uruchomieniu funkcji Start/Stop VMs during off-hours mogą wystąpić błędy, ponieważ zawiera polecenia cmdlet służące do zarządzania klasycznymi zasobami. Aby dowiedzieć się więcej na temat dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services). W przypadku korzystania z subskrypcji dostawcy CSP należy ustawić dla zmiennej [External_EnableClassicVMs](#variables) wartość false po wdrożeniu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Wyświetl funkcję

Aby uzyskać dostęp do włączonej funkcji, użyj jednego z następujących mechanizmów:

* Z poziomu konta usługi Automation wybierz pozycję **Uruchom/Zatrzymaj maszynę wirtualną** w obszarze **powiązane zasoby**. Na stronie Uruchamianie/zatrzymywanie maszyny wirtualnej wybierz pozycję **Zarządzaj rozwiązaniem** w obszarze **Zarządzaj rozwiązaniami uruchamiania/zatrzymywania maszyn wirtualnych**.

* Przejdź do obszaru roboczego Log Analytics połączonego z kontem usługi Automation. Po wybraniu obszaru roboczego wybierz pozycję **rozwiązania** z okienka po lewej stronie. Na stronie rozwiązania wybierz z listy pozycję **Start-Stop-VM [obszar roboczy]** .  

Po wybraniu funkcji zostanie wyświetlona strona **Start-Stop-VM [obszar roboczy]** . W tym miejscu możesz przejrzeć ważne szczegóły, takie jak informacje na kafelku **StartStopVM** . Tak jak w obszarze roboczym Log Analytics, ten kafelek przedstawia liczbę i graficzną reprezentację zadań elementu Runbook dla funkcji, która została uruchomiona i zakończyła się pomyślnie.

![Strona Update Management automatyzacji](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Możesz przeprowadzić dalsze analizy rekordów zadań, klikając kafelek pierścieniowy. Pulpit nawigacyjny zawiera historię zadań i wstępnie zdefiniowane zapytania dotyczące wyszukiwania w dziennikach. Przejdź do portalu zaawansowanego usługi log Analytics, aby wyszukiwać na podstawie zapytań wyszukiwania.

## <a name="update-the-feature"></a>Aktualizowanie funkcji

Jeśli wdrożono poprzednią wersję Start/Stop VMs during off-hours, usuń ją z konta przed wdrożeniem zaktualizowanej wersji. Postępuj zgodnie z instrukcjami, aby [usunąć tę funkcję](automation-solution-vm-management-remove.md#delete-the-feature) , a następnie postępuj zgodnie z instrukcjami, aby [je włączyć](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Następne kroki

Aby włączyć tę funkcję na maszynach wirtualnych w środowisku, zobacz [włączanie Start/Stop VMS during off-hours](automation-solution-vm-management-enable.md).