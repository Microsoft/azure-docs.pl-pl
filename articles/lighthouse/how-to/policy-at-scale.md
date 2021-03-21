---
title: Wdrażanie Azure Policy do delegowanych subskrypcji na dużą skalę
description: Dowiedz się, jak usługa Azure Lighthouse umożliwia wdrożenie definicji zasad i przypisania zasad dla wielu dzierżawców.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 48354c3cca7574b1d5acf71865218564591bc23e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049784"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Wdrażanie Azure Policy do delegowanych subskrypcji na dużą skalę

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do [usługi Azure Lighthouse](../overview.md). Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawcach, co sprawia, że zadania zarządzania są bardziej wydajne.

W tym temacie przedstawiono sposób użycia [Azure Policy](../../governance/policy/index.yml) do wdrożenia definicji zasad i przypisania zasad dla wielu dzierżawców przy użyciu poleceń programu PowerShell. W tym przykładzie definicja zasad gwarantuje, że konta magazynu są zabezpieczone przez umożliwienie tylko ruchu HTTPS.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tych samych procesów.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Korzystanie z grafu zasobów platformy Azure do wykonywania zapytań w dzierżawach klientów

Korzystając z [grafu zasobów platformy Azure](../../governance/resource-graph/index.yml) , można wykonywać zapytania dotyczące wszystkich subskrypcji w dzierżawach klientów, którymi zarządzasz. W tym przykładzie określimy wszystkie konta magazynu w tych subskrypcjach, które nie wymagają obecnie ruchu HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Wdrażanie zasad dla wielu dzierżawców klientów

W poniższym przykładzie pokazano, jak użyć [szablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) do wdrożenia definicji zasad i przypisania zasad w ramach delegowanych subskrypcji w wielu dzierżawach klientów. Ta definicja zasad wymaga, aby wszystkie konta magazynu używały ruchu HTTPS, uniemożliwiając tworzenie nowych kont magazynu, które nie spełniają wymagań i oznacza istniejące konta magazynu bez ustawienia jako niezgodne.

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

> [!NOTE]
> Chociaż można wdrażać zasady dla wielu dzierżawców, obecnie nie można [wyświetlić szczegółów zgodności](../../governance/policy/how-to/determine-non-compliance.md#compliance-details) dla niezgodnych zasobów w tych dzierżawcach.

## <a name="validate-the-policy-deployment"></a>Weryfikowanie wdrożenia zasad

Po wdrożeniu szablonu Azure Resource Manager można potwierdzić, że definicja zasad została pomyślnie zastosowana, próbując utworzyć konto magazynu z **EnableHttpsTrafficOnly** ustawionym na **wartość false** w jednej z delegowanych subskrypcji. W związku z przypisaniem zasad nie powinno być możliwe utworzenie tego konta magazynu.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

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

- Dowiedz się więcej na temat [Azure Policy](../../governance/policy/index.yml).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- Dowiedz się [, jak wdrożyć zasady, które można skorygować](deploy-policy-remediation.md) w ramach delegowanej subskrypcji.
