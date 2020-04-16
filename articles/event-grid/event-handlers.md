---
title: Programy obsługi zdarzeń usługi Azure Event Grid
description: Zawiera opis obsługiwanych programów obsługi zdarzeń dla usługi Azure Event Grid. Automatyzacja platformy Azure, funkcje, centra zdarzeń, połączenia hybrydowe, aplikacje logiki, magistrala usług, magazyn kolejek, elementy webhook.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 73118823aec9b8d4bd872986b13f19496240c0b9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393438"
---
# <a name="event-handlers-in-azure-event-grid"></a>Programy obsługi zdarzeń w usłudze Azure Event Grid

Program obsługi zdarzeń jest miejscem, w którym jest wysyłane zdarzenie. Program obsługi podejmuje kilka dalszych działań do przetwarzania zdarzenia. Kilka usług platformy Azure są automatycznie skonfigurowane do obsługi zdarzeń. Można również użyć dowolnego elementu WebHook do obsługi zdarzeń. Element WebHook nie musi być hostowany na platformie Azure do obsługi zdarzeń. Usługa Event Grid obsługuje tylko punkty końcowe https webhook.

Ten artykuł zawiera łącza do zawartości dla każdego programu obsługi zdarzeń.

## <a name="azure-automation"></a>Azure Automation

Usługa Azure Automation umożliwia przetwarzanie zdarzeń za pomocą automatycznych śmiób.

|Tytuł  |Opis  |
|---------|---------|
|[Samouczek: Automatyzacja platformy Azure z siatką zdarzeń i zespołami Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element runbook automatyzacji, który oznacza maszynę wirtualną i wyzwala komunikat, który jest wysyłany do kanału usługi Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Użyj usługi Azure Functions dla bezserwerowej odpowiedzi na zdarzenia.

W przypadku używania usługi Azure Functions jako procedury obsługi należy korzystać z wyzwalaczy usługi Event Grid, a nie ogólnych wyzwalaczy HTTP. Usługa Event Grid automatycznie weryfikuje wyzwalacze funkcji usługi Event Grid. W przypadku ogólnych wyzwalaczy HTTP trzeba zaimplementować [odpowiedź weryfikacji](security-authentication.md#webhook-event-delivery).

|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: obsługa zdarzeń za pomocą funkcji](custom-event-to-function.md) | Wysyła zdarzenie niestandardowe do funkcji do przetwarzania. |
| [Wyzwalacz siatki zdarzeń dla funkcji platformy Azure](../azure-functions/functions-bindings-event-grid.md) | Omówienie używania wyzwalacza siatki zdarzeń w funkcjach. |
| [Samouczek: automatyzacja zmiany rozmiaru przesłanych obrazów przy użyciu siatki zdarzeń](resize-images-on-storage-blob-upload-event.md) | Użytkownicy przesyłają obrazy za pośrednictwem aplikacji internetowej na konto magazynu. Po utworzeniu obiektu blob magazynu usługa Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe dużych zbiorów danych do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Centra zdarzeń tworzy plik przechwytywania, usługa Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik Przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: przykłady integracji usługi Azure Service Bus to Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła wiadomości z tematu usługi Service Bus do aplikacji funkcji i aplikacji logiki. |

## <a name="event-hubs"></a>Event Hubs

Użyj Centrum zdarzeń, gdy rozwiązanie pobiera zdarzenia szybciej niż może przetwarzać zdarzenia. Aplikacja przetwarza zdarzenia z Centrów zdarzeń w swoim własnym harmonogramie. Można skalować przetwarzania zdarzeń do obsługi zdarzeń przychodzących.

Centra zdarzeń może działać jako źródło zdarzeń lub program obsługi zdarzeń. W poniższym artykule pokazano, jak używać centrów zdarzeń jako programu obsługi.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: kierowanie zdarzeń niestandardowych do centrów zdarzeń platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure i siatki zdarzeń](custom-event-to-eventhub.md) | Wysyła zdarzenie niestandardowe do centrum zdarzeń do przetwarzania przez aplikację. |
| [Szablon Menedżera zasobów: temat niestandardowy i punkt końcowy centrów zdarzeń](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżera zasobów, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do usługi Azure Event Hubs. |

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Użyj usługi Azure Relay połączeń hybrydowych do wysyłania zdarzeń do aplikacji, które znajdują się w sieci przedsiębiorstwa i nie mają publicznie dostępnego punktu końcowego.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: wysyłanie zdarzeń do połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła zdarzenie niestandardowe do istniejącego połączenia hybrydowego do przetwarzania przez aplikację odbiornika. |

## <a name="logic-apps"></a>Logic Apps

Aplikacje logiki za pomocą automatyzacji procesów biznesowych do reagowania na zdarzenia.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian na maszynach wirtualnych za pomocą usługi Azure Event Grid i aplikacji logiki](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany na maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Samouczek: wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie jest dodawane do centrum IoT Hub. |
| [Samouczek: przykłady integracji usługi Azure Service Bus to Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła wiadomości z tematu usługi Service Bus do aplikacji funkcji i aplikacji logiki. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Kolejki usługi Service Bus

Zdarzenia w usłudze Event Grid można kierować bezpośrednio do kolejek usługi Service Bus do użycia w buforowaniu lub poleceniach & scenariuszy kontroli w aplikacjach korporacyjnych.

W witrynie Azure portal, podczas tworzenia subskrypcji zdarzeń, wybierz "Kolejka usługi Service Bus" jako typ punktu końcowego, a następnie kliknij przycisk "wybierz punkt końcowy", aby wybrać kolejkę usługi Service Bus.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Dodawanie programu obsługi kolejki usługi Service Bus za pomocą interfejsu wiersza polecenia

W przypadku interfejsu wiersza polecenia platformy Azure poniższy przykład subskrybuje i łączy temat siatki zdarzeń z kolejką usługi Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Tematy usługi Service Bus

Zdarzenia w usłudze Event Grid można kierować bezpośrednio do tematów usługi Service Bus w celu obsługi zdarzeń systemu Azure za pomocą tematów usługi Service Bus lub w przypadku scenariuszy obsługi & sterowania wiadomościami.

W witrynie Azure portal, podczas tworzenia subskrypcji zdarzeń, wybierz "Temat usługi Service Bus" jako typ punktu końcowego, a następnie kliknij "wybierz i punkt końcowy", aby wybrać temat usługi Service Bus.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Dodawanie programu obsługi tematów usługi Service Bus za pomocą interfejsu wiersza polecenia

W przypadku interfejsu wiersza polecenia platformy Azure poniższy przykład subskrybuje i łączy temat siatki zdarzeń z kolejką usługi Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

Użyj magazynu kolejki, aby odbierać zdarzenia, które muszą zostać pobrane. Magazyn kolejki może być używany, gdy masz długotrwały proces, który trwa zbyt długo, aby odpowiedzieć. Wysyłając zdarzenia do magazynu kolejki, aplikacja może ściągać i przetwarzać zdarzenia według własnego harmonogramu.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: kierowanie zdarzeń niestandardowych do magazynu kolejki platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure i siatki zdarzeń](custom-event-to-queue-storage.md) | W tym artykule opisano sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |

## <a name="webhooks"></a>Elementy webhook

Użyj webhooks dla konfigurowalnych punktów końcowych, które reagują na zdarzenia.

|Tytuł  |Opis  |
|---------|---------|
| Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą — [narzędzia Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md)i [portalu](custom-event-quickstart-portal.md). | Pokazuje, jak wysyłać niestandardowe zdarzenia do elementu WebHook. |
| Szybki start: kierowanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web za pomocą — [narzędzia Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)i [portalu](blob-event-quickstart-portal.md). | Pokazuje, jak wysyłać zdarzenia magazynu obiektów blob do elementu WebHook. |
| [Szybki start: wysyłanie zdarzeń rejestru kontenerów](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń rejestru kontenerów. |
| [Omówienie: odbieranie zdarzeń do punktu końcowego HTTP](receive-events.md) | W tym artykule opisano, jak sprawdzić poprawność punktu końcowego HTTP do odbierania zdarzeń z subskrypcji zdarzeń i odbierać i deserializacji zdarzeń. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
