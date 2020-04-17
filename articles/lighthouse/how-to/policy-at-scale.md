---
title: Wdrażanie zasad platformy Azure w przypadku delegowanych subskrypcji na dużą skalę
description: Dowiedz się, jak zarządzanie zasobami delegowanymi platformy Azure umożliwia wdrażanie definicji zasad i przypisywania zasad w wielu dzierżawach.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481823"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Wdrażanie zasad platformy Azure w przypadku delegowanych subskrypcji na dużą skalę

Jako dostawca usług może mieć wbudowane wielu dzierżawców klientów do zarządzania zasobami delegowanymi platformy Azure. [Latarnia morska platformy Azure](../overview.md) umożliwia dostawcom usług wykonywanie operacji na dużą skalę w kilku dzierżawach jednocześnie, dzięki czemu zadania zarządzania są bardziej wydajne.

W tym temacie pokazano, jak używać [zasad platformy Azure](../../governance/policy/index.yml) do wdrażania definicji zasad i przypisywania zasad w wielu dzierżawach przy użyciu poleceń programu PowerShell. W tym przykładzie definicja zasad zapewnia, że konta magazynu są zabezpieczone, zezwalając tylko na ruch HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Używanie programu Azure Resource Graph do wykonywania zapytań między dzierżawcami klientów

Za pomocą [usługi Azure Resource Graph](../../governance/resource-graph/index.yml) można przeszukiwać wszystkie subskrypcje w dzierżawach klientów, którymi zarządzasz. W tym przykładzie zidentyfikujemy wszystkie konta magazynu w tych subskrypcjach, które obecnie nie wymagają ruchu HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Wdrażanie zasad w wielu dzierżawach klientów

W poniższym przykładzie pokazano, jak używać [szablonu usługi Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) do wdrażania definicji zasad i przypisania zasad w delegowanych subskrypcjach w wielu dzierżawach klientów. Ta definicja zasad wymaga, aby wszystkie konta magazynu używały ruchu HTTPS, uniemożliwiając tworzenie nowych kont magazynu, które nie są zgodne, i oznaczanie istniejących kont magazynu bez ustawienia jako niezgodnego.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Sprawdzanie poprawności wdrażania zasad

Po wdrożeniu szablonu usługi Azure Resource Manager można potwierdzić, że definicja zasad została pomyślnie zastosowana, próbując utworzyć konto magazynu z **włączhttpsTrafficOnly ustawiona** na **false** w jednej z delegowanych subskrypcji. Ze względu na przypisanie zasad nie można utworzyć tego konta magazynu.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu usuń definicję zasad i przypisanie utworzone przez wdrożenie.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zasadach platformy Azure](../../governance/policy/index.yml).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
