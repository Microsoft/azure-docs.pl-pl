---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej do jednego zasobu, uzyskiwania dostępu do innego zasobu przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3b06ce76ae77aa62b20b707a736e8e20e5f6c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99090048"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak udostępnić maszynę wirtualną platformy Azure lub zarządzaną tożsamość zestawu skalowania maszyn wirtualnych do konta usługi Azure Storage przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o zarządzanych typach tożsamości przypisanych do systemu i przez użytkownika, zobacz [typy tożsamości zarządzanych](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Korzystanie z usługi Azure RBAC do przypisywania zarządzanej tożsamości do innego zasobu

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład w przypadku [maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vm.md) lub [zestawu skalowania maszyn wirtualnych platformy Azure](qs-configure-cli-windows-vmss.md): 

1. W tym przykładzie przydajemy maszynom wirtualnym platformy Azure dostęp do konta magazynu. Najpierw użyj [AZ Resource list](/cli/azure/resource/#az-resource-list) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   W przypadku zestawu skalowania maszyn wirtualnych platformy Azure polecenie jest takie samo, chyba że w tym miejscu uzyskasz nazwę główną usługi dla zestawu skalowania maszyn wirtualnych o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Gdy masz identyfikator jednostki usługi, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby nadać maszynie wirtualnej lub zestaw skalowania maszyn wirtualnych "czytnik" dostęp do konta magazynu o nazwie "ciąg mystorageacct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).
- Aby włączyć zarządzaną tożsamość w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vmss.md).
