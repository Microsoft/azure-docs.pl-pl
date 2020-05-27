---
title: Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak przełączyć identyfikator dzierżawy magazynu kluczy po przeniesieniu subskrypcji do innej dzierżawy
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 9e35f5c9288860056a910f54f9601b2178a628bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828087"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Po utworzeniu nowego magazynu kluczy w subskrypcji zostaje on automatycznie powiązany z domyślnym identyfikatorem dzierżawy usługi Azure Active Directory dla tej subskrypcji. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. 

Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy są niedostępne dla podmiotów zabezpieczeń (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji na dzierżawcę B.
* usunąć wszystkie istniejące wpisy zasad dostępu,
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawcą B.

Jeśli na przykład masz Magazyn kluczy "Moja magazyn" w subskrypcji, która została przeniesiona z dzierżawy A do dzierżawy B, możesz użyć Azure PowerShell do zmiany identyfikatora dzierżawy i usunięcia starych zasad dostępu.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Można też użyć interfejsu wiersza polecenia platformy Azure.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Po skojarzeniu magazynu z prawidłowym IDENTYFIKATORem dzierżawy i usunięciu starych wpisów zasad dostępu Ustaw nowe wpisy zasad dostępu za pomocą polecenia cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) lub interfejsu wiersza polecenia platformy Azure [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Jeśli używasz zarządzanej tożsamości dla zasobów platformy Azure, musisz zaktualizować ją również do nowej dzierżawy usługi Azure AD. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zapewnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej](managed-identity.md).


Jeśli używasz pliku MSI, musisz również zaktualizować tożsamość MSI, ponieważ stara tożsamość nie będzie już w poprawnej dzierżawie usługi AAD.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące Azure Key Vault, odwiedź [stronę pytań&Microsoft pytań i odpowiedzi na Azure Key Vault](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
