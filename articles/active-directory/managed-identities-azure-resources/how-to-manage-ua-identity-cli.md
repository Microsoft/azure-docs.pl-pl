---
title: Zarządzanie tożsamością zarządzaną przypisaną przez użytkownika — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, tworzenia, tworzenia listy i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784879"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Tworzenie, tworzenie, tworzenie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure


Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure tożsamość zarządzaną w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

Z tego artykułu dowiesz się, jak utworzyć, wyświetlić i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz Co to są [tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o typach tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika, zobacz [Typy tożsamości zarządzanych.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Aby zmodyfikować uprawnienia użytkownika podczas korzystania z jednostki usługi App Service przy użyciu interfejsu wiersza polecenia, musisz podać jednostki usługi dodatkowe uprawnienia w usłudze Azure AD interfejs Graph API, ponieważ części interfejsu wiersza polecenia wykonują żądania GET względem interfejs Graph API. W przeciwnym razie może zostać wyświetlony komunikat "Uprawnienia niewystarczające do ukończenia operacji". W tym celu musisz przejść do strony Rejestracja aplikacji w programie Azure Active Directory, wybrać aplikację, kliknąć uprawnienia interfejsu API, przewinąć w dół i wybrać pozycję Azure Active Directory Graph. W tym miejscu wybierz pozycję Uprawnienia aplikacji, a następnie dodaj odpowiednie uprawnienia. 

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto musi mieć przypisanie roli [Współautor tożsamości zarządzanej.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Użyj polecenia [az identity create,](/cli/azure/identity#az_identity_create) aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika. Parametr określa grupę zasobów, w której ma zostać utworzyć tożsamość zarządzaną przypisaną przez użytkownika, a `-g` parametr określa jej `-n` nazwę. Zastąp wartości `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parametrów i własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto musi mieć przypisanie roli [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej lub Współautor [tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zarządzanej.

Aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika, użyj [polecenia az identity list.](/cli/azure/identity#az_identity_list) Zastąp `<RESOURCE GROUP>` wartość własną wartością:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

W odpowiedzi JSON tożsamości zarządzane przypisane przez użytkownika mają zwracaną wartość `"Microsoft.ManagedIdentity/userAssignedIdentities"` klucza , `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto musi mieć przypisanie roli Współautor [tożsamości](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zarządzanej.

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj [polecenia az identity delete.](/cli/azure/identity#az_identity_delete)  -n parametr określa jego nazwę i -g parametr określa grupę zasobów, w której została utworzona tożsamość zarządzana przypisana przez użytkownika. Zastąp wartości `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` parametrów i własnymi wartościami:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z żadnego zasobu, do którym została przypisana. Usuń je z maszyny wirtualnej/usługi VMSS za pomocą `az vm/vmss identity remove` polecenia

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę poleceń tożsamości interfejsu wiersza polecenia platformy Azure, zobacz [az identity](/cli/azure/identity).

Aby uzyskać informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure, zobacz Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu [wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
