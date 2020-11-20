---
title: Szybki Start — Konfigurowanie IoT Hub Device Provisioning platformy Azure przy użyciu szablonu Azure Resource Manager
description: Przewodnik Szybki Start platformy Azure — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu szablonu
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 59b68730710de189d690c367e2c04e3a433c9af2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964749"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service przy użyciu szablonu Azure Resource Manager

Przy użyciu usługi [Azure Resource Manager](../azure-resource-manager/management/overview.md) można skonfigurować programowo zasoby w chmurze platformy Azure niezbędne do aprowizacji urządzeń. W tych krokach przedstawiono sposób tworzenia Centrum IoT i nowego IoT Hub Device Provisioning Service oraz łączenia tych dwóch usług przy użyciu szablonu Azure Resource Manager. Ten przewodnik Szybki Start korzysta z [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) w celu wykonania czynności programistycznych niezbędnych do utworzenia grupy zasobów i wdrożenia szablonu, ale w celu wykonania tych kroków i wdrożenia szablonu można łatwo użyć [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [programu PowerShell](../azure-resource-manager/templates/deploy-powershell.md), platformy .NET, języka Ruby lub innych języków programowania. 


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ten przewodnik Szybki Start wymaga lokalnego uruchomienia interfejsu wiersza polecenia platformy Azure. Musi być zainstalowany wiersz polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Logowanie się do platformy Azure i tworzenie grupy zasobów

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia uruchom [polecenie logowania][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure][lnk-az-account-command] dostępnych do użycia:
    
    ```azurecli
    az account list 
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Podczas tworzenia zasobów w chmurze platformy Azure, takich jak centra IoT i usługi aprowizacji, można tworzyć je w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

Użyj szablonu JSON, aby utworzyć usługę inicjowania i połączone centrum IoT w grupie zasobów. Można również użyć szablonu usługi Azure Resource Manager, aby wprowadzić zmiany w istniejącej usłudze aprowizacji lub centrum IoT.

1. Użyj edytora tekstu, aby utworzyć szablon usługi Azure Resource Manager o nazwie **template.json** z następującym szkieletem zawartości. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Zastąp sekcję **parameters** następującą zawartością. Sekcja Parameters definiuje parametry, których wartości mogą być przesyłane z innego pliku. W tej sekcji zdefiniowano nazwę Centrum IoT i usługi aprowizacji, które mają zostać utworzone. Definiuje również lokalizację centrum IoT i usługi aprowizacji. Wartości będą ograniczone do regionów świadczenia usługi Azure, które obsługują centra IoT i usługi aprowizacji. Aby uzyskać listę obsługiwanych lokalizacji usługi Device Provisioning, można uruchomić następujące polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub odwiedzić stronę [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukać frazę „usługa Device Provisioning”.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Zastąp sekcję **variables** następującą zawartością. Ta sekcja definiuje wartości, które są później używane do tworzenia parametrów połączenia z centrum IoT, które jest wymagane do połączenia usługi aprowizacji i centrum IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Aby utworzyć centrum IoT, dodaj następujące wiersze do kolekcji **resources**. KOD JSON określa minimalne właściwości wymagane do utworzenia Centrum IoT Hub. Wartości **nazwy** i **lokalizacji** zostaną przesłane jako parametry z innego pliku. Aby dowiedzieć się więcej o właściwościach, które można określić dla Centrum IoT w szablonie, zobacz [Microsoft. Devices/IotHubs Template Reference](/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Aby utworzyć usługę aprowizacji, dodaj następujące wiersze po specyfikacji centrum IoT w kolekcji **resources**. **Nazwa** i **Lokalizacja** usługi aprowizacji zostaną przesłane jako parametry. Kolekcja **iotHubs** określa centra IoT, które mają zostać połączone z usługą aprowizacji. Należy co najmniej określić właściwości **connectionString** i **location** dla każdego połączonego centrum IoT. Można również ustawić właściwości, takie jak **allocationWeight** i **applyAllocationPolicy** dla każdego centrum IoT, a także właściwości, takie jak **allocationPolicy** i **authorizationPolicies**, dla usługi aprowizacji. Więcej informacji zawiera dokumentacja dotycząca szablonów [Microsoft.Devices/provisioningServices](/azure/templates/microsoft.devices/provisioningservices).

   Właściwość **dependsOn** służy do zapewnienia, że usługa Resource Manager utworzy centrum IoT zanim utworzy usługę aprowizacji. Szablon wymaga parametrów połączenia z centrum IoT, aby móc określić połączenie z usługą, dlatego centrum i jego klucze muszą zostać utworzone w pierwszej kolejności. Szablon używa funkcji, takich jak **concat** i **listKeys** , do tworzenia parametrów połączenia z zmiennych sparametryzowanych. Więcej informacji zawiera dokumentacja [funkcji szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-functions.md).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Zapisz plik szablonu. Ukończony szablon powinien wyglądać następująco:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Tworzenie pliku parametrów usługi Resource Manager

Szablon zdefiniowany w ostatnim kroku używa parametrów, aby określić nazwę Centrum IoT, nazwę usługi aprowizacji i lokalizację (region platformy Azure), aby je utworzyć. Te parametry są przekazywane do szablonu z osobnego pliku. Umożliwia to ponowne użycie tego samego szablonu podczas wielu wdrożeń. Aby utworzyć plik parametrów, wykonaj następujące kroki:

1. Użyj edytora tekstów, aby utworzyć plik parametrów usługi Azure Resource Manager o nazwie **parameters.json** z następującym szkieletem zawartości: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Dodaj wartość **iotHubName** do sekcji parametrów.  Nazwa Centrum IoT musi być globalnie unikatowa na platformie Azure, dlatego możesz chcieć dodać unikatowy prefiks lub sufiks do nazwy przykładu lub wybrać nową nazwę. Upewnij się, że nazwa jest zgodna z właściwymi konwencjami nazewnictwa Centrum IoT: długość powinna wynosić 3-50 znaków i może zawierać tylko znaki alfanumeryczne wielkie lub małe litery ("-"). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Dodaj wartość **provisioningServiceName** do sekcji parametrów. Należy również wybrać globalnie unikatową nazwę dla usługi aprowizacji. Upewnij się, że są zgodne z właściwymi konwencjami nazewnictwa dla IoT Hub Device Provisioning Service: długość powinna wynosić 3-64 znaków i może zawierać tylko wielkie lub małe litery lub łączniki ("-").

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Dodaj wartość **hubLocation** do sekcji parametrów. Ta wartość określa również lokalizację centrum IoT i usługi aprowizacji. Ta wartość musi odpowiadać jednej z lokalizacji określonej w kolekcji **allowedValues** w definicji parametrów w pliku szablonu. Ta kolekcja ogranicza wartości do lokalizacji platformy Azure, które obsługują centra IoT i usługi aprowizacji. Aby uzyskać listę obsługiwanych lokalizacji dla usługi Device Provisioning, możesz uruchomić polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przejść na stronę [stanu platformy Azure](https://azure.microsoft.com/status/) i wyszukać "usługa Device Provisioning".

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Zapisz plik. 


> [!IMPORTANT]
> Centrum IoT i usługa aprowizacji będą publicznie wykrywalne jako punkty końcowe DNS, dlatego unikaj wprowadzania jakichkolwiek poufnych informacji podczas nadawania im nazw.
>

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Użyj następujących poleceń interfejsu wiersza polecenia Azure, aby wdrożyć szablony i zweryfikować wdrożenie.

1. Aby wdrożyć szablon, przejdź do folderu zawierającego pliki szablonu i parametrów, a następnie uruchom następujące [polecenie, aby rozpocząć wdrożenie](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create&preserve-view=true):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Wykonanie tej operacji może potrwać kilka minut. Po zakończeniu Wyszukaj Właściwość **provisioningState** , która zawiera wartość "powodzenie" w danych wyjściowych. 

   ![Dane wyjściowe aprowizacji](./media/quick-setup-auto-provision-rm/output.png) 


2. Aby zweryfikować wdrożenie, uruchom następujące [polecenie w celu wyświetlenia listy zasobów](/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) i wyszukaj nową usługę aprowizacji i centrum IoT w danych wyjściowych:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, możesz użyć interfejsu wiersza polecenia platformy Azure, aby [usunąć poszczególne zasoby][lnk-az-resource-command], takie jak centrum IoT lub usługa aprowizacji, lub usunąć grupę zasobów i wszystkie jej zasoby.

Aby usunąć usługę aprowizacji, uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Aby usunąć centrum IoT, uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Aby usunąć grupę zasobów i jej zasoby, uruchom następujące polecenie:

```azurecli
az group delete --name {your resource group name}
```

Grupy zasobów i poszczególne zasoby można także usuwać za pomocą Azure Portal, programu PowerShell lub interfejsów API REST, a także z obsługiwanymi zestawami SDK platformy opublikowanymi dla Azure Resource Manager lub IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Centrum IoT Hub i wystąpienie usługi Device Provisioning Service i połączono te dwa zasoby. Aby dowiedzieć się, jak używać tego Instalatora do aprowizacji symulowanego urządzenia, przejdź do przewodnika Szybki Start dotyczącego tworzenia symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący tworzenia symulowanego urządzenia](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: /cli/azure/install-az-cli2
[lnk-login-command]: /cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: /cli/azure/account
[lnk-az-register-command]: /cli/azure/provider
[lnk-az-addcomponent-command]: /cli/azure/component
[lnk-az-resource-command]: /cli/azure/resource
[lnk-az-iot-command]: /cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md