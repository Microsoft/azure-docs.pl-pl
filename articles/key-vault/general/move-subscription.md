---
title: Azure Key Vault magazynu do innej subskrypcji | Microsoft Docs
description: Wskazówki dotyczące przenoszenia magazynu kluczy do innej subskrypcji.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817634"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Przenoszenie magazynu kluczy platformy Azure do innej subskrypcji

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> **Przeniesienie magazynu kluczy do innej subskrypcji spowoduje przełomową zmianę w środowisku.**
> Przed podjęciem decyzji o przeniesienia magazynu kluczy do nowej subskrypcji upewnij się, że rozumiesz wpływ tej zmiany i dokładnie postępuj zgodnie ze wskazówkami w tym artykule.
> Jeśli używasz tożsamości usługi zarządzanej (MSI), zapoznaj się z instrukcjami po przenoszeniu na końcu dokumentu. 

[Azure Key Vault](overview.md) jest automatycznie powiązany z domyślnym [identyfikatorem Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) dzierżawy dla subskrypcji, w której została utworzona. Identyfikator dzierżawy skojarzony z subskrypcją można znaleźć, korzystając z tego [przewodnika.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) Wszystkie wpisy zasad dostępu i przypisania ról są również powiązane z tym identyfikatorem dzierżawy.  Jeśli przeniesiesz subskrypcję platformy Azure z dzierżawy A do dzierżawy B, istniejące magazyny kluczy będą niedostępne dla podmiotów usługi (użytkowników i aplikacji) w dzierżawie B. Aby rozwiązać ten problem, należy:

* Zmień identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji na dzierżawę B.
* usunąć wszystkie istniejące wpisy zasad dostępu,
* Dodaj nowe wpisy zasad dostępu skojarzone z dzierżawą B.

Aby uzyskać więcej informacji na Azure Key Vault i Azure Active Directory, zobacz
- [Informacje o usłudze Azure Key Vault](overview.md)
- [Co to jest usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Jak znaleźć identyfikator dzierżawy](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> **Nie można przenieść magazynów kluczy używanych do szyfrowania dysków** Jeśli używasz magazynu kluczy z szyfrowaniem dysków dla maszyny wirtualnej, nie można przenieść magazynu kluczy do innej grupy zasobów ani subskrypcji, gdy szyfrowanie dysków jest włączone. Przed przeniesieniem magazynu kluczy do nowej grupy zasobów lub subskrypcji należy wyłączyć szyfrowanie dysków. 

Niektóre jednostki usługi (użytkownicy i aplikacje) są powiązane z określoną dzierżawą. Jeśli przeniesiesz magazyn kluczy do subskrypcji w innej dzierżawie, istnieje prawdopodobieństwo, że nie będzie można przywrócić dostępu do określonej jednostki usługi. Upewnij się, że w dzierżawie, w której przenosisz magazyn kluczy, istnieją wszystkie podstawowe jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dostęp](../../role-based-access-control/built-in-roles.md#contributor) na poziomie współautora lub wyższy do bieżącej subskrypcji, w której istnieje magazyn kluczy. Rolę można przypisać przy użyciu [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub programu [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* [Dostęp](../../role-based-access-control/built-in-roles.md#contributor) na poziomie współautora lub wyższy do subskrypcji, do której chcesz przenieść magazyn kluczy. Rolę można przypisać przy użyciu [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub programu [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* Grupa zasobów w nowej subskrypcji. Możesz go utworzyć przy użyciu [programu Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), [programu PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)lub [interfejsu wiersza polecenia platformy Azure.](../../azure-resource-manager/management/manage-resource-groups-cli.md)

Istniejące role można sprawdzić przy [użyciu](../../role-based-access-control/role-assignments-list-portal.md)Azure Portal , [programu PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md)interfejsu [wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-list-cli.md)lub [interfejsu API REST.](../../role-based-access-control/role-assignments-list-rest.md)


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Przenoszenie magazynu kluczy do nowej subskrypcji

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Przechodzenie do [magazynu kluczy](overview.md)
3. Kliknij kartę "Przegląd"
4. Wybierz przycisk "Przenieś"
5. Wybierz pozycję "Przenieś do innej subskrypcji" z opcji listy rozwijanej
6. Wybierz grupę zasobów, do której chcesz przenieść magazyn kluczy
7. Potwierdzenie ostrzeżenia dotyczące przenoszenia zasobów
8. Wybierz pozycję „OK”

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Dodatkowe kroki, gdy subskrypcja znajduje się w nowej dzierżawie

Jeśli magazyn kluczy został przeniesiony do subskrypcji w nowej dzierżawie, należy ręcznie zaktualizować identyfikator dzierżawy i usunąć stare zasady dostępu i przypisania ról. Poniżej znajdują się samouczki dotyczące tych kroków w programie PowerShell i interfejsie wiersza polecenia platformy Azure. Jeśli używasz programu PowerShell, może być konieczne uruchomienie polecenia Clear-AzContext udokumentowanego poniżej, aby umożliwić wyświetlanie zasobów spoza bieżącego wybranego zakresu. 

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
> Jeśli Key Vault model uprawnień RBAC platformy [Azure.](../../role-based-access-control/overview.md) Należy również usunąć przypisania ról magazynu kluczy. Przypisania ról można usunąć przy użyciu witryny [Azure Portal,](../../role-based-access-control/role-assignments-portal.md)interfejsu [wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)lub programu [PowerShell.](../../role-based-access-control/role-assignments-powershell.md) 

Po skojarzeniu magazynu z prawidłowym identyfikatorem dzierżawy i usunięciu starych wpisów zasad dostępu lub przypisań ról ustaw nowe wpisy zasad dostępu lub przypisania ról.

Aby uzyskać informacje na temat przypisywania zasad, zobacz:
- [Przypisywanie zasad dostępu przy użyciu portalu](assign-access-policy-portal.md)
- [Przypisywanie zasad dostępu przy użyciu interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md)
- [Przypisywanie zasad dostępu przy użyciu programu PowerShell](assign-access-policy-powershell.md)

Aby dodać przypisania ról, zobacz:
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
- [Przypisywanie ról platformy Azure przy użyciu programu PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Aktualizowanie tożsamości zarządzanych

Jeśli przenosisz całą subskrypcję i używasz tożsamości zarządzanej dla zasobów platformy Azure, musisz również zaktualizować ją do nowej Azure Active Directory dzierżawy. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, omówienie [tożsamości zarządzanych.](../../active-directory/managed-identities-azure-resources/overview.md)

Jeśli używasz tożsamości zarządzanej, musisz również zaktualizować tożsamość, ponieważ stara tożsamość nie będzie już w prawidłowej Azure Active Directory dzierżawy. Zapoznaj się z następującymi dokumentami, aby rozwiązać ten problem. 

* [Aktualizowanie msi](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Przenoszenie subskrypcji do nowego katalogu](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md)
- Aby uzyskać informacje koncepcyjne, w tym informacje o Key Vault dziennikach, [zobacz Key Vault rejestrowanie](logging.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
- [Azure Key Vault zabezpieczeń](security-features.md)
- [Konfigurowanie Azure Key Vault sieci wirtualnych i zapory sieci wirtualnych](network-security.md)