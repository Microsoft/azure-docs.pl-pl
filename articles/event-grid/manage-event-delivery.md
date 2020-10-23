---
title: Utracony komunikat i zasady ponawiania — Azure Event Grid
description: Opisuje sposób dostosowywania opcji dostarczania zdarzeń dla Event Grid. Ustaw miejsce docelowe utraconych wiadomości, a następnie określ, jak długo ma być ponawiane dostarczanie.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 7d8cd74ccfb77bcec45d06071a4f46fb2a640cf8
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460941"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>Ustawianie lokalizacji utraconych komunikatów i zasad ponawiania

Podczas tworzenia subskrypcji zdarzeń można dostosować ustawienia dostarczania zdarzeń. W tym artykule opisano sposób konfigurowania martwej lokalizacji i dostosowywania ustawień ponownych prób. Aby uzyskać informacje o tych funkcjach, zobacz [Event Grid dostarczania komunikatów i ponów próbę](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Aby dowiedzieć się więcej na temat dostarczania komunikatów, ponawiania prób i utraconych wiadomości, zobacz artykuł [dotyczący pojęć: Event Grid dostarczania komunikatów i ponów próbę](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Ustaw lokalizację wiadomości utraconych

Aby ustawić pustą lokalizację, musisz mieć konto magazynu do przechowywania zdarzeń, których nie można dostarczyć do punktu końcowego. Przykłady pobierają identyfikator zasobu istniejącego konta magazynu. Tworzą subskrypcję zdarzeń, która używa kontenera w tym koncie magazynu dla punktu końcowego utraconych wiadomości.

> [!NOTE]
> - Utwórz konto magazynu i kontener obiektów BLOB w magazynie przed uruchomieniem poleceń w tym artykule.
> - Usługa Event Grid tworzy obiekty blob w tym kontenerze. Nazwy obiektów BLOB będą mieć nazwę subskrypcji Event Grid ze wszystkimi literami w Wielkiej litery. Na przykład, jeśli nazwa subskrypcji to my-BLOB-Subscription, nazwy obiektów blob z utraconą listą będą miały wartość MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.jsw systemie). Takie zachowanie ma na celu ochronę przed różnicami w przypadku obsługi w przypadku korzystania z usług platformy Azure.


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Aby wyłączyć obsługę utraconych wiadomości, należy ponownie uruchomić polecenie, aby utworzyć subskrypcję zdarzeń, ale nie podawać wartości dla `deadletter-endpoint` . Nie musisz usuwać subskrypcji zdarzeń.

> [!NOTE]
> Jeśli używasz interfejsu wiersza polecenia platformy Azure na komputerze lokalnym, powinien on być w wersji co najmniej 2.0.56. Aby uzyskać instrukcje na temat instalowania najnowszej wersji interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="powershell"></a>Program PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Aby wyłączyć obsługę utraconych wiadomości, należy ponownie uruchomić polecenie, aby utworzyć subskrypcję zdarzeń, ale nie podawać wartości dla `DeadLetterEndpoint` . Nie musisz usuwać subskrypcji zdarzeń.

> [!NOTE]
> Jeśli używasz usługi Azure PowerShell na komputerze lokalnym, użyj Azure PowerShell w wersji 1.1.0 lub nowszej. Pobierz i zainstaluj najnowszą Azure PowerShell z [usługi Azure downloads](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Ustaw zasady ponawiania

Podczas tworzenia subskrypcji Event Grid można ustawić wartości, o ile długo Event Grid powinna próbować dostarczyć zdarzenia. Domyślnie program Event Grid próbuje wykonać przez 24 godziny (1440 minut) lub 30 razy. Można ustawić jedną z tych wartości dla subskrypcji usługi Event Grid. Wartość czasu wygaśnięcia zdarzenia musi być liczbą całkowitą z przedziału od 1 do 1440. Wartość maksymalna liczba ponownych prób musi być liczbą całkowitą z przedziału od 1 do 30.

Nie można skonfigurować [harmonogramu ponownych prób](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ustawić czas wygaśnięcia zdarzenia na żywo na wartość inną niż 1440 minut, użyj:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, użyj:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> Po ustawieniu obu tych opcji `event-ttl` i `max-deliver-attempts` , Event Grid używa pierwszej do wygaśnięcia, aby określić, kiedy należy zatrzymać dostarczanie zdarzeń. Na przykład jeśli ustawisz 30 minut jako czas wygaśnięcia (TTL) i 10 maksymalnej liczby prób dostarczenia. Gdy zdarzenie nie zostanie dostarczone po 30 minutach (lub) nie zostanie dostarczone po 10 próbach, w zależności od tego, gdy wystąpi pierwsze, zdarzenie zostanie utracone.  

### <a name="powershell"></a>Program PowerShell

Aby ustawić czas wygaśnięcia zdarzenia na żywo na wartość inną niż 1440 minut, użyj:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Aby ustawić maksymalną liczbę ponownych prób na wartość inną niż 30, użyj:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> Po ustawieniu obu tych opcji `event-ttl` i `max-deliver-attempts` , Event Grid używa pierwszej do wygaśnięcia, aby określić, kiedy należy zatrzymać dostarczanie zdarzeń. Na przykład jeśli ustawisz 30 minut jako czas wygaśnięcia (TTL) i 10 maksymalnej liczby prób dostarczenia. Gdy zdarzenie nie zostanie dostarczone po 30 minutach (lub) nie zostanie dostarczone po 10 próbach, w zależności od tego, gdy wystąpi pierwsze, zdarzenie zostanie utracone.  

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładową aplikacją, która używa aplikacji funkcji platformy Azure do przetwarzania zdarzeń utraconych wiadomości, zobacz [Azure Event Grid utraconych próbek dla platformy .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
