---
title: Konfigurowanie routingu komunikatów dla usługi Azure IoT Hub przy użyciu Azure PowerShell
description: Skonfiguruj Routing komunikatów dla platformy Azure IoT Hub przy użyciu Azure PowerShell. W zależności od właściwości komunikatu, trasy do konta magazynu lub kolejki Service Bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 92c30287e836ccadda915dab70a4d40e9c4e851a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073407"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Samouczek: używanie Azure PowerShell do konfigurowania routingu komunikatów IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Pobierz skrypt (opcjonalnie)

W drugiej części tego samouczka pobierasz i uruchamiasz aplikację Visual Studio w celu wysyłania komunikatów do IoT Hub. Pobierany jest folder zawierający plik szablonu i parametrów Azure Resource Manager, jak również interfejs wiersza polecenia platformy Azure i skrypty programu PowerShell. 

Jeśli chcesz wyświetlić gotowy skrypt, Pobierz [przykłady w języku C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozpakuj plik master.zip. Skrypt interfejsu wiersza polecenia platformy Azure jest w/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Tworzenie zasobów

Zacznij od utworzenia zasobów przy użyciu programu PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Tworzenie zasobów podstawowych przy użyciu programu PowerShell

Skopiuj i wklej poniższy skrypt do Cloud Shell i naciśnij klawisz ENTER. Skrypt uruchamia jeden wiersz jednocześnie. Ta pierwsza sekcja skryptu spowoduje utworzenie zasobów podstawowych dla tego samouczka, w tym konta magazynu, IoT Hub, Service Bus przestrzeni nazw i kolejki Service Bus. Po przejściu przez samouczek skopiuj każdy blok skryptu i wklej go do Cloud Shell, aby uruchomić go.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takie jak nazwa IoT Hub i nazwa konta magazynu. Aby to ułatwić, nazwy tych zasobów są dołączane losowo wartością alfanumeryczną o nazwie *randomValue*. RandomValue jest generowany raz u góry skryptu i dołączany do nazw zasobów zgodnie z wymaganiami w całym skrypcie. Jeśli nie chcesz go losowo, możesz ustawić go na pusty ciąg lub do określonej wartości. 

> [!IMPORTANT]
> Zmienne ustawione w skrypcie początkowym są również używane przez skrypt routingu, więc Uruchom cały skrypt w tej samej sesji Cloud Shell. Jeśli otworzysz nową sesję, aby uruchomić skrypt w celu skonfigurowania routingu, w kilku zmiennych brakuje wartości. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Teraz, gdy zasoby podstawowe są skonfigurowane, można skonfigurować Routing komunikatów.

## <a name="set-up-message-routing"></a>Konfigurowanie routingu komunikatów

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Aby utworzyć punkt końcowy routingu, użyj polecenie [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Aby utworzyć trasę obsługi komunikatów dla punktu końcowego, użyj polecenie [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Kierowanie do konta magazynu 

Najpierw należy skonfigurować punkt końcowy dla konta magazynu, a następnie utworzyć trasę wiadomości.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Są to zmienne używane przez skrypt, które muszą być ustawione w ramach sesji Cloud Shell:

Grupa **zasobów: istnieją**dwa wystąpienia tego pola — Ustaw obydwie te elementy w danej grupie.

**Nazwa**: to pole jest nazwą IoT Hub, do którego zostanie zastosowana trasa.

**EndpointName**: to pole jest nazwą identyfikującą punkt końcowy. 

**EndpointType**: to pole jest typem punktu końcowego. Ta wartość musi być ustawiona na `azurestoragecontainer` , `eventhub` , `servicebusqueue` , lub `servicebustopic` . W tym miejscu ustaw wartość `azurestoragecontainer` .

Identyfikator **subskrypcji**: to pole jest ustawione na identyfikator subskrypcji dla Twojego konta platformy Azure.

**storageConnectionString**: Ta wartość jest pobierana z konta magazynu skonfigurowanego w poprzednim skrypcie. Jest ona używana przez Routing do uzyskiwania dostępu do konta magazynu.

**ContainerName**: to pole jest nazwą kontenera na koncie magazynu, w którym będą zapisywane dane.

**Kodowanie**: Ustaw dla tego pola wartość `AVRO` lub `JSON` . Oznacza to format przechowywanych danych. Wartość domyślna to AVRO.

**RouteName**: to pole jest nazwą konfigurowanej trasy. 

**warunek**: to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów, które są przesyłane do magazynu, to `level="storage"` .

**włączone**: to pole ma wartość domyślną `true` , co oznacza, że trasa wiadomości powinna być włączona po utworzeniu.

Skopiuj ten skrypt i wklej go do okna Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Następnym krokiem jest utworzenie punktu końcowego routingu dla konta magazynu. Należy również określić kontener, w którym będą przechowywane wyniki. Kontener został utworzony podczas tworzenia konta magazynu.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Następnie utwórz trasę wiadomości dla punktu końcowego magazynu. Trasa komunikatów określa miejsce, w którym mają zostać wysłane komunikaty zgodne ze specyfikacją zapytania.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Kierowanie do kolejki Service Bus

Teraz skonfigurujesz routing dla kolejki usługi Service Bus. Aby pobrać parametry połączenia dla kolejki Service Bus, należy utworzyć regułę autoryzacji z zdefiniowanymi prawidłowymi prawami. Poniższy skrypt tworzy regułę autoryzacji dla kolejki Service Bus o nazwie `sbauthrule` i ustawia prawa do `Listen Manage Send` . Po skonfigurowaniu tej reguły autoryzacji można jej użyć do pobrania parametrów połączenia dla kolejki.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Teraz użyj reguły autoryzacji, aby pobrać klucz kolejki Service Bus. Ta reguła autoryzacji zostanie użyta do pobrania parametrów połączenia później w skrypcie.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Teraz Skonfiguruj punkt końcowy routingu i trasę wiadomości dla kolejki Service Bus. Są to zmienne używane przez skrypt, które muszą być ustawione w ramach sesji Cloud Shell:

**EndpointName**: to pole jest nazwą identyfikującą punkt końcowy. 

**EndpointType**: to pole jest typem punktu końcowego. Ta wartość musi być ustawiona na `azurestoragecontainer` , `eventhub` , `servicebusqueue` , lub `servicebustopic` . W tym miejscu ustaw wartość `servicebusqueue` .

**RouteName**: to pole jest nazwą konfigurowanej trasy. 

**warunek**: to pole jest zapytaniem używanym do filtrowania komunikatów wysyłanych do tego punktu końcowego. Warunek zapytania dla komunikatów przesyłanych do kolejki Service Bus jest `level="critical"` .

Poniżej przedstawiono Azure PowerShell dla routingu wiadomości dla kolejki Service Bus.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Wyświetlanie routingu wiadomości w portalu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zasobów oraz skonfigurowaniu tras komunikatów przejdź do następnego samouczka, aby dowiedzieć się, jak wysyłać komunikaty do centrum IoT Hub i zobaczyć je do różnych miejsc docelowych. 

> [!div class="nextstepaction"]
> [Część 2 — Wyświetlanie wyników routingu komunikatów](tutorial-routing-view-message-routing-results.md)
