---
title: Konfigurowanie zarządzanych tożsamości w usłudze Azure VMSS przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika w usłudze Azure VMSS przy użyciu zwinięcia do wykonywania wywołań interfejsu API REST.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253340"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawie skalowania maszyn wirtualnych przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością systemu w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule przy użyciu zapełniania w celu wykonywania wywołań do punktu końcowego Azure Resource Manager REST dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu oparte na rolach na platformie Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia tożsamości zarządzanej system i/lub przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych.
    - Rola [współautor tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - Rola [operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Jeśli używasz systemu Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) w Azure Portal.
- [Zainstaluj konsolę lokalną interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), jeśli używasz [podsystemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [systemu operacyjnego dystrybucji Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz lokalnej konsoli interfejsu wiersza polecenia platformy Azure, zaloguj się do platformy `az login` Azure przy użyciu konta skojarzonego z subskrypcją platformy Azure, do której chcesz zarządzać tożsamościami zarządzanymi przez system lub użytkownika.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system w zestawie skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wykonać wywołania do punktu końcowego Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system, należy utworzyć zestaw skalowania maszyn wirtualnych i pobrać token dostępu służący do wywoływania punktu końcowego Menedżer zasobów przy użyciu wartości typu tożsamości zarządzanej przypisanej do systemu.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) na potrzeby zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanych z nim zasobów przy użyciu polecenia [AZ Group Create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla zestawu skalowania maszyn wirtualnych:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz zestaw skalowania maszyn wirtualnych przy użyciu funkcji ZWINIĘCIE, aby wywołać punkt końcowy usługi REST Azure Resource Manager. Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* w liście *zasobów* przy użyciu tożsamości zarządzanej przypisanej do systemu, jak określono w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

2. Użyj następującego polecenia zapełnienia, aby wywołać punkt końcowy Azure Resource Manager REST w celu włączenia zarządzanej tożsamości przypisanej przez system do zestawu skalowania maszyn wirtualnych, zgodnie z definicją w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` zestawu skalowania maszyn wirtualnych o nazwie *myVMSS*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika przypisanych do zestawu skalowania maszyn wirtualnych, należy listę zarządzanych tożsamości przypisanych przez użytkownika przy użyciu tego polecenia `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`zwinięcie:. Jeśli do zestawu skalowania maszyn wirtualnych są przypisane skojarzone tożsamości przypisane przez użytkownika, które zostały określone w `identity` wartości odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamość zarządzaną przez użytkownika podczas włączania zarządzanej tożsamości przypisanej do systemu w zestawie skalowania maszyn wirtualnych.

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną przez system w zestawie skalowania maszyn wirtualnych z istniejącymi tożsamościami zarządzanymi przypisanymi przez `SystemAssigned` użytkownika, `type` należy dodać do tej wartości.  
   
   Na przykład jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości `ID1` przypisane przez użytkownika i `ID2` jest do niego przypisany, a chcesz dodać tożsamość zarządzaną przez system do zestawu skalowania maszyn wirtualnych, użyj następującego elementu zawieszania. Zamień `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` na wartości odpowiednie dla danego środowiska.

   Wersja `2018-06-01` interfejsu API przechowuje tożsamości zarządzane przypisane przez użytkownika w `userAssignedIdentities` wartości w formacie słownika, w przeciwieństwie do `identityIds` wartości w formacie tablicy używanym w wersji `2017-12-01`interfejsu API.
   
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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
 
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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

Aby wyłączyć tożsamość przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych, należy uzyskać token dostępu, a następnie użyć ZWINIĘCIEa do wywołania punktu końcowego Menedżer zasobów REST w celu zaktualizowania typu tożsamości `None`na.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj zestaw skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wywołać punkt końcowy Azure Resource Manager REST w celu wyłączenia zarządzanej tożsamości przypisanej do systemu.  W poniższym przykładzie jest wyłączona tożsamość zarządzana przypisana przez system zgodnie z definicją w treści `{"identity":{"type":"None"}}` żądania przez wartość z zestawu skalowania maszyn wirtualnych o nazwie *myVMSS*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika przypisanych do zestawu skalowania maszyn wirtualnych, należy listę zarządzanych tożsamości przypisanych przez użytkownika przy użyciu tego polecenia `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`zwinięcie:. Jeśli masz zarządzaną tożsamość przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas usuwania zarządzanej tożsamości przypisanej do systemu z zestawu skalowania maszyn wirtualnych.

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych z tożsamościami zarządzanymi przez użytkownika `SystemAssigned` , Usuń `{"identity":{"type:" "}}` z tej wartości, `UserAssigned` zachowując wartość `userAssignedIdentities` i wartości słownikowe, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub starszej, Zachowaj `identityIds` tablicę.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika w zestawie skalowania maszyn wirtualnych przy użyciu programu ZWINIĘCIE, aby wykonać wywołania do punktu końcowego usługi REST Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla zestawu skalowania maszyn wirtualnych:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji w celu utworzenia zestawu skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji dostępnych tutaj: [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz zestaw skalowania maszyn wirtualnych przy użyciu funkcji ZWINIĘCIE, aby wywołać punkt końcowy usługi REST Azure Resource Manager. W poniższym przykładzie tworzony jest zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* *w grupie zasobów Grupa zasobu z* tożsamością `ID1`zarządzaną przez użytkownika, jak określono w treści żądania przez wartość. `"identity":{"type":"UserAssigned"}` Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.
 
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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
 
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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |   
 

4. Jeśli nie masz żadnych tożsamości zarządzanych użytkownika lub systemu przypisanych do zestawu skalowania maszyn wirtualnych, użyj następującego polecenia, aby wywołać Azure Resource Manager punkt końcowy REST, aby przypisać do zestawu skalowania maszyn wirtualnych pierwszą tożsamość zarządzaną przez użytkownika.  Jeśli masz przypisane tożsamości zarządzane przez użytkownika lub system do zestawu skalowania maszyn wirtualnych, przejdź do kroku 5, w którym pokazano, jak dodać wiele zarządzanych tożsamości przypisanych do użytkownika do zestawu skalowania maszyn wirtualnych, jednocześnie zachowując także tożsamość zarządzaną przypisaną przez system.

   Poniższy przykład przypisuje tożsamość zarządzaną przypisaną przez użytkownika `ID1` do zestawu skalowania maszyn wirtualnych o nazwie *myVMSS* *w grupie zasobów Grupa zasobu.*  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz `<SUBSCRIPTION ID>` wartości odpowiedniej dla danego środowiska.

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

   Jeśli na przykład masz tożsamość zarządzaną przez system i tożsamość `ID1` zarządzaną przypisaną przez użytkownika, która jest aktualnie przypisana do skali maszyny wirtualnej i chcesz dodać do niej tożsamość `ID2` zarządzaną przez użytkownika:

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

   Zachowaj zarządzane tożsamości przypisane przez użytkownika, które chcesz zachować w wartości `identityIds` tablicy podczas dodawania nowej tożsamości zarządzanej przypisanej przez użytkownika.

   Jeśli na przykład masz tożsamość przypisaną przez system i tożsamość `ID1` zarządzaną przypisaną przez użytkownika, która jest aktualnie przypisana do zestawu skalowania maszyn wirtualnych i chcesz dodać do niej tożsamość `ID2` zarządzaną przez użytkownika:

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
   
   Jeśli masz zarządzane tożsamości przypisane do maszyny wirtualnej, są one wyświetlane w odpowiedzi w `identity` wartości. 
    
   Na przykład jeśli masz przypisane przez użytkownika tożsamości `ID1` zarządzane i `ID2` przypisane do zestawu skalowania maszyn wirtualnych, a chcesz zachować `ID1` tylko przypisane i zachować tożsamość zarządzaną przypisaną przez system:

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
   |*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
|*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
|*Zgody*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
