---
title: Konfigurowanie tożsamości zarządzanych w zestawie skalowania maszyn wirtualnych — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez system i użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788191"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. 

Z tego artykułu dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie przypisanej przez system tożsamości zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz Co to są [tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o typach tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika, zobacz [Typy tożsamości zarządzanych.](overview.md#managed-identity-types)

- Aby wykonać operacje zarządzania w tym artykule, Twoje konto wymaga następujących przypisań kontroli dostępu opartej na rolach platformy Azure:

  - [Współautor maszyny wirtualnej—](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) tworzenie zestawu skalowania maszyn wirtualnych oraz włączanie i usuwanie tożsamości zarządzanej przypisanej przez system i/lub użytkownika z zestawu skalowania maszyn wirtualnych.

  - [Rola Współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.

  - [Rola Operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

  > [!NOTE]
  > Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system dla zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system:

1. Utwórz [grupę zasobów do](../../azure-resource-manager/management/overview.md#terminology) zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanych z nim zasobów przy użyciu [narzędzia az group create](/cli/azure/group/#az_group_create). Możesz pominąć ten krok, jeśli masz już grupę zasobów, która ma być zamiast tego:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Utwórz](/cli/azure/vmss/#az_vmss_create) zestaw skalowania maszyn wirtualnych. Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* z tożsamością zarządzaną przypisaną przez system zgodnie z żądaniem `--assign-identity` parametru . Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć [tożsamość](/cli/azure/vmss/identity/#az_vmss_identity_assign) zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej przez system, ale nadal wymaga tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej przez system i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W wartości jest `none` wróżniana wielkość liter. Musi to być mała litera. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

Ta sekcja zawiera informacje na temat tworzenia zestawu skalowania maszyn wirtualnych i przypisywania tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych. Jeśli masz już zestaw skalowania maszyn wirtualnych, którego chcesz użyć, pomiń tę sekcję i przejdź do następnej.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, z których chcesz korzystać. Utwórz [grupę zasobów do](~/articles/azure-resource-manager/management/overview.md#terminology) zawierania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu [narzędzia az group create](/cli/azure/group/#az_group_create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az_identity_create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpowiedź zawiera szczegóły utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do poniższej. Wartość zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika `id` jest używana w następnym kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [Utwórz](/cli/azure/vmss/#az_vmss_create) zestaw skalowania maszyn wirtualnych. Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych skojarzony z nową tożsamością zarządzaną przypisaną przez użytkownika, zgodnie z `--assign-identity` parametrem . Upewnij się, że parametry `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az_identity_create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegóły utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Przypisz](/cli/azure/vmss/identity) tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. To `<USER ASSIGNED IDENTITY>` właściwość zasobu tożsamości przypisanej przez użytkownika, utworzona w poprzednim `name` kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Aby [usunąć](/cli/azure/vmss/identity#az_vmss_identity_remove) tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyn wirtualnych, użyj funkcji `az vmss identity remove` . Jeśli jest to jedyna tożsamość zarządzana przypisana przez użytkownika przypisana do zestawu skalowania maszyn wirtualnych, zostanie usunięta `UserAssigned` z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. Będzie to właściwość tożsamości zarządzanej przypisanej przez użytkownika, którą można znaleźć w sekcji tożsamości zestawu skalowania `<USER ASSIGNED IDENTITY>` maszyn wirtualnych przy użyciu funkcji `name` `az vmss identity show` :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli zestaw skalowania maszyn wirtualnych nie ma przypisanej przez system tożsamości zarządzanej i chcesz usunąć z niego wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W wartości jest `none` zróżnicowaana wielkość liter. Musi być mała.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli zestaw skalowania maszyn wirtualnych ma tożsamości zarządzane przypisane przez system i przez użytkownika, możesz usunąć wszystkie tożsamości przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej przez system. Użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełny przewodnik Szybki start na temat tworzenia zestawu skalowania maszyn wirtualnych platformy Azure, zobacz [Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
