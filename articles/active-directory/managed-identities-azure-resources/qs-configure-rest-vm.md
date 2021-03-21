---
title: Konfigurowanie zarządzanych tożsamości na maszynie wirtualnej platformy Azure przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika na maszynie wirtualnej platformy Azure przy użyciu zawieszania do wykonywania wywołań interfejsu API REST.
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
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587523"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością systemu w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, za pomocą zapełniania w celu wykonywania wywołań do punktu końcowego Azure Resource Manager REST, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej platformy Azure

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o zarządzanych typach tożsamości przypisanych do systemu i przez użytkownika, zobacz [typy tożsamości zarządzanych](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej platformy Azure przy użyciu zwinięcia, aby nawiązywać wywołania do punktu końcowego REST Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby można było utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic#az-network-nic-create) dla maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Korzystając z Azure Cloud Shell, Utwórz maszynę wirtualną przy użyciu ZWINIĘCIEa, aby wywołać Azure Resource Manager punkt końcowy REST. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością zarządzaną przypisaną przez system, jak określono w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}` . Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**
   
   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 
   
   **Treść żądania**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości przypisanej do systemu na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia zapełnienia, aby wywołać punkt końcowy Azure Resource Manager REST w celu włączenia zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej zgodnie z definicją w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` maszyny wirtualnej o nazwie *myVM*.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, należy listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli do maszyny wirtualnej są przypisane skojarzone tożsamości przypisane przez użytkownika, które zostały określone w `identity` wartości odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamość zarządzaną przez użytkownika podczas włączania zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 
   
   **Treść żądania**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej z istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy dodać `SystemAssigned` do tej `type` wartości.  
   
   Na przykład jeśli maszyna wirtualna ma zarządzane tożsamości przypisane przez użytkownika `ID1` i `ID2` przypisane do niej, a chcesz dodać tożsamość zarządzaną przypisaną przez system do maszyny wirtualnej, użyj następującego Wywołaj metodę. Zamień `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` na wartości odpowiednie dla danego środowiska.

   Wersja interfejsu API `2018-06-01` przechowuje tożsamości zarządzane przypisane przez użytkownika w `userAssignedIdentities` wartości w formacie słownika, w przeciwieństwie do `identityIds` wartości w formacie tablicy używanym w wersji interfejsu API `2017-12-01` .
   
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj maszynę wirtualną za pomocą zwinięcia, aby wywołać punkt końcowy Azure Resource Manager REST w celu wyłączenia zarządzanej tożsamości przypisanej do systemu.  W poniższym przykładzie jest wyłączona tożsamość zarządzana przypisana przez system zgodnie z definicją w treści żądania przez wartość `{"identity":{"type":"None"}}` z maszyny wirtualnej o nazwie *myVM*.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, należy listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli do maszyny wirtualnej są przypisane skojarzone tożsamości przypisane przez użytkownika, które zostały określone w `identity` wartości odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przez użytkownika podczas wyłączania tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, która ma tożsamości zarządzane przypisane przez użytkownika, Usuń `SystemAssigned` z `{"identity":{"type:" "}}` wartości, zachowując `UserAssigned` wartość i `userAssignedIdentities` wartości słownikowe, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub starszej, Zachowaj `identityIds` tablicę.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika na maszynie wirtualnej platformy Azure przy użyciu zwinięcia, aby wykonać wywołania do punktu końcowego REST Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic#az-network-nic-create) dla maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji dostępnych tutaj: [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz maszynę wirtualną przy użyciu ZWINIĘCIEa, aby wywołać punkt końcowy usługi REST Azure Resource Manager. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* *w grupie zasobów Grupa zasobu z* tożsamością zarządzaną przez użytkownika `ID1` , jak określono w treści żądania przez wartość `"identity":{"type":"UserAssigned"}` . Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
 
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji znalezionych w tym miejscu, [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby nie usuwać istniejących tożsamości zarządzanych użytkownika lub systemu przypisanych do maszyny wirtualnej, należy wyświetlić listę typów tożsamości przypisanych do maszyny wirtualnej za pomocą następującego polecenia ZWINIĘCIE. Jeśli masz zarządzane tożsamości przypisane do zestawu skalowania maszyn wirtualnych, zostaną one wyświetlone w obszarze w polu `identity` wartość.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.

    Jeśli masz przypisane tożsamości zarządzane przez użytkownika lub system do maszyny wirtualnej zgodnie z opisem w `identity` wartości w odpowiedzi, przejdź do kroku 5, w którym pokazano, jak zachować tożsamość zarządzaną przypisaną przez system podczas dodawania tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej.

4. Jeśli nie masz żadnych tożsamości zarządzanych przypisanych przez użytkownika do maszyny wirtualnej, użyj następującego polecenia ZWINIĘCIE, aby wywołać punkt końcowy Azure Resource Manager REST w celu przypisania do maszyny wirtualnej pierwszej tożsamości zarządzanej przypisanej przez użytkownika.

   Poniższy przykład przypisuje tożsamość zarządzaną przypisaną przez użytkownika `ID1` do maszyny wirtualnej o nazwie *myVM* w grupie zasobów Grupa zasobu.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        |
 
   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Jeśli masz istniejącą tożsamość zarządzaną przez użytkownika lub przypisanej do systemu przypisanej do maszyny wirtualnej:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną przez użytkownika do `userAssignedIdentities` wartości dictionary.
    
   Jeśli na przykład masz tożsamość zarządzaną przez system i tożsamość zarządzaną przypisaną przez użytkownika, która jest `ID1` aktualnie przypisana do maszyny wirtualnej i chcesz dodać do niej tożsamość zarządzaną przez użytkownika `ID2` :

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Zachowaj zarządzane tożsamości przypisane przez użytkownika, które chcesz zachować w `identityIds` wartości tablicy podczas dodawania nowej tożsamości zarządzanej przypisanej przez użytkownika.

   Jeśli na przykład masz tożsamość zarządzaną przez system i tożsamość zarządzaną przypisaną przez użytkownika, która jest `ID1` aktualnie przypisana do maszyny wirtualnej i chcesz dodać do niej tożsamość zarządzaną przez użytkownika `ID2` : 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które mają być przypisane do maszyny wirtualnej lub nie można usunąć tożsamości zarządzanej przypisanej przez system, należy wyświetlić listę zarządzanych tożsamości za pomocą następującego polecenia ZWINIĘCIE: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.
 
   Jeśli masz zarządzane tożsamości przypisane do maszyny wirtualnej, są one wyświetlane w odpowiedzi w `identity` wartości.

   Na przykład jeśli masz przypisane przez użytkownika tożsamości zarządzane `ID1` i `ID2` przypisane do maszyny wirtualnej, a chcesz zachować tylko `ID1` przypisane i zachować tożsamość przypisaną przez system:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Przechowuj tylko tożsamości zarządzane przypisane przez użytkownika, które chcesz przechowywać w `identityIds` tablicy:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Jeśli maszyna wirtualna ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, możesz usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając do używania tylko tożsamości zarządzanej przypisanej do systemu przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Jeśli maszyna wirtualna ma tylko tożsamości zarządzane przypisane przez użytkownika, a chcesz je usunąć, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu.| 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat tworzenia, wyświetlania lub usuwania tożsamości zarządzanych przez użytkownika przy użyciu usługi REST, zobacz:

- [Utwórz, Wyświetl lub Usuń zarządzane tożsamości przypisane przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
