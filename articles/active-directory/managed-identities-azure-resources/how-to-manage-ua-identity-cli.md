---
title: Zarządzanie tożsamością zarządzaną przez użytkownika — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące sposobu tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: edb326c889a76eedd82c8918c705bdb5bbe5d195
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893612"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure


Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z zarządzaną tożsamością w Azure Active Directory. Ta tożsamość służy do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności podania poświadczeń w kodzie. 

Ten artykuł zawiera informacje na temat tworzenia, wyświetlania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o zarządzanych typach tożsamości przypisanych do systemu i przez użytkownika, zobacz [typy tożsamości zarządzanych](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Aby zmodyfikować uprawnienia użytkownika w przypadku korzystania z jednostki usługi App Service przy użyciu interfejsu wiersza polecenia, należy podać dodatkowe uprawnienia jednostki usługi w usłudze Azure AD interfejs API programu Graph jako części interfejsu wiersza polecenia wykonaj żądania GET względem interfejs API programu Graph. W przeciwnym razie można przestać otrzymywać komunikat "niewystarczające uprawnienia do ukończenia tej operacji". Aby to zrobić, musisz przejść do rejestracji aplikacji w Azure Active Directory wybierz aplikację, kliknij pozycję uprawnienia interfejsu API, przewiń w dół i wybierz pozycję Azure Active Directory Graf. Z poziomu wybranych uprawnień aplikacji, a następnie Dodaj odpowiednie uprawnienia. 

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Za pomocą polecenia [AZ Identity Create](/cli/azure/identity#az-identity-create) Utwórz tożsamość zarządzaną przypisaną przez użytkownika. `-g`Parametr określa grupę zasobów, w której ma zostać utworzona tożsamość zarządzana przypisana przez użytkownika, a `-n` parametr określa nazwę. Zastąp `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` wartości parametrów i własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga [operatora zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-operator) lub przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Aby wyświetlić tożsamości zarządzane przypisane przez użytkownika, użyj polecenia [AZ Identity list](/cli/azure/identity#az-identity-list) . Zamień na `<RESOURCE GROUP>` własną wartość:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

W odpowiedzi JSON tożsamości zarządzane przypisane przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracaną dla klucza, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [współautor zarządzanej tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj polecenia [AZ Identity Delete](/cli/azure/identity#az-identity-delete) .  Parametr-n określa nazwę i parametr-g określa grupę zasobów, w której utworzono tożsamość zarządzaną przez użytkownika. Zastąp `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` wartości parametrów i własnymi wartościami:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, do którego została przypisana. Usuń te z maszyny wirtualnej/VMSS przy użyciu `az vm/vmss identity remove` polecenia

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z pełną listą poleceń dotyczących tożsamości interfejsu wiersza polecenia platformy Azure, zobacz [AZ Identity](/cli/azure/identity).

Aby uzyskać informacje na temat sposobu przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
