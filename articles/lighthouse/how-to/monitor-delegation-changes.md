---
title: Monitorowanie zmian delegowania w dzierżawie zarządzającej
description: Dowiedz się, jak monitorować działania delegowania od dzierżawców klientów do dzierżawy zarządzającej.
ms.date: 09/08/2020
ms.topic: how-to
ms.openlocfilehash: 15e96939d4115bd93260687f637143cc798a4331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336602"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorowanie zmian delegowania w dzierżawie zarządzającej

Jako usługodawcę możesz mieć świadomość, że subskrypcje klienta lub grupy zasobów są delegowane do dzierżawy za pomocą [usługi Azure Lighthouse](../overview.md)lub gdy wcześniej delegowane zasoby zostaną usunięte.

W dzierżawie zarządzającej [Dziennik aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) śledzi działania delegowania na poziomie dzierżawy. To zarejestrowane działanie obejmuje wszystkie dodane lub usunięte delegowania ze wszystkich dzierżawców klientów.

W tym temacie objaśniono uprawnienia potrzebne do monitorowania działania delegowania dla dzierżawy (dla wszystkich klientów) oraz najlepszych rozwiązań związanych z tym rozwiązaniem. Zawiera również przykładowy skrypt, który pokazuje jedną metodę wykonywania zapytań i raportowania na tych danych.

> [!IMPORTANT]
> Wszystkie te kroki muszą być wykonywane w dzierżawie zarządzającej, a nie w żadnym dzierżawie klienta.

## <a name="enable-access-to-tenant-level-data"></a>Włącz dostęp do danych na poziomie dzierżawy

Aby uzyskać dostęp do danych dziennika aktywności na poziomie dzierżawy, konto musi mieć przypisaną wbudowaną rolę [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure w zakresie głównym (/). To przypisanie musi być wykonywane przez użytkownika, który ma rolę administratora globalnego z dodatkowym podwyższonym dostępem.

### <a name="elevate-access-for-a-global-administrator-account"></a>Podnieś poziom dostępu do konta administratora globalnego

Aby przypisać rolę w zakresie głównym (/), należy mieć rolę administratora globalnego z podwyższonym poziomem uprawnień. Ten podwyższony poziom dostępu powinien zostać dodany tylko wtedy, gdy musisz wykonać przypisanie roli, a następnie usunąć je po zakończeniu.

Aby uzyskać szczegółowe instrukcje dotyczące dodawania i usuwania podniesienia uprawnień, zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../../role-based-access-control/elevate-access-global-admin.md).

Po podwyższeniu poziomu dostępu Twoje konto będzie miało rolę administratora dostępu użytkownika na platformie Azure w zakresie głównym. To przypisanie roli umożliwia wyświetlanie wszystkich zasobów i przypisywanie dostępu w ramach dowolnej subskrypcji lub grupy zarządzania w katalogu, a także tworzenie przypisań ról w zakresie głównym.

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Utwórz nowe konto głównej usługi, aby uzyskać dostęp do danych na poziomie dzierżawy

Po podwyższeniu poziomu dostępu można przypisać odpowiednie uprawnienia do konta, aby można było wykonywać zapytania dotyczące danych dziennika aktywności na poziomie dzierżawy. To konto musi mieć przypisaną wbudowaną rolę [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) w głównym zakresie dzierżawy zarządzającej.

> [!IMPORTANT]
> Przyznanie przypisania roli w zakresie głównym oznacza, że te same uprawnienia będą stosowane do każdego zasobu w dzierżawie.

Ponieważ jest to szeroki poziom dostępu, zalecamy przypisanie tej roli do konta głównego usługi, a nie do pojedynczego użytkownika lub grupy.

 Ponadto zalecamy stosowanie następujących najlepszych rozwiązań:

- [Utwórz nowe konto nazwy głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md) , które ma być używane tylko dla tej funkcji, zamiast przypisywać tę rolę do istniejącej jednostki usługi używanej do innej automatyzacji.
- Upewnij się, że ta jednostka usługi nie ma dostępu do żadnych delegowanych zasobów klienta.
- [Użyj certyfikatu do uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) i [bezpiecznego przechowywania w Azure Key Vault](../../key-vault/general/best-practices.md).
- Ogranicz użytkowników, którzy mają dostęp do działania w imieniu jednostki usługi.

> [!NOTE]
> Możesz również przypisać wbudowaną rolę czytnika monitorowania platformy Azure w zakresie głównym do poszczególnych użytkowników lub grup użytkowników. Może to być przydatne, jeśli użytkownik ma mieć możliwość [wyświetlania informacji o delegowaniu bezpośrednio w Azure Portal](#view-delegation-changes-in-the-azure-portal). W takim przypadku należy pamiętać, że jest to szeroki poziom dostępu, który powinien być ograniczony do możliwie najmniejszej liczby użytkowników.

Użyj jednej z następujących metod, aby utworzyć przypisania zakresu głównego.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Usuń podwyższony poziom dostępu dla konta administratora globalnego

Po utworzeniu konta nazwy głównej usługi i przypisaniu roli czytnika monitorowania w zakresie głównym należy [usunąć podwyższony poziom dostępu](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) do konta administratora globalnego, ponieważ nie będzie już potrzebne.

## <a name="query-the-activity-log"></a>Wykonywanie zapytań względem dziennika aktywności

Po utworzeniu nowego konta nazwy głównej usługi z dostępem do programu z możliwością monitorowania do zakresu głównego dzierżawy zarządzającej możesz użyć go do zapytania i raportu o działaniach delegowania w dzierżawie.

[Ten skrypt Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) może służyć do wykonywania zapytań dotyczących ostatnich 1 dnia działania i raportów dotyczących dodanych lub usuniętych delegacji (lub nieudanych prób). Wysyła zapytanie do danych [dziennika aktywności dzierżawców](/rest/api/monitor/TenantActivityLogs/List) , a następnie konstruuje następujące wartości w celu raportowania delegowania, które są dodawane lub usuwane:

- **DelegatedResourceId**: Identyfikator delegowanej subskrypcji lub grupy zasobów
- **CustomerTenantId**: Identyfikator dzierżawy klienta
- **CustomerSubscriptionId**: delegowany Identyfikator subskrypcji lub zawierający delegowaną grupę zasobów
- **CustomerDelegationStatus**: zmiana stanu delegowanego zasobu (powodzenie lub niepowodzenie)
- **EventTimeStamp**: Data i godzina zarejestrowania zmiany delegowania

Podczas wykonywania zapytania o te dane należy pamiętać o następujących kwestiach:

- W przypadku delegowania wielu grup zasobów w jednym wdrożeniu dla każdej grupy zasobów zostaną zwrócone oddzielne wpisy.
- Zmiany wprowadzone do poprzedniego delegowania (takie jak aktualizacja struktury uprawnień) będą rejestrowane jako dodane delegowanie.
- Jak wspomniano powyżej, konto musi mieć wbudowaną rolę czytnika monitorowania Azure w zakresie głównym (/), aby uzyskać dostęp do tych danych na poziomie dzierżawy.
- Te dane mogą być używane we własnych przepływach pracy i w raportach. Na przykład możesz użyć [interfejsu API modułu zbierającego dane http (publiczna wersja zapoznawcza)](../../azure-monitor/platform/data-collector-api.md) do rejestrowania danych do Azure monitor z klienta interfejsu API REST, a następnie użyć [grup akcji](../../azure-monitor/platform/action-groups.md) do tworzenia powiadomień lub alertów.

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

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tych samych procesów.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Wyświetl zmiany delegowania w Azure Portal

Użytkownicy, którym przypisano wbudowaną rolę czytelnik monitorowania platformy Azure w zakresie głównym, mogą przeglądać zmiany delegowania bezpośrednio w Azure Portal.

1. Przejdź do strony **moi klienci** , a następnie wybierz pozycję **Dziennik aktywności** z menu nawigacji po lewej stronie.
1. Upewnij się, że w filtrze w górnej części ekranu zaznaczono **aktywność katalogu** .

Zostanie wyświetlona lista zmian delegowania. Możesz wybrać opcję **Edytuj kolumny** , aby pokazać lub ukryć **stan**, **kategorię zdarzenia**, **godzinę**, **sygnaturę czasową**, **subskrypcję**, **zdarzenie zainicjowane przez**, **grupę zasobów**, **Typ zasobu**i wartości **zasobów** .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak dołączyć klientów do [usługi Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Dowiedz się więcej o [Azure monitor](../../azure-monitor/index.yml) i [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md).
