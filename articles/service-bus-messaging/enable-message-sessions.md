---
title: Włączanie Azure Service Bus sesji komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak włączyć sesje komunikatów przy użyciu języków Azure Portal, PowerShell, CLI i programowania (C#, Java, Python i JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755248"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Włączanie sesji komunikatów dla Azure Service Bus lub subskrypcji
Azure Service Bus umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Sesje mogą być używane we **wzorcach pierwszy na w/wy (FIFO)** i **żądanie-odpowiedź.** Aby uzyskać więcej informacji, zobacz [Sesje komunikatów](message-sessions.md). W tym artykule przedstawiono różne sposoby włączania sesji dla Service Bus lub subskrypcji. 

> [!IMPORTANT]
> - Warstwa podstawowa Service Bus nie obsługuje sesji. Warstwy Standardowa i Premium obsługują sesje. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik.](https://azure.microsoft.com/pricing/details/service-bus/)
> - Po utworzeniu kolejki lub subskrypcji nie można włączać ani wyłączać sesji komunikatów. Można to zrobić tylko w czasie tworzenia kolejki lub subskrypcji. 

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia kolejki **w** Azure Portal wybierz pozycję **Włącz sesje, jak** pokazano na poniższej ilustracji. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Włączanie sesji w czasie tworzenia kolejki":::

Podczas tworzenia subskrypcji dla tematu w Azure Portal wybierz pozycję **Włącz sesje,** jak pokazano na poniższej ilustracji. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Włączanie sesji w czasie tworzenia subskrypcji":::

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Aby **utworzyć kolejkę z włączonymi sesjami komunikatów,** użyj [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) polecenia z `--enable-session` ustawioną na `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Aby **utworzyć subskrypcję tematu z włączonymi sesjami komunikatów,** użyj [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) polecenia z `--enable-session` ustawioną na `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Aby **utworzyć kolejkę z włączonymi sesjami komunikatów,** użyj [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) polecenia z `-RequiresSession` ustawioną na `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Aby **utworzyć subskrypcję tematu z włączonymi sesjami komunikatów,** użyj [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) polecenia z `-RequiresSession` ustawioną na `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Aby **utworzyć kolejkę z włączonymi sesjami komunikatów,** ustaw `requiresSession` wartość na w sekcji właściwości `true` kolejki. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informacje o szablonie przestrzeni nazw/kolejek Microsoft.ServiceBus). 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Aby **utworzyć subskrypcję dla tematu z włączonymi sesjami komunikatów,** ustaw `requiresSession` wartość na w sekcji właściwości `true` subskrypcji. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)(Informacje o szablonie przestrzeni nazw/tematów/subskrypcji microsoft.ServiceBus). 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "requiresSession": true
              }
            }
          ]
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