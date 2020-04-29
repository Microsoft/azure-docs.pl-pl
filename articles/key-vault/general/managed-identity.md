---
title: Dostęp do Azure Key Vault za pomocą zarządzanej tożsamości przypisanej do systemu
description: Dowiedz się, jak utworzyć zarządzaną tożsamość dla aplikacji App Service i jak używać jej do uzyskiwania dostępu do Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432127"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Zapewnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej

Zarządzana tożsamość z Azure Active Directory umożliwia aplikacji łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę Azure AD. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

W tym artykule opisano sposób tworzenia tożsamości zarządzanej dla aplikacji App Service i używania jej w celu uzyskania dostępu do Azure Key Vault. W przypadku aplikacji hostowanych na maszynach wirtualnych platformy Azure zobacz [Używanie tożsamości zarządzanej przypisanej przez system Windows VM do uzyskiwania dostępu do Azure Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten przewodnik, musisz dysponować następującymi zasobami. 

- Magazyn kluczy. Możesz użyć istniejącego magazynu kluczy lub utworzyć nowy, wykonując czynności opisane w jednym z następujących przewodników szybki start:
   - [Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md)
   - [Tworzenie magazynu kluczy za pomocą Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Utwórz magazyn kluczy z Azure Portal](../secrets/quick-create-portal.md).
- Istniejąca aplikacja App Service, do której ma zostać udzielony dostęp do magazynu kluczy. Można ją szybko utworzyć, wykonując czynności opisane w [dokumentacji App Service](../../app-service/overview.md).
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/overview). Alternatywnie możesz użyć [Azure Portal](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu 

Najpierw należy dodać tożsamość przypisaną do systemu do aplikacji. 
 
### <a name="azure-portal"></a>Azure Portal 

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję. 

1. W przypadku korzystania z aplikacji funkcji przejdź do **opcji funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **ustawień** w okienku nawigacji po lewej stronie. 

1. Wybierz pozycję **zarządzana tożsamość**. 

1. W ramach karty **przypisanej do systemu** Przełącz pozycję **stan** na wartość **włączone**. Kliknij przycisk **Zapisz**. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Aby zalogować się za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Aby utworzyć tożsamość dla tej aplikacji, użyj polecenia [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) lub [AZ functionapp Identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) polecenia:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Zanotuj element `PrincipalId`, który będzie wymagany w następnej sekcji.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Przyznaj aplikacji dostęp do Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Przejdź do zasobu Key Vault. 

1.  Wybierz pozycję **zasady dostępu** , a następnie kliknij pozycję **Dodaj zasady dostępu**. 

1.  W obszarze **uprawnienia klucza tajnego**wybierz pozycję **Pobierz, aby wyświetlić listę**. 

1.  Wybierz **pozycję Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę aplikacji.  Wybierz aplikację z listy wynik, a następnie kliknij pozycję **Wybierz**. 

1.  Kliknij przycisk **Dodaj** , aby zakończyć dodawanie nowych zasad dostępu.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby udzielić aplikacji dostępu do magazynu kluczy, użyj interfejsu wiersza polecenia platformy Azure [AZ Key principalId Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , dostarczając parametr **objectid** z zanotowanym powyżej **principalId** .

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management)
- [Zapewnianie uwierzytelniania Key Vault przy użyciu zasad kontroli dostępu](group-permissions-for-apps.md)
- [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md)).
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](best-practices.md) rozwiązań