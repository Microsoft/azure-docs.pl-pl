---
title: Konfigurowanie tożsamości zarządzanych na maszynie wirtualnej platformy Azure przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez system i użytkowników na maszynie wirtualnej platformy Azure przy użyciu narzędzia CURL do tworzenia wywołań interfejsu API REST.
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
ms.openlocfilehash: 9b74e7d312133c24daad448e029a3c3d4cbdce79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773085"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość systemu w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. 

W tym artykule, używając narzędzia CURL do wykonywania wywołań do punktu końcowego REST usługi Azure Resource Manager, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji na zasobach platformy Azure na maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej platformy Azure

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz Co to są tożsamości zarządzane [dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o typach tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika, zobacz [Typy tożsamości zarządzanych.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej platformy Azure przy użyciu programu CURL w celu tworzenia wywołań do Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto musi mieć przypisanie roli [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az_group_create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz interfejs [sieciowy dla](/cli/azure/network/nic#az_network_nic_create) maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku Autoryzacja, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Za Azure Cloud Shell utwórz maszynę wirtualną przy użyciu narzędzia CURL, aby wywołać Azure Resource Manager REST. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z przypisaną przez system tożsamością zarządzaną, która została zidentyfikowana w treści żądania za pomocą wartości `"identity":{"type":"SystemAssigned"}` . Zastąp wartość wartością otrzymaną w poprzednim kroku podczas zażądania tokenu dostępu bearer i wartością odpowiednią `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` dla Twojego środowiska.

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 
   
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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości przypisanej przez system na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie aprowizowana bez tej tożsamości, Twoje konto wymaga przypisania roli [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) maszyny wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z przypisaną przez system tożsamością zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia curl, aby wywołać punkt końcowy REST usługi Azure Resource Manager, aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej zidentyfikowaną w treści żądania przez wartość dla maszyny wirtualnej o `{"identity":{"type":"SystemAssigned"}` nazwie *myVM.*  Zastąp wartość wartością otrzymaną w poprzednim kroku podczas zażądania tokenu dostępu bearer i wartością odpowiednią `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` dla Twojego środowiska.
   
   > [!IMPORTANT]
   > Aby mieć pewność, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, musisz wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia programu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli masz przypisane przez użytkownika tożsamości zarządzane przypisane do maszyny wirtualnej określone w wartości w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas włączania tożsamości zarządzanej przypisanej przez system na maszynie `identity` wirtualnej.

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 
   
   **Treść żądania**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej z istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy `SystemAssigned` dodać wartość do wartości `type` .  
   
   Jeśli na przykład maszyna wirtualna ma przypisane przez użytkownika tożsamości zarządzane i jest do nich przypisana, a chcesz dodać tożsamość zarządzaną przypisaną przez system do maszyny wirtualnej, użyj następującego wywołania `ID1` `ID2` programu CURL. Zastąp `<ACCESS TOKEN>` wartości i `<SUBSCRIPTION ID>` wartościami odpowiednimi dla Twojego środowiska.

   Wersja interfejsu API przechowuje tożsamości zarządzane przypisane przez użytkownika w wartości w formacie słownika, w przeciwieństwie do wartości w formacie tablicy używanym `2018-06-01` `userAssignedIdentities` w wersji `identityIds` interfejsu API `2017-12-01` .
   
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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto musi mieć przypisanie roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.  Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku Autoryzacja, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj maszynę wirtualną przy użyciu narzędzia CURL, aby Azure Resource Manager punkt końcowy REST, aby wyłączyć tożsamość zarządzaną przypisaną przez system.  Poniższy przykład wyłącza tożsamość zarządzaną przypisaną przez system zidentyfikowaną w treści żądania przez wartość z maszyny wirtualnej `{"identity":{"type":"None"}}` *o nazwie myVM.*  Zastąp wartość wartością otrzymaną w poprzednim kroku podczas zażądania tokenu dostępu bearer i wartością odpowiednią `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` dla Twojego środowiska.

   > [!IMPORTANT]
   > Aby mieć pewność, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, musisz wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia programu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli masz przypisane przez użytkownika tożsamości zarządzane przypisane do maszyny wirtualnej określone w wartości w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas wyłączania tożsamości zarządzanej przypisanej przez system na maszynie `identity` wirtualnej.

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, która ma przypisane przez użytkownika tożsamości zarządzane, usuń z wartości przy zachowaniu wartości i wartości słownika, jeśli używasz interfejsu API w wersji `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **2018-06-01.** Jeśli używasz interfejsu **API w wersji 2017-12-01** lub starszej, zachowaj `identityIds` tablicę .

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika na maszynie wirtualnej platformy Azure przy użyciu narzędzia CURL w celu tworzenia wywołań do Azure Resource Manager REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej, Twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z przypisaną przez system tożsamością zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz interfejs [sieciowy dla](/cli/azure/network/nic#az_network_nic_create) maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z przypisaną przez system tożsamością zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji podanych tutaj: [Create a user-assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)(Tworzenie tożsamości zarządzanej przypisanej przez użytkownika).

5. Utwórz maszynę wirtualną przy użyciu narzędzia CURL, aby wywołać Azure Resource Manager REST. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w grupie zasobów *myResourceGroup* z tożsamością zarządzaną przypisaną przez użytkownika, zidentyfikowaną w treści żądania przez `ID1` wartość `"identity":{"type":"UserAssigned"}` . Zastąp wartość wartością otrzymaną w poprzednim kroku podczas zażądania tokenu dostępu bearer i wartością odpowiednią `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` dla Twojego środowiska.
 
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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej, Twoje konto wymaga przypisań ról Współautor maszyny wirtualnej i [Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) tożsamości zarządzanej. Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

1. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku Autoryzacja, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji podanych tutaj: [Create a user-assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)(Tworzenie tożsamości zarządzanej przypisanej przez użytkownika).

3. Aby mieć pewność, że nie usuniesz istniejących tożsamości zarządzanych przypisanych do użytkownika lub systemu przypisanych do maszyny wirtualnej, musisz wyświetlić listę typów tożsamości przypisanych do maszyny wirtualnej przy użyciu następującego polecenia CURL. Jeśli masz tożsamości zarządzane przypisane do zestawu skalowania maszyn wirtualnych, są one wyświetlane w obszarze w `identity` wartości .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.

    Jeśli do maszyny wirtualnej są przypisane jakiekolwiek tożsamości zarządzane przypisane przez użytkownika lub system zgodnie z wartością w odpowiedzi, przejdź do kroku 5, który pokazuje, jak zachować tożsamość zarządzaną przypisaną przez system podczas dodawania tożsamości zarządzanej przypisanej przez użytkownika na maszynie `identity` wirtualnej.

4. Jeśli nie masz żadnych tożsamości zarządzanych przypisanych przez użytkownika do maszyny wirtualnej, użyj następującego polecenia curl, aby wywołać punkt końcowy REST usługi Azure Resource Manager w celu przypisania pierwszej tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej.

   W poniższym przykładzie przypisano tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej o nazwie myVM w grupie zasobów `ID1` *myResourceGroup.*   Zastąp wartość wartością otrzymaną w poprzednim kroku podczas zażądania tokenu dostępu bearer i wartością odpowiednią `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` dla Twojego środowiska.

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        |
 
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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

5. Jeśli masz istniejącą tożsamość zarządzaną przypisaną przez użytkownika lub systemową do maszyny wirtualnej:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną przez użytkownika do `userAssignedIdentities` wartości słownika.
    
   Jeśli na przykład masz przypisaną przez system tożsamość zarządzaną i tożsamość zarządzaną przypisaną przez użytkownika aktualnie przypisaną do maszyny wirtualnej i chcesz dodać do niego tożsamość zarządzaną przypisaną `ID1` `ID2` przez użytkownika:

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

   Zachowaj tożsamości zarządzane przypisane przez użytkownika, które chcesz zachować w wartości tablicy podczas dodawania nowej tożsamości zarządzanej `identityIds` przypisanej przez użytkownika.

   Jeśli na przykład tożsamość zarządzana przypisana przez system i tożsamość zarządzana przypisana przez użytkownika są obecnie przypisane do maszyny wirtualnej i chcesz dodać do niego tożsamość zarządzaną przypisaną `ID1` `ID2` przez użytkownika: 

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto musi mieć przypisanie roli [Współautor maszyny](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) wirtualnej.

1. Pobierz token dostępu bearer, którego użyjemy w następnym kroku w nagłówku Autoryzacja, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby mieć pewność, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które chcesz przypisać do maszyny wirtualnej, ani nie usuniesz tożsamości zarządzanej przypisanej przez system, musisz wyświetlić listę tożsamości zarządzanych przy użyciu następującego polecenia curl: 

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.
 
   Jeśli masz tożsamości zarządzane przypisane do maszyny wirtualnej, są one wyświetlane w odpowiedzi w `identity` wartości .

   Jeśli na przykład masz przypisane przez użytkownika tożsamości zarządzane i przypisane do maszyny wirtualnej, a chcesz zachować tylko przypisaną tożsamość przypisaną przez `ID1` `ID2` `ID1` system:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, który chcesz usunąć:

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

   Zachowaj tylko tożsamości zarządzane przypisane przez użytkownika, które chcesz zachować w `identityIds` tablicy:

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
   |*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.        | 

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

Jeśli maszyna wirtualna ma przypisane przez system i przypisane przez użytkownika tożsamości zarządzane, możesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej przez system przy użyciu następującego polecenia:

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
|*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Jeśli maszyna wirtualna ma tylko tożsamości zarządzane przypisane przez użytkownika i chcesz je wszystkie usunąć, użyj następującego polecenia:

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
|*Autoryzacja*     | Wymagane. Ustaw na prawidłowy `Bearer` token dostępu.| 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat tworzenia, tworzenia, tworzenia listy lub usuwania tożsamości zarządzanych przypisanych przez użytkownika przy użyciu interfejsu REST, zobacz:

- [Tworzenie, tworzenie listy lub usuwanie tożsamości zarządzanych przypisanych przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
