---
title: Zmienianie identyfikatora dzierżawy magazynu kluczy po przejściu subskrypcji — Usługa Azure Key Vault | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 94bcba80e5768d57e3dc97bed1a74a8369ac60b9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422893"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Po utworzeniu nowego magazynu kluczy w subskrypcji zostaje on automatycznie powiązany z domyślnym identyfikatorem dzierżawy usługi Azure Active Directory dla tej subskrypcji. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. 

Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy będą niedostępne przez podmioty zabezpieczeń (użytkowników i aplikacje) w dzierżawie B. Aby rozwiązać ten problem, musisz:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji do dzierżawy B.
* usunąć wszystkie istniejące wpisy zasad dostępu,
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawą B.

Na przykład jeśli masz magazyn kluczy "myvault" w subskrypcji, która została przeniesiona z dzierżawy A do dzierżawy B, można użyć programu Azure PowerShell, aby zmienić identyfikator dzierżawy i usunąć stare zasady dostępu.

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

Teraz, gdy magazyn jest skojarzony z poprawnym identyfikatorem dzierżawy i stare wpisy zasad dostępu są usuwane, ustaw nowe wpisy zasad dostępu za pomocą polecenia cmdlet zasad azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) lub polecenia [azure cli az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Jeśli używasz tożsamości zarządzanej dla zasobów platformy Azure, należy zaktualizować go do nowej dzierżawy usługi Azure AD, jak również. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Zapewnianie uwierzytelniania magazynu kluczy z tożsamością zarządzaną](managed-identity.md).


Jeśli używasz MSI, musisz również zaktualizować tożsamość MSI, ponieważ stara tożsamość nie będzie już w odpowiedniej dzierżawie usługi AAD.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące usługi Azure Key Vault, odwiedź [forum usługi Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
