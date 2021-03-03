---
title: Zarządzanie obszarami roboczymi Log Analytics w Azure Monitor | Microsoft Docs
description: Możesz zarządzać dostępem do danych przechowywanych w obszarze roboczym Log Analytics w Azure Monitor przy użyciu zasobów, obszaru roboczego lub uprawnień na poziomie tabeli. W tym artykule szczegółowo opisano, jak to zrobić.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: bc03d8cdcaf0079ef15e648d16fd5656d9da368c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725683"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Zarządzanie dostępem do danych dziennika i obszarami roboczymi w usłudze Azure Monitor

Azure Monitor przechowuje dane [dzienników](../logs/data-platform-logs.md) w obszarze roboczym log Analytics. Obszar roboczy to kontener zawierający dane i informacje o konfiguracji. Aby zarządzać dostępem do danych dziennika, należy wykonać różne zadania administracyjne związane z obszarem roboczym.

W tym artykule wyjaśniono, jak zarządzać dostępem do dzienników i administrować obszarami roboczymi, które je zawierają, w tym w jaki sposób udzielić dostępu do: 

* Obszar roboczy przy użyciu uprawnień obszaru roboczego.
* Użytkownicy, którzy potrzebują dostępu do danych dzienników z określonych zasobów przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) — znanego również jako [kontekst zasobów](../logs/design-logs-deployment.md#access-mode)
* Użytkownicy, którzy potrzebują dostępu do danych dziennika w określonej tabeli w obszarze roboczym przy użyciu funkcji RBAC platformy Azure.

Aby zrozumieć koncepcje dotyczące pojęć związanych z usługą Azure RBAC i strategiami dostępu, przeczytaj artykuł [projektowanie wdrożenia dzienników Azure monitor](../logs/design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>Konfigurowanie trybu kontroli dostępu

[Tryb kontroli dostępu](../logs/design-logs-deployment.md) skonfigurowany w obszarze roboczym można wyświetlić z poziomu Azure Portal lub Azure PowerShell.  To ustawienie można zmienić przy użyciu jednej z następujących obsługiwanych metod:

* Azure Portal

* Azure PowerShell

* Szablon usługi Azure Resource Manager

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

Bieżący tryb kontroli dostępu obszaru roboczego można wyświetlić na stronie **Przegląd** obszaru roboczego w menu **log Analytics obszaru roboczego** .

![Wyświetlanie trybu kontroli dostępu do obszaru roboczego](media/manage-access/view-access-control-mode.png)

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. W Azure Portal wybierz pozycję Log Analytics obszary robocze > obszarze roboczym.

To ustawienie można zmienić na stronie **Właściwości** obszaru roboczego. Zmiana ustawienia zostanie wyłączona, jeśli nie masz uprawnień do konfigurowania obszaru roboczego.

![Zmień tryb dostępu obszaru roboczego](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Użyj następującego polecenia, aby przeanalizować tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Dane wyjściowe powinny wyglądać podobnie do następujących:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Wartość `False` oznacza, że obszar roboczy jest skonfigurowany z trybem dostępu do kontekstu obszaru roboczego.  Wartość `True` oznacza, że obszar roboczy jest skonfigurowany z trybem dostępu do kontekstu zasobów.

> [!NOTE]
> Jeśli obszar roboczy jest zwracany bez wartości logicznej i jest pusty, jest również zgodny z wynikami `False` wartości.
>

Użyj poniższego skryptu, aby ustawić tryb kontroli dostępu dla określonego obszaru roboczego na uprawnienie do kontekstu zasobu:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Poniższy skrypt służy do ustawiania trybu kontroli dostępu dla wszystkich obszarów roboczych w ramach subskrypcji do uprawnienia kontekstu zasobu:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Aby skonfigurować tryb dostępu w szablonie Azure Resource Manager, Ustaw flagę funkcji **enableLogAccessUsingOnlyResourcePermissions** w obszarze roboczym na jedną z następujących wartości.

* **false**: Ustaw obszar roboczy na uprawnienia kontekstu obszaru roboczego. Jest to ustawienie domyślne, jeśli flaga nie jest ustawiona.
* **prawda**: Ustaw obszar roboczy na uprawnienia do zasobów.

## <a name="manage-access-using-workspace-permissions"></a>Zarządzanie dostępem przy użyciu uprawnień obszaru roboczego

Z każdym obszarem roboczym może być skojarzonych wiele kont, a każde konto może mieć dostęp do wielu obszarów roboczych. Dostęp jest zarządzany przy użyciu [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Następujące działania również wymagają uprawnień platformy Azure:

|Akcja |Wymagane uprawnienia platformy Azure |Uwagi |
|-------|-------------------------|------|
| Dodawanie i usuwanie rozwiązań do monitorowania | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Wyświetlanie podstawowych właściwości obszaru roboczego i wprowadzanie bloku obszaru roboczego w portalu | `Microsoft.OperationalInsights/workspaces/read` ||
| Wykonywanie zapytań dzienników przy użyciu dowolnego interfejsu | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Dostęp do wszystkich typów dzienników przy użyciu zapytań | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Dostęp do określonej tabeli dziennika | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Odczytaj klucze obszaru roboczego, aby umożliwić wysyłanie dzienników do tego obszaru roboczego | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Zarządzanie dostępem przy użyciu uprawnień platformy Azure

Aby udzielić dostępu do obszaru roboczego Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w sekcji [Przypisywanie ról platformy Azure w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md). Przykład ról niestandardowych można znaleźć w temacie [przykład role niestandardowe](#custom-role-examples)

Platforma Azure ma dwie wbudowane role użytkowników dla Log Analytics obszarów roboczych:

* Czytelnik usługi Log Analytics
* Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:

* Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych
* Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola czytelnika Log Analytics obejmuje następujące działania platformy Azure:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Możliwość wyświetlania wszystkich zasobów i konfiguracji zasobów platformy Azure. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytywania ustawień obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
| Akcja | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. |
| Akcja | `Microsoft.OperationalInsights/workspaces/search/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. |
| Akcja | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
|Inne | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Uniemożliwia odczytywanie klucza obszaru roboczego wymaganego do korzystania z interfejsu API zbierania danych i instalowania agentów. Zapobiega to dodawaniu przez użytkownika nowych zasobów do obszaru roboczego |

Członkowie roli *Współautor usługi Log Analytics* mogą wykonywać następujące czynności:

* Obejmuje wszystkie uprawnienia *roli czytelnik log Analytics*, co umożliwia użytkownikowi odczytywanie wszystkich danych monitorowania
* Tworzenie i Konfigurowanie kont usługi Automation
* Dodawanie i usuwanie rozwiązań do zarządzania

    > [!NOTE]
    > Aby pomyślnie wykonać ostatnie dwie akcje, należy przyznać to uprawnienie na poziomie grupy zasobów lub subskrypcji.

* Odczytaj klucze konta magazynu
* Konfigurowanie kolekcji dzienników z usługi Azure Storage
* Edytowanie ustawień monitorowania dla zasobów platformy Azure, w tym:
  * Dodawanie rozszerzenia do maszyn wirtualnych
  * Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure

> [!NOTE]
> Możliwości dodania rozszerzenia do maszyny wirtualnej możesz użyć, aby zyskać pełną kontrolę nad maszyną wirtualną.

Rola współautor Log Analytics obejmuje następujące działania platformy Azure:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `*/read`     | Możliwość wyświetlania wszystkich zasobów i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytu ustawienia obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
| `Microsoft.Automation/automationAccounts/*` | Możliwość tworzenia i konfigurowania kont usługi Azure Automation, w tym dodawania i edytowania elementów runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Dodawanie, aktualizowanie i usuwanie rozszerzeń maszyn wirtualnych, w tym rozszerzenia Microsoft Monitoring Agent oraz rozszerzenia OMS Agent for Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Wyświetlanie klucza konta magazynu. Wymagane do skonfigurowania usługi Log Analytics w celu odczytu dzienników z kont magazynów platformy Azure |
| `Microsoft.Insights/alertRules/*` | Dodawanie, aktualizowanie i usuwanie reguł alertu |
| `Microsoft.Insights/diagnosticSettings/*` | Dodawanie, aktualizowanie i usuwanie ustawień diagnostycznych dla zasobów platformy Azure |
| `Microsoft.OperationalInsights/*` | Dodawanie, aktualizowanie i usuwanie konfiguracji dla obszarów roboczych Log Analytics. Aby edytować zaawansowane ustawienia obszaru roboczego, wymagane przez użytkownika `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Dodawanie i usuwanie rozwiązań do zarządzania |
| `Microsoft.Resources/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |

Aby dodawać użytkowników do roli użytkownika i usuwać ich z niej, wymagane jest uprawnienie `Microsoft.Authorization/*/Delete` i `Microsoft.Authorization/*/Write`.

Za pomocą następujących ról możesz udzielić użytkownikom dostępu w różnych zakresach:

* Subskrypcja — dostęp do wszystkich obszarów roboczych w subskrypcji
* Grupa zasobów — dostęp do wszystkich obszarów roboczych w grupie zasobów
* Zasób — dostęp tylko do określonego obszaru roboczego

Zalecamy wykonywanie przypisań na poziomie zasobów (w obszarze roboczym) w celu zapewnienia dokładnej kontroli dostępu. Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

### <a name="resource-permissions"></a>Uprawnienia zasobów

Gdy użytkownicy wykonują zapytania dotyczące dzienników z obszaru roboczego przy użyciu dostępu do kontekstu zasobów, będą mieć następujące uprawnienia do zasobu:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Przykłady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Możliwość wyświetlania wszystkich danych dziennika dla zasobu.  |
| `Microsoft.Insights/diagnosticSettings/write` | Możliwość skonfigurowania ustawień diagnostycznych w celu zezwolenia na Konfigurowanie dzienników dla tego zasobu. |

`/read`uprawnienie jest zazwyczaj udzielane z roli obejmującej _\* /Read lub_ _\*_ uprawnienia, takie jak wbudowane [czytniki](../../role-based-access-control/built-in-roles.md#reader) i role [współautor](../../role-based-access-control/built-in-roles.md#contributor) . Role niestandardowe zawierające określone akcje lub dedykowane role wbudowane mogą nie uwzględniać tego uprawnienia.

Zobacz [Definiowanie kontroli dostępu dla tabel](#table-level-azure-rbac) poniżej, jeśli chcesz utworzyć inną kontrolę dostępu dla różnych tabel.

## <a name="custom-role-examples"></a>Przykłady ról niestandardowych

1. Aby udzielić użytkownikowi dostępu do danych dziennika z zasobów, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień obszaru roboczego lub zasobu**

    * Przyznaj użytkownikom `*/read` lub `Microsoft.Insights/logs/*/read` uprawnienia do ich zasobów. Jeśli przypisano już rolę [czytnika log Analytics](../../role-based-access-control/built-in-roles.md#reader) w obszarze roboczym, wystarczy.

2. Aby udzielić użytkownikowi dostępu do danych dziennika z zasobów i skonfigurować zasoby do wysyłania dzienników do obszaru roboczego, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień obszaru roboczego lub zasobu**

    * Przyznaj użytkownikom następujące uprawnienia w obszarze roboczym: `Microsoft.OperationalInsights/workspaces/read` i `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Przy użyciu tych uprawnień użytkownicy nie mogą wykonywać żadnych zapytań na poziomie obszaru roboczego. Można wyliczyć ten obszar roboczy i użyć go jako miejsca docelowego dla ustawień diagnostycznych lub konfiguracji agenta.

    * Udziel użytkownikom następujących uprawnień do swoich zasobów: `Microsoft.Insights/logs/*/read` i `Microsoft.Insights/diagnosticSettings/write` . Jeśli przypisano już rolę [współautor log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , przypisano rolę czytelnika lub udzielono `*/read` uprawnień do tego zasobu, wystarczy.

3. Aby udzielić użytkownikowi dostępu do danych dziennika z zasobów bez możliwości odczytywania zdarzeń zabezpieczeń i wysyłania danych, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień obszaru roboczego lub zasobu**

    * Udziel użytkownikom następujących uprawnień do swoich zasobów: `Microsoft.Insights/logs/*/read` .

    * Dodaj następującą akcję nieactionową, aby uniemożliwić użytkownikom odczytywanie typu SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read` . Akcja nie powinna znajdować się w tej samej roli niestandardowej, co akcja, która zapewnia uprawnienia do odczytu ( `Microsoft.Insights/logs/*/read` ). Jeśli użytkownik zauważał akcję odczytu z innej roli przypisanej do tego zasobu lub subskrypcji lub grupy zasobów, może odczytywać wszystkie typy dzienników. Jest to również prawdziwe, Jeśli dziedziczą `*/read` , która istnieje na przykład z rolą czytelnik lub współautor.

4. Aby udzielić użytkownikowi dostępu do danych dziennika z zasobów i odczytywać wszystkie logowania do usługi Azure AD i odczytywać Update Management dane dziennika rozwiązania z obszaru roboczego, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień obszaru roboczego lub zasobu**

    * Przyznaj użytkownikom następujące uprawnienia w obszarze roboczym: 

        * `Microsoft.OperationalInsights/workspaces/read` — wymagane, aby użytkownik mógł wyliczyć obszar roboczy i otworzyć blok obszaru roboczego w Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` — wymagane przez każdego użytkownika, który może wykonywać zapytania
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` — Aby móc odczytywać dzienniki logowania usługi Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` — Aby można było odczytać dzienniki rozwiązań Update Management
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` — Aby można było odczytać dzienniki rozwiązań Update Management
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` — Aby można było odczytać dzienniki zarządzania aktualizacjami
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` — wymagane jest, aby można było używać rozwiązania Update Management
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` — wymagane jest, aby można było używać rozwiązania Update Management

    * Udziel użytkownikom następujących uprawnień do swoich zasobów: `*/read` , przypisanych do roli czytelnik lub `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>Na poziomie tabeli Azure RBAC

Na **poziomie tabeli usługa Azure RBAC** umożliwia zdefiniowanie bardziej szczegółowej kontroli nad danymi w obszarze roboczym log Analytics oprócz innych uprawnień. Ta kontrolka umożliwia definiowanie określonych typów danych, które są dostępne tylko dla określonego zestawu użytkowników.

Aby udzielić dostępu do określonych [tabel](../logs/data-platform-logs.md) w obszarze roboczym, zaimplementuj kontrolę dostępu do tabel przy użyciu [ról niestandardowych platformy Azure](../../role-based-access-control/custom-roles.md) . Te role są stosowane do obszarów roboczych z [trybami kontroli dostępu](../logs/design-logs-deployment.md#access-control-mode) do kontekstu obszaru roboczego lub zasobu, niezależnie od [trybu dostępu](../logs/design-logs-deployment.md#access-mode)użytkownika.

Utwórz [niestandardową rolę](../../role-based-access-control/custom-roles.md) z poniższymi akcjami, aby zdefiniować dostęp do kontroli dostępu do tabeli.

* Aby udzielić dostępu do tabeli, Uwzględnij ją w sekcji **Akcje** w definicji roli. Aby odjąć dostęp od dozwolonych **akcji**, należy uwzględnić go w **sekcji** noactions.
* Użyj Microsoft. OperationalInsights/Workspaces/Query/*, aby określić wszystkie tabele.

Na przykład aby utworzyć rolę z dostępem do tabel _pulsu_ i _Azure_ , Utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Aby utworzyć rolę mającą dostęp tylko do tabeli _SecurityBaseline_ , Utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
Powyższe przykłady definiują listę dozwolonych tabel. Ten przykład pokazuje listę zablokowanych definicji, gdy użytkownik może uzyskać dostęp do wszystkich tabel, ale _SecurityAlert_ tabelę:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Niestandardowe dzienniki

 Dzienniki niestandardowe są tworzone na podstawie źródeł danych, takich jak dzienniki niestandardowe i interfejs API modułu zbierającego dane HTTP. Najprostszym sposobem identyfikacji typu dziennika jest sprawdzenie tabel wymienionych w obszarze [dzienniki niestandardowe w schemacie dziennika](./log-analytics-tutorial.md#table-schema).

 Nie można udzielić dostępu do poszczególnych dzienników niestandardowych, ale można udzielić dostępu do wszystkich dzienników niestandardowych. Aby utworzyć rolę mającą dostęp do wszystkich dzienników niestandardowych, Utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Alternatywnym podejściem do zarządzania dostępem do dzienników niestandardowych jest przypisanie ich do zasobu platformy Azure i zarządzanie dostępem przy użyciu modelu kontekstu zasobów. Aby użyć tej metody, należy dołączyć identyfikator zasobu przez określenie go w nagłówku [x-MS-AzureResourceId](../logs/data-collector-api.md#request-headers) , gdy dane są pozyskiwane do log Analytics za pośrednictwem [interfejsu API modułu zbierającego dane http](../logs/data-collector-api.md). Identyfikator zasobu musi być prawidłowy i mieć do niego zastosowane reguły dostępu. Po pozyskaniu dzienników są one dostępne dla osób z dostępem do odczytu do zasobu, jak wyjaśniono tutaj.

Czasami dzienniki niestandardowe pochodzą ze źródeł, które nie są bezpośrednio skojarzone z określonym zasobem. W takim przypadku należy utworzyć grupę zasobów tylko w celu zarządzania dostępem do tych dzienników. Grupa zasobów nie wiąże się z żadnym kosztem, ale zapewnia prawidłowy identyfikator zasobu do kontrolowania dostępu do dzienników niestandardowych. Na przykład jeśli określona Zapora wysyła dzienniki niestandardowe, Utwórz grupę zasobów o nazwie "MyFireWallLogs" i upewnij się, że żądania interfejsu API zawierają identyfikator zasobu "MyFireWallLogs". Rekordy dziennika zapory są następnie dostępne tylko dla użytkowników, którym udzielono dostępu do MyFireWallLogs lub z pełnymi prawami dostępu do obszaru roboczego.          

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jeśli użytkownik otrzymuje globalne uprawnienie do odczytu ze standardowym czytnikiem lub rolą współautor, które zawiera akcję _\* /Read_ , zastąpi kontrolę dostępu do tabeli i przekaże im dostęp do wszystkich danych dziennika.
* Jeśli użytkownik uzyska dostęp do tabeli, ale nie ma żadnych innych uprawnień, może uzyskać dostęp do danych dziennika z interfejsu API, ale nie z Azure Portal. Aby zapewnić dostęp z Azure Portal, Użyj czytnika Log Analytics jako roli podstawowej.
* Administratorzy i właściciele subskrypcji będą mieć dostęp do wszystkich typów danych, niezależnie od innych ustawień uprawnień.
* Właściciele obszarów roboczych są traktowani jak każdy inny użytkownik do kontroli dostępu do tabeli.
* Zalecamy przypisanie ról do grup zabezpieczeń zamiast poszczególnych użytkowników w celu zmniejszenia liczby przypisań. Ułatwi to również korzystanie z istniejących narzędzi do zarządzania grupami w celu konfigurowania i weryfikowania dostępu.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Omówienie agenta log Analytics](../agents/log-analytics-agent.md) , aby zebrać dane z komputerów w centrum danych lub w innym środowisku chmury.

* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [zbieranie danych o maszynach wirtualnych platformy Azure](../vm/quick-collect-azurevm.md) .