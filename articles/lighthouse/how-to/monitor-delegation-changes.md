---
title: Monitorowanie zmian delegowania w dzierżawie zarządzającej
description: Dowiedz się, jak monitorować działania delegowania z dzierżaw klientów do dzierżawy zarządzającej.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 1a12b916fae9794d6d695191a81ec076917bda31
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814896"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorowanie zmian delegowania w dzierżawie zarządzającej

Jako dostawca usług możesz chcieć wiedzieć, kiedy subskrypcje klientów lub grupy zasobów są delegowane do dzierżawy za pośrednictwem usługi [Azure Lighthouse](../overview.md)lub gdy wcześniej delegowane zasoby są usuwane.

W dzierżawie zarządzającej dziennik [aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md) śledzi aktywność delegowania na poziomie dzierżawy. To zarejestrowane działanie obejmuje wszelkie dodane lub usunięte delegowania z dzierżaw klientów.

W tym temacie wyjaśniono uprawnienia wymagane do monitorowania działania delegowania do dzierżawy (we wszystkich klientach). Zawiera również przykładowy skrypt, który pokazuje jedną metodę wykonywania zapytań i raportowania na podstawie tych danych.

> [!IMPORTANT]
> Wszystkie te kroki należy wykonać w dzierżawie zarządzającej, a nie w dzierżawach klientów.
>
> Chociaż w tym temacie odnosimy się do dostawców usług i klientów, przedsiębiorstwa zarządzające wieloma [dzierżawami](../concepts/enterprise.md) mogą używać tych samych procesów.

## <a name="enable-access-to-tenant-level-data"></a>Zapewnianie dostępu do danych na poziomie dzierżawy

Aby uzyskać dostęp do danych dziennika aktywności na poziomie dzierżawy, do konta musi być przypisana wbudowana rola czytelnika monitorowania platformy [Azure](../../role-based-access-control/built-in-roles.md#monitoring-reader) w zakresie głównym (/). To przypisanie musi wykonać użytkownik, który ma rolę administratora globalnego z dodatkowym podwyższonym poziomem dostępu.

### <a name="elevate-access-for-a-global-administrator-account"></a>Podnieś uprawnienia dostępu dla konta administratora globalnego

Aby przypisać rolę w zakresie głównym (/), musisz mieć rolę administratora globalnego z podwyższonym poziomem dostępu. Ten dostęp z podwyższonym poziomem uprawnień powinien być dodawany tylko wtedy, gdy konieczne jest przypisanie roli, a następnie usunięty po jego przypisaniu.

Aby uzyskać szczegółowe instrukcje dotyczące dodawania i usuwania podniesienia uprawnień, zobacz Podniesienie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami i grupami [zarządzania platformy Azure.](../../role-based-access-control/elevate-access-global-admin.md)

Po podnieśeniu uprawnień dostępu Twoje konto będzie mieć rolę administratora dostępu użytkowników na platformie Azure w zakresie głównym. To przypisanie roli umożliwia wyświetlanie wszystkich zasobów i przypisywanie dostępu w dowolnej subskrypcji lub grupie zarządzania w katalogu, a także tworzenie przypisań ról w zakresie głównym.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Przypisywanie roli Czytelnik monitorowania w zakresie głównym

Po podwyższonym poziomie dostępu możesz przypisać odpowiednie uprawnienia do konta, aby można było na nim zapytania dotyczące danych dziennika aktywności na poziomie dzierżawy. To konto musi mieć wbudowaną rolę czytelnika monitorowania platformy [Azure](../../role-based-access-control/built-in-roles.md#monitoring-reader) przypisaną do głównego zakresu dzierżawy zarządzającej.

> [!IMPORTANT]
> Przyznanie przypisania roli w zakresie głównym oznacza, że te same uprawnienia będą stosowane do każdego zasobu w dzierżawie. Ponieważ jest to szeroki poziom dostępu, możesz przypisać tę rolę do konta jednostki usługi i użyć tego konta do wykonywania [zapytań o dane.](#use-a-service-principal-account-to-query-the-activity-log) Można również przypisać rolę Czytelnik monitorowania w zakresie głównym do poszczególnych użytkowników lub do grup użytkowników, dzięki czemu mogą oni wyświetlać informacje o delegowaniu bezpośrednio w [Azure Portal](#view-delegation-changes-in-the-azure-portal). W takim przypadku należy pamiętać, że jest to szeroki poziom dostępu, który powinien być ograniczony do jak najmniejszej możliwej liczby użytkowników.

Użyj jednej z następujących metod, aby wykonać przypisanie zakresu głównego.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Usuwanie podwyższonego poziomu dostępu dla konta administratora globalnego

Po przypisaniu roli Czytelnik monitorowania w zakresie głównym do żądanego [](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) konta pamiętaj o usunięciu podwyższonego poziomu dostępu dla konta administratora globalnego, ponieważ ten poziom dostępu nie będzie już potrzebny.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Wyświetlanie zmian delegowania w Azure Portal

Użytkownicy, którym przypisano rolę Czytelnik monitorowania w zakresie głównym, mogą wyświetlać zmiany delegowania bezpośrednio w Azure Portal.

1. Przejdź do strony **Moi klienci,** a następnie wybierz pozycję **Dziennik aktywności** z menu nawigacji po lewej stronie.
1. Upewnij **się, że w** filtrze w górnej części ekranu wybrano opcję Działanie katalogu.

Zostanie wyświetlona lista zmian delegowania. Możesz wybrać pozycję **Edytuj** kolumny, aby wyświetlić lub ukryć wartości **Stan,** Kategoria **zdarzenia,** **Czas,** Sygnatura czasowa,  **Subskrypcja,** Zdarzenie **zainicjowane** przez , **Grupa** zasobów, **Typ** zasobu **i Zasób.**

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Zrzut ekranu przedstawiający zmiany delegowania w Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Używanie konta jednostki usługi do wykonywania zapytań w dzienniku aktywności

Ponieważ rola Czytelnik monitorowania w zakresie głównym ma tak szeroki poziom dostępu, możesz przypisać tę rolę do konta jednostki usługi i użyć tego konta do wykonywania zapytań o dane przy użyciu poniższego skryptu.

> [!IMPORTANT]
> Obecnie w dzierżawach z dużą ilością działań delegowania mogą wystąpić błędy podczas wykonywania zapytań o te dane.

W przypadku używania konta jednostki usługi do wykonywania zapytań w dzienniku aktywności zalecamy stosowanie następujących najlepszych rozwiązań:

- [Utwórz nowe konto jednostki usługi,](../../active-directory/develop/howto-create-service-principal-portal.md) które będzie używane tylko dla tej funkcji, zamiast przypisywać tę rolę do istniejącej jednostki usługi używanej do innej automatyzacji.
- Upewnij się, że ta jednostka usługi nie ma dostępu do żadnych delegowanych zasobów klienta.
- [Użyj certyfikatu do uwierzytelniania i](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) [bezpiecznego przechowywania go w Azure Key Vault](../../key-vault/general/security-features.md).
- Ogranicz użytkowników, którzy mają dostęp do działania w imieniu jednostki usługi.

Po utworzeniu nowego konta jednostki usługi z dostępem czytelnika monitorowania do zakresu głównego dzierżawy zarządzającej można używać go do wykonywania zapytań i zgłaszania działań delegowania w dzierżawie.

[Ten Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) może służyć do wykonywania zapytań dotyczących ostatniego 1 dnia aktywności i raportów dotyczących wszelkich dodanych lub usuniętych delegowania (lub prób, które nie powiedzyły się). Wysyła zapytanie do [danych dziennika aktywności dzierżawy,](/rest/api/monitor/TenantActivityLogs/List) a następnie tworzy następujące wartości w celu utworzenia raportu na temat delegowania, które zostały dodane lub usunięte:

- **DelegatedResourceId:** identyfikator delegowanego subskrypcji lub grupy zasobów
- **CustomerTenantId:** identyfikator dzierżawy klienta
- **CustomerSubscriptionId:** identyfikator subskrypcji, który został delegowany lub który zawiera delegowaną grupę zasobów
- **CustomerDelegationStatus:** zmiana stanu zasobu delegowanego (powodzenie lub niepowodzenie)
- **EventTimeStamp:** data i godzina, o której zarejestrowano zmianę delegowania

Podczas wykonywania zapytań o te dane należy pamiętać o:

- Jeśli wiele grup zasobów jest delegowanych w ramach jednego wdrożenia, dla każdej grupy zasobów zostaną zwrócone oddzielne wpisy.
- Zmiany wprowadzone w poprzednim delegowaniu (takie jak aktualizacja struktury uprawnień) będą rejestrowane jako dodane delegowanie.
- Jak wspomniano powyżej, konto musi mieć wbudowaną rolę czytelnika monitorowania platformy Azure w zakresie głównym (/), aby uzyskać dostęp do tych danych na poziomie dzierżawy.
- Tych danych można używać we własnych przepływach pracy i raportach. Na przykład można użyć interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza), aby rejestrować dane do usługi Azure Monitor z klienta interfejsu [API](../../azure-monitor/logs/data-collector-api.md) REST, a następnie użyć grup akcji do tworzenia powiadomień lub alertów. [](../../azure-monitor/alerts/action-groups.md)

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak dołączać klientów do [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Dowiedz się [więcej Azure Monitor](../../azure-monitor/index.yml) i dziennika aktywności [platformy Azure.](../../azure-monitor/essentials/platform-logs-overview.md)
- Przejrzyj [przykładowy skoroszyt Dzienniki aktywności według](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) domeny, aby dowiedzieć się, jak wyświetlać dzienniki aktywności platformy Azure w subskrypcjach z opcją filtrowania ich według nazwy domeny.
