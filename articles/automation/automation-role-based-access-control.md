---
title: Kontrola dostępu oparta na rolach w usłudze Azure Automation
description: Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Ten artykuł zawiera opis sposobu konfigurowania funkcji RBAC w usłudze Azure Automation.
keywords: automation rbac, kontrola dostępu oparta na rolach, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: a49f2596df91c44deafa1be83483f8972e223742
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535574"
---
# <a name="role-based-access-control-in-azure-automation"></a>Kontrola dostępu oparta na rolach w usłudze Azure Automation

Funkcja kontroli dostępu opartej na rolach (role-based access control, RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Za pomocą [RBAC](../role-based-access-control/overview.md)można segregować obowiązki w zespole i przyznawać tylko ilość dostępu do użytkowników, grup i aplikacji, których potrzebują do wykonywania swoich zadań. Dostęp oparty na rolach można przyznać użytkownikom za pomocą witryny Azure portal, narzędzi wiersza polecenia platformy Azure lub interfejsów API usługi Azure Management.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="roles-in-automation-accounts"></a>Role na kontach automatyzacji

W usłudze Azure Automation prawo dostępu jest nadawane poprzez przypisywanie użytkownikom, grupom i aplikacjom odpowiednich ról RBAC w zakresie konta usługi. Poniżej przedstawiono wbudowane role obsługiwane przez konto automatyzacji:

| **Rola** | **Opis** |
|:--- |:--- |
| Właściciel |Rola Właściciel umożliwia dostęp do wszystkich zasobów i akcji w ramach konta automatyzacji, w tym zapewnienie dostępu do innych użytkowników, grup i aplikacji do zarządzania kontem automatyzacji. |
| Współautor |Rola Współautor umożliwia zarządzanie wszystkim, z wyjątkiem modyfikowania uprawnień dostępu innych użytkowników do konta usługi Automation. |
| Czytelnik |Rola Czytelnik służy do wyświetlania wszystkich zasobów w ramach konta usługi Automation, ale nie pozwala na wprowadzanie żadnych zmian. |
| Operator usługi |Rola Operator automatyzacji umożliwia wyświetlanie nazwy i właściwości elementu runbook oraz tworzenie zadań i zarządzanie nimi dla wszystkich śmigieł na koncie automatyzacji. Ta rola jest przydatna, jeśli chcesz chronić zasoby konta automatyzacji, takie jak zasoby poświadczeń i elementy runbook przed wyświetlaniem lub modyfikowaniem, ale nadal zezwalaj członkom instytucji na wykonywanie tych elementów runbook. |
|Operator zadań automatyzacji|Rola Operator zadań automatyzacji umożliwia tworzenie zadań i zarządzanie nimi dla wszystkich śmigieł na koncie automatyzacji.|
|Operator systemu runbook automatyzacji|Rola Operator elementu runbook automatyzacji umożliwia wyświetlanie nazwy i właściwości elementu runbook.|
| Współautor usługi Log Analytics | Rola Współautora analizy dzienników umożliwia odczytanie wszystkich danych monitorowania i edytowanie ustawień monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych, odczytywanie kluczy kont magazynu, aby móc skonfigurować kolekcję dzienników z usługi Azure Storage, tworzenie i konfigurowanie kont automatyzacji, dodawanie rozwiązań i konfigurowanie diagnostyki platformy Azure na wszystkich zasobach platformy Azure.|
| Czytelnik usługi Log Analytics | Rola czytnika usługi Log Analytics umożliwia wyświetlanie i przeszukiwanie wszystkich danych monitorowania, a także wyświetlanie ustawień monitorowania. Obejmuje to wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. |
| Współautor monitorowania | Rola Monitorowanie współautora umożliwia odczytanie wszystkich danych monitorowania i aktualizowanie ustawień monitorowania.|
| Czytnik monitoringu | Rola Czytnik monitorowania umożliwia odczytanie wszystkich danych monitorowania. |
| Administrator dostępu użytkowników |Rola Administrator dostępu użytkowników umożliwia zarządzanie dostępem użytkowników do kont usługi Azure Automation. |

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia przyznane każdej roli. Może to obejmować akcje, które dają uprawnienia, i NotActions, które je ograniczają.

### <a name="owner"></a>Właściciel

Właściciel może zarządzać wszystkim, w tym dostępem. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|Akcje|Opis|
|---|---|
|Microsoft.Automation/automationKonta/|Twórz zasoby wszystkich typów i zarządzaj nimi.|

### <a name="contributor"></a>Współautor

Współautor może zarządzać wszystkim, z wyjątkiem dostępu. W poniższej tabeli przedstawiono uprawnienia przyznane i odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Automation/automationKonta/|Tworzenie zasobów wszystkich typów i zarządzanie nimi|
|**Nie akcje**||
|Microsoft.Authorization/*/Delete| Usuwanie ról i przypisań ról.       |
|Microsoft.Authorization/*/Write     |  Tworzenie ról i przypisań ról.       |
|Microsoft.Authorization/elevateAccess/Action    | Odmawia możliwości utworzenia administratora dostępu użytkownika.       |

### <a name="reader"></a>Czytelnik

Czytnik można wyświetlić wszystkie zasoby na koncie automatyzacji, ale nie można wprowadzić żadnych zmian.

|**Akcje**  |**Opis**  |
|---------|---------|
|Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt|Służy do wyświetlania wszystkich zasobów na koncie automatyzacji. |

### <a name="automation-operator"></a>Operator usługi

Operator automatyzacji jest w stanie tworzyć zadania i zarządzać nimi oraz odczytywać nazwy i właściwości elementu runbook dla wszystkich śmiób ekupadowych na koncie automatyzacji.  Uwaga: Jeśli chcesz kontrolować dostęp operatora do poszczególnych śmigieł, nie ustawiaj tej roli, a zamiast tego użyj ról "Operator zadania automatyzacji" i "Operator yjście automatyzacji" w połączeniu. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Odczyt autoryzacji.|
|Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/hybrydaRunbookWorkerGroups/read|Odczyt zasobów procesu roboczego egoisty hybrydowego.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read|Lista zadań egonika.|
|Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/wznowienie/akcja|Wznowienie zadania, które zostało wstrzymane.|
|Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/zatrzymanie/akcja|Anulowanie zadania w toku.|
|Automatyzacja firmy Microsoft/automationKonta/zadania/strumienie/odczyt|Odczyt strumieni zadań i danych wyjściowych.|
|Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/dane wyjściowe/odczyt|Pobierz dane wyjściowe zadania.|
|Automatyzacja firmy Microsoft.Automation/automationKonta/zadania/wstrzymanie/działanie|Wstrzymaj zadanie w toku.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write|Tworzenie miejsc pracy.|
|Automatyzacja Microsoft.Automation/automationKonta/zadaniaSześci/odczyt|Uzyskaj harmonogram zadań usługi Azure Automation.|
|Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/zadaniaSześci/zapis|Tworzenie harmonogramu zadań usługi Azure Automation.|
|Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/linkedWorkspace/read|Pobierz obszar roboczy połączony z kontem automatyzacji.|
|Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt|Pobierz konto usługi Azure Automation.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/read|Pobierz element runbook usługi Azure Automation.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/read|Pobierz zasób harmonogramu usługi Azure Automation.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/write|Tworzenie lub aktualizowanie zasobu harmonogramu usługi Azure Automation.|
|Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt      |Odczytywanie ról i przypisań ról.         |
|Zasoby/wdrożenia firmy Microsoft/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Pomoc techniczna firmy Microsoft.Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-job-operator"></a>Operator zadań automatyzacji

Rola operatora zadania automatyzacji jest przyznawana w zakresie konta automatyzacji.Dzięki temu uprawnienia operatora do tworzenia zadań i zarządzania nimi dla wszystkich wiązków wiązkowych na koncie. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Odczyt autoryzacji.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read|Lista zadań egonika.|
|Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/wznowienie/akcja|Wznowienie zadania, które zostało wstrzymane.|
|Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/zatrzymanie/akcja|Anulowanie zadania w toku.|
|Automatyzacja firmy Microsoft/automationKonta/zadania/strumienie/odczyt|Odczyt strumieni zadań i danych wyjściowych.|
|Automatyzacja firmy Microsoft.Automation/automationKonta/zadania/wstrzymanie/działanie|Wstrzymaj zadanie w toku.|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write|Tworzenie miejsc pracy.|
|Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt      |  Odczytywanie ról i przypisań ról.       |
|Zasoby/wdrożenia firmy Microsoft/*      |Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Pomoc techniczna firmy Microsoft.Support/* |Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="automation-runbook-operator"></a>Operator systemu runbook automatyzacji

Rola operatora misji automatyzacji jest przyznawana w zakresie runbook. Operator elementu runbook automatyzacji można wyświetlić nazwę elementu runbook i właściwości.Ta rola w połączeniu z rolą "Operator zadań automatyzacji" umożliwia operatorowi również tworzenie zadań i zarządzanie nimi dla systemu runbook. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/read     | Wyświetl listę ekwu.        |
|Microsoft.Authorization/*/read      | Odczyt autoryzacji.        |
|Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt      |Odczytywanie ról i przypisań ról.         |
|Zasoby/wdrożenia firmy Microsoft/*      | Tworzenie wdrożeń grup zasobów i zarządzanie nimi.         |
|Microsoft.Insights/alertRules/*      | Tworzenie reguł alertów i zarządzanie nimi.        |
|Pomoc techniczna firmy Microsoft.Support/*      | Twórz bilety pomocy technicznej i zarządzaj nimi.        |

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Ustawienia monitorowania edycji obejmują dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu, aby móc skonfigurować zbieranie dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont automatyzacji; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure na wszystkich zasobach platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.Automation/automationKonta/*|Zarządzanie kontami automatyzacji.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Tworzenie rozszerzeń maszyn wirtualnych i zarządzanie nimi.|
|Microsoft.ClassicStorage/storageKonta/listKeys/akcja|Wyświetl listę klasycznych kluczy konta magazynu.|
|Microsoft.Compute/virtualMachines/extensions/*|Tworzenie klasycznych rozszerzeń maszyn wirtualnych i zarządzanie nimi.|
|Microsoft.Insights/alertRules/*|Reguły alertów odczytu/zapisu/usuwania.|
|Microsoft.Insights/diagnosticSettings/*|Ustawienia diagnostyczne odczytu/zapisu/usuwania.|
|Microsoft.OperationalInsights/*|Zarządzanie dziennikami usługi Azure Monitor.|
|Microsoft.OperationsManagement/*|Zarządzanie rozwiązaniami w obszarach roboczych.|
|Zasoby/wdrożenia firmy Microsoft/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Zasoby firmy Microsoft/subskrypcje/grupy/wdrożenia zasobów/*|Tworzenie wdrożeń grup zasobów i zarządzanie nimi.|
|Magazyn/magazyn microsoftKonserwuje/listKeys/akcja|Lista kluczy konta magazynu.|
|Pomoc techniczna firmy Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Czytnik usługi Log Analytics można wyświetlać i przeszukiwać wszystkie dane monitorowania, a także ustawienia monitorowania wyświetlania, w tym wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane lub odrzucone dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Zarządzanie zapytaniami w dziennikach usługi Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukaj dane dziennika usługi Azure Monitor.|
|Pomoc techniczna firmy Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|**Nie akcje**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nie można odczytać udostępnionych kluczy dostępu.|

### <a name="monitoring-contributor"></a>Współautor monitorowania

Współautor monitorowania może odczytać wszystkie dane monitorowania i zaktualizować ustawienia monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.AlertsManiuł/Alerty/*|Zarządzanie alertami.|
|Microsoft.AlertsManagement/alertsSummary/*|Zarządzanie pulpitem nawigacyjnym alertów.|
|Microsoft.Insights/AlertRules/*|Zarządzanie regułami alertów.|
|Microsoft.Insights/składniki/*|Zarządzanie składnikami usługi Application Insights.|
|Microsoft.Insights/DiagnostykaStawienia/*|Zarządzanie ustawieniami diagnostycznymi.|
|Microsoft.Insights/eventtypes/*|Lista zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie ma zastosowanie zarówno do programu, jak i dostępu do dziennika aktywności w portalu.|
|Microsoft.Insights/LogDefinitions/*|To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dziennika w dzienniku aktywności.|
|Microsoft.Insights/MetricDefinitions/*|Odczyt definicji metryk (lista dostępnych typów metryk dla zasobu).|
|Microsoft.Insights/Metryki/*|Odczyt metryki zasobu.|
|Microsoft.Insights/Zarejestruj się/Akcja|Zarejestruj dostawcę microsoft.insights.|
|Microsoft.Insights/webtests/*|Zarządzanie testami sieci Web usługi Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Zarządzanie pakietami rozwiązań dziennika usługi Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Zarządzanie dziennikami usługi Azure Monitor zapisanych wyszukiwań.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukiwanie obszarów roboczych analizy dzienników.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listy kluczy dla obszaru roboczego usługi Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Zarządzanie konfiguracjami wglądu w dane magazynu usługi Azure Monitor.|
|Pomoc techniczna firmy Microsoft.Support/*|Twórz bilety pomocy technicznej i zarządzaj nimi.|
|Microsoft.WorkloadMonitor/obciążeń/*|Zarządzanie obciążeniami.|

### <a name="monitoring-reader"></a>Czytnik monitoringu

Czytnik monitorowania może odczytać wszystkie dane monitorowania. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych.|
|Microsoft.OperationalInsights/workspaces/search/action|Przeszukiwanie obszarów roboczych analizy dzienników.|
|Pomoc techniczna firmy Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Administrator dostępu użytkownika może zarządzać dostępem użytkowników do zasobów platformy Azure. W poniższej tabeli przedstawiono uprawnienia przyznane dla roli:

|**Akcje**  |**Opis**  |
|---------|---------|
|*/odczyt|Przeczytaj wszystkie zasoby|
|Microsoft.Authorization/*|Zarządzanie autoryzacją|
|Pomoc techniczna firmy Microsoft.Support/*|Tworzenie biletów pomocy technicznej i zarządzanie nimi|

## <a name="onboarding-permissions"></a>Uprawnienia dołączania

W poniższych sekcjach opisano minimalne wymagane uprawnienia wymagane do dołączania maszyn wirtualnych do śledzenia zmian lub zarządzania aktualizacjami rozwiązań.

### <a name="permissions-for-onboarding-from-a-vm"></a>Uprawnienia do dołączania z maszyny Wirtualnej

|**Akcja**  |**Uprawnienie**  |**Minimalny zakres**  |
|---------|---------|---------|
|Napisz nowe wdrożenie      | Zasoby/wdrożenia firmy Microsoft/*          |Subskrypcja          |
|Pisanie nowej grupy zasobów      | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/zapis        | Subskrypcja          |
|Tworzenie nowego domyślnego obszaru roboczego      | Microsoft.OperationalInsights/workspaces/write         | Grupa zasobów         |
|Utwórz nowe konto      |  Automatyzacja/automatyzacja Microsoft.AutomationKonta/zapis        |Grupa zasobów         |
|Połącz obszar roboczy i konto      |Microsoft.OperationalInsights/workspaces/write</br>Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt|Workspace</br>Konto usługi Automation
|Tworzenie rozszerzenia MMA      | Microsoft.Compute/virtualMachines/write         | Maszyna wirtualna         |
|Tworzenie zapisanego wyszukiwania      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Tworzenie konfiguracji zakresu      | Microsoft.OperationalInsights/workspaces/write          | Workspace         |
|Sprawdzanie stanu dołączania — odczyt obszaru roboczego      | Microsoft.OperationalInsights/workspaces/read         | Workspace         |
|Sprawdzanie stanu dołączania — odczyt właściwości połączonego obszaru roboczego konta     | Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt      | Konto usługi Automation        |
|Sprawdzanie stanu dołączania — odczyt rozwiązania      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Rozwiązanie         |
|Sprawdzanie stanu dołączania — odczyt maszyny Wirtualnej      | Microsoft.Compute/virtualMachines/read         | Maszyna wirtualna         |
|Sprawdzanie stanu dołączania — odczyt konta      | Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt  |  Konto usługi Automation   |
| Sprawdzanie obszaru roboczego dołączania dla maszyny Wirtualnej<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subskrypcja         |
| Zarejestruj dostawcę usługi Log Analytics |Microsoft.Insights/register/action | Subskrypcja|

<sup>1</sup> To uprawnienie jest potrzebne do wbudowanego za pośrednictwem środowiska portalu maszyny Wirtualnej.

### <a name="permissions-for-onboarding-from-automation-account"></a>Uprawnienia do dołączania z konta automatyzacji

|**Akcja**  |**Uprawnienie** |**Minimalny zakres**  |
|---------|---------|---------|
|Tworzenie nowego wdrożenia     | Zasoby/wdrożenia firmy Microsoft/*        | Subskrypcja         |
|Tworzenie nowej grupy zasobów     | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/zapis         | Subskrypcja        |
|Ostrze AutomationOnboarding — tworzenie nowego obszaru roboczego     |Microsoft.OperationalInsights/workspaces/write           | Grupa zasobów        |
|Ostrówka AutomationOnboarding - odczyt połączonego obszaru roboczego     | Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt        | Konto usługi Automation       |
|AutomationOnboarding blade - rozwiązanie do odczytu     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Rozwiązanie        |
|Ostrze AutomationOnboarding - obszar roboczy odczytu     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Workspace        |
|Tworzenie łącza dla obszaru roboczego i konta     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Napisz konto dla shoebox      | Automatyzacja/automatyzacja Microsoft.AutomationKonta/zapis        | Konto        |
|Tworzenie/edytowanie zapisanego wyszukiwania     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
|Tworzenie/edytowanie konfiguracji zakresu     | Microsoft.OperationalInsights/workspaces/write        | Workspace        |
| Zarejestruj dostawcę usługi Log Analytics |Microsoft.Insights/register/action | Subskrypcja|
|**Krok 2 - Wbudowane wiele maszyn wirtualnych**     |         |         |
|VMOnboarding blade - Tworzenie rozszerzenia MMA     | Microsoft.Compute/virtualMachines/write           | Maszyna wirtualna        |
|Tworzenie / edytowanie zapisanego wyszukiwania     | Microsoft.OperationalInsights/workspaces/write           | Workspace        |
|Tworzenie / edytowanie konfiguracji zakresu  | Microsoft.OperationalInsights/workspaces/write   | Workspace|

## <a name="update-management-permissions"></a>Aktualizowanie uprawnień do zarządzania

Zarządzanie aktualizacjami dociera do wielu usług, aby świadczyć swoje usługi. W poniższej tabeli przedstawiono uprawnienia potrzebne do zarządzania wdrożeniami zarządzania aktualizacjami:

|**Zasób**  |**Rola**  |**Zakres**  |
|---------|---------|---------|
|Konto usługi Automation     | Współautor usługi Log Analytics       | Konto usługi Automation        |
|Konto usługi Automation    | Współautor maszyny wirtualnej        | Grupa zasobów dla konta        |
|Obszar roboczy usługi Log Analytics     | Współautor usługi Log Analytics| Obszar roboczy usługi Log Analytics        |
|Obszar roboczy usługi Log Analytics |Czytelnik usługi Log Analytics| Subskrypcja|
|Rozwiązanie     |Współautor usługi Log Analytics         | Rozwiązanie|
|Maszyna wirtualna     | Współautor maszyny wirtualnej        | Maszyna wirtualna        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurowanie rbac dla konta automatyzacji

W poniższej sekcji pokazano, jak skonfigurować rbac na koncie automatyzacji za pośrednictwem [portalu Azure](#configure-rbac-using-the-azure-portal) i [programu PowerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurowanie funkcji RBAC przy użyciu portalu Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i otwórz swoje konto usługi Automation ze strony Konta usługi Automation.
2. Kliknij **pozycję Kontrola dostępu (IAM),** aby otworzyć stronę kontroli dostępu (IAM). Za pomocą tej strony można dodawać nowych użytkowników, grupy i aplikacje do zarządzania kontem automatyzacji i wyświetlania istniejących ról, które można skonfigurować dla konta automatyzacji.
3. Kliknij kartę **Przypisania ról**.

   ![Przycisk Dostęp](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Dodawanie nowego użytkownika i przypisywanie roli

1. Na stronie Kontrola dostępu (IAM) kliknij pozycję **+ Dodaj przypisanie roli**. Ta akcja otwiera stronę Dodaj przypisanie roli, na której można dodać użytkownika, grupę lub aplikację i przypisać odpowiednią rolę.

2. Wybierz rolę z listy dostępnych ról. Można wybrać dowolną z dostępnych ról wbudowanych, które obsługuje konto automatyzacji lub dowolną rolę niestandardową, którą można zdefiniować.

3. Wpisz nazwę użytkownika, do którego chcesz nadać uprawnienia w polu **Wybierz.** Wybierz użytkownika z listy i kliknij przycisk **Zapisz**.

   ![Dodawanie użytkowników](media/automation-role-based-access-control/automation-04-add-users.png)

   Teraz powinieneś zobaczyć użytkownika dodanego do strony Użytkownicy z przypisaną wybraną rolą.

   ![Wyświetlanie użytkowników](media/automation-role-based-access-control/automation-05-list-users.png)

   Możesz także przypisać rolę użytkownikowi na stronie Role.
4. Kliknij **pozycję Role** na stronie Kontrola dostępu (IAM), aby otworzyć stronę Role. Można wyświetlić nazwę roli oraz liczbę użytkowników i grup przypisanych do tej roli.

    ![Przypisywanie roli na stronie użytkowników](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Kontrola dostępu oparta na rolach można ustawić tylko w zakresie konta automatyzacji, a nie w żadnym zasobie poniżej konta automatyzacji.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Można usunąć uprawnienie dostępu dla użytkownika, który nie zarządza kontem automatyzacji lub który nie pracuje już dla organizacji. Poniżej przedstawiono kroki prowadzące do usunięcia użytkownika:

1. Na stronie Kontrola dostępu (IAM) wybierz użytkownika, który ma usunąć, a następnie kliknij przycisk **Usuń**.
2. Kliknij przycisk **Usuń** w okienku szczegółów przypisania.
3. Kliknij przycisk **Tak**, aby potwierdzić usunięcie.

   ![Usuwanie użytkowników](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurowanie funkcji RBAC przy użyciu programu PowerShell

Można również skonfigurować dostęp oparty na rolach do konta automatyzacji przy użyciu następujących [poleceń cmdlet programu Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleDefinition?view=azps-3.7.0) zawiera listę wszystkich ról RBAC, które są dostępne w usłudze Azure Active Directory. Tego polecenia cmdlet z `Name` parametrem można użyć do wyświetlenia listy wszystkich akcji, które może wykonać określona rola.

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

[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0) wyświetla listę przypisań ról RBAC usługi Azure AD w określonym zakresie. Bez żadnych parametrów to polecenie cmdlet zwraca wszystkie przypisania ról dokonane w ramach subskrypcji. Parametr `ExpandPrincipalGroups` służy do listy przypisań dostępu dla określonego użytkownika, a także grup, do których należy użytkownik.

**Przykład:** Użyj następującego polecenia cmdlet, aby wyświetlić listę wszystkich użytkowników i ich ról w ramach konta automatyzacji.

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

Użyj [new-AzRoleAssignment,](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzRoleAssignment?view=azps-3.7.0) aby przypisać dostęp do użytkowników, grup i aplikacji do określonego zakresu.
    
**Przykład:** Użyj następującego polecenia, aby przypisać rolę "Operator automatyzacji" dla użytkownika w zakresie konta automatyzacji.

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

Użyj [usuń azRoleAssignment,](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzRoleAssignment?view=azps-3.7.0) aby usunąć dostęp określonego użytkownika, grupy lub aplikacji z określonego zakresu.

**Przykład:** Użyj następującego polecenia, aby usunąć użytkownika z roli operatora automatyzacji w zakresie konta automatyzacji.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

W poprzednim przykładzie `sign-in ID of a user you wish to remove`zastąp , `SubscriptionID`, `Resource Group Name`i `Automation account name` z danymi konta. Wybierz **tak,** gdy zostanie wyświetlony monit o potwierdzenie przed kontynuowaniem usuwania przypisań ról użytkownika.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Środowisko użytkownika dla roli operatora automatyzacji — konto automatyzacji

Gdy użytkownik przypisany do roli operatora automatyzacji w zakresie konta automatyzacji wyświetla konto automatyzacji, do którego jest przypisany, użytkownik może wyświetlać tylko listę śmigłów, zadań uruchomieniu i harmonogramów utworzonych na koncie automatyzacji. Ten użytkownik nie może wyświetlić definicji tych elementów. Użytkownik może uruchamiać, zatrzymywać, zawieszać, wznawiać lub planować zadanie uruchomieniu. Nijamic. Jednak użytkownik nie ma dostępu do innych zasobów automatyzacji, takich jak konfiguracje, hybrydowe grupy robocze lub węzły DSC.

![Brak dostępu do zasobów](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurowanie rbac dla kreśleń

Usługa Azure Automation umożliwia przypisanie rbac do określonych śmiób. Aby to zrobić, uruchom następujący skrypt, aby dodać użytkownika do określonego elementów runbook. Administrator konta automatyzacji lub administrator dzierżawy może uruchomić ten skrypt.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Po uruchomieniu skryptu należy zalogować się do portalu Azure i wybrać **pozycję Wszystkie zasoby**. Na liście użytkownik może zobaczyć element runbook, dla którego został dodany jako operator systemu uruchomieniu automatyzacji.

![Runbook RBAC w portalu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Środowisko użytkownika dla roli operatora automatyzacji — Runbook

Gdy użytkownik przypisany do roli operatora automatyzacji w zakresie runbook wyświetla przypisany element runbook, użytkownik może tylko uruchomić element runbook i wyświetlić zadania uruchomieniu. .

![Ma tylko dostęp do startu](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat sposobów konfigurowania rbac dla usługi Azure Automation, zobacz [zarządzanie RBAC za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Aby uzyskać szczegółowe informacje na temat sposobów uruchamiania śmiętu, zobacz [Uruchamianie śmiętu](automation-starting-a-runbook.md).
* Aby uzyskać informacje na temat typów elementów [runbook, zobacz typy elementów runbook usługi Azure Automation](automation-runbook-types.md).