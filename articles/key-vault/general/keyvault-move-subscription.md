---
title: Azure Key Vault przenieść magazyn do innej subskrypcji | Microsoft Docs
description: Wskazówki dotyczące przeniesienia magazynu kluczy do innej subskrypcji.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 6fe25efe8f38772162c916c8d0b965267d457c0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488821"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Przeniesienie Azure Key Vault do innej subskrypcji

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

**Przeniesienie magazynu kluczy do innej subskrypcji spowoduje nieprzerwaną zmianę w Twoim środowisku.**

Upewnij się, że rozumiesz wpływ tej zmiany i postępuj zgodnie ze wskazówkami w tym artykule, uważnie, zanim zdecydujesz się przenieść magazyn kluczy do nowej subskrypcji.

Podczas tworzenia magazynu kluczy jest on automatycznie powiązany z domyślnym IDENTYFIKATORem dzierżawy Azure Active Directory dla subskrypcji, w której został utworzony. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy będą niedostępne dla podmiotów usługi (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji na dzierżawcę B.
* usunąć wszystkie istniejące wpisy zasad dostępu,
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawcą B.

## <a name="limitations"></a>Ograniczenia

Niektóre jednostki usługi (Użytkownicy i aplikacje) są powiązane z określoną dzierżawą. W przypadku przeniesienia magazynu kluczy do subskrypcji w innej dzierżawie istnieje prawdopodobieństwo, że nie będzie można przywrócić dostępu do określonej jednostki usługi. Upewnij się, że wszystkie podstawowe jednostki usługi istnieją w dzierżawie, w której chcesz przenieść magazyn kluczy.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Twoja organizacja może mieć zaimplementowany Azure Policy z wymuszeniem lub wykluczeniem na poziomie subskrypcji. W subskrypcji, w której znajduje się magazyn kluczy, może istnieć inny zestaw przypisań zasad i subskrypcja, w której jest już używany Magazyn kluczy. W przypadku konfliktu w wymaganiach dotyczących zasad istnieje możliwość przerwania aplikacji.

### <a name="example"></a>Przykład

Aplikacja jest połączona z magazynem kluczy, który tworzy certyfikaty, które są ważne przez dwa lata. Subskrypcja, w której próbujesz przenieść magazyn kluczy, ma przypisanie zasad, które blokuje tworzenie certyfikatów, które są ważne przez okres dłuższy niż jeden rok. Po przeniesieniu magazynu kluczy do nowej subskrypcji operacja utworzenia certyfikatu ważnego przez dwa lata zostanie zablokowana przez przypisanie zasady platformy Azure.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że przejdź do strony Azure Policy na Azure Portal i przyjrzyj się przypisań zasad dla bieżącej subskrypcji, a także subskrypcji, w której przejdziesz, i upewnij się, że nie ma żadnych niezgodności.

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp na poziomie współautora lub wyższy do bieżącej subskrypcji, w której znajduje się magazyn kluczy.
* Dostęp na poziomie współautor lub wyższy do subskrypcji, w której chcesz przenieść magazyn kluczy.
* Grupa zasobów w nowej subskrypcji.

## <a name="procedure"></a>Procedura

### <a name="initial-steps-moving-key-vault"></a>Początkowe kroki (trwa przechodzenie Key Vault)

1. Logowanie do witryny Azure Portal
2. Przejdź do magazynu kluczy
3. Kliknij kartę "przegląd"
4. Wybierz przycisk "Przenieś"
5. Wybierz pozycję "Przenieś do innej subskrypcji" z opcji listy rozwijanej
6. Wybierz grupę zasobów, w której chcesz przenieść magazyn kluczy
7. Potwierdzenie ostrzeżenia dotyczącego przeniesienia zasobów
8. Wybierz pozycję "OK"

### <a name="additional-steps-post-move"></a>Dodatkowe kroki (po przeniesieniu)

Po przeniesieniu magazynu kluczy do nowej subskrypcji należy zaktualizować identyfikator dzierżawy i usunąć stare zasady dostępu. Poniżej przedstawiono samouczki dotyczące tych kroków w programie PowerShell i interfejsie wiersza polecenia platformy Azure.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Po skojarzeniu magazynu z prawidłowym IDENTYFIKATORem dzierżawy i usunięciu starych wpisów zasad dostępu Ustaw nowe wpisy zasad dostępu za pomocą polecenia cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) lub interfejsu wiersza polecenia platformy Azure [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Jeśli używasz zarządzanej tożsamości dla zasobów platformy Azure, musisz zaktualizować ją również do nowej dzierżawy usługi Azure AD. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zapewnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej](managed-identity.md).

Jeśli używasz pliku MSI, musisz również zaktualizować tożsamość MSI, ponieważ stara tożsamość nie będzie już w poprawnej dzierżawie usługi AAD.


