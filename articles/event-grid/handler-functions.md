---
title: Używanie funkcji na platformie Azure jako programu obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można używać funkcji utworzonych w programach i hostowanych przez Azure Functions jako programy obsługi zdarzeń dla zdarzeń Event Grid.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f547b09fe7e62eb3fa9e02bd17298a936350f871
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496545"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Używanie funkcji jako programu obsługi zdarzeń dla zdarzeń Event Grid

Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi wykonuje akcję, aby przetworzyć zdarzenie. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Functions** jest jednym z nich. 


Aby użyć funkcji na platformie Azure jako procedury obsługi dla zdarzeń, wykonaj jedną z następujących metod: 

-   Użyj [wyzwalacza Event Grid](../azure-functions/functions-bindings-event-grid-trigger.md).  Określ **funkcję platformy Azure** jako **Typ punktu końcowego**. Następnie określ aplikację funkcji i funkcję, która będzie obsługiwać zdarzenia. 
-   Użyj [wyzwalacza http](../azure-functions/functions-bindings-http-webhook.md).  Określ **element Hook sieci Web** jako **Typ punktu końcowego**. Następnie określ adres URL funkcji, która będzie obsługiwać zdarzenia. 

Zalecamy używanie pierwszego podejścia (Event Grid wyzwalacza), ponieważ ma ono następujące zalety w porównaniu z drugim podejściem:
-   Event Grid automatycznie weryfikuje wyzwalacze Event Grid. W przypadku ogólnych wyzwalaczy HTTP należy samodzielnie zaimplementować [odpowiedź na weryfikację](webhook-event-delivery.md) .
-   Event Grid automatycznie dostosowuje Częstotliwość dostarczania zdarzeń do funkcji wyzwalanej przez zdarzenie Event Grid na podstawie postrzeganej szybkości, z jaką funkcja może przetwarzać zdarzenia. Ta funkcja dopasowania współczynnika powoduje, że nie są dostępne błędy dostarczania, które wynikają z niezdolności funkcji do przetwarzania zdarzeń, ponieważ szybkość przetwarzania zdarzeń funkcji może się różnić w miarę upływu czasu. Aby zwiększyć wydajność przy wysokiej przepływności, należy włączyć przetwarzanie wsadowe w ramach subskrypcji zdarzeń. Aby uzyskać więcej informacji, zobacz [Włączanie przetwarzania wsadowego](#enable-batching).

    > [!NOTE]
    > Obecnie nie można używać wyzwalacza Event Grid dla aplikacji funkcji, gdy zdarzenie jest dostarczane w schemacie **CloudEvents** . Zamiast tego należy użyć wyzwalacza HTTP.

## <a name="tutorials"></a>Samouczki

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: obsługa zdarzeń za pomocą funkcji](custom-event-to-function.md) | Wysyła niestandardowe zdarzenie do funkcji do przetworzenia. |
| [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy mogą przekazywać obrazy za poorednictwem aplikacji sieci Web do konta magazynu. Po utworzeniu obiektu blob magazynu Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła komunikaty z Service Bus tematu do aplikacji funkcji i aplikacji logiki. |

## <a name="rest-example-for-put"></a>Przykład REST (na potrzeby PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Włącz przetwarzanie wsadowe
W celu uzyskania wyższej przepływności należy włączyć przetwarzanie wsadowe w ramach subskrypcji. Jeśli używasz Azure Portal, możesz ustawić maksymalne zdarzenia na partię i preferowany rozmiar wsadu w kilobajtach w czasie tworzenia subskrypcji lub po utworzeniu. 

Ustawienia usługi Batch można skonfigurować przy użyciu szablonu Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub Menedżer zasobów. 

### <a name="azure-portal"></a>Azure Portal
W czasie tworzenia subskrypcji w interfejsie użytkownika na stronie **Tworzenie subskrypcji zdarzeń** przejdź do karty **funkcje zaawansowane** , a następnie ustaw wartości w polu **Maksymalna liczba zdarzeń na partię** i **preferowany rozmiar partii (w kilobajtach**). 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Włącz przetwarzanie wsadowe w momencie tworzenia subskrypcji":::

Te wartości dla istniejącej subskrypcji można zaktualizować na karcie **funkcje** na stronie **tematu Event Grid** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Włącz przetwarzanie wsadowe po utworzeniu":::

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
W szablonie Azure Resource Manager można ustawić **maxEventsPerBatch** i **preferredBatchSizeInKilobytes** . Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu Microsoft. EventGrid eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby skonfigurować ustawienia związane z usługą Batch przy użyciu następujących parametrów, można użyć polecenia [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) lub [AZ eventgrid Event-Subscription Update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) . `--max-events-per-batch` `--preferred-batch-size-in-kilobytes`

### <a name="azure-powershell"></a>Azure PowerShell
Za pomocą polecenia cmdlet [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) lub [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) można skonfigurować ustawienia związane z usługą Batch, korzystając z następujących parametrów: `-MaxEventsPerBatch` lub `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> W przypadku korzystania z wyzwalacza Event Grid usługa Event Grid Pobiera klucz tajny klienta dla docelowej funkcji platformy Azure i używa jej do dostarczania zdarzeń do funkcji platformy Azure. W przypadku ochrony funkcji platformy Azure za pomocą aplikacji Azure Active Directory należy zastosować podejście ogólnego elementu Hook sieci Web i użyć wyzwalacza HTTP.

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) .
