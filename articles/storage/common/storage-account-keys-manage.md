---
title: Zarządzanie kluczami dostępu do konta
titleSuffix: Azure Storage
description: Dowiedz się, jak wyświetlać klucze dostępu do konta magazynu, zarządzać nimi i obracać je.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 50c0980800bbc9b2951bf9107114c1a4d9265558
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454666"
---
# <a name="manage-storage-account-access-keys"></a>Zarządzanie kluczami dostępu do konta magazynu

Podczas tworzenia konta magazynu platforma Azure generuje dwa klucze dostępu do konta magazynu 512-bitowego. Klucze te mogą być używane do autoryzowania dostępu do danych na koncie magazynu za pośrednictwem autoryzacji klucza udostępnionego.

Firma Microsoft zaleca, aby używać usługi Azure Key Vault do zarządzania kluczami dostępu oraz regularnego obracania i ponownego generowania kluczy. Korzystanie z usługi Azure Key Vault ułatwia obracanie kluczy bez przerw do aplikacji. Można również ręcznie obracać klawisze.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Wyświetlanie klawiszy dostępu i ciągu połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Zarządzanie kluczami dostępu za pomocą usługi Azure Key Vault

Firma Microsoft zaleca używanie usługi Azure Key Vault do zarządzania kluczami dostępu i obracania ich. Aplikacja może bezpiecznie uzyskać dostęp do kluczy w magazynie kluczy, dzięki czemu można uniknąć ich przechowywania przy kodzie aplikacji. Aby uzyskać więcej informacji na temat używania usługi Key Vault do zarządzania kluczami, zobacz następujące artykuły:

- [Zarządzanie kluczami kont magazynu za pomocą usługi Azure Key Vault i programu PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Zarządzanie kluczami kont magazynu za pomocą usługi Azure Key Vault i interfejsu wiersza polecenia platformy Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ręczne obracanie klawiszy dostępu

Firma Microsoft zaleca okresowe obracanie kluczy dostępu w celu zapewnienia bezpieczeństwa konta magazynu. Jeśli to możliwe, użyj usługi Azure Key Vault do zarządzania kluczami dostępu. Jeśli nie korzystasz z usługi Key Vault, konieczne będzie ręczne obracanie klawiszy.

Przypisano dwa klucze dostępu, dzięki czemu można obracać klawisze. Posiadanie dwóch kluczy zapewnia, że aplikacja zachowuje dostęp do usługi Azure Storage w trakcie całego procesu.

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Wszyscy klienci korzystający z klucza konta w celu uzyskania dostępu do konta magazynu muszą zostać zaktualizowani, aby korzystać z nowego klucza, w tym usług multimedialnych, aplikacji chmurowych, komputerowych i mobilnych oraz graficznych aplikacji interfejsu użytkownika dla usługi Azure Storage, takich jak [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

Postępuj zgodnie z tym procesem, aby obrócić klucze konta magazynu:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby użyć klucza pomocniczego.
2. Ponownie wygeneruj podstawowy klucz dostępu do konta magazynu. W bloku **Klucze dostępu** w witrynie Azure portal kliknij **pozycjęGeneruj klucz1**, a następnie kliknij przycisk **Tak,** aby potwierdzić, że chcesz wygenerować nowy klucz.
3. Zaktualizuj parametry połączenia w kodzie, wprowadzając odwołanie do nowego podstawowego klucza dostępu.
4. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

> [!NOTE]
> Firma Microsoft zaleca używanie tylko jednego klucza we wszystkich aplikacjach w tym samym czasie. Jeśli używasz klucza 1 w niektórych miejscach i klucza 2 w innych, nie będzie można obracać kluczy bez utraty dostępu przez jakąś aplikację.

Aby obrócić klucze dostępu do konta, użytkownik musi być administratorem usługi lub musi mieć przypisaną rolę RBAC, która zawiera **microsoft.Storage/storageAccounts/regeneratekey/action**. Niektóre wbudowane role RBAC, które zawierają tę akcję, to role roli roli usługi **Właściciel,** **Współautor**i **Operator klucza magazynu.** Aby uzyskać więcej informacji na temat roli administratora usługi, zobacz [klasyczne role administratora subskrypcji, role usługi Azure RBAC i role usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Aby uzyskać szczegółowe informacje na temat wbudowanych ról RBAC dla usługi Azure Storage, zobacz sekcję **Magazyn** [w wbudowanych rolach platformy Azure dla usługi Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta magazynu platformy Azure](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-account-create.md)
