---
title: Włączanie wykrywania zduplikowanych komunikatów — Azure Service Bus
description: W tym artykule wyjaśniono, jak włączyć wykrywanie zduplikowanych komunikatów przy użyciu języka Azure Portal, programu PowerShell, interfejsu wiersza polecenia i języków programowania (C#, Java, Python i JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755251"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Włączanie wykrywania zduplikowanych komunikatów Azure Service Bus kolejki lub tematu
Po włączeniu wykrywania duplikatów dla kolejki lub tematu program Azure Service Bus przechowuje historię wszystkich komunikatów wysyłanych do kolejki lub tematu przez skonfigurowany czas. W tym interwale kolejka lub temat nie będą przechowywać żadnych zduplikowanych komunikatów. Włączenie tej właściwości gwarantuje dokładnie raz dostarczenie w okresie zdefiniowanym przez użytkownika. Aby uzyskać więcej informacji, zobacz [Wykrywanie duplikatów.](duplicate-detection.md) W tym artykule przedstawiono różne sposoby włączania wykrywania zduplikowanych komunikatów Service Bus kolejki lub tematu. 


> [!NOTE]
> - Podstawowa warstwa Service Bus nie obsługuje wykrywania duplikatów. Warstwy Standardowa i Premium obsługują wykrywanie duplikatów. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik .](https://azure.microsoft.com/pricing/details/service-bus/)
> - Po utworzeniu kolejki lub tematu nie można włączyć ani wyłączyć wykrywania duplikatów. Można to zrobić tylko w czasie tworzenia kolejki lub tematu. 

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia kolejki **w** Azure Portal wybierz pozycję **Włącz wykrywanie duplikatów,** jak pokazano na poniższej ilustracji. Rozmiar okna wykrywania duplikatów można skonfigurować podczas tworzenia kolejki lub tematu. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Włączanie wykrywania duplikatów w czasie tworzenia kolejki":::

Podczas tworzenia tematu w Azure Portal wybierz pozycję **Włącz wykrywanie duplikatów,** jak pokazano na poniższej ilustracji. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Włączanie wykrywania duplikatów w czasie tworzenia tematu":::

Możesz również skonfigurować to ustawienie dla istniejącej kolejki lub tematu, jeśli w momencie tworzenia włączono wykrywanie duplikatów. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Aktualizowanie rozmiaru okna wykrywania duplikatów dla istniejącej kolejki lub tematu
Aby zmienić rozmiar okna wykrywania duplikatów dla istniejącej kolejki  lub tematu, na stronie Przegląd wybierz pozycję Zmień dla **okna wykrywania duplikatów.**   

#### <a name="queue"></a>Kolejka
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Ustawianie rozmiaru okna wykrywania duplikatów dla kolejki":::

#### <a name="topic"></a>Temat
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Ustawianie rozmiaru okna wykrywania duplikatów dla tematu":::


## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Aby **utworzyć kolejkę z włączonym wykrywaniem duplikatów,** użyj [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) polecenia z `--enable-duplicate-detection` ustawioną wartością `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Aby **utworzyć temat z włączonym wykrywaniem duplikatów,** użyj polecenia z [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-duplicate-detection` ustawioną wartością `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

W powyższych przykładach ustawiono również rozmiar okna wykrywania duplikatów przy użyciu `--duplicate-detection-history-time-window` parametru . Rozmiar okna jest ustawiony na jeden dzień. Wartość domyślna to 10 minut, a maksymalna dozwolona wartość to siedem dni.

Aby **zaktualizować kolejkę przy użyciu nowego rozmiaru okna wykrywania,** [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) użyj polecenia z `--duplicate-detection-history-time-window` parametrem . W tym przykładzie rozmiar okna został zaktualizowany do siedmiu dni. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Podobnie, aby **zaktualizować temat o nowy rozmiar okna wykrywania,** użyj [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) polecenia z `--duplicate-detection-history-time-window` parametrem . W tym przykładzie rozmiar okna został zaktualizowany do siedmiu dni.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Aby **utworzyć kolejkę z włączonym wykrywaniem duplikatów,** użyj [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) polecenia z `-RequiresDuplicateDetection` ustawioną wartością `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Aby **utworzyć temat z włączonym wykrywaniem duplikatów,** użyj polecenia z [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` ustawioną wartością `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

W powyższych przykładach ustawiono również rozmiar okna wykrywania duplikatów przy użyciu `-DuplicateDetectionHistoryTimeWindow` parametru . Rozmiar okna jest ustawiony na jeden dzień. Wartość domyślna to 10 minut, a maksymalna dozwolona wartość to siedem dni.

Aby **zaktualizować kolejkę przy użyciu nowego rozmiaru okna wykrywania,** zobacz poniższy przykład.  W tym przykładzie rozmiar okna został zaktualizowany do siedmiu dni.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Aby **zaktualizować temat o nowy rozmiar okna wykrywania,** zobacz poniższy przykład. W tym przykładzie rozmiar okna został zaktualizowany do siedmiu dni.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Aby **utworzyć kolejkę z włączonym wykrywaniem duplikatów,** ustaw `requiresDuplicateDetection` wartość na w sekcji właściwości `true` kolejki. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informacje o szablonie przestrzeni nazw/kolejek Microsoft.ServiceBus). Określ wartość `duplicateDetectionHistoryTimeWindow` właściwości, aby ustawić rozmiar okna wykrywania duplikatów. W poniższym przykładzie ustawiono ją na jeden dzień.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Aby **utworzyć temat z włączonym wykrywaniem duplikatów,** ustaw wartość na w sekcji właściwości `requiresDuplicateDetection` `true` tematu. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/topics template reference](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)(Informacje o szablonach przestrzeni nazw/tematów Microsoft.ServiceBus). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Następne kroki
Wypróbuj przykłady w swoim języku, aby eksplorować Azure Service Bus funkcji. 

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Przykłady azure.Messaging.ServiceBus dla platformy .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Poniżej znajdują się przykłady dla starszych bibliotek klienta .NET i Java:
- [Microsoft.Azure.ServiceBus samples for .NET (Przykłady microsoft.Azure.ServiceBus dla platformy .NET)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)