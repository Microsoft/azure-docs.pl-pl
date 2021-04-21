---
title: Włączanie utraconych komunikatów dla Azure Service Bus i subskrypcji
description: W tym artykule wyjaśniono, jak włączyć utracony komunikat dla kolejek i subskrypcji przy użyciu języka Azure Portal, programu PowerShell, interfejsu wiersza polecenia i języków programowania (C#, Java, Python i JavaScript)
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819402"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Włączanie utraconych komunikatów po wygaśnięciu komunikatów dla Azure Service Bus i subskrypcji
Azure Service Bus i subskrypcje tematów zapewniają dodatkową kolejkę podrzędną nazywaną kolejką utraconych wiadomości (DLQ). Kolejka utraconych wiadomości nie musi być jawnie tworzona i nie można jej usunąć ani zarządzać niezależnie od jednostki głównej. Kolejka utraconych wiadomości ma na celu przechowywania komunikatów, których nie można dostarczyć do żadnego odbiorcy lub komunikatów, których nie można przetworzyć. Aby uzyskać więcej informacji, zobacz [Overview of Service Bus dead-letter queues (Omówienie kolejek utraconych komunikatów).](service-bus-dead-letter-queues.md) W tym artykule przedstawiono różne sposoby włączania utraconych komunikatów dla Service Bus i subskrypcji. 

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia kolejki **lub** **subskrypcji** dla tematu w Azure Portal wybierz pozycję Włącz utraconych wiadomości po wygaśnięciu wiadomości, jak pokazano w poniższych przykładach.  

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Tworzenie kolejki z włączonym utraconą obsługą utraconych komunikatów
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Włączanie utraconych komunikatów w czasie tworzenia kolejki":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Tworzenie subskrypcji z włączonym utraconą obsługą utraconych list
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Włączanie utraconych list w czasie tworzenia subskrypcji":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Aktualizowanie ustawienia utraconych komunikatów podczas wygasania komunikatów dla istniejącej kolejki
Na stronie **Przegląd** kolejki Service Bus wybierz bieżącą wartość ustawienia **Utraconych** wiadomości po wygaśnięciu wiadomości. W poniższym przykładzie bieżąca wartość to **Wyłączone.** W oknie podręcznym można włączyć lub wyłączyć utraconych komunikatów wygasających. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Włączanie utraconych komunikatów po wygaśnięciu komunikatu dla istniejącej kolejki":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Aktualizowanie ustawienia utraconych wiadomości podczas wygasania komunikatów dla istniejącej subskrypcji
Na stronie **Przegląd** subskrypcji Service Bus wybierz bieżącą wartość ustawienia **Utraconych** wiadomości po wygaśnięciu wiadomości. W poniższym przykładzie bieżąca wartość to **Wyłączone.** W oknie podręcznym można włączyć lub wyłączyć utraconych komunikatów po wygaśnięciu wiadomości. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Włączanie utraconych komunikatów po wygaśnięciu komunikatu dla istniejącej subskrypcji":::

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Aby **utworzyć kolejkę z włączonym utraconą obsługą** komunikatów wygasających, użyj polecenia z [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-dead-lettering-on-message-expiration` ustawieniem `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Aby **włączyć ustawienie utraconych komunikatów podczas wygasania komunikatów** dla istniejącej kolejki, użyj [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) polecenia z `--enable-dead-lettering-on-message-expiration` ustawioną na `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Aby **utworzyć subskrypcję tematu z włączonym utraconą obsługą** komunikatów wygasających, użyj polecenia z [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-dead-lettering-on-message-expiration` ustawioną na `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Aby **włączyć ustawienie utraconych komunikatów podczas wygasania** wiadomości dla subskrypcji tematu, użyj [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) polecenia z `--enable-dead-lettering-on-message-expiration` zestawem `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Aby **utworzyć kolejkę z włączonym utraconą obsługą** komunikatów wygasających, użyj polecenia z [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-DeadLetteringOnMessageExpiration` ustawieniem `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Aby **włączyć ustawienie utraconych komunikatów podczas wygasania** komunikatów dla istniejącej kolejki, użyj [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) polecenia , jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Aby **utworzyć subskrypcję dla tematu z włączonym utraconą obsługą** komunikatów wygasających, użyj polecenia z [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-DeadLetteringOnMessageExpiration` ustawioną na `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Aby **włączyć ustawienie utraconych komunikatów o wygaśnięciu dla istniejącej subskrypcji,** zobacz poniższy przykład.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Aby **utworzyć kolejkę z włączonym utraconą obsługą** komunikatów wygasających, w sekcji właściwości kolejki ustaw wartość `deadLetteringOnMessageExpiration` `true` . Aby uzyskać więcej informacji, zobacz temat [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informacje o szablonie przestrzeni nazw/kolejek Microsoft.ServiceBus). 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Aby **utworzyć subskrypcję dla tematu z włączonym utraconą obsługą** komunikatów wygasających, w sekcji właściwości kolejki ustaw `deadLetteringOnMessageExpiration` wartość `true` . Aby uzyskać więcej informacji, zobacz [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)(Informacje o szablonie przestrzeni nazw/tematów/subskrypcji Microsoft.ServiceBus). 

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
                "deadLetteringOnMessageExpiration": true
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
Wypróbuj przykłady w wybranego języku, aby poznać Azure Service Bus funkcji. 

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Przykłady azure.Messaging.ServiceBus dla platformy .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Poniżej znajdują się przykłady dla starszych bibliotek klienta .NET i Java:
- [Microsoft.Azure.ServiceBus samples for .NET (Przykłady microsoft.Azure.ServiceBus dla platformy .NET)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)