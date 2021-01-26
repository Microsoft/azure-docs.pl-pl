---
title: Azure Key Vault przenieść magazyn do innej subskrypcji | Microsoft Docs
description: Wskazówki dotyczące przeniesienia magazynu kluczy do innej subskrypcji.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 23be8e667d435c2d91d32ebeac30b1e96b45a77e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790295"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Przenoszenie magazynu kluczy platformy Azure do innej subskrypcji

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> **Przeniesienie magazynu kluczy do innej subskrypcji spowoduje nieprzerwaną zmianę w Twoim środowisku.**
> Upewnij się, że rozumiesz wpływ tej zmiany i postępuj zgodnie ze wskazówkami w tym artykule, uważnie, zanim zdecydujesz się przenieść magazyn kluczy do nowej subskrypcji.
> Jeśli używasz tożsamości usługi zarządzanej (MSI), przeczytaj instrukcje po przeniesieniu na końcu dokumentu. 

[Azure Key Vault](overview.md) jest automatycznie wiązana z domyślnym identyfikatorem dzierżawy [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) dla subskrypcji, w której został utworzony. Identyfikator dzierżawy skojarzony z subskrypcją można znaleźć, wykonując czynności opisane w tym [przewodniku](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md). Wszystkie wpisy zasad dostępu i przypisane role są również powiązane z tym IDENTYFIKATORem dzierżawy.  Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy będą niedostępne dla podmiotów usługi (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji na dzierżawcę B.
* usunąć wszystkie istniejące wpisy zasad dostępu,
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawcą B.

Aby uzyskać więcej informacji na temat Azure Key Vault i Azure Active Directory, zobacz
- [Informacje o usłudze Azure Key Vault](overview.md)
- [Co to jest Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Jak znaleźć identyfikator dzierżawy](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> **Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione** Jeśli używasz magazynu kluczy z szyfrowaniem dysków dla maszyny wirtualnej, nie można przenieść magazynu kluczy do innej grupy zasobów lub subskrypcji, gdy jest włączone szyfrowanie dysku. Przed przeniesieniem magazynu kluczy do nowej grupy zasobów lub subskrypcji należy wyłączyć szyfrowanie dysku. 

Niektóre jednostki usługi (Użytkownicy i aplikacje) są powiązane z określoną dzierżawą. W przypadku przeniesienia magazynu kluczy do subskrypcji w innej dzierżawie istnieje prawdopodobieństwo, że nie będzie można przywrócić dostępu do określonej jednostki usługi. Upewnij się, że wszystkie podstawowe jednostki usługi istnieją w dzierżawie, w której chcesz przenieść magazyn kluczy.

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp na poziomie [współautora](../../role-based-access-control/built-in-roles.md#contributor) lub wyższy do bieżącej subskrypcji, w której znajduje się magazyn kluczy. Rolę można przypisywać przy użyciu [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub [programu PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Dostęp na poziomie [współautor](../../role-based-access-control/built-in-roles.md#contributor) lub wyższy do subskrypcji, w której chcesz przenieść magazyn kluczy. Rolę można przypisywać przy użyciu [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub [programu PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Grupa zasobów w nowej subskrypcji. Można go utworzyć przy użyciu [Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), [programu PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/management/manage-resource-groups-cli.md).

Istniejące role można sprawdzać za pomocą [Azure Portal](../../role-based-access-control/role-assignments-list-portal.md), [programu PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), interfejsu [wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-list-cli.md)lub [API REST](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Przeniesienie magazynu kluczy do nowej subskrypcji

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Przejdź do [magazynu kluczy](overview.md)
3. Kliknij kartę "przegląd"
4. Wybierz przycisk "Przenieś"
5. Wybierz pozycję "Przenieś do innej subskrypcji" z opcji listy rozwijanej
6. Wybierz grupę zasobów, w której chcesz przenieść magazyn kluczy
7. Potwierdzenie ostrzeżenia dotyczącego przeniesienia zasobów
8. Wybierz pozycję „OK”

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Dodatkowe kroki, które należy wykonać, gdy subskrypcja jest w nowej dzierżawie

Jeśli magazyn kluczy został przeniesiony do subskrypcji w nowej dzierżawie, musisz ręcznie zaktualizować identyfikator dzierżawy i usunąć stare zasady dostępu oraz przypisania ról. Poniżej przedstawiono samouczki dotyczące tych kroków w programie PowerShell i interfejsie wiersza polecenia platformy Azure. Jeśli używasz programu PowerShell, może być konieczne uruchomienie polecenia Clear-AzContext opisanego poniżej, aby umożliwić wyświetlanie zasobów poza bieżącym wybranym zakresem. 

### <a name="update-tenant-id-in-a-key-vault"></a>Aktualizowanie identyfikatora dzierżawy w magazynie kluczy

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
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
### <a name="update-access-policies-and-role-assignments"></a>Aktualizowanie zasad dostępu i przypisań ról

> [!NOTE]
> Jeśli Key Vault korzysta z modelu uprawnień [RBAC platformy Azure](../../role-based-access-control/overview.md) . Należy również usunąć przypisania roli magazynu kluczy. Przypisania ról można usunąć za pomocą witryny [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub [programu PowerShell](../../role-based-access-control/role-assignments-powershell.md). 

Teraz, gdy magazyn jest skojarzony z prawidłowym IDENTYFIKATORem dzierżawy i zostaną usunięte stare wpisy zasad dostępu lub przypisania ról, Ustaw nowe wpisy zasad dostępu lub przypisania ról.

Aby przypisywać zasady, zobacz:
- [Przypisywanie zasad dostępu przy użyciu portalu](assign-access-policy-portal.md)
- [Przypisywanie zasad dostępu przy użyciu interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md)
- [Przypisywanie zasad dostępu przy użyciu programu PowerShell](assign-access-policy-powershell.md)

Aby dodać przypisania ról, zobacz:
- [Dodawanie przypisania roli przy użyciu portalu](../../role-based-access-control/role-assignments-portal.md)
- [Dodawanie przypisania roli przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
- [Dodawanie przypisania roli przy użyciu programu PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Aktualizowanie tożsamości zarządzanych

Jeśli przesyłasz całą subskrypcję i używasz zarządzanej tożsamości dla zasobów platformy Azure, musisz zaktualizować ją do nowej dzierżawy Azure Active Directory. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, [Omówienie tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md).

Jeśli używasz tożsamości zarządzanej, musisz również zaktualizować tożsamość, ponieważ stara tożsamość nie będzie już poprawna Azure Active Directory dzierżawy. Zobacz następujące dokumenty, aby pomóc w rozwiązaniu tego problemu. 

* [Aktualizowanie pliku MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Prześlij subskrypcję do nowego katalogu](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md)
- Aby uzyskać informacje koncepcyjne, w tym sposób interpretacji dzienników Key Vault, zobacz [rejestrowanie Key Vault](logging.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
- [Zabezpieczanie magazynu kluczy](secure-your-key-vault.md)
- [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](network-security.md)