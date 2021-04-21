---
title: Włączanie partycjonowania w Azure Service Bus i tematach
description: W tym artykule wyjaśniono, jak włączyć partycjonowanie w kolejkach i tematach usługi Azure Service Bus przy użyciu języków Azure Portal, PowerShell, CLI i programowania (C#, Java, Python i JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755240"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Włączanie partycjonowania dla Azure Service Bus lub tematu
Service Bus partycje umożliwiają partycjonowanie kolejek i tematów lub jednostek obsługi komunikatów między wieloma brokerami komunikatów i magazynami obsługi komunikatów. Partycjonowanie oznacza, że ogólna przepływność partycjonowanej jednostki nie jest już ograniczona przez wydajność pojedynczego brokera komunikatów lub magazynu komunikatów. Ponadto tymczasowa niedostępność magazynu obsługi komunikatów nie powoduje, że partycjonowana kolejka lub temat są niedostępne. Partycjonowane kolejki i tematy mogą zawierać wszystkie zaawansowane Service Bus, takie jak obsługa transakcji i sesji. Aby uzyskać więcej informacji, zobacz [Partycjonowane kolejki i tematy](service-bus-partitioning.md). W tym artykule przedstawiono różne sposoby włączania wykrywania zduplikowanych komunikatów Service Bus kolejki lub tematu. 

> [!IMPORTANT]
> - Partycjonowanie jest dostępne podczas tworzenia jednostki dla wszystkich kolejek i tematów w podstawowych lub standardowych jednostkach SKU. Nie jest ona dostępna w przypadku jednostki SKU obsługi komunikatów Premium, ale wszystkie wcześniej partycjonowane jednostki w przestrzeniach nazw Premium nadal działają zgodnie z oczekiwaniami.
> - Nie można zmienić opcji partycjonowania w żadnej istniejącej kolejce lub temacie. Tę opcję można ustawić tylko podczas tworzenia kolejki lub tematu. 

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia **kolejki w** Azure Portal wybierz pozycję Włącz **partycjonowanie,** jak pokazano na poniższej ilustracji. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Włączanie partycjonowania w czasie tworzenia kolejki":::

Podczas tworzenia tematu w Azure Portal wybierz pozycję **Włącz partycjonowanie,** jak pokazano na poniższej ilustracji. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Włączanie partycjonowania w czasie tworzenia tematu":::

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Aby **utworzyć kolejkę z włączoną partycjonowania,** użyj [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) polecenia z `--enable-partitioning` ustawioną na `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Aby **utworzyć temat z włączoną partycjonowania,** użyj polecenia z [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` ustawioną na `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Aby **utworzyć kolejkę z włączoną partycjonowania,** użyj [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) polecenia z `-EnablePartitioning` ustawioną na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Aby **utworzyć temat z włączoną partycjonowania,** użyj polecenia z [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` ustawioną na `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Aby **utworzyć kolejkę z włączoną partycjonowania,** ustaw `enablePartitioning` wartość na w sekcji właściwości `true` kolejki. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informacje o szablonie przestrzeni nazw/kolejek Microsoft.ServiceBus). 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Aby **utworzyć temat z włączonym wykrywaniem duplikatów,** ustaw wartość na w sekcji właściwości `enablePartitioning` `true` tematu. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/topics template reference](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)(Informacje o szablonach przestrzeni nazw/tematów Microsoft.ServiceBus). 

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
            "enablePartitioning": true
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