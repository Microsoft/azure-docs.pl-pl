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
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791215"
---
# <a name="manage-storage-account-access-keys"></a>Zarządzanie kluczami dostępu do konta magazynu

Podczas tworzenia konta magazynu platforma Azure generuje dwa 512-bitowe klucze dostępu do konta magazynu. Te klucze mogą służyć do autoryzowania dostępu do danych na koncie magazynu za pośrednictwem autoryzacji klucza wspólnego.

Firma Microsoft zaleca używanie Azure Key Vault do zarządzania kluczami dostępu oraz regularne obracanie i ponowne generowanie kluczy. Użycie Azure Key Vault ułatwia obracanie kluczy bez zakłóceń w aplikacjach. Klucze można również obracać ręcznie.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Wyświetlanie kluczy dostępu do konta

Klucze dostępu do konta można wyświetlać i kopiować za pomocą interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Ponadto Azure Portal parametrów połączenia dla konta magazynu, które można skopiować.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyświetlić i skopiować klucze dostępu do konta magazynu lub parametrów połączenia z Azure Portal:

1. Przejdź do konta magazynu w Azure Portal [.](https://portal.azure.com)
1. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
1. Znajdź wartość **Klucz** w obszarze **klucz1** i kliknij przycisk **Kopiuj,** aby skopiować klucz konta.
1. Alternatywnie możesz skopiować wszystkie ciągi połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania kluczy dostępu w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby pobrać klucze dostępu do konta za pomocą programu PowerShell, wywołaj polecenie [Get-AzStorageAccountKey.](/powershell/module/az.Storage/Get-azStorageAccountKey)

Poniższy przykład pobiera pierwszy klucz. Aby pobrać drugi klucz, użyj `Value[1]` zamiast `Value[0]` . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę kluczy dostępu do konta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account keys list,](/cli/azure/storage/account/keys#az_storage_account_keys_list) jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Możesz użyć jednego z tych dwóch kluczy, aby uzyskać dostęp do usługi Azure Storage, ale ogólnie rzecz biorąc, dobrym rozwiązaniem jest użycie pierwszego klucza i zarezerwowanie drugiego klucza na czas rotacji kluczy.

Aby wyświetlić lub odczytać klucze dostępu konta, użytkownik musi być administratorem usługi lub mieć przypisaną rolę platformy Azure, która zawiera akcję **Microsoft.Storage/storageAccounts/listkeys/.** Niektóre wbudowane role platformy Azure, które obejmują tę akcję, to **role właściciela,** **współautora** i operatora usługi **klucza konta magazynu.** Aby uzyskać więcej informacji na temat roli administratora usługi, zobacz Role klasycznego administratora subskrypcji, role platformy [Azure i role usługi Azure AD.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Aby uzyskać szczegółowe informacje na temat wbudowanych ról dla usługi Azure Storage, zobacz sekcję **Magazyn** we wbudowanych rolach platformy [Azure dla kontroli RBAC platformy Azure.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Używanie Azure Key Vault do zarządzania kluczami dostępu

Firma Microsoft zaleca używanie Azure Key Vault do zarządzania kluczami dostępu i ich rotacji. Aplikacja może bezpiecznie uzyskać dostęp do kluczy w Key Vault, dzięki czemu można uniknąć przechowywania ich przy użyciu kodu aplikacji. Aby uzyskać więcej informacji na temat Key Vault zarządzania kluczami, zobacz następujące artykuły:

- [Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i programu PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Zarządzanie kluczami konta magazynu za pomocą Azure Key Vault i interfejsu wiersza polecenia platformy Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ręczne obracanie kluczy dostępu

Firma Microsoft zaleca okresową rotację kluczy dostępu w celu zapewnienia bezpieczeństwa konta magazynu. Jeśli to możliwe, użyj Azure Key Vault do zarządzania kluczami dostępu. Jeśli nie korzystasz z Key Vault, musisz obrócić klucze ręcznie.

Przypisane są dwa klucze dostępu, dzięki czemu można obracać klucze. Posiadanie dwóch kluczy gwarantuje, że aplikacja zachowuje dostęp do usługi Azure Storage w całym procesie.

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Wszystkich klientów, którzy używają klucza konta do uzyskiwania dostępu do konta magazynu, należy zaktualizować w celu używania nowego klucza, w tym usług Media Services, aplikacji w chmurze, aplikacji klasycznych i mobilnych oraz graficznych aplikacji interfejsu użytkownika dla usługi Azure Storage, takich [jak Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby obrócić klucze dostępu konta magazynu w Azure Portal:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwoływać się do pomocniczego klucza dostępu dla konta magazynu.
1. Przejdź do konta magazynu w Azure Portal [.](https://portal.azure.com)
1. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.
1. Aby ponownie wygenerować podstawowy klucz dostępu dla konta magazynu, wybierz przycisk **Wygeneruj** ponownie obok podstawowego klucza dostępu.
1. Zaktualizuj parametry połączenia w kodzie za pomocą odwołania do nowego podstawowego klucza dostępu.
1. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby obrócić klucze dostępu konta magazynu przy użyciu programu PowerShell:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwoływać się do pomocniczego klucza dostępu dla konta magazynu.
1. Wywołaj [polecenie New-AzStorageAccountKey,](/powershell/module/az.storage/new-azstorageaccountkey) aby ponownie wygenerować podstawowy klucz dostępu, jak pokazano w poniższym przykładzie:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Zaktualizuj parametry połączenia w kodzie za pomocą odwołania do nowego podstawowego klucza dostępu.
1. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu. Aby ponownie wygenerować klucz pomocniczy, użyj `key2` jako nazwy klucza zamiast `key1` .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby obrócić klucze dostępu konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby odwoływać się do pomocniczego klucza dostępu dla konta magazynu.
1. Wywołaj [polecenie az storage account keys renew,](/cli/azure/storage/account/keys#az_storage_account_keys_renew) aby ponownie wygenerować podstawowy klucz dostępu, jak pokazano w poniższym przykładzie:

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
> Firma Microsoft zaleca używanie tylko jednego z kluczy we wszystkich aplikacjach w tym samym czasie. Jeśli w niektórych miejscach użyjemy klucza 1, a w innych klucza 2, nie będzie można obracać kluczy bez utraty dostępu przez niektóre aplikacje.

Aby można było obracać klucze dostępu konta, użytkownik musi być administratorem usługi lub mieć przypisaną rolę platformy Azure, która zawiera element **Microsoft.Storage/storageAccounts/regeneratekey/action.** Niektóre wbudowane role platformy Azure, które obejmują tę akcję, to **role właściciela,** **współautora** i operatora usługi klucza **konta magazynu.** Aby uzyskać więcej informacji na temat roli administratora usługi, zobacz Role klasycznego administratora subskrypcji, role platformy [Azure i role usługi Azure AD.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Aby uzyskać szczegółowe informacje na temat wbudowanych ról platformy Azure dla usługi Azure Storage, zobacz sekcję **Magazyn** we wbudowanych rolach platformy [Azure dla kontroli RBAC platformy Azure.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta usługi Azure Storage](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-account-create.md)
