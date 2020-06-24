---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej do jednego zasobu, uzyskiwania dostępu do innego zasobu przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba16f59944f94d505dd3da92e00830885ce86cf
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693908"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. Ten przykład pokazuje, jak nadawać zarządzanej tożsamości maszyny wirtualnej platformy Azure dostęp do konta usługi Azure Storage przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) , jeśli jeszcze tego nie zrobiono.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu do tożsamości zarządzanej do innego zasobu za pomocą RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład na [maszynie wirtualnej platformy Azure](qs-configure-powershell-windows-vm.md):

1. Zaloguj się do platformy Azure przy użyciu `Connect-AzAccount` polecenia cmdlet. Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której skonfigurowano zarządzaną tożsamość:

   ```powershell
   Connect-AzAccount
   ```
2. W tym przykładzie nadajemy MASZYNom wirtualnym platformy Azure dostęp do konta magazynu. Najpierw użyjemy polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie `myVM` , która została utworzona podczas włączania zarządzanej tożsamości. Następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) , aby uzyskać dostęp do **czytnika** maszyn wirtualnych do konta magazynu o nazwie `myStorageAcct` :

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).
