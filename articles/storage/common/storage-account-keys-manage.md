---
title: Zarządzanie kluczami dostępu do konta
titleSuffix: Azure Storage
description: Dowiedz się, jak wyświetlać i obracać klucze dostępu do konta magazynu oraz zarządzać nimi.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069663"
---
# <a name="manage-storage-account-access-keys"></a>Zarządzanie kluczami dostępu do konta magazynu

Podczas tworzenia konta magazynu platforma Azure generuje 2 512-bitowe klucze dostępu do konta magazynu. Te klucze mogą służyć do autoryzowania dostępu do danych na koncie magazynu za pośrednictwem autoryzacji klucza współużytkowanego.

Firma Microsoft zaleca, aby zarządzać kluczami dostępu za pomocą Azure Key Vault i regularnie obracać i generować klucze. Za pomocą Azure Key Vault można łatwo obrócić klucze bez zakłócania swoich aplikacji. Możesz również ręcznie obrócić klucze.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Wyświetlanie kluczy dostępu do konta

Klucze dostępu do konta można wyświetlać i kopiować przy użyciu interfejsu wiersza polecenia Azure Portal, PowerShell lub platformy Azure. Azure Portal zawiera również parametry połączenia dla konta magazynu, które można skopiować.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyświetlić i skopiować klucze dostępu do konta magazynu lub parametry połączenia z Azure Portal:

1. Przejdź do swojego konta magazynu w [Azure Portal](https://portal.azure.com).
1. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
1. Znajdź wartość **klucza** w obszarze **Klucz1**, a następnie kliknij przycisk **Kopiuj** , aby skopiować klucz konta.
1. Alternatywnie można skopiować wszystkie parametry połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania kluczy dostępu w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby pobrać klucze dostępu do konta za pomocą programu PowerShell, wywołaj polecenie [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) .

Poniższy przykład pobiera pierwszy klucz. Aby pobrać drugi klucz, użyj `Value[1]` zamiast `Value[0]` . Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę kluczy dostępu do konta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Aby uzyskać dostęp do usługi Azure Storage, możesz użyć jednego z tych dwóch kluczy, ale ogólnie rzecz biorąc, warto użyć pierwszego klucza i zastrzec użycie drugiego klucza w przypadku rotacji kluczy.

Aby wyświetlić lub odczytać klucze dostępu konta, użytkownik musi być administratorem usługi lub musi mieć przypisaną rolę platformy Azure, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**. Niektóre wbudowane role platformy Azure, które obejmują tę akcję, to role **roli usługi operatora kluczy** **właściciel**, **współautor**i konto magazynu. Aby uzyskać więcej informacji o roli administratora usługi, zobacz [klasyczne role administratora subskrypcji, role platformy Azure i role usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Aby uzyskać szczegółowe informacje na temat ról wbudowanych usługi Azure Storage, zobacz sekcję dotyczącą **magazynu** w [rolach wbudowanych platformy Azure dla usługi Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Zarządzanie kluczami dostępu za pomocą Azure Key Vault

Firma Microsoft zaleca używanie Azure Key Vault do zarządzania i obracania kluczy dostępu. Twoja aplikacja może bezpiecznie uzyskiwać dostęp do kluczy w Key Vault, dzięki czemu można uniknąć zapisywania ich w kodzie aplikacji. Aby uzyskać więcej informacji na temat używania Key Vault do zarządzania kluczami, zobacz następujące artykuły:

- [Zarządzanie kluczami konta magazynu za pomocą Azure Key Vault i programu PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ręcznie Obróć klucze dostępu

Firma Microsoft zaleca, aby okresowo obrócić klucze dostępu w celu zapewnienia bezpieczeństwa konta magazynu. Jeśli to możliwe, użyj Azure Key Vault do zarządzania kluczami dostępu. Jeśli nie używasz Key Vault, musisz ręcznie obrócić klucze.

Przypisane są dwa klucze dostępu, dzięki czemu można obrócić klucze. Posiadanie dwóch kluczy gwarantuje, że aplikacja utrzymuje dostęp do usługi Azure Storage w trakcie całego procesu.

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Wszyscy klienci korzystający z klucza konta do uzyskiwania dostępu do konta magazynu muszą zostać zaktualizowani, aby używać nowego klucza, w tym usług Media Services, chmury, pulpitu i aplikacji mobilnych, oraz graficznych aplikacji interfejsu użytkownika dla usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby obrócić klucze dostępu do konta magazynu w Azure Portal:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwołać się do pomocniczego klucza dostępu dla konta magazynu.
1. Przejdź do swojego konta magazynu w [Azure Portal](https://portal.azure.com).
1. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.
1. Aby ponownie wygenerować podstawowy klucz dostępu dla konta magazynu, wybierz przycisk **Wygeneruj ponownie** obok podstawowego klucza dostępu.
1. Zaktualizuj parametry połączenia w kodzie za pomocą odwołania do nowego podstawowego klucza dostępu.
1. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby obrócić klucze dostępu do konta magazynu za pomocą programu PowerShell:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwołać się do pomocniczego klucza dostępu dla konta magazynu.
1. Wywołaj polecenie [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) , aby ponownie wygenerować podstawowy klucz dostępu, jak pokazano w następującym przykładzie:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Zaktualizuj parametry połączenia w kodzie za pomocą odwołania do nowego podstawowego klucza dostępu.
1. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu. Aby ponownie wygenerować klucz pomocniczy, użyj `key2` jako nazwy klucza zamiast `key1` .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby obrócić klucze dostępu do konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwołać się do pomocniczego klucza dostępu dla konta magazynu.
1. Wywołaj polecenie [AZ Storage account Keys Renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) , aby ponownie wygenerować podstawowy klucz dostępu, jak pokazano w następującym przykładzie:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Zaktualizuj parametry połączenia w kodzie za pomocą odwołania do nowego podstawowego klucza dostępu.
1. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu. Aby ponownie wygenerować klucz pomocniczy, użyj `key2` jako nazwy klucza zamiast `key1` .

---

> [!NOTE]
> Firma Microsoft zaleca używanie tylko jednego z kluczy we wszystkich aplikacjach w tym samym czasie. Jeśli używasz klucza 1 w niektórych miejscach i w innym miejscu niż 2, nie będziesz w stanie obrócić swoich kluczy bez utraty dostępu do aplikacji.

Aby można było obrócić klucze dostępu konta, użytkownik musi być administratorem usługi lub musi mieć przypisaną rolę platformy Azure, która zawiera element **Microsoft. Storage/storageAccounts/regeneratekey/Action**. Niektóre wbudowane role platformy Azure, które obejmują tę akcję, to role **roli usługi operatora kluczy** **właściciel**, **współautor**i konto magazynu. Aby uzyskać więcej informacji o roli administratora usługi, zobacz [klasyczne role administratora subskrypcji, role platformy Azure i role usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Aby uzyskać szczegółowe informacje na temat wbudowanych ról platformy Azure dla usługi Azure Storage, zobacz sekcję dotyczącą **magazynu** w [rolach wbudowanych platformy Azure dla usługi Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta usługi Azure Storage](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-account-create.md)
