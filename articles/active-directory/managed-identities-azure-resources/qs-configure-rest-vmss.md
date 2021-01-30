---
title: Konfigurowanie zarządzanych tożsamości w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu programu ZWINIĘCIE, aby umożliwić wywoływanie interfejsu API REST.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14a6a82535ac5a7d36213b082d08aedbb44da5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090624"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawie skalowania maszyn wirtualnych przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością systemu w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule przy użyciu zapełniania w celu wykonywania wywołań do punktu końcowego Azure Resource Manager REST dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](overview.md). Aby dowiedzieć się więcej o zarządzanych typach tożsamości przypisanych do systemu i przez użytkownika, zobacz [typy tożsamości zarządzanych](overview.md#managed-identity-types).

- Aby wykonać operacje zarządzania w tym artykule, Twoje konto wymaga następujących przypisań ról platformy Azure:

  - [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia tożsamości zarządzanej system i/lub przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych.

  - Rola [współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.

  - Rola [operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

  > [!NOTE]
  > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system w zestawie skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wykonać wywołania do punktu końcowego Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system, należy utworzyć zestaw skalowania maszyn wirtualnych i pobrać token dostępu służący do wywoływania punktu końcowego Menedżer zasobów przy użyciu wartości typu tożsamości zarządzanej przypisanej do systemu.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) na potrzeby zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanych z nim zasobów przy użyciu polecenia [AZ Group Create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic#az-network-nic-create) dla zestawu skalowania maszyn wirtualnych:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Za pomocą Azure Cloud Shell Utwórz zestaw skalowania maszyn wirtualnych przy użyciu zwinięcia, aby wywołać Azure Resource Manager punkt końcowy REST. Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* w liście *zasobów* przy użyciu tożsamości zarządzanej przypisanej do systemu, jak określono w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}` . Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych

Aby włączyć tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych, należy uzyskać token dostępu, a następnie użyć zawieszania do wywołania punktu końcowego Menedżer zasobów REST w celu zaktualizowania typu tożsamości.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia zapełnienia, aby wywołać punkt końcowy Azure Resource Manager REST w celu włączenia zarządzanej tożsamości przypisanej przez system do zestawu skalowania maszyn wirtualnych, zgodnie z definicją w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` zestawu skalowania maszyn wirtualnych o nazwie *myVMSS*.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika przypisanych do zestawu skalowania maszyn wirtualnych, należy listę zarządzanych tożsamości przypisanych przez użytkownika przy użyciu tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli do zestawu skalowania maszyn wirtualnych są przypisane skojarzone tożsamości przypisane przez użytkownika, które zostały określone w `identity` wartości odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamość zarządzaną przez użytkownika podczas włączania zarządzanej tożsamości przypisanej do systemu w zestawie skalowania maszyn wirtualnych.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Aby włączyć tożsamość zarządzaną przypisaną przez system w zestawie skalowania maszyn wirtualnych z istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy dodać `SystemAssigned` do tej `type` wartości.  
   
   Na przykład jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości przypisane przez użytkownika i jest `ID1` do `ID2` niego przypisany, a chcesz dodać tożsamość zarządzaną przez system do zestawu skalowania maszyn wirtualnych, użyj następującego elementu zawieszania. Zamień `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` na wartości odpowiednie dla danego środowiska.

   Wersja interfejsu API `2018-06-01` przechowuje tożsamości zarządzane przypisane przez użytkownika w `userAssignedIdentities` wartości w formacie słownika, w przeciwieństwie do `identityIds` wartości w formacie tablicy używanym w wersji interfejsu API `2017-12-01` .
   
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Wyłącz tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych

Aby wyłączyć tożsamość przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych, należy uzyskać token dostępu, a następnie użyć ZWINIĘCIEa do wywołania punktu końcowego Menedżer zasobów REST w celu zaktualizowania typu tożsamości na `None` .

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj zestaw skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wywołać punkt końcowy Azure Resource Manager REST w celu wyłączenia zarządzanej tożsamości przypisanej do systemu.  W poniższym przykładzie jest wyłączona tożsamość zarządzana przypisana przez system zgodnie z definicją w treści żądania przez wartość `{"identity":{"type":"None"}}` z zestawu skalowania maszyn wirtualnych o nazwie *myVMSS*.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika przypisanych do zestawu skalowania maszyn wirtualnych, należy listę zarządzanych tożsamości przypisanych przez użytkownika przy użyciu tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Jeśli masz zarządzaną tożsamość przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas usuwania zarządzanej tożsamości przypisanej do systemu z zestawu skalowania maszyn wirtualnych.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych z tożsamościami zarządzanymi przez użytkownika, Usuń `SystemAssigned` z tej wartości, zachowując `{"identity":{"type:" "}}` `UserAssigned` wartość i `userAssignedIdentities` wartości słownikowe, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub starszej, Zachowaj `identityIds` tablicę.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika w zestawie skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wykonać wywołania do punktu końcowego usługi REST Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic#az-network-nic-create) dla zestawu skalowania maszyn wirtualnych:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji dostępnych tutaj: [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz zestaw skalowania maszyn wirtualnych przy użyciu funkcji ZWINIĘCIE, aby wywołać punkt końcowy usługi REST Azure Resource Manager. W poniższym przykładzie tworzony jest zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* *w grupie zasobów Grupa zasobu z* tożsamością zarządzaną przez użytkownika `ID1` , jak określono w treści żądania przez wartość `"identity":{"type":"UserAssigned"}` . Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
 
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. |
 
   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji znalezionych w tym miejscu, [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby nie usuwać istniejących tożsamości zarządzanych użytkownika lub systemu przypisanych do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę typów tożsamości przypisanych do zestawu skalowania maszyn wirtualnych za pomocą następującego polecenia ZWINIĘCIE. Jeśli zarządzane tożsamości są przypisane do zestawu skalowania maszyn wirtualnych, są one wymienione w polu `identity` wartość.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. |   
 

4. Jeśli nie masz żadnych tożsamości zarządzanych użytkownika lub systemu przypisanych do zestawu skalowania maszyn wirtualnych, użyj następującego polecenia, aby wywołać Azure Resource Manager punkt końcowy REST, aby przypisać do zestawu skalowania maszyn wirtualnych pierwszą tożsamość zarządzaną przez użytkownika.  Jeśli masz przypisane tożsamości zarządzane przez użytkownika lub system do zestawu skalowania maszyn wirtualnych, przejdź do kroku 5, w którym pokazano, jak dodać wiele zarządzanych tożsamości przypisanych do użytkownika do zestawu skalowania maszyn wirtualnych, jednocześnie zachowując także tożsamość zarządzaną przypisaną przez system.

   Poniższy przykład przypisuje tożsamość zarządzaną przypisaną przez użytkownika `ID1` do zestawu skalowania maszyn wirtualnych o nazwie *myVMSS* w grupie zasobów Grupa zasobu.  Zamień na `<ACCESS TOKEN>` wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Jeśli masz istniejącą tożsamość zarządzaną przez użytkownika lub przypisanej do systemu przypisany do zestawu skalowania maszyn wirtualnych:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną przez użytkownika do `userAssignedIdentities` wartości dictionary.

   Jeśli na przykład masz tożsamość zarządzaną przez system i tożsamość zarządzaną przypisaną przez użytkownika, która jest `ID1` aktualnie przypisana do skali maszyny wirtualnej i chcesz dodać do niej tożsamość zarządzaną przez użytkownika `ID2` :

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Jeśli na przykład masz tożsamość przypisaną przez system i tożsamość zarządzaną przypisaną przez użytkownika, która jest `ID1` aktualnie przypisana do zestawu skalowania maszyn wirtualnych i chcesz dodać do niej tożsamość zarządzaną przez użytkownika `ID2` :

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie zostaną usunięte żadne istniejące tożsamości zarządzane przypisane przez użytkownika, które mają być przypisane do zestawu skalowania maszyn wirtualnych, lub usunąć tożsamość zarządzaną przypisaną przez system, należy wyświetlić listę zarządzanych tożsamości za pomocą następującego polecenia ZWINIĘCIE:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagane. Ustaw prawidłowy `Bearer` token dostępu. |
   
   Jeśli masz zarządzane tożsamości przypisane do maszyny wirtualnej, są one wyświetlane w odpowiedzi w `identity` wartości. 
    
   Na przykład jeśli masz przypisane przez użytkownika tożsamości zarządzane `ID1` i `ID2` przypisane do zestawu skalowania maszyn wirtualnych, a chcesz zachować tylko `ID1` przypisane i zachować tożsamość zarządzaną przypisaną przez system:

   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, można usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając do użycia tylko przypisanego do systemu przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Jeśli zestaw skalowania maszyn wirtualnych ma tylko tożsamości zarządzane przypisane przez użytkownika i chcesz je usunąć, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat tworzenia, wyświetlania lub usuwania tożsamości zarządzanych przez użytkownika przy użyciu usługi REST, zobacz:

- [Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
