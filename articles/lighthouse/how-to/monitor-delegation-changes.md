---
title: Monitorowanie zmian delegowania w dzierżawie zarządzającej
description: Dowiedz się, jak monitorować aktywność delegowania od dzierżawców klientów do dzierżawy zarządzającej.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a4593b34311eca34e4fb68926a3820899ab3f324
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458815"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorowanie zmian delegowania w dzierżawie zarządzającej

Jako dostawca usług możesz mieć świadomość, kiedy subskrypcje klientów lub grupy zasobów są delegowane do dzierżawy za pośrednictwem [usługi Azure zarządzania zasobami delegowanymi](../concepts/azure-delegated-resource-management.md)lub gdy wcześniej delegowane zasoby są usuwane.

W dzierżawie zarządzania [dziennik działania platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) śledzi działania delegowania na poziomie dzierżawy. To zarejestrowane działanie obejmuje wszelkie dodane lub usunięte delegacje ze wszystkich dzierżawców klientów.

W tym temacie wyjaśniono uprawnienia potrzebne do monitorowania aktywności delegowania do dzierżawy (we wszystkich klientach) i najlepsze rozwiązania w tym zakresie. Zawiera również przykładowy skrypt, który pokazuje jedną metodę wykonywania zapytań i raportowania tych danych.

> [!IMPORTANT]
> Wszystkie te kroki muszą być wykonywane w dzierżawie zarządzającej, a nie w dzierżawie klientów.

## <a name="enable-access-to-tenant-level-data"></a>Włączanie dostępu do danych na poziomie dzierżawy

Aby uzyskać dostęp do danych dziennika aktywności na poziomie dzierżawy, konto musi być przypisane do wbudowanej roli [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) w zakresie głównym (/). To przypisanie musi być wykonywane przez użytkownika, który ma rolę administratora globalnego z dodatkowym podwyższonym poziomem dostępu.

### <a name="elevate-access-for-a-global-administrator-account"></a>Podniesienie poziomu dostępu dla konta administratora globalnego

Aby przypisać rolę w zakresie głównym (/), musisz mieć rolę administratora globalnego z podwyższonym poziomem dostępu. Ten podwyższony poziom dostępu powinien być dodawany tylko wtedy, gdy trzeba dokonać przypisania roli, a następnie usunąć po zakończeniu.

Aby uzyskać szczegółowe instrukcje dotyczące dodawania i usuwania elewacji, zobacz [Zwiększanie poziomu dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania.](../../role-based-access-control/elevate-access-global-admin.md)

Po podniesieniu poziomu dostępu twoje konto będzie miało rolę administratora dostępu użytkownika na platformie Azure w zakresie głównym. To przypisanie roli umożliwia wyświetlanie wszystkich zasobów i przypisywanie dostępu w dowolnej subskrypcji lub grupie zarządzania w katalogu, a także przypisywanie ról w zakresie głównym. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Tworzenie nowego konta jednostkowego usługi w celu uzyskania dostępu do danych na poziomie dzierżawy

Po podwyższeniu poziomu dostępu można przypisać odpowiednie uprawnienia do konta, aby mogło wysyłać zapytania do danych dziennika aktywności na poziomie dzierżawy. To konto musi mieć wbudowaną rolę [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) przypisaną w zakresie głównym dzierżawy zarządzającej.

> [!IMPORTANT]
> Przyznanie przypisania roli w zakresie głównym oznacza, że te same uprawnienia będą miały zastosowanie do każdego zasobu w dzierżawie.

Ponieważ jest to szeroki poziom dostępu, zaleca się przypisanie tej roli do konta jednostki usługi, a nie do pojedynczego użytkownika lub grupy. Ponadto zaleca się następujące najlepsze rozwiązania:

- [Utwórz nowe konto głównej usługi,](../../active-directory/develop/howto-create-service-principal-portal.md) które ma być używane tylko dla tej funkcji, zamiast przypisywać tę rolę do istniejącej jednostki usługi używanej do innych automatyzacji.
- Upewnij się, że ten podmiot zabezpieczeń usługi nie ma dostępu do żadnych delegowanych zasobów klienta.
- [Użyj certyfikatu, aby uwierzytelnić](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) go i [bezpiecznie przechowywać w usłudze Azure Key Vault](../../key-vault/general/best-practices.md).
- Ogranicz użytkowników, którzy mają dostęp do działania w imieniu jednostki usługi.

Użyj jednej z następujących metod, aby dokonać przypisania zakresu głównego.

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Usuwanie podwyższonego poziomu dostępu dla konta administratora globalnego

Po utworzeniu konta jednostki usługi i przypisaniu roli czytnik monitorowania w zakresie głównym należy [usunąć podwyższony dostęp](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) do konta administratora globalnego, ponieważ ten poziom dostępu nie będzie już potrzebny.

## <a name="query-the-activity-log"></a>Kwerenda w dzienniku aktywności

Po utworzeniu nowego konta jednostkowego usługi z dostępem czytnika monitorowania do głównego zakresu dzierżawy zarządzającej można go używać do wykonywania zapytań i raportowania aktywności delegowania w dzierżawie. 

[Ten skrypt programu Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) może służyć do kwerendy ostatnich 1 dzień działania i raporty na wszelkie dodane lub usunięte delegacje (lub próby, które nie powiodły się). Odpytywany [dane dziennika działania dzierżawy,](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) a następnie konstruuje następujące wartości do raportu na delegacje, które są dodawane lub usuwane:

- **Identyfikator delegowanego źródła:** Identyfikator delegowanej subskrypcji lub grupy zasobów
- **CustomerTenantId**: Identyfikator dzierżawy klienta
- **CustomerSubscriptionId**: Identyfikator subskrypcji, który został delegowany lub który zawiera grupę zasobów, która została delegowana
- **CustomerDelegationStatus**: Zmiana stanu zasobu delegowanego (powiodła się lub nie powiodła się)
- **EventTimeStamp**: data i godzina zarejestrowania zmiany delegacji

Podczas wykonywania zapytań o te dane należy pamiętać:

- Jeśli w jednym wdrożeniu zostanie delegowanych wiele grup zasobów, dla każdej grupy zasobów zostaną zwrócone oddzielne wpisy.
- Zmiany wprowadzone w poprzedniej delegacji (takie jak aktualizowanie struktury uprawnień) będą rejestrowane jako dodane delegowanie.
- Jak wspomniano powyżej, konto musi mieć funkcję wbudowaną czytnika monitorowania w zakresie głównym (/), aby uzyskać dostęp do tych danych na poziomie dzierżawy.
- Można użyć tych danych w własnych przepływów pracy i raportowania. Na przykład można użyć [interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](../../azure-monitor/platform/data-collector-api.md) do rejestrowania danych w usłudze Azure Monitor z klienta interfejsu API REST, a następnie do tworzenia powiadomień lub alertów za pomocą [grup akcji.](../../azure-monitor/platform/action-groups.md)

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak dołączać klientów do [zarządzania zasobami delegowanymi platformy Azure.](../concepts/azure-delegated-resource-management.md)
- Dowiedz się więcej o [usłudze Azure Monitor](../../azure-monitor/index.yml) i [dzienniku aktywności platformy Azure.](../../azure-monitor/platform/platform-logs-overview.md)
