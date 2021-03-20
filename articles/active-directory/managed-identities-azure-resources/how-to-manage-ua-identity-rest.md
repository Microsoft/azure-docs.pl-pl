---
title: Zarządzanie tożsamościami zarządzanymi przez użytkownika przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące sposobu tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika w celu wykonania wywołań interfejsu API REST.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546696"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu wywołań interfejsu API REST

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługom platformy Azure możliwość uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności korzystania z poświadczeń w kodzie. 

Ten artykuł zawiera informacje na temat tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu zwinięcia w celu utworzenia wywołań interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Wszystkie polecenia w tym artykule można uruchomić w chmurze lub lokalnie:
    - Aby uruchomić program w chmurze, użyj [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Aby uruchamiać lokalnie, zainstaluj [zwinięcie](https://curl.haxx.se/download.html) i [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Uzyskaj token dostępu okaziciela

1. Jeśli działa lokalnie, zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure:

    ```
    az login
    ```

1. Uzyskiwanie tokenu dostępu za pomocą polecenia [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        |

**Treść żądania**

|Nazwa  |Opis  |
|---------|---------|
|location     | Wymagane. Lokalizacja zasobu.        |

## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga [operatora zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-operator) lub przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z zasobu, do którego została przypisana. Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny wirtualnej przy użyciu narzędzia ZWINIĘCIE [, zobacz Usuwanie tożsamości przypisanej do użytkownika z maszyny wirtualnej platformy Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure/VMSS za pomocą [ZAwieszania, zobacz Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) i [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu wywołań interfejsu API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity).
