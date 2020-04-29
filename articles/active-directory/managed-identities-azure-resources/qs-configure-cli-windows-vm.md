---
title: Konfigurowanie zarządzanych tożsamości na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244149"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, korzystając z interfejsu wiersza polecenia platformy Azure, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, można korzystać z trzech opcji:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (zobacz następną sekcję).
    - Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) Jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowane w celu odzwierciedlenia najnowszej wersji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przez system na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby można było utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć maszynę wirtualną:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością zarządzaną przypisaną przez system, zgodnie `--assign-identity` z żądaniem parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

   ```azurecli-interactive
   az login
   ```

2. Użyj `identity assign` polecenia [AZ VM Identity Assign](/cli/azure/vm/identity/) z poleceniem Enable a Assigned system Identity to a Existing VM:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Wyłączanie tożsamości przypisanej do systemu z poziomu maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości przypisanej do systemu, ale nadal wymagana jest tożsamość przypisana przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości przypisanej do systemu i nie ma żadnych tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` uwzględnia wielkość liter. Musi być małymi literami. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej podczas jej tworzenia wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć. Utwórz [grupę zasobów](~/articles/azure-resource-manager/management/overview.md#terminology) na potrzeby zawierania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu polecenia [AZ Group Create](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpowiedź zawiera szczegółowe informacje o utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. Wartość identyfikatora zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika jest używana w następnym kroku.

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

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład tworzy maszynę wirtualną skojarzoną z nową tożsamością przypisaną przez użytkownika, określoną przez `--assign-identity` parametr. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Utwórz tożsamość przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupę zasobów, w której utworzono tożsamość przypisaną przez użytkownika, a `-n` parametr określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika za pomocą znaków specjalnych (tj. podkreślenia) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Wracaj tutaj, aby zapoznać się z aktualizacjami.  Aby uzyskać więcej informacji [, zobacz często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegółowe informacje o utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. 

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

2. Przypisz tożsamość przypisaną przez użytkownika do maszyny wirtualnej przy użyciu polecenia [AZ VM Identity Assign](/cli/azure/vm). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest to właściwość zasobu `name` tożsamości zarządzanej przypisanej przez użytkownika, jak została utworzona w poprzednim kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . 

Jeśli jest to jedyna zarządzana tożsamość użytkownika skojarzona z maszyną wirtualną, `UserAssigned` zostanie usunięta z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` Będzie to `name` właściwość tożsamości przypisanej przez użytkownika, którą można znaleźć w sekcji tożsamość maszyny wirtualnej przy użyciu `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli maszyna wirtualna nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć z niej wszystkie tożsamości przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` uwzględnia wielkość liter. Musi być małymi literami.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli maszyna wirtualna ma zarówno tożsamość przypisaną do systemu, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości przypisane do użytkownika, przełączając do używania tylko przypisanych do systemu. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnymi przewodnikami szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz: 
  - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md) 

















