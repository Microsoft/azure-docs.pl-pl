---
title: Zarządzanie uprawnieniami ról i zabezpieczeniami w Azure Automation
description: W tym artykule opisano sposób korzystania z kontroli dostępu na podstawie ról (RBAC) platformy Azure, która umożliwia zarządzanie dostępem do zasobów platformy Azure.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0727d3342c73d9aa4d15e84aacb82bd8fea01d65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833584"
---
# <a name="manage-role-permissions-and-security"></a>Zarządzanie uprawnieniami ról i zabezpieczeniami

Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia zarządzanie dostępem do zasobów platformy Azure. Przy [użyciu kontroli dostępu na](../role-based-access-control/overview.md)podstawie ról platformy Azure można segregować obowiązki w ramach zespołu i udzielać użytkownikom, grupom i aplikacjom tylko dostępu potrzebnego do wykonywania zadań. Możesz udzielić użytkownikom dostępu opartego na rolach przy użyciu narzędzi Azure Portal azure Command-Line lub interfejsów API usługi Azure Management.

## <a name="roles-in-automation-accounts"></a>Role na kontach usługi Automation

W Azure Automation dostęp jest udzielany przez przypisanie odpowiedniej roli platformy Azure użytkownikom, grupom i aplikacjom w zakresie konta usługi Automation. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Role** | **Opis** |
|:--- |:--- |
| Właściciel |Rola Właściciel umożliwia dostęp do wszystkich zasobów i akcji w ramach konta usługi Automation, w tym zapewnienie innym użytkownikom, grupom i aplikacjom dostępu do zarządzania kontem usługi Automation. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola Operator usługi umożliwia wyświetlanie nazwy i właściwości element runbook oraz tworzenie zadań dla wszystkich elementów Runbook na koncie usługi Automation i zarządzanie nimi. Ta rola jest przydatna, jeśli chcesz chronić zasoby konta usługi Automation, takie jak zasoby poświadczeń i elementy Runbook, przed wyświetlaniem lub modyfikacją, ale nadal zezwalasz członkom organizacji na wykonywanie tych elementów Runbook. |
|Operator zadania usługi Automation|Rola Operator zadań usługi Automation umożliwia tworzenie zadań dla wszystkich elementów Runbook na koncie usługi Automation i zarządzanie nimi.|
|Automation Runbook Operator|Rola Operator runbook usługi Automation umożliwia wyświetlanie nazwy i właściwości obiektu Runbook.|
| Współautor usługi Log Analytics | Rola Współautor usługi Log Analytics umożliwia odczytywanie wszystkich danych monitorowania i edytowanie ustawień monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych, odczytywanie kluczy konta magazynu w celu skonfigurowania zbierania dzienników z usługi Azure Storage, tworzenie i konfigurowanie kont usługi Automation, dodawanie funkcji usługi Azure Automation oraz konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.|
| Czytelnik usługi Log Analytics | Rola Czytelnik usługi Log Analytics umożliwia wyświetlanie i przeszukiwanie wszystkich danych monitorowania, a także wyświetlanie ustawień monitorowania. Obejmuje to wyświetlanie konfiguracji diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| Współautor monitorowania | Rola Współautor monitorowania umożliwia odczytywanie wszystkich danych monitorowania i aktualizowanie ustawień monitorowania.|
| Czytelnik monitorowania | Rola Czytelnik monitorowania umożliwia odczytywanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia nadane każdej roli. Może to obejmować akcje, które dają uprawnienia, i NotActions, które je ograniczają.

### <a name="owner"></a>Właściciel

Właściciel może zarządzać wszystkim, w tym dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft.Automation/automationAccounts/|Tworzenie zasobów wszystkich typów i zarządzanie nimi.|

### <a name="contributor"></a>Współautor

Współautor może zarządzać wszystkim z wyjątkiem dostępu. W poniższej tabeli przedstawiono uprawnienia przyznane i odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Tworzenie zasobów wszystkich typów i zarządzanie nimi|
|**Not Actions**||
|Microsoft.Authorization/*/Delete| Usuwanie ról i przypisań ról.       |
|Microsoft.Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft.Authorization/elevateAccess/Action    | Nie zezwala na tworzenie administratora dostępu użytkowników.       |

### <a name="reader"></a>Czytelnik

Czytelnik może wyświetlać wszystkie zasoby na koncie usługi Automation, ale nie może wprowadzać żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Wyświetl wszystko zasobów na koncie usługi Automation. |

### <a name="automation-operator"></a>Operator usługi

Operator automatyzacji może tworzyć zadania i zarządzać nimi oraz odczytywać nazwy i właściwości runbook dla wszystkich elementach Runbook na koncie usługi Automation.

>[!NOTE]
>Jeśli chcesz kontrolować dostęp operatora do poszczególnych elementy Runbook, nie ustawiaj tej roli. Zamiast tego należy **używać w połączeniu ról Operator zadania** automatyzacji i Operator elementów Runbook usługi **Automation.**

W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autoryzacja odczytu.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Przeczytaj zasoby hybrydowego procesu roboczego runbook.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementów Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które jest wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Odczytaj strumienie zadań i dane wyjściowe.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Pobierz dane wyjściowe zadania.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymywanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Pobierz harmonogram Azure Automation zadania.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Utwórz Azure Automation zadania.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Pobierz obszar roboczy połączony z kontem usługi Automation.|
|Microsoft.Automation/automationAccounts/read|Uzyskaj konto Azure Automation konto.|
|Microsoft.Automation/automationAccounts/runbooks/read|Pobierz Azure Automation Runbook.|
|Microsoft.Automation/automationAccounts/schedules/read|Pobierz zasób Azure Automation harmonogramu.|
|Microsoft.Automation/automationAccounts/schedules/write|Tworzenie lub aktualizowanie zasobu Azure Automation harmonogramu.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytywanie ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/* |Tworzenie biletów pomocy technicznej i zarządzanie nimi.|

### <a name="automation-job-operator"></a>Operator zadania usługi Automation

Rola Operatora zadań automatyzacji jest udzielana w zakresie konta usługi Automation.Dzięki temu operator może tworzyć zadania dla wszystkich runbook na koncie i zarządzać nimi. Jeśli rola Operator zadań ma przyznane uprawnienia do odczytu do grupy zasobów zawierającej konto usługi Automation, członkowie roli mają możliwość uruchamiania elementów Runbook. Nie mają jednak możliwości ich tworzenia, edytowania ani usuwania.

W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autoryzacja odczytu.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista zadań elementów Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Wznów zadanie, które jest wstrzymane.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Anulowanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Odczytaj strumienie zadań i dane wyjściowe.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Wstrzymywanie zadania w toku.|
|Microsoft.Automation/automationAccounts/jobs/write|Tworzenie zadań.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Odczytywanie ról i przypisań ról.       |
|Microsoft.Resources/deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/* |Tworzenie biletów pomocy technicznej i zarządzanie nimi.|

### <a name="automation-runbook-operator"></a>Automation Runbook Operator

Rola operatora runbook usługi Automation jest udzielana w zakresie runbook. Operator automatyzacji runbook może wyświetlać nazwę i właściwości tego elementu Runbook.Ta rola w połączeniu z **rolą Operator zadań automatyzacji** umożliwia operatorowi również tworzenie zadań dla tego runbook i zarządzanie nimi. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Lista elementów Runbook.        |
|Microsoft.Authorization/*/read      | Autoryzacja odczytu.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Odczytywanie ról i przypisań ról.         |
|Microsoft.Resources/deployments/*      | Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft.Support/*      | Tworzenie biletów pomocy technicznej i zarządzanie nimi.        |

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu w celu skonfigurowania zbierania dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont usługi Automation; dodawanie funkcji; i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.Automation/automationAccounts/*|Zarządzanie kontami usługi Automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Tworzenie rozszerzeń maszyn wirtualnych i zarządzanie nimi.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista klasycznych kluczy kont magazynu.|
|Microsoft.Compute/virtualMachines/extensions/*|Tworzenie klasycznych rozszerzeń maszyn wirtualnych i zarządzanie nimi.|
|Microsoft.Insights/alertRules/*|Reguły alertów odczytu/zapisu/usuwania.|
|Microsoft.Insights/diagnosticSettings/*|Odczyt/zapis/usuwanie ustawień diagnostycznych.|
|Microsoft.OperationalInsights/*|Zarządzanie Azure Monitor użytkownikami.|
|Microsoft.OperationsManagement/*|Zarządzanie Azure Automation w obszarach roboczych.|
|Microsoft.Resources/deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista kluczy kont magazynu.|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi.|

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytelnik usługi Log Analytics może wyświetlać i przeszukiwać wszystkie dane monitorowania oraz wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane lub odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Zarządzanie zapytaniami w Azure Monitor dziennikach.|
|Microsoft.OperationalInsights/workspaces/search/action|Wyszukaj Azure Monitor danych dziennika.|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi.|
|**Not Actions**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nie można odczytać kluczy dostępu współdzielonych.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania może odczytywać wszystkie dane monitorowania i aktualizować ustawienia monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.AlertsManagement/alerts/*|Zarządzanie alertami.|
|Microsoft.AlertsManagement/alertsSummary/*|Zarządzanie pulpitem nawigacyjnym alertów.|
|Microsoft.Insights/AlertRules/*|Zarządzanie regułami alertów.|
|Microsoft.Insights/components/*|Zarządzanie Application Insights składników.|
|Microsoft.Insights/DiagnosticSettings/*|Zarządzanie ustawieniami diagnostyczni.|
|Microsoft.Insights/eventtypes/*|Lista zdarzeń dziennika aktywności (zdarzeń zarządzania) w subskrypcji. To uprawnienie ma zastosowanie zarówno do dostępu programowego, jak i do portalu do dziennika aktywności.|
|Microsoft.Insights/LogDefinitions/*|To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dzienników w dzienniku aktywności.|
|Microsoft.Insights/MetricDefinitions/*|Odczytywanie definicji metryk (lista dostępnych typów metryk dla zasobu).|
|Microsoft.Insights/Metrics/*|Odczytywanie metryk dla zasobu.|
|Microsoft.Insights/Register/Action|Zarejestruj dostawcę Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Zarządzanie Application Insights testami sieci Web.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Zarządzaj Azure Monitor dziennikami pakietów rozwiązań.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Zarządzaj Azure Monitor wyszukiwaniami zapisanymi w dziennikach.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukiwanie obszarów roboczych usługi Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista kluczy dla obszaru roboczego usługi Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Zarządzanie Azure Monitor magazynu dzienników konfiguracji szczegółowych informacji.|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi.|
|Microsoft.WorkloadMonitor/workloads/*|Zarządzanie obciążeniami.|

### <a name="monitoring-reader"></a>Czytelnik monitorowania

Czytelnik monitorowania może odczytywać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukiwanie obszarów roboczych usługi Log Analytics.|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkowników może zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie wszystkich zasobów|
|Microsoft.Authorization/*|Zarządzanie autoryzacją|
|Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

## <a name="feature-setup-permissions"></a>Uprawnienia do konfigurowania funkcji

W poniższych sekcjach opisano minimalne wymagane uprawnienia wymagane do włączenia Update Management i Śledzenie zmian i spis funkcji.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Uprawnienia do włączania Update Management i Śledzenie zmian i spis z maszyny wirtualnej

|**Akcja**  |**Uprawnienie**  |**Minimalny zakres**  |
|---------|---------|---------|
|Pisanie nowego wdrożenia      | Microsoft.Resources/deployments/*          |Subskrypcja          |
|Pisanie nowej grupy zasobów      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subskrypcja          |
|Tworzenie nowego domyślnego obszaru roboczego      | Microsoft.OperationalInsights/workspaces/write         | Grupa zasobów         |
|Tworzenie nowego konta      |  Microsoft.Automation/automationAccounts/write        |Grupa zasobów         |
|Łączenie obszaru roboczego i konta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Workspace</br>Konto usługi Automation
|Tworzenie rozszerzenia MMA      | Microsoft.Compute/virtualMachines/write         | Maszyna wirtualna         |
|Tworzenie zapisanego wyszukiwania      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Tworzenie konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Sprawdzanie stanu dołączania — odczyt obszaru roboczego      | Microsoft.OperationalInsights/workspaces/read         | Workspace         |
|Sprawdzanie stanu dołączania — odczytywanie połączonej właściwości obszaru roboczego konta     | Microsoft.Automation/automationAccounts/read      | Konto usługi Automation        |
|Sprawdzanie stanu dołączania — odczyt rozwiązania      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Rozwiązanie         |
|Sprawdzanie stanu dołączania — odczyt maszyny wirtualnej      | Microsoft.Compute/virtualMachines/read         | Maszyna wirtualna         |
|Sprawdzanie stanu dołączania — odczyt konta      | Microsoft.Automation/automationAccounts/read  |  Konto usługi Automation   |
| Sprawdzanie obszaru roboczego dołączania dla maszyny wirtualnej<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subskrypcja         |
| Rejestrowanie dostawcy usługi Log Analytics |Microsoft.Insights/register/action | Subskrypcja|

<sup>1</sup> To uprawnienie jest potrzebne do włączenia funkcji za pośrednictwem portalu maszyny wirtualnej.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Uprawnienia do włączania Update Management i Śledzenie zmian i spis z konta usługi Automation

|**Akcja**  |**Uprawnienie** |**Minimalny zakres**  |
|---------|---------|---------|
|Tworzenie nowego wdrożenia     | Microsoft.Resources/deployments/*        | Subskrypcja         |
|Tworzenie nowej grupy zasobów     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subskrypcja        |
|Blok AutomationOnboarding — tworzenie nowego obszaru roboczego     |Microsoft.OperationalInsights/workspaces/write           | Grupa zasobów        |
|Blok AutomationOnboarding — odczytywanie połączonego obszaru roboczego     | Microsoft.Automation/automationAccounts/read        | Konto usługi Automation       |
|Blok AutomationOnboarding — odczyt rozwiązania     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Rozwiązanie        |
|Blok AutomationOnboarding — odczyt obszaru roboczego     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Workspace        |
|Tworzenie linku dla obszaru roboczego i konta     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Pisanie konta dla shoebox      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Tworzenie/edytowanie zapisanego wyszukiwania     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Tworzenie/edytowanie konfiguracji zakresu     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
| Rejestrowanie dostawcy usługi Log Analytics |Microsoft.Insights/register/action | Subskrypcja|
|**Krok 2. Włączanie wielu maszyn wirtualnych**     |         |         |
|Blok VMOnboarding — tworzenie rozszerzenia MMA     | Microsoft.Compute/virtualMachines/write           | Maszyna wirtualna        |
|Tworzenie/edytowanie zapisanego wyszukiwania     | Microsoft.OperationalInsights/workspaces/write           | Workspace        |
|Tworzenie/edytowanie konfiguracji zakresu  | Microsoft.OperationalInsights/workspaces/write   | Workspace|

## <a name="update-management-permissions"></a>Uprawnienia do zarządzania aktualizacjami

Zarządzanie aktualizacjami dociera do wielu usług, aby zapewnić swoją usługę. W poniższej tabeli przedstawiono uprawnienia wymagane do zarządzania wdrożeniami rozwiązania Update Management:

|**Zasób**  |**Role**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Konfigurowanie kontroli RBAC platformy Azure dla konta usługi Automation

W poniższej sekcji przedstawiono sposób konfigurowania kontroli RBAC platformy Azure na koncie usługi Automation za pomocą [Azure Portal](#configure-azure-rbac-using-the-azure-portal) i [programu PowerShell.](#configure-azure-rbac-using-powershell)

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Konfigurowanie kontroli RBAC platformy Azure przy użyciu Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.
2. Kliknij pozycję **Kontrola dostępu (IAM),** aby otworzyć stronę Kontrola dostępu (IAM). Ta strona umożliwia dodawanie nowych użytkowników, grup i aplikacji w celu zarządzania kontem usługi Automation i wyświetlania istniejących ról, które można skonfigurować dla konta usługi Automation.
3. Kliknij kartę **Przypisania ról**.

   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli

1. Na stronie Kontrola dostępu (IAM) kliknij pozycję **+ Dodaj przypisanie roli.** Ta akcja powoduje otwarcie strony Dodawanie przypisania roli, na której można dodać użytkownika, grupę lub aplikację i przypisać odpowiednią rolę.

2. Wybierz rolę z listy dostępnych ról. Możesz wybrać dowolną z dostępnych wbudowanych ról, które obsługuje konto usługi Automation, lub dowolną zdefiniowaną przez Ciebie rolę niestandardową.

3. W polu **Wybierz** wpisz nazwę użytkownika, do którego chcesz nadać uprawnienia. Wybierz użytkownika z listy i kliknij przycisk **Zapisz.**

   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)

   Teraz powinien zostać wyświetlony użytkownik dodany do strony Użytkownicy z przypisaną wybraną rolą.

   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)

   Możesz także przypisać rolę użytkownikowi na stronie Role.

4. Kliknij **pozycję Role** na stronie Kontrola dostępu (IAM), aby otworzyć stronę Role. Możesz wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.

    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Kontrolę dostępu opartą na rolach można ustawić tylko w zakresie konta usługi Automation, a nie w żadnym zasobie poniżej konta usługi Automation.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Możesz usunąć uprawnienie dostępu dla użytkownika, który nie zarządza kontem usługi Automation lub nie pracuje już w organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika:

1. Na stronie Kontrola dostępu (IAM) wybierz użytkownika do usunięcia, a następnie kliknij pozycję **Usuń.**
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Konfigurowanie kontroli RBAC platformy Azure przy użyciu programu PowerShell

Możesz również skonfigurować dostęp oparty na rolach do konta usługi Automation przy użyciu następujących Azure PowerShell [cmdlet:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzRoleDefinition wyświetla](/powershell/module/Az.Resources/Get-AzRoleDefinition) listę wszystkich ról platformy Azure, które są dostępne w Azure Active Directory. Tego polecenia cmdlet można użyć z parametrem , aby wyświetlić listę wszystkich akcji, które może wykonywać `Name` konkletna rola.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment wyświetla](/powershell/module/az.resources/get-azroleassignment) listę przypisań ról platformy Azure w określonym zakresie. Bez żadnych parametrów to polecenie cmdlet zwraca wszystkie przypisania ról w ramach subskrypcji. Użyj parametru , aby wyświetlić listę przypisań dostępu dla określonego użytkownika, a także `ExpandPrincipalGroups` grup, do których należy użytkownik.

**Przykład:** Użyj następującego polecenia cmdlet, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta usługi Automation.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Użyj [new-AzRoleAssignment,](/powershell/module/Az.Resources/New-AzRoleAssignment) aby przypisać dostęp do użytkowników, grup i aplikacji do określonego zakresu.

**Przykład:** Użyj następującego polecenia, aby przypisać użytkownikowi rolę "Operator usługi" w zakresie konta usługi Automation.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Użyj [remove-AzRoleAssignment,](/powershell/module/Az.Resources/Remove-AzRoleAssignment) aby usunąć dostęp określonego użytkownika, grupy lub aplikacji z określonego zakresu.

**Przykład:** Użyj następującego polecenia, aby usunąć użytkownika z roli Operator usługi w zakresie konta usługi Automation.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W poprzednim przykładzie zastąp `sign-in ID of a user you wish to remove` , `SubscriptionID` , i `Resource Group Name` `Automation account name` szczegółami swojego konta. Wybierz **opcję tak** po wyświetleniu monitu o potwierdzenie przed kontynuowaniem usuwania przypisań ról użytkownika.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Środowisko użytkownika dla roli operator automatyzacji — konto usługi Automation

Gdy użytkownik przypisany do roli Operator usługi w zakresie konta usługi Automation wyświetla konto usługi Automation, do którego jest przypisany, może wyświetlać tylko listę elementów Runbook, zadań elementów Runbook i harmonogramów utworzonych na koncie usługi Automation. Ten użytkownik nie może wyświetlić definicji tych elementów. Użytkownik może uruchomić, zatrzymać, wstrzymać, wznowić lub zaplanować zadanie runbook. Jednak użytkownik nie ma dostępu do innych zasobów usługi Automation, takich jak konfiguracje, grupy hybrydowych procesów roboczych lub węzły DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Konfigurowanie kontroli RBAC platformy Azure dla podręczników Runbook

Azure Automation umożliwia przypisywanie ról platformy Azure do określonych runbook. W tym celu uruchom następujący skrypt, aby dodać użytkownika do określonego runbook. Administrator konta usługi Automation lub Administrator dzierżawy może uruchomić ten skrypt.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Po uruchomieniu skryptu użytkownik musi zalogować się do Azure Portal i wybrać **pozycję Wszystkie zasoby.** Na liście użytkownik może zobaczyć element Runbook, dla którego został dodany jako operator elementów Runbook usługi Automation.

![Runbook Azure RBAC w portalu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Środowisko użytkownika dla roli operatora usługi Automation — Runbook

Gdy użytkownik przypisany do roli Operator automatyzacji w zakresie runbook wyświetla przypisany element Runbook, może tylko uruchomić element Runbook i wyświetlić zadania runbook.

![Ma dostęp tylko do uruchamiania](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat kontroli RBAC platformy Azure przy użyciu programu PowerShell, zobacz Dodawanie lub usuwanie przypisań ról platformy Azure przy [użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Aby uzyskać szczegółowe informacje o typach elementów Runbook, zobacz [Azure Automation runbook](automation-runbook-types.md).
* Aby uruchomić runbook, zobacz [Uruchamianie runbook w programie Azure Automation.](start-runbooks.md)
