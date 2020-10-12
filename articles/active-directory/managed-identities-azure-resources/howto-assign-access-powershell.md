---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej do jednego zasobu, uzyskiwania dostępu do innego zasobu przy użyciu programu PowerShell.
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
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66567275bf2c7454a2d4bb87dcd4c14bb1fb9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969292"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. Ten przykład pokazuje, jak nadawać zarządzanej tożsamości maszyny wirtualnej platformy Azure dostęp do konta usługi Azure Storage przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję [Azure PowerShell](/powershell/azure/install-az-ps), a następnie zaloguj się do platformy Azure przy użyciu `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Korzystanie z usługi Azure RBAC do przypisywania zarządzanej tożsamości do innego zasobu

1. Włącz zarządzaną tożsamość w ramach zasobu platformy Azure, na przykład [maszynę wirtualną platformy Azure](qs-configure-powershell-windows-vm.md).

1. W tym przykładzie nadajemy MASZYNom wirtualnym platformy Azure dostęp do konta magazynu. Najpierw użyjemy polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie `myVM` , która została utworzona podczas włączania zarządzanej tożsamości. Następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) , aby uzyskać dostęp do **czytnika** maszyn wirtualnych do konta magazynu o nazwie `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).
