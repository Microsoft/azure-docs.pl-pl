---
title: Samouczek — Konfigurowanie routingu komunikatów dla platformy Azure IoT Hub przy użyciu szablonu Azure Resource Manager
description: Samouczek — Konfigurowanie routingu komunikatów dla platformy Azure IoT Hub przy użyciu szablonu Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621431"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Samouczek: używanie szablonu Azure Resource Manager do konfigurowania routingu komunikatów IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Routing komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Pobieranie szablonu i pliku parametrów

W drugiej części tego samouczka pobierasz i uruchamiasz aplikację Visual Studio w celu wysyłania komunikatów do IoT Hub. Istnieje folder, który zawiera plik szablonu i parametrów Azure Resource Manager, a także skrypt interfejsu wiersza polecenia platformy Azure i skryptów programu PowerShell.

Możesz teraz pobrać przykłady w [języku C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) . Rozpakuj plik master.zip. Szablon Menedżer zasobów i plik parametrów są w/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **template_iothub.json** i **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Zamierzasz utworzyć wszystkie zasoby przy użyciu szablonu Azure Resource Manager (RM). Za pomocą interfejsu wiersza polecenia platformy Azure i skryptów programu PowerShell można uruchamiać kilka wierszy naraz. Szablon RM został wdrożony w jednym kroku. W tym artykule przedstawiono oddzielne sekcje, które ułatwiają zrozumienie każdego z nich. Następnie pokazano, jak wdrożyć szablon i utworzyć urządzenie wirtualne na potrzeby testowania. Po wdrożeniu szablonu można wyświetlić konfigurację routingu wiadomości w portalu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takie jak nazwa IoT Hub i nazwa konta magazynu. Aby ułatwić nazywanie zasobów, te nazwy zasobów są konfigurowane do dołączania losowo wygenerowanej wartości alfanumerycznej z bieżącej daty/godziny. 

Jeśli zobaczysz szablon, zobaczysz, gdzie są skonfigurowane zmienne dla tych zasobów, które pobierają parametr i łączą *randomValue* z parametrem. 

W poniższej sekcji opisano używane parametry.

### <a name="parameters"></a>Parametry

Większość z tych parametrów ma wartości domyślne. Te kończące się na **_IN** są łączone z *randomValue* , aby uczynić je globalnie unikatowymi. 

**randomValue**: Ta wartość jest generowana na podstawie bieżącej daty/godziny podczas wdrażania szablonu. To pole nie znajduje się w pliku parametrów, ponieważ jest generowane w samym szablonie.

Identyfikator **subskrypcji**: to pole jest ustawione dla subskrypcji, w której wdrażasz szablon. To pole nie znajduje się w pliku parametrów, ponieważ jest ono ustawione dla Ciebie.

**IoTHubName_in**: to pole jest podstawową nazwą IoT Hub, która jest połączona z randomValue, aby była unikatowa w skali globalnej.

**Lokalizacja**: to pole jest regionem platformy Azure, w którym są wdrażane, na przykład "zachodnie".

**consumer_group**: to pole jest grupą odbiorców ustawioną dla komunikatów przychodzących przez punkt końcowy routingu. Służy do filtrowania wyników w Azure Stream Analytics. Na przykład istnieje cały strumień, w którym można uzyskać wszystko, lub jeśli masz dane przesyłane przez consumer_group do firmy **contoso**, możesz skonfigurować strumień Azure Stream Analytics (i raport Power BI), aby wyświetlić tylko te wpisy. To pole jest używane w części 2 tego samouczka.

**sku_name**: to pole jest skalowaniem dla IoT Hub. Ta wartość musi być równa S1 lub większa; warstwa Bezpłatna nie działa w tym samouczku, ponieważ nie zezwala na używanie wielu punktów końcowych.

**sku_units**: to pole zawiera **sku_name** i jest liczbą IoT Hub jednostek, które mogą być używane.

**d2c_partitions**: to pole jest liczbą partycji używanych przez strumień zdarzeń.

**storageAccountName_in**: to pole jest nazwą konta magazynu, które ma zostać utworzone. Komunikaty są kierowane do kontenera na koncie magazynu. To pole jest połączone z randomValue, aby uczynić je globalnie unikatowymi.

**storageContainerName**: to pole jest nazwą kontenera, w którym są przechowywane komunikaty kierowane do konta magazynu.

**storage_endpoint**: to pole jest nazwą punktu końcowego konta magazynu używanego przez routing wiadomości.

**service_bus_namespace_in**: to pole jest nazwą Service Bus przestrzeni nazw, która ma zostać utworzona. Ta wartość jest łączona z randomValue, aby uczynić ją globalnie unikatową.

**service_bus_queue_in**: to pole jest nazwą kolejki Service Bus używanej w przypadku komunikatów routingu. Ta wartość jest łączona z randomValue, aby uczynić ją globalnie unikatową.

**AuthRules_sb_queue**: to pole jest regułami autoryzacji dla kolejki usługi Service Bus, które są używane do pobierania parametrów połączenia dla kolejki.

### <a name="variables"></a>Zmienne

Te wartości są używane w szablonie i są głównie wyprowadzane z parametrów.

**queueAuthorizationRuleResourceId**: to pole jest identyfikatorem zasobu dla reguły autoryzacji dla kolejki Service Bus. Identyfikator ResourceId jest z kolei używany do pobierania parametrów połączenia dla kolejki.

**iotHubName**: to pole jest nazwą IoT Hub po randomValue łączenia. 

**storageAccountName**: to pole jest nazwą konta magazynu po połączona z randomValue. 

**service_bus_namespace**: to pole jest przestrzenią nazw po randomValue połączonej.

**service_bus_queue**: to pole jest nazwą kolejki Service Bus po randomValue łączenia.

**sbVersion**: wersja interfejsu API Service Bus do użycia. W tym przypadku jest to "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Zasoby: konto magazynu i kontener

Pierwszy utworzony zasób to konto magazynu, a także kontener, do którego są kierowane komunikaty. Kontener jest zasobem na koncie magazynu. Ma `dependsOn` klauzulę dla konta magazynu, co wymaga utworzenia konta magazynu przed kontenerem.

Oto jak wygląda następująca sekcja:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Zasoby: Service Bus przestrzeni nazw i kolejki

Drugim utworzonym zasobem jest przestrzeń nazw Service Bus wraz z kolejką Service Bus, do której są kierowane komunikaty. Jednostka SKU jest ustawiona na wartość standardowa. Wersja interfejsu API jest pobierana ze zmiennych. Jest również ustawiony na aktywowanie Service Bus przestrzeni nazw podczas wdrażania tej sekcji (stan: aktywny). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Ta sekcja służy do tworzenia kolejki Service Bus. Ta część skryptu ma `dependsOn` klauzulę zapewniającą, że przestrzeń nazw jest tworzona przed kolejką.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Zasoby: Centrum IoT i Routing komunikatów

Teraz, gdy utworzono konto magazynu i kolejkę Service Bus, utworzysz IoT Hub, który kieruje do nich komunikaty. Szablon RM używa `dependsOn` klauzul, dlatego nie próbuje utworzyć centrum przed Service Bus zasobów i utworzono konto magazynu. 

Poniżej znajduje się pierwsza część sekcji IoT Hub. Ta część szablonu ustawia zależności i zaczyna się od właściwości.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Następna sekcja to sekcja dotycząca konfiguracji routingu wiadomości dla Centrum IoT Hub. Pierwsza to sekcja dla punktów końcowych. Ta część szablonu służy do konfigurowania punktów końcowych routingu dla kolejki Service Bus i konta magazynu, w tym parametrów połączenia.

Aby utworzyć parametry połączenia dla kolejki, potrzebne jest queueAuthorizationRulesResourcedId, która jest pobierana wewnętrznie. Aby utworzyć parametry połączenia dla konta magazynu, należy pobrać podstawowy klucz magazynu, a następnie użyć go w formacie parametrów połączenia.

Konfiguracja punktu końcowego służy również do ustawiania formatu obiektu BLOB na `AVRO` lub `JSON` .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Ta Następna sekcja dotyczy tras komunikatów do punktów końcowych. Istnieje jeden skonfigurowany dla każdego punktu końcowego, dlatego istnieje jeden dla kolejki Service Bus i jeden dla kontenera konta magazynu.

Należy pamiętać, że warunek zapytania dla komunikatów przesyłanych do magazynu jest `level="storage"` i warunek zapytania dla komunikatów kierowanych do kolejki Service Bus `level="critical"` .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Ten kod JSON pokazuje resztę sekcji IoT Hub, która zawiera informacje domyślne i jednostkę SKU centrum.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Zasoby: reguły autoryzacji kolejki Service Bus

Reguła autoryzacji kolejki Service Bus służy do pobierania parametrów połączenia dla kolejki Service Bus. Używa klauzuli, `dependsOn` Aby upewnić się, że nie została utworzona przed przestrzenią nazw Service Bus i Service Bus kolejką.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Zasoby: Grupa odbiorców

W tej sekcji utworzysz grupę odbiorców dla IoT Hub danych, które będą używane przez Azure Stream Analytics w drugiej części tego samouczka.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Zasoby: dane wyjściowe

Jeśli chcesz wysłać wartość z powrotem do skryptu wdrożenia, który ma być wyświetlany, użyj sekcji wyjściowej. Ta część szablonu zwraca parametry połączenia dla kolejki Service Bus. Zwrócenie wartości nie jest wymagane, jest ona dołączana jako przykład sposobu zwracania wyników do wywołującego skryptu.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Wdrażanie szablonu RM

Aby wdrożyć szablon na platformie Azure, Przekaż szablon i plik parametrów do Azure Cloud Shell, a następnie wykonaj skrypt w celu wdrożenia szablonu. Otwórz Azure Cloud Shell i zaloguj się. W tym przykładzie używa programu PowerShell.

Aby przekazać pliki, wybierz ikonę **Przekaż/Pobierz pliki** na pasku menu, a następnie wybierz polecenie Przekaż.

![Zrzut ekranu, który podświetla ikonę pliki przekazywania/pobierania.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Użyj Eksploratora plików, który znajduje się w programie, aby znaleźć pliki na dysku lokalnym, a następnie wybierz je, a następnie wybierz **Otwórz**.

Po przekazaniu plików w oknie dialogowym wyników zostanie wyświetlony komunikat podobny do następującego.

![Pasek menu Cloud Shell z wyróżnionymi wynikami przekazywania/pobierania](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Pliki są przekazywane do udziału używanego przez wystąpienie Cloud Shell. 

Uruchom skrypt, aby wykonać wdrożenie. Ostatni wiersz tego skryptu pobiera zmienną, która została skonfigurowana do zwrócenia — parametry połączenia kolejki Service Bus.

Skrypt ustawia i używa tych zmiennych:

**$RGName** to nazwa grupy zasobów, w której ma zostać wdrożony szablon. To pole jest tworzone przed wdrożeniem szablonu.

**$Location** to lokalizacja platformy Azure, która ma być używana dla szablonu, na przykład "zachodnie".

**deploymentname** to nazwa przypisana do wdrożenia w celu pobrania wartości zmiennej zwracanej.

Oto skrypt programu PowerShell. Skopiuj ten skrypt programu PowerShell i wklej go do okna Cloud Shell, a następnie naciśnij klawisz ENTER, aby go uruchomić.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Jeśli masz błędy skryptów, możesz edytować skrypt lokalnie, przekazać go ponownie do Cloud Shell i ponownie uruchomić skrypt. Po pomyślnym zakończeniu działania skryptu przejdź do następnego kroku.

## <a name="create-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wszystkie zasoby zostały skonfigurowane i trasy komunikatów zostały skonfigurowane, przejdź do następnego samouczka, aby dowiedzieć się, jak przetwarzać i wyświetlać informacje o rozesłanych wiadomościach.

> [!div class="nextstepaction"]
> [Część 2 — Wyświetlanie wyników routingu komunikatów](tutorial-routing-view-message-routing-results.md)
