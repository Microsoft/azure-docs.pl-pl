---
title: Zarządzanie tożsamością zarządzaną przypisaną przez użytkownika — interfejs wiersza polecenia platformy Azure — usługa Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, listy i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639757"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure


Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure z tożsamością zarządzaną w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak utworzyć, wyświetlić listę i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, dostępne są trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) z witryny Azure portal (zobacz następną sekcję).
    - Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu.
    - Jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia, należy [zainstalować najnowszą wersję interfejsu wiersza](https://docs.microsoft.com/cli/azure/install-azure-cli) polecenia platformy Azure (2.0.13 lub nowszym). Zaloguj się na `az login`platformie Azure przy użyciu konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć tożsamość zarządzaną przypisaną przez użytkownika.


> [!NOTE]
> Aby zmodyfikować uprawnienia użytkownika podczas korzystania z jednostki servivce aplikacji przy użyciu interfejsu wiersza polecenia, należy podać jednostki usługi dodatkowe uprawnienia w interfejsie API programu Azure AD Graph jako części interfejsu wiersza polecenia wykonywania żądań GET względem interfejsu API wykresu. W przeciwnym razie może skończyć się otrzymaniem komunikatu "Niewystarczające uprawnienia do ukończenia operacji". Aby to zrobić, musisz przejść do rejestracji aplikacji w usłudze Azure Active Directory, wybierz aplikację, kliknij uprawnienia interfejsu API, przewiń w dół i wybierz pozycję Wykres usługi Azure Active Directory. Następnie wybierz pozycję Uprawnienia aplikacji, a następnie dodaj odpowiednie uprawnienia. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Polecenie [tworzenie tożsamości az](/cli/azure/identity#az-identity-create) służy do tworzenia tożsamości zarządzanej przypisanej przez użytkownika. Parametr `-g` określa grupę zasobów, w której ma być tworzę tożsamość zarządzaną przypisaną przez użytkownika, a `-n` parametr określa jego nazwę. Zastąp `<RESOURCE GROUP>` wartości i `<USER ASSIGNED IDENTITY NAME>` parametrów własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić listę/odczytywanie tożsamości zarządzanej przypisanej przez użytkownika, twoje konto wymaga przypisania roli [Operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika, użyj polecenia [az identity list.](/cli/azure/identity#az-identity-list) Wymień własną `<RESOURCE GROUP>` wartość:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
W odpowiedzi json tożsamości zarządzane przypisane `"Microsoft.ManagedIdentity/userAssignedIdentities"` przez użytkownika mają `type`wartość zwróconą dla klucza, .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, użyj polecenia [az identity delete.](/cli/azure/identity#az-identity-delete)  Parametr -n określa jego nazwę, a parametr -g określa grupę zasobów, do której została utworzona tożsamość zarządzana przypisana przez użytkownika. Zastąp `<USER ASSIGNED IDENTITY NAME>` wartości i `<RESOURCE GROUP>` parametry własnymi wartościami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usunięcie tożsamości zarządzanej przypisanej przez użytkownika nie spowoduje usunięcia odwołania z dowolnego zasobu, do którego został przypisany. Usuń je z maszyn wirtualnych/VMSS za pomocą `az vm/vmss identity remove` polecenia

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną listę poleceń tożsamości interfejsu wiersza polecenia platformy Azure, zobacz [az identity](/cli/azure/identity).

Aby uzyskać informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
