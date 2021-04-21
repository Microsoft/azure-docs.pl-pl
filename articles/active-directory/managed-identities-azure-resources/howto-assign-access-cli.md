---
title: Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
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
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783291"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej możesz udzielić tożsamości zarządzanej dostępu do innego zasobu, podobnie jak do dowolnego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak udzielić tożsamości zarządzanej tożsamości zarządzanej maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych do konta usługi Azure Storage przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz Co to są [tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o typach tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika, zobacz [Typy tożsamości zarządzanych.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use Azure RBAC to assign a managed identity access to another resource (Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli dostępu na platformie Azure)

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, takim jak maszyna wirtualna platformy [Azure](qs-configure-cli-windows-vm.md) lub zestaw [skalowania maszyn wirtualnych platformy Azure:](qs-configure-cli-windows-vmss.md) 

1. W tym przykładzie zapewniamy maszynie wirtualnej platformy Azure dostęp do konta magazynu. Najpierw [użyjemy az resource list,](/cli/azure/resource/#az_resource_list) aby pobrać jednostkę usługi dla maszyny wirtualnej o nazwie myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   W przypadku zestawu skalowania maszyn wirtualnych platformy Azure polecenie jest takie samo, z wyjątkiem tego, że w tym miejscu otrzymasz jednostkę usługi dla zestawu skalowania maszyn wirtualnych o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Po utworzeniu identyfikatora jednostki usługi użyj funkcji [az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby udzielić maszynie wirtualnej lub zestawowi skalowania maszyn wirtualnych dostępu "Czytelnik" do konta magazynu o nazwie "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz Configure managed identities for Azure resources on an Azure VM using Azure CLI (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej [platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure).](qs-configure-cli-windows-vm.md)
- Aby włączyć tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz Configure managed identities for Azure resources on a virtual machine scale set using Azure CLI (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych [przy użyciu interfejsu wiersza polecenia platformy Azure).](qs-configure-cli-windows-vmss.md)
