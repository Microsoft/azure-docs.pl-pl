---
title: Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu programu PowerShell — Azure AD
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764373"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej możesz udzielić tożsamości zarządzanej dostępu do innego zasobu, podobnie jak do dowolnego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak udzielić tożsamości zarządzanej maszyny wirtualnej platformy Azure dostępu do konta usługi Azure Storage przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością zarządzaną przypisaną przez system i **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykładowe skrypty, masz dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą możesz otworzyć przy użyciu **przycisku Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję programu Azure PowerShell , [a](/powershell/azure/install-az-ps)następnie zaloguj się do platformy Azure przy użyciu polecenia `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use Azure RBAC to assign a managed identity access to another resource (Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli dostępu na platformie Azure)

1. Włącz tożsamość zarządzaną w zasobie platformy Azure, [takim jak maszyna wirtualna platformy Azure.](qs-configure-powershell-windows-vm.md)

1. W tym przykładzie zapewniamy maszynie wirtualnej platformy Azure dostęp do konta magazynu. Najpierw użyjemy [get-AzVM,](/powershell/module/az.compute/get-azvm) aby pobrać jednostkę usługi dla maszyny wirtualnej o nazwie , która została utworzona `myVM` podczas włączonej tożsamości zarządzanej. Następnie użyj [new-AzRoleAssignment,](/powershell/module/Az.Resources/New-AzRoleAssignment) aby  udzielić czytelnikowi maszyny wirtualnej dostępu do konta magazynu o nazwie `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanej dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz Configure managed identities for Azure resources on an Azure VM using PowerShell (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie [wirtualnej platformy Azure przy użyciu programu PowerShell).](qs-configure-powershell-windows-vm.md)
