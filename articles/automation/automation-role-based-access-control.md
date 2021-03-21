---
title: Zarządzanie uprawnieniami roli i zabezpieczeniami w Azure Automation
description: W tym artykule opisano sposób korzystania z kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, która umożliwia zarządzanie dostępem do zasobów platformy Azure.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.openlocfilehash: 320668f9596376cf7aa12ed97872671404a07658
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895921"
---
# <a name="manage-role-permissions-and-security"></a>Zarządzanie uprawnieniami ról i zabezpieczeniami

Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Korzystając z funkcji [RBAC systemu Azure](../role-based-access-control/overview.md), można oddzielić cła w obrębie zespołu i przyznać tylko dostęp do użytkowników, grup i aplikacji, których potrzebują do wykonywania swoich zadań. Dostęp oparty na rolach można udzielić użytkownikom przy użyciu Azure Portal, narzędzi Command-Line platformy Azure lub interfejsów API zarządzania platformy Azure.

## <a name="roles-in-automation-accounts"></a>Role na kontach usługi Automation

W Azure Automation dostęp jest udzielany przez przypisanie odpowiedniej roli platformy Azure użytkownikom, grupom i aplikacjom w zakresie konta usługi Automation. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Role** | **Opis** |
|:--- |:--- |
| Właściciel |Rola właściciela umożliwia dostęp do wszystkich zasobów i akcji w ramach konta usługi Automation, w tym zapewnia dostęp innym użytkownikom, grupom i aplikacjom w celu zarządzania kontem usługi Automation. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola operatora automatyzacji umożliwia wyświetlenie nazwy i właściwości elementu Runbook oraz tworzenie zadań i zarządzanie nimi dla wszystkich elementów Runbook w ramach konta usługi Automation. Ta rola jest przydatna, jeśli chcesz chronić zasoby konta usługi Automation, takie jak zasoby poświadczeń i elementy Runbook, które mają być przeglądane lub modyfikowane, ale nadal Zezwól członkom organizacji na wykonywanie tych elementów Runbook. |
|Operator zadania automatyzacji|Rola operatora zadań automatyzacji umożliwia tworzenie zadań dla wszystkich elementów Runbook w ramach konta usługi Automation i zarządzanie nimi.|
|Operator elementu Runbook usługi Automation|Rola operatora elementu Runbook usługi Automation umożliwia wyświetlenie nazwy i właściwości elementu Runbook.|
| Współautor usługi Log Analytics | Rola współautor Log Analytics umożliwia odczytywanie wszystkich danych monitorowania i edytowanie ustawień monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych, przeczytanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage, tworzenia i konfigurowania kont usługi Automation, dodawania funkcji Azure Automation i konfigurowania diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.|
| Czytelnik usługi Log Analytics | Rola czytelnik Log Analytics umożliwia wyświetlanie i przeszukiwanie wszystkich danych monitorowania oraz Wyświetlanie ustawień monitorowania. Obejmuje to wyświetlanie konfiguracji diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| Współautor monitorowania | Rola współautor monitorowania umożliwia odczytywanie wszystkich danych monitorowania i aktualizowanie ustawień monitorowania.|
| Czytnik monitorowania | Rola czytelnik monitorowania umożliwia odczytywanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia dla każdej roli. Może to obejmować akcje, które powodują, że uprawnienia i nienaruszone ograniczenia.

### <a name="owner"></a>Właściciel

Właściciel może zarządzać wszystkimi, w tym dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft. Automation/automationAccounts/|Twórz zasoby wszystkich typów i zarządzaj nimi.|

### <a name="contributor"></a>Współautor

Współautor może zarządzać wszystko z wyjątkiem dostępu. W poniższej tabeli przedstawiono uprawnienia udzielone i odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Twórz zasoby wszystkich typów i zarządzaj nimi|
|**Nie akcje**||
|Microsoft. Authorization/*/Delete| Usuń role i przypisania ról.       |
|Microsoft. Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft. Authorization/elevateAccess/akcja    | Odrzuca możliwość tworzenia administratora dostępu użytkownika.       |

### <a name="reader"></a>Czytelnik

Czytelnik może wyświetlać wszystkie zasoby na koncie usługi Automation, ale nie może wprowadzać żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Read|Wyświetl wszystkie zasoby na koncie usługi Automation. |

### <a name="automation-operator"></a>Operator usługi

Operator usługi Automation może tworzyć zadania i zarządzać nimi oraz odczytywać nazwy elementów Runbook i właściwości wszystkich elementów Runbook w ramach konta usługi Automation.

>[!NOTE]
>Jeśli chcesz kontrolować dostęp operatorów do poszczególnych elementów Runbook, nie ustawiaj tej roli. Zamiast tego należy użyć **operatora zadania automatyzacji** i ról **operatora elementu Runbook usługi Automation** w połączeniu.

W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Zapoznaj się z autoryzacją.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/odczyt|Odczytaj zasoby hybrydowego procesu roboczego elementu Runbook.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Wyświetl listę zadań elementu Runbook.|
|Microsoft. Automation/automationAccounts/Jobs/Resume/Action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft. Automation/automationAccounts/Jobs/akcja|Anuluj zadanie w toku.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Odczytaj strumienie i dane wyjściowe zadania.|
|Microsoft. Automation/automationAccounts/Jobs/Output/Read|Pobierz dane wyjściowe zadania.|
|Microsoft. Automation/automationAccounts/Jobs/Suspend/Action|Wstrzymaj zadanie w toku.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Tworzenie zadań.|
|Microsoft. Automation/automationAccounts/jobSchedules/odczyt|Pobierz harmonogram zadań Azure Automation.|
|Microsoft. Automation/automationAccounts/jobSchedules/Write|Utwórz harmonogram zadań Azure Automation.|
|Microsoft. Automation/automationAccounts/linkedWorkspace/odczyt|Pobierz obszar roboczy połączony z kontem usługi Automation.|
|Microsoft. Automation/automationAccounts/Read|Pobierz konto Azure Automation.|
|Microsoft. Automation/automationAccounts/Runbooks/Read|Pobierz element Runbook Azure Automation.|
|Microsoft. Automation/automationAccounts/Schedules/Read|Pobierz zasób harmonogramu Azure Automation.|
|Microsoft. Automation/automationAccounts/Schedules/Write|Utwórz lub zaktualizuj zasób harmonogramu Azure Automation.|
|Microsoft. resources/subscriptions/resourceGroups/Read      |Odczytuj role i przypisania ról.         |
|Microsoft. resources/Deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft. Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft. Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-job-operator"></a>Operator zadania automatyzacji

Rola operatora zadań automatyzacji jest przyznawana w zakresie konta usługi Automation.Umożliwia to uprawnienia operatora do tworzenia zadań dla wszystkich elementów Runbook w ramach konta i zarządzania nimi. Jeśli rola operatora zadań ma przyznane uprawnienia do odczytu w grupie zasobów zawierającej konto usługi Automation, członkowie roli mają możliwość uruchamiania elementów Runbook. Jednak nie mają możliwości tworzenia, edytowania ani usuwania.

W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Zapoznaj się z autoryzacją.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Wyświetl listę zadań elementu Runbook.|
|Microsoft. Automation/automationAccounts/Jobs/Resume/Action|Wznów zadanie, które zostało wstrzymane.|
|Microsoft. Automation/automationAccounts/Jobs/akcja|Anuluj zadanie w toku.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Odczytaj strumienie i dane wyjściowe zadania.|
|Microsoft. Automation/automationAccounts/Jobs/Suspend/Action|Wstrzymaj zadanie w toku.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Tworzenie zadań.|
|Microsoft. resources/subscriptions/resourceGroups/Read      |  Odczytuj role i przypisania ról.       |
|Microsoft. resources/Deployments/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft. Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft. Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-runbook-operator"></a>Operator elementu Runbook usługi Automation

Rola operatora elementu Runbook usługi Automation jest przyznawana w zakresie elementu Runbook. Operator elementu Runbook usługi Automation może wyświetlać nazwę i właściwości elementu Runbook.Ta rola w połączeniu z rolą **operatora zadań automatyzacji** umożliwia operatorowi tworzenie zadań dla elementu Runbook i zarządzanie nimi. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Runbooks/Read     | Wyświetl listę elementów Runbook.        |
|Microsoft. Authorization/*/Read      | Zapoznaj się z autoryzacją.        |
|Microsoft. resources/subscriptions/resourceGroups/Read      |Odczytuj role i przypisania ról.         |
|Microsoft. resources/Deployments/*      | Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft. Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Microsoft. Support/*      | Twórz bilety pomocy technicznej i zarządzaj nimi.        |

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie funkcji; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft. Automation/automationAccounts/*|Zarządzaj kontami usługi Automation.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Utwórz rozszerzenia maszyny wirtualnej i zarządzaj nimi.|
|Microsoft. ClassicStorage/storageAccounts/listKeys/akcja|Wyświetl listę kluczy klasycznych kont magazynu.|
|Microsoft. COMPUTE/virtualMachines/Extensions/*|Utwórz klasyczne rozszerzenia maszyn wirtualnych i zarządzaj nimi.|
|Microsoft. Insights/alertRules/*|Odczyt/zapis/usuwanie reguł alertów.|
|Microsoft. Insights/diagnosticSettings/*|Odczyt/zapis/usuwanie ustawień diagnostycznych.|
|Microsoft. OperationalInsights/*|Zarządzaj dziennikami Azure Monitor.|
|Microsoft. OperationsManagement/*|Zarządzaj funkcjami Azure Automation w obszarach roboczych.|
|Microsoft. resources/Deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft. resources/subscriptions/ResourceGroups/Deployments/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Microsoft. Storage/storageAccounts/listKeys/akcja|Wyświetl listę kluczy konta magazynu.|
|Microsoft. Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytelnik Log Analytics może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. W poniższej tabeli przedstawiono uprawnienia udzielone lub odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja|Zarządzanie zapytaniami w dziennikach Azure Monitor.|
|Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja|Wyszukaj Azure Monitor dane dziennika.|
|Microsoft. Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|**Nie akcje**| |
|Microsoft. OperationalInsights/Workspaces/sharedKeys/Read|Nie można odczytać kluczy dostępu współdzielonego.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania może odczytać wszystkie dane monitorowania i zaktualizować ustawienia monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft. AlertsManagement/alerty/*|Zarządzanie alertami.|
|Microsoft. AlertsManagement/alertsSummary/*|Zarządzaj pulpitem nawigacyjnym alertów.|
|Microsoft. Insights/AlertRules/*|Zarządzaj regułami alertów.|
|Microsoft. Insights/Components/*|Zarządzanie składnikami Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Zarządzaj ustawieniami diagnostycznymi.|
|Microsoft. Insights/EventType|Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności.|
|Microsoft. Insights/LogDefinitions/*|To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności.|
|Microsoft. Insights/MetricDefinitions/*|Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu).|
|Microsoft. Insights/Metrics/*|Odczytaj metryki dla zasobu.|
|Microsoft. Insights/Register/Action|Zarejestruj dostawcę Microsoft. Insights.|
|Microsoft. Insights/webtests/*|Zarządzaj Application Insights testami sieci Web.|
|Microsoft. OperationalInsights/Workspaces/intelligencepacks/*|Zarządzanie Azure Monitor rejestruje pakiety rozwiązań.|
|Microsoft. OperationalInsights/Workspaces/savedSearches/*|Zarządzaj zapisanymi wyszukiwaniami dzienników Azure Monitor.|
|Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja|Wyszukaj Log Analytics obszary robocze.|
|Microsoft. OperationalInsights/obszary robocze/sharedKeys/akcja|Lista kluczy dla obszaru roboczego Log Analytics.|
|Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/*|Zarządzaj Azure Monitor dzienników usługi Storage Insights.|
|Microsoft. Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|Microsoft. Monitor obciążenia został/obciążenia/*|Zarządzanie obciążeniami.|

### <a name="monitoring-reader"></a>Czytnik monitorowania

Czytnik monitorowania może odczytywać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych.|
|Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja|Wyszukaj Log Analytics obszary robocze.|
|Microsoft. Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkowników może zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/read|Odczytuj wszystkie zasoby|
|Microsoft. Authorization/*|Zarządzanie autoryzacją|
|Microsoft. Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

## <a name="feature-setup-permissions"></a>Uprawnienia do konfigurowania funkcji

W poniższych sekcjach opisano minimalne wymagane uprawnienia wymagane do włączenia funkcji Update Management i Change Tracking i spisu.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Uprawnienia do włączania Update Management i Change Tracking i spisu z maszyny wirtualnej

|**Akcja**  |**Uprawnienie**  |**Zakres minimalny**  |
|---------|---------|---------|
|Napisz nowe wdrożenie      | Microsoft. resources/Deployments/*          |Subskrypcja          |
|Zapisz nową grupę zasobów      | Microsoft. resources/subscriptions/resourceGroups/Write        | Subskrypcja          |
|Utwórz nowy domyślny obszar roboczy      | Microsoft. OperationalInsights/obszary robocze/zapis         | Grupa zasobów         |
|Utwórz nowe konto      |  Microsoft. Automation/automationAccounts/Write        |Grupa zasobów         |
|Połącz obszar roboczy i konto      |Microsoft. OperationalInsights/obszary robocze/zapis</br>Microsoft. Automation/automationAccounts/Read|Workspace</br>Konto usługi Automation
|Utwórz rozszerzenie MMA      | Microsoft. COMPUTE/virtualMachines/Write         | Maszyna wirtualna         |
|Utwórz zapisane wyszukiwanie      | Microsoft. OperationalInsights/obszary robocze/zapis          | Workspace         |
|Utwórz konfigurację zakresu      | Microsoft. OperationalInsights/obszary robocze/zapis          | Workspace         |
|Sprawdzanie stanu dołączania — obszar roboczy odczytu      | Microsoft. OperationalInsights/obszary robocze/odczyt         | Workspace         |
|Sprawdzanie stanu dołączania — odczytywanie właściwości połączonego obszaru roboczego konta     | Microsoft. Automation/automationAccounts/Read      | Konto usługi Automation        |
|Sprawdzanie stanu dołączania — Rozpoznaj rozwiązanie      | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read          | Rozwiązanie         |
|Sprawdzanie stanu dołączania — Odczytaj maszynę wirtualną      | Microsoft. COMPUTE/virtualMachines/Read         | Maszyna wirtualna         |
|Sprawdzanie stanu dołączania — konto do odczytu      | Microsoft. Automation/automationAccounts/Read  |  Konto usługi Automation   |
| Sprawdzanie obszaru roboczego dołączania dla maszyny wirtualnej<sup>1</sup>       | Microsoft. OperationalInsights/obszary robocze/odczyt         | Subskrypcja         |
| Rejestrowanie dostawcy Log Analytics |Microsoft. Insights/Register/Action | Subskrypcja|

<sup>1</sup> to uprawnienie jest konieczne do włączenia funkcji w ramach środowiska portalu maszyn wirtualnych.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Uprawnienia do włączania Update Management i Change Tracking i spisu na podstawie konta usługi Automation

|**Akcja**  |**Uprawnienie** |**Zakres minimalny**  |
|---------|---------|---------|
|Utwórz nowe wdrożenie     | Microsoft. resources/Deployments/*        | Subskrypcja         |
|Utwórz nową grupę zasobów     | Microsoft. resources/subscriptions/resourceGroups/Write         | Subskrypcja        |
|Blok AutomationOnboarding — Tworzenie nowego obszaru roboczego     |Microsoft. OperationalInsights/obszary robocze/zapis           | Grupa zasobów        |
|Blok AutomationOnboarding — odczytywanie połączonego obszaru roboczego     | Microsoft. Automation/automationAccounts/Read        | Konto usługi Automation       |
|Blok AutomationOnboarding — rozwiązanie do odczytu     | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read         | Rozwiązanie        |
|Blok AutomationOnboarding — obszar roboczy odczytywania     | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read        | Workspace        |
|Utwórz łącze dla obszaru roboczego i konta     | Microsoft. OperationalInsights/obszary robocze/zapis        | Workspace        |
|Zapisz konto dla Shoebox      | Microsoft. Automation/automationAccounts/Write        | Konto        |
|Utwórz/Edytuj zapisane wyszukiwanie     | Microsoft. OperationalInsights/obszary robocze/zapis        | Workspace        |
|Utwórz/edytuj konfigurację zakresu     | Microsoft. OperationalInsights/obszary robocze/zapis        | Workspace        |
| Rejestrowanie dostawcy Log Analytics |Microsoft. Insights/Register/Action | Subskrypcja|
|**Krok 2 — Włączanie wielu maszyn wirtualnych**     |         |         |
|Blok VMOnboarding — tworzenie rozszerzenia MMA     | Microsoft. COMPUTE/virtualMachines/Write           | Maszyna wirtualna        |
|Utwórz/Edytuj zapisane wyszukiwanie     | Microsoft. OperationalInsights/obszary robocze/zapis           | Workspace        |
|Utwórz/edytuj konfigurację zakresu  | Microsoft. OperationalInsights/obszary robocze/zapis   | Workspace|

## <a name="update-management-permissions"></a>Uprawnienia do zarządzania aktualizacjami

Zarządzanie aktualizacjami dociera do wielu usług w celu zapewnienia swojej usługi. W poniższej tabeli przedstawiono uprawnienia, które są konieczne do zarządzania wdrożeniami zarządzania aktualizacjami:

|**Zasób**  |**Role**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Konfigurowanie kontroli RBAC platformy Azure dla konta usługi Automation

W poniższej sekcji pokazano, jak skonfigurować usługę Azure RBAC na koncie usługi Automation za pomocą [Azure Portal](#configure-azure-rbac-using-the-azure-portal) i [programu PowerShell](#configure-azure-rbac-using-powershell).

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Konfigurowanie kontroli RBAC platformy Azure przy użyciu Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.
2. Kliknij pozycję **Kontrola dostępu (IAM)** , aby otworzyć stronę kontrola dostępu (IAM). Za pomocą tej strony można dodawać nowych użytkowników, grupy i aplikacje do zarządzania kontem usługi Automation oraz wyświetlać istniejące role, które można skonfigurować dla konta usługi Automation.
3. Kliknij kartę **Przypisania ról**.

   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli

1. Na stronie kontrola dostępu (IAM) kliknij pozycję **+ Dodaj przypisanie roli**. Ta akcja powoduje otwarcie strony Dodawanie przypisania roli, na której można dodać użytkownika, grupę lub aplikację, a następnie przypisać odpowiednią rolę.

2. Wybierz rolę z listy dostępnych ról. Można wybrać dowolną z dostępnych ról wbudowanych, które obsługuje konto usługi Automation, lub dowolną rolę niestandardową, która może być zdefiniowana.

3. Wpisz nazwę użytkownika, do którego chcesz nadać uprawnienia w polu **Wybierz** . Wybierz użytkownika z listy i kliknij przycisk **Zapisz**.

   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)

   Teraz powinien zostać wyświetlony użytkownik dodany do strony użytkownicy z przypisaną rolą wybrana rola.

   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)

   Możesz także przypisać rolę użytkownikowi na stronie Role.

4. Kliknij pozycję **role** na stronie kontroli dostępu (IAM), aby otworzyć stronę role. Można wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.

    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Kontrolę dostępu opartą na rolach można ustawić tylko w zakresie konta usługi Automation, a nie w żadnym z zasobów poniżej konta usługi Automation.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Możesz usunąć uprawnienie dostępu dla użytkownika, który nie zarządza kontem usługi Automation lub który nie działa już w organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika:

1. Na stronie kontrola dostępu (IAM) wybierz użytkownika, którego chcesz usunąć, a następnie kliknij przycisk **Usuń**.
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Konfigurowanie kontroli RBAC platformy Azure przy użyciu programu PowerShell

Możesz również skonfigurować dostęp oparty na rolach do konta usługi Automation przy użyciu następujących [poleceń cmdlet Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) wyświetla wszystkie role platformy Azure, które są dostępne w Azure Active Directory. Tego polecenia cmdlet można użyć z `Name` parametrem, aby wyświetlić listę wszystkich akcji, które może wykonać określona rola.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Oto przykładowe dane wyjściowe:

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

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) wyświetla listę przypisań ról platformy Azure w określonym zakresie. Bez żadnych parametrów to polecenie cmdlet zwraca wszystkie przypisania roli wykonane w ramach subskrypcji. Użyj `ExpandPrincipalGroups` parametru, aby wyświetlić listę przypisań dostępu dla określonego użytkownika, a także grupy, do których należy użytkownik.

**Przykład:** Użyj poniższego polecenia cmdlet, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta usługi Automation.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Oto przykładowe dane wyjściowe:

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

Użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) , aby przypisać dostęp do użytkowników, grup i aplikacji do określonego zakresu.

**Przykład:** Użyj poniższego polecenia, aby przypisać rolę "operator usługi" dla użytkownika w zakresie konta usługi Automation.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Oto przykładowe dane wyjściowe:

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

Użyj polecenie [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment) , aby usunąć dostęp określonego użytkownika, grupy lub aplikacji z określonego zakresu.

**Przykład:** Użyj poniższego polecenia, aby usunąć użytkownika z roli operatora automatyzacji w zakresie konta usługi Automation.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W powyższym przykładzie Zastąp `sign-in ID of a user you wish to remove` elementy,, `SubscriptionID` `Resource Group Name` i `Automation account name` ze szczegółami Twojego konta. Wybierz opcję **tak** , gdy zostanie wyświetlony monit o potwierdzenie przed kontynuowaniem usuwania przypisań roli użytkownika.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Środowisko użytkownika dla roli operatora automatyzacji — konto usługi Automation

Gdy użytkownik przypisany do roli operatora usługi Automation w zakresie konta usługi Automation przegląda konto usługi Automation, do którego jest przypisany, użytkownik może wyświetlić tylko listę elementów Runbook, zadań elementów Runbook i harmonogramów utworzonych w ramach konta usługi Automation. Ten użytkownik nie może wyświetlić definicji tych elementów. Użytkownik może uruchamiać, zatrzymywać, wstrzymywać, wznawiać lub planować zadanie elementu Runbook. Jednak użytkownik nie ma dostępu do innych zasobów usługi Automation, takich jak konfiguracje, grupy hybrydowych procesów roboczych lub węzły DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Konfigurowanie usługi Azure RBAC dla elementów Runbook

Azure Automation umożliwia przypisywanie ról platformy Azure do określonych elementów Runbook. Aby to zrobić, uruchom następujący skrypt, aby dodać użytkownika do określonego elementu Runbook. Administrator konta usługi Automation lub Administrator dzierżawy może uruchomić ten skrypt.

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

Po uruchomieniu skryptu, użytkownik powinien zalogować się do Azure Portal i wybrać pozycję **wszystkie zasoby**. Na liście użytkownik może zobaczyć element Runbook, dla którego został dodany jako operator elementu Runbook usługi Automation.

![Kontrola dostępu do elementów Runbook platformy Azure w portalu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Środowisko użytkownika dla roli operatora automatyzacji — element Runbook

Gdy użytkownik przypisany do roli operatora usługi w zakresie elementu Runbook przegląda przypisany element Runbook, użytkownik może uruchomić element Runbook i wyświetlić zadania elementu Runbook.

![Tylko ma dostęp do uruchamiania](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o usłudze Azure RBAC przy użyciu programu PowerShell, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Aby uzyskać szczegółowe informacje na temat typów elementów Runbook, zobacz [Azure Automation typów elementów Runbook](automation-runbook-types.md).
* Aby uruchomić element Runbook, zobacz temat [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md).
