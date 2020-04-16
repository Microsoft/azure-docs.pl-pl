---
title: Uzyskiwanie dostępu do usługi Azure Key Vault za pomocą tożsamości zarządzanej przypisanej do systemu
description: Dowiedz się, jak utworzyć tożsamość zarządzaną dla aplikacji usługi App Service i jak jej używać do uzyskiwania dostępu do usługi Azure Key Vault
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432127"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Zapewnianie uwierzytelniania usługi Key Vault z tożsamością zarządzaną

Tożsamość zarządzana z usługi Azure Active Directory umożliwia aplikacji łatwy dostęp do innych zasobów chronionych usługą Azure AD. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowidizacji ani obracania żadnych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Zarządzane tożsamości zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla aplikacji usługi App Service i używać jej do uzyskiwania dostępu do usługi Azure Key Vault. W przypadku aplikacji hostowanych na maszynach wirtualnych platformy Azure zobacz [Uzyskiwanie dostępu do usługi Azure Key Vault za pomocą tożsamości zarządzanej przypisanej przez system Windows.](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten przewodnik, musisz mieć następujące zasoby. 

- Przechowalnia kluczy. Można użyć istniejącego magazynu kluczy lub utworzyć nowy, wykonując kroki opisane w jednym z tych przewodników Szybki start:
   - [Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md)
   - [Tworzenie magazynu kluczy za pomocą programu Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Utwórz magazyn kluczy za pomocą portalu Azure](../secrets/quick-create-portal.md).
- Istniejąca aplikacja usługi App Service, do której ma być udzielony dostęp do magazynu kluczy. Można go szybko utworzyć, wykonując kroki opisane w [dokumentacji usługi App Service.](../../app-service/overview.md)
- [Narzędzie interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest) platformy Azure lub [programu Azure PowerShell](/powershell/azure/overview). Alternatywnie można użyć [witryny Azure portal](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu 

Najpierw należy dodać tożsamość przypisaną do systemu do aplikacji. 
 
### <a name="azure-portal"></a>Azure Portal 

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję. 

1. Jeśli korzystasz z aplikacji funkcyjnej, przejdź do **pozycji Funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **Ustawienia** w lewej nawigacji. 

1. Wybierz **pozycję Tożsamość zarządzana**. 

1. Na karcie **Przypisany** system **przełącz** stan **na Włączone**. Kliknij pozycję **Zapisz**. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Aby zalogować się za pomocą interfejsu [wiersza polecenia interfejsu wiersza polecenia](/cli/azure/reference-index?view=azure-cli-latest#az-login) platformy Azure, użyj polecenia logowania az:

```azurecli-interactive
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Aby utworzyć tożsamość dla tej aplikacji, użyj polecenia [przypisywania tożsamości aplikacji WebApp](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) platformy Azure az lub polecenia [przypisywania tożsamości az functionapp:](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Zanotuj `PrincipalId`, które będą potrzebne w następnej sekcji.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Udzielanie dostępu aplikacji do usługi Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Przejdź do zasobu Usługi Key Vault. 

1.  Wybierz pozycję **Zasady programu Access** i kliknij pozycję Dodaj zasady **dostępu**. 

1.  W **obszarze Uprawnienia tajne**wybierz pozycję **Pobierz, Lista**. 

1.  Wybierz **pozycję Wybierz podmiot,** a w polu wyszukiwania wprowadź nazwę aplikacji.  Wybierz aplikację na liście wyników i kliknij przycisk **Wybierz**. 

1.  Kliknij **przycisk Dodaj,** aby zakończyć dodawanie nowych zasad dostępu.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby udzielić aplikacji dostępu do magazynu kluczy, należy użyć polecenia [azure cli az keyvault set-policy,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) podając **ObjectId** parametr z **identyfikatorem głównym,** który został opisany powyżej.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia usługi Azure Key Vault: zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management)
- [Zapewnianie uwierzytelniania usługi Key Vault z zasadą kontroli dostępu](group-permissions-for-apps.md)
- [Zabezpiecz przechowalnię kluczy).](secure-your-key-vault.md)
- [Przewodnik dla deweloperów usługi Azure Key Vault](developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](best-practices.md)