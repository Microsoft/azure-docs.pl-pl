---
title: Konfigurowanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez system i użytkownika na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: a8dbe9665612e888b7e7afe95a472ba6b0de8d48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762519"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. 

Z tego artykułu, korzystając z interfejsu wiersza polecenia platformy Azure, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji na zasobach platformy Azure na maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej platformy Azure

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz Co to są tożsamości zarządzane [dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o typach tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika, zobacz [Typy tożsamości zarządzanych.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto musi mieć przypisanie roli [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az_group_create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością zarządzaną przypisaną przez system zgodnie z żądaniem `--assign-identity` parametru . Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto musi mieć przypisanie roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az_login). Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```azurecli-interactive
   az login
   ```

2. Użyj [polecenia az vm identity assign](/cli/azure/vm/identity/) z `identity assign` poleceniem enable the system-assigned identity to an existing VM:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Wyłączanie tożsamości przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości przypisanej przez system, ale nadal wymaga tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości przypisanej przez system i nie ma tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W wartości jest `none` zróżnicowaana wielkość liter. Musi być mała. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli tworzysz tożsamość zarządzaną przypisaną przez użytkownika w innej rg niż maszyna wirtualna. Aby przypisać ją do maszyny wirtualnej, należy użyć adresu URL tożsamości zarządzanej.
tj. --identities "/subscriptions/ <SUBID> /resourcegroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej platformy Azure

Aby podczas tworzenia przypisać tożsamość przypisaną przez użytkownika do [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej, twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, z których chcesz korzystać. Utwórz [grupę zasobów](~/articles/azure-resource-manager/management/overview.md#terminology) do zawierania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu [narzędzia az group create](/cli/azure/group/#az_group_create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az_identity_create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpowiedź zawiera szczegóły utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. W następnym kroku zostanie użyta wartość identyfikatora zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy maszynę wirtualną skojarzoną z nową tożsamością przypisaną przez użytkownika, jak określono w `--assign-identity` parametrze . Upewnij się, że parametry `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej, Twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Utwórz tożsamość przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az_identity_create).  Parametr określa grupę zasobów, w której jest tworzona tożsamość przypisana przez `-g` użytkownika, a `-n` parametr określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika ze znakami specjalnymi (tj. podkreśleniem) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Wracaj tutaj, aby zapoznać się z aktualizacjami.  Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegóły utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. 

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

2. Przypisz tożsamość przypisaną przez użytkownika do maszyny wirtualnej za pomocą [narzędzia az vm identity assign](/cli/azure/vm). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. To właściwość zasobu tożsamości zarządzanej przypisana przez użytkownika, utworzona `<USER ASSIGNED IDENTITY NAME>` `name` w poprzednim kroku. Jeśli tożsamość zarządzana przypisana przez użytkownika została utworzona w innej rg niż maszyna wirtualna. Musisz użyć adresu URL tożsamości zarządzanej.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej. 

Jeśli jest to jedyna tożsamość zarządzana przypisana przez użytkownika przypisana do maszyny wirtualnej, zostanie usunięta `UserAssigned` z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. Wartość będzie właściwością tożsamości przypisanej przez użytkownika, którą można znaleźć w sekcji tożsamości maszyny `<USER ASSIGNED IDENTITY>` `name` wirtualnej przy użyciu funkcji `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli maszyna wirtualna nie ma przypisanej przez system tożsamości zarządzanej i chcesz usunąć z niego wszystkie tożsamości przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W wartości jest `none` zróżnicowaana wielkość liter. Musi być mała.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli maszyna wirtualna ma zarówno tożsamości przypisane przez system, jak i przypisane przez użytkownika, możesz usunąć wszystkie tożsamości przypisane przez użytkownika, przełączając się do używania tylko tożsamości przypisanych przez system. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki
- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełne informacje o przewodnikach Szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz: 
  - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md)
