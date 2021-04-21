---
title: Samouczek — konfigurowanie routingu komunikatów dla Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure
description: Samouczek — konfigurowanie routingu komunikatów dla Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. W zależności od właściwości komunikatu przekieruj do konta magazynu lub Service Bus kolejki.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cf994dfe3d53232ab1e2374fda620dc768127097
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788893"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Samouczek: konfigurowanie routingu komunikatów za pomocą IoT Hub azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobieranie skryptu (opcjonalnie)

W drugiej części tego samouczka pobierzemy i uruchomemy aplikację Visual Studio do wysyłania komunikatów do IoT Hub. W pliku do pobrania znajduje się folder zawierający plik Azure Resource Manager parametrów, a także skrypty interfejsu wiersza polecenia platformy Azure i programu PowerShell.

Jeśli chcesz wyświetlić gotowy skrypt, pobierz przykłady [dla języka C# usługi Azure IoT.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rozpakować master.zip plik. Skrypt interfejsu wiersza polecenia platformy Azure znajduje się w skrypcie /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **iothub_routing_cli.azcli.**

## <a name="use-the-azure-cli-to-create-your-resources"></a>Tworzenie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej poniższy skrypt do pliku Cloud Shell naciśnij klawisz Enter. Skrypt jest uruchamiany po jednym wierszu na raz. Pierwsza sekcja skryptu utworzy zasoby podstawowe dla tego samouczka, w tym konto magazynu, IoT Hub, Service Bus przestrzeń nazw i Service Bus kolejki. W dalszej części samouczka skopiuj każdy blok skryptu i wklej go do Cloud Shell, aby go uruchomić.

> [!TIP]
> Porada na temat debugowania: ten skrypt używa symbolu kontynuacji (ukośnika odwrotnego ), aby `\` skrypt był bardziej czytelny. Jeśli masz problem z uruchomieniem skryptu, upewnij się, że sesja Cloud Shell jest uruchomiona i że po żadnym ukośniku odwrotnym nie ma `bash` spacji.
> 

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takich jak nazwa IoT Hub i nazwa konta magazynu. Aby to ułatwić, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. Wartość randomValue jest generowana raz w górnej części skryptu i dołączana do nazw zasobów zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby była losowa, możesz ustawić dla niego pusty ciąg lub konkretną wartość. 

> [!IMPORTANT]
> Zmienne ustawione w skrypcie początkowym są również używane przez skrypt routingu, więc uruchom wszystkie skrypty w tej samej Cloud Shell sesji. Jeśli otworzysz nową sesję w celu uruchomienia skryptu w celu skonfigurowania routingu, w kilku zmiennych brakuje wartości.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Teraz, po skonfigurowaniu zasobów podstawowych, możesz skonfigurować routing komunikatów.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, użyj [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create). Aby utworzyć trasę komunikatów dla punktu końcowego, użyj [az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create).

### <a name="route-to-a-storage-account"></a>Przekieruj do konta magazynu

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Najpierw skonfiguruj punkt końcowy dla konta magazynu, a następnie skonfiguruj trasę. 

Są to zmienne używane przez skrypt, które muszą być ustawione w Cloud Shell sesji:

**storageConnectionString:** ta wartość jest pobierana z konta magazynu ustawionego w poprzednim skrypcie. Jest on używany przez routing komunikatów w celu uzyskania dostępu do konta magazynu.

  **resourceGroup:** istnieją dwa wystąpienia grupy zasobów — ustaw je na grupę zasobów.

**endpoint subscriptionID:** to pole jest ustawione na wartość Azure subscriptionID dla punktu końcowego. 

**endpointType:** to pole jest typem punktu końcowego. Ta wartość musi być ustawiona `azurestoragecontainer` na , , lub `eventhub` `servicebusqueue` `servicebustopic` . Dla twoich celów w tym miejscu ustaw go na `azurestoragecontainer` wartość .

**iotHubName:** to pole jest nazwą centrum, które będzie routingiem.

**containerName:** to pole to nazwa kontenera na koncie magazynu, na którym będą zapisywane dane.

**kodowanie:** to pole będzie mieć format `avro` lub `json` . Oznacza to format przechowywanych danych.

**routeName:** to pole jest nazwą trasy, która jest ustawiana. 

**endpointName:** to pole jest nazwą identyfikującą punkt końcowy. 

**enabled:** to pole ma wartość domyślną , co oznacza, że po utworzeniu należy `true` włączyć trasę komunikatów.

**warunek:** to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów, które są kierowane do magazynu, to `level="storage"` .

Skopiuj ten skrypt, wklej go w oknie Cloud Shell i uruchom go.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Następnym krokiem jest utworzenie punktu końcowego routingu dla konta magazynu. Należy również określić kontener, w którym będą przechowywane wyniki. Kontener został utworzony wcześniej podczas tworzenia konta magazynu.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Następnie utwórz trasę dla punktu końcowego magazynu. Trasa komunikatów określa miejsce wysyłania komunikatów, które spełniają specyfikację zapytania. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Route to a Service Bus queue (Trasa do Service Bus kolejki)

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametrów połączenia dla kolejki Service Bus, należy utworzyć regułę autoryzacji, która ma zdefiniowane poprawne prawa. Poniższy skrypt tworzy regułę autoryzacji dla kolejki Service Bus o nazwie `sbauthrule` i ustawia prawa na `Listen Manage Send` . Po zdefiniować tę regułę autoryzacji można użyć jej do pobrania parametrów połączenia dla kolejki.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Teraz użyj reguły autoryzacji, aby pobrać ciąg połączenia do Service Bus kolejki.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Teraz skonfiguruj punkt końcowy routingu i trasę komunikatów dla kolejki Service Bus routingu. Są to zmienne używane przez skrypt, które należy ustawić w Cloud Shell sesji:

**endpointName:** to pole jest nazwą identyfikującą punkt końcowy. 

**endpointType:** to pole jest typem punktu końcowego. Ta wartość musi być ustawiona `azurestoragecontainer` na , , lub `eventhub` `servicebusqueue` `servicebustopic` . Dla twoich celów w tym miejscu ustaw go na `servicebusqueue` wartość .

**routeName:** to pole jest nazwą trasy, która jest ustawiana. 

**warunek:** to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów, które są kierowane do kolejki Service Bus to `level="critical"` .

Oto interfejs wiersza polecenia platformy Azure dla punktu końcowego routingu i trasy komunikatów dla Service Bus kolejki.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu komunikatów w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zasobów i skonfigurowanych tras komunikatów możesz przejść do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do centrum IoT i zobaczyć, że są one kierowane do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2. Wyświetlanie wyników routingu komunikatów](tutorial-routing-view-message-routing-results.md)
