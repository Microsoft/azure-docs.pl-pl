---
title: Azure Service Bus — liczba komunikatów
description: Pobieranie liczby komunikatów w kolejkach i subskrypcjach przy użyciu Azure Resource Manager i interfejsów API Azure Service Bus NamespaceManager.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814614"
---
# <a name="get-message-counters"></a>Uzyskiwanie liczników komunikatów
W tym artykule przedstawiono różne sposoby uzyskiwania następujących liczników komunikatów dla kolejki lub subskrypcji. Znajomość liczby aktywnych komunikatów przydaje się do określenia, czy kolejka tworzy zaległości wymagające większej liczby zasobów do przetwarzania niż aktualnie wdrożona. 

| Licznik | Opis |
| ----- | ---------- | 
| ActiveMessageCount | Liczba komunikatów w kolejce lub subskrypcji, które są aktywne i są gotowe do dostarczenia. |
| ScheduledMessageCount | Liczba komunikatów w zaplanowanym stanie. |
| DeadLetterMessageCount | Liczba komunikatów w kolejce utraconych wiadomości. |
| TransferMessageCount | Liczba komunikatów oczekujących na przeniesienie do innej kolejki lub tematu. |
| TransferDeadLetterMessageCount | Liczba komunikatów, których nie można przenieść do innej kolejki lub tematu i które zostały przeniesione do kolejki utraconych wiadomości transferu. |

Jeśli aplikacja chce skalować zasoby na podstawie długości kolejki, powinna to zrobić w mierzonym tempie. Pozyskiwanie liczników komunikatów jest kosztowną operacją wewnątrz brokera komunikatów i często wykonywane bezpośrednio i niekorzystnie wpływa na wydajność jednostki.

> [!NOTE]
> Komunikaty wysyłane do tematu Service Bus są przekazywane do subskrypcji dla tego tematu. Dlatego liczba aktywnych komunikatów w samym temacie wynosi 0, ponieważ te komunikaty zostały pomyślnie przekazane do subskrypcji. Pobierz liczbę komunikatów w subskrypcji i sprawdź, czy jest ona większa niż 0. Mimo że komunikaty są wyświetlane w ramach subskrypcji, są one w rzeczywistości przechowywane w magazynie należącym do tematu. Jeśli przyjrzysz się subskrypcjom, będą one miały niezerową liczbę komunikatów (co dodaje do 323 MB miejsca dla całej jednostki).


## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Przejdź do przestrzeni nazw i wybierz kolejkę. Zobaczysz liczniki komunikatów na **stronie Przegląd** kolejki.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Liczniki komunikatów na stronie przeglądu kolejki":::

Przejdź do przestrzeni nazw, wybierz temat, a następnie wybierz subskrypcję tematu. Zobaczysz liczniki komunikatów na **stronie Przegląd** kolejki.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Liczniki komunikatów na stronie przeglądu subskrypcji":::

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Użyj polecenia [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) , aby uzyskać szczegóły liczby komunikatów dla kolejki, jak pokazano w poniższym przykładzie. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Oto przykładowe dane wyjściowe:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Użyj polecenia [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) , aby uzyskać szczegóły liczby komunikatów dla subskrypcji, jak pokazano w poniższym przykładzie. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Za pomocą programu PowerShell można uzyskać szczegóły liczby komunikatów dla kolejki w następujący sposób:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Oto przykładowe dane wyjściowe:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

Szczegóły liczby komunikatów dla subskrypcji można uzyskać w następujący sposób:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Zwrócony `MessageCountDetails` obiekt ma następujące właściwości: `ActiveMessageCount` , , , , `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` `TransferMessageCount` . 

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
