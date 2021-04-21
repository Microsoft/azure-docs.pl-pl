---
title: Włączanie automatycznego przekazywania dla Azure Service Bus i subskrypcji
description: W tym artykule wyjaśniono, jak włączyć automatyczne przekazywanie kolejek i subskrypcji przy użyciu języków Azure Portal, PowerShell, CLI i programowania (C#, Java, Python i JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755284"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Włączanie automatycznego przekazywania dla Azure Service Bus i subskrypcji
Funkcja Service Bus automatycznego przekazywania umożliwia tworzenie łańcucha kolejki lub subskrypcji z inną kolejką lub tematem, który jest częścią tej samej przestrzeni nazw. Po włączeniu automatycznego przekazywania program Service Bus automatycznie usuwa komunikaty umieszczone w pierwszej kolejce lub subskrypcji (źródle) i umieszcza je w drugiej kolejce lub temacie (docelowym). Nadal można wysyłać komunikat bezpośrednio do jednostki docelowej. Aby uzyskać więcej informacji, zobacz [Chaining Service Bus entities with auto forwarding (Łańcuchowe przekazywanie](service-bus-auto-forwarding.md)jednostek przy użyciu automatycznego przekazywania). W tym artykule przedstawiono różne sposoby włączania automatycznego przekazywania dla Service Bus i subskrypcji. 

> [!IMPORTANT]
> Podstawowa warstwa Service Bus nie obsługuje funkcji automatycznego przekazywania dalej. Ta funkcja jest dostępna w warstwach Standardowa i Premium. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia kolejki **lub** **subskrypcji** dla tematu w skrypcie Azure Portal, wybierz pozycję Przekaż komunikaty do **kolejki/tematu,** jak pokazano w poniższych przykładach. Następnie określ, czy komunikaty mają być przekazywane do kolejki, czy tematu. W tym przykładzie **wybrano opcję Kolejka** i wybrano kolejkę **(myqueue)** z tej samej przestrzeni nazw.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Tworzenie kolejki z włączonym automatycznym przekazywaniem
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Włączanie automatycznego przekazywania dalej w czasie tworzenia kolejki":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Tworzenie subskrypcji dla tematu z włączonym automatycznym przekazywaniem
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Włączanie automatycznego przekazywania dalej w czasie tworzenia subskrypcji":::

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Aby **utworzyć kolejkę z włączonym** automatycznym przekazywaniem, użyj polecenia z ustawionym na nazwę kolejki lub tematu, do którego mają być przekazywane [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` komunikaty. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Aby **utworzyć subskrypcję** dla tematu z włączonym automatycznym przekazywaniem, użyj polecenia z ustawionym na nazwę kolejki lub tematu, do którego mają być przekazywane [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--forward-to` komunikaty.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Aby **utworzyć kolejkę z włączonym** automatycznym przekazywaniem, użyj polecenia z ustawionym na nazwę kolejki lub tematu, do którego mają być przekazywane [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` komunikaty. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Aby **utworzyć subskrypcję** dla tematu z włączonym automatycznym przekazywaniem, użyj polecenia z ustawionym na nazwę kolejki lub tematu, do którego mają być przekazywane [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-ForwardTo` komunikaty.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Aby **utworzyć kolejkę z** włączonym automatycznym przekazywaniem, ustaw w sekcji właściwości kolejki nazwę kolejki lub tematu, do którego mają być przekazywane `forwardTo` komunikaty. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informacje o szablonie przestrzeni nazw/kolejek Microsoft.ServiceBus). 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Aby **utworzyć subskrypcję** dla tematu z włączonym automatycznym przekazywaniem, ustaw w sekcji właściwości kolejki nazwę kolejki lub tematu, do którego mają być przekazywane `forwardTo` komunikaty. Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)(Informacje o szablonie przestrzeni nazw/tematów/subskrypcji microsoft.ServiceBus). 

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
                "forwardTo": "myqueue2"
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