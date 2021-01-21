---
title: Samouczek — Konfigurowanie routingu komunikatów dla platformy Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure
description: Samouczek — Konfigurowanie routingu komunikatów dla platformy Azure IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. W zależności od właściwości komunikatu, trasy do konta magazynu lub kolejki Service Bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 25c3ff7582ed408776c0ae6904e4bacddd89e40b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624174"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Samouczek: Konfigurowanie routingu komunikatów IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

W drugiej części tego samouczka pobierasz i uruchamiasz aplikację Visual Studio w celu wysyłania komunikatów do IoT Hub. Pobierany jest folder zawierający plik szablonu i parametrów Azure Resource Manager, jak również interfejs wiersza polecenia platformy Azure i skrypty programu PowerShell.

Jeśli chcesz wyświetlić gotowy skrypt, Pobierz [przykłady w języku C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozpakuj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Tworzenie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Skopiuj i wklej poniższy skrypt do Cloud Shell i naciśnij klawisz ENTER. Skrypt uruchamia jeden wiersz jednocześnie. Ta pierwsza sekcja skryptu spowoduje utworzenie zasobów podstawowych dla tego samouczka, w tym konta magazynu, IoT Hub, Service Bus przestrzeni nazw i kolejki Service Bus. Po pozostałej części samouczka skopiuj każdy blok skryptu i wklej go do Cloud Shell, aby uruchomić go.

> [!TIP]
> Wskazówka dotycząca debugowania: ten skrypt używa symbolu kontynuacji (ukośnika odwrotnego `\` ), aby zwiększyć czytelność skryptu. Jeśli wystąpił problem z uruchomieniem skryptu, upewnij się, że sesja Cloud Shell jest uruchomiona `bash` i że nie ma spacji po żadnym z ukośników odwrotnych.
> 

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takie jak nazwa IoT Hub i nazwa konta magazynu. Aby to ułatwić, nazwy tych zasobów są dołączane losowo wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz u góry skryptu i dołączany do nazw zasobów zgodnie z wymaganiami w całym skrypcie. Jeśli nie chcesz go losowo, możesz ustawić go na pusty ciąg lub do określonej wartości. 

> [!IMPORTANT]
> Zmienne ustawione w skrypcie początkowym są również używane przez skrypt routingu, więc Uruchom cały skrypt w tej samej sesji Cloud Shell. Jeśli otworzysz nową sesję, aby uruchomić skrypt w celu skonfigurowania routingu, w kilku zmiennych brakuje wartości.
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

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować Routing komunikatów.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, użyj [AZ IoT Hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create&preserve-view=true). Aby utworzyć trasę wiadomości dla punktu końcowego, użyj [AZ IoT Hub Route Create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create&preserve-view=true).

### <a name="route-to-a-storage-account"></a>Kierowanie do konta magazynu

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Najpierw Skonfiguruj punkt końcowy dla konta magazynu, a następnie skonfiguruj trasę. 

Są to zmienne używane przez skrypt, które muszą być ustawione w ramach sesji Cloud Shell:

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu skonfigurowanego w poprzednim skrypcie. Jest ona używana przez Routing komunikatów w celu uzyskania dostępu do konta magazynu.

  Grupa **zasobów: istnieją** dwa wystąpienia grupy zasobu — Ustaw je na grupę zasobów.

Identyfikator **subskrypcji punktu końcowego**: to pole jest ustawione na identyfikator subskrypcji platformy Azure dla punktu końcowego. 

**EndpointType**: to pole jest typem punktu końcowego. Ta wartość musi być ustawiona na `azurestoragecontainer` , `eventhub` , `servicebusqueue` , lub `servicebustopic` . W tym miejscu ustaw wartość `azurestoragecontainer` .

**iotHubName**: to pole jest nazwą centrum, które wykona Routing.

**ContainerName**: to pole jest nazwą kontenera na koncie magazynu, w którym będą zapisywane dane.

**kodowanie**: to pole ma wartość `avro` lub `json` . Oznacza to format przechowywanych danych.

**RouteName**: to pole jest nazwą konfigurowanej trasy. 

**EndpointName**: to pole jest nazwą identyfikującą punkt końcowy. 

**włączone**: to pole ma wartość domyślną `true` , co oznacza, że trasa wiadomości powinna być włączona po utworzeniu.

**warunek**: to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów, które są przesyłane do magazynu, to `level="storage"` .

Skopiuj ten skrypt i wklej go do okna Cloud Shell i uruchom go.

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

Następnie utwórz trasę dla punktu końcowego magazynu. Trasa komunikatów określa miejsce, w którym mają zostać wysłane komunikaty zgodne ze specyfikacją zapytania. 

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

### <a name="route-to-a-service-bus-queue"></a>Kierowanie do kolejki Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki Service Bus, należy utworzyć regułę autoryzacji z zdefiniowanymi prawidłowymi prawami. Poniższy skrypt tworzy regułę autoryzacji dla kolejki Service Bus o nazwie `sbauthrule` i ustawia prawa do `Listen Manage Send` . Po zdefiniowaniu tej reguły autoryzacji można jej użyć do pobrania parametrów połączenia dla kolejki.

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

Teraz użyj reguły autoryzacji, aby pobrać parametry połączenia do kolejki Service Bus.

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

Teraz Skonfiguruj punkt końcowy routingu i trasę wiadomości dla kolejki Service Bus. Są to zmienne używane przez skrypt, które muszą być ustawione w ramach sesji Cloud Shell:

**EndpointName**: to pole jest nazwą identyfikującą punkt końcowy. 

**EndpointType**: to pole jest typem punktu końcowego. Ta wartość musi być ustawiona na `azurestoragecontainer` , `eventhub` , `servicebusqueue` , lub `servicebustopic` . W tym miejscu ustaw wartość `servicebusqueue` .

**RouteName**: to pole jest nazwą konfigurowanej trasy. 

**warunek**: to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów przesyłanych do kolejki Service Bus jest `level="critical"` .

Oto interfejs wiersza polecenia platformy Azure dla punktu końcowego routingu i trasy wiadomości dla kolejki Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zasobów oraz skonfigurowaniu tras komunikatów przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do centrum IoT Hub i zobaczyć je do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 — Wyświetlanie wyników routingu komunikatów](tutorial-routing-view-message-routing-results.md)
