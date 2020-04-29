---
title: Obsługa zdarzeń Azure Event Grid
description: Opisuje obsługiwane procedury obsługi zdarzeń dla Azure Event Grid. Azure Automation, funkcje, Event Hubs, Połączenia hybrydowe, Logic Apps, Service Bus, Queue Storage, elementy webhook.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 73118823aec9b8d4bd872986b13f19496240c0b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393438"
---
# <a name="event-handlers-in-azure-event-grid"></a>Programy obsługi zdarzeń w Azure Event Grid

Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń. Możesz również użyć dowolnego elementu webhook do obsługi zdarzeń. Elementy webhook nie muszą być hostowane na platformie Azure, aby obsługiwać zdarzenia. Event Grid obsługuje tylko punkty końcowe elementu webhook protokołu HTTPS.

Ten artykuł zawiera linki do zawartości dla każdego programu obsługi zdarzeń.

## <a name="azure-automation"></a>Azure Automation

Użyj Azure Automation, aby przetwarzać zdarzenia za pomocą zautomatyzowanych elementów Runbook.

|Tytuł  |Opis  |
|---------|---------|
|[Samouczek: Azure Automation z Event Grid i Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element Runbook usługi Automation, który oznacza maszynę wirtualną, i wyzwala komunikat wysyłany do kanału Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Użyj Azure Functions, aby uzyskać bezserwerową odpowiedź do zdarzeń.

W przypadku używania usługi Azure Functions jako procedury obsługi należy korzystać z wyzwalaczy usługi Event Grid, a nie ogólnych wyzwalaczy HTTP. Usługa Event Grid automatycznie weryfikuje wyzwalacze funkcji usługi Event Grid. W przypadku ogólnych wyzwalaczy HTTP trzeba zaimplementować [odpowiedź weryfikacji](security-authentication.md#webhook-event-delivery).

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: obsługa zdarzeń za pomocą funkcji](custom-event-to-function.md) | Wysyła niestandardowe zdarzenie do funkcji do przetworzenia. |
| [Wyzwalacz Event Grid dla Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Omówienie korzystania z wyzwalacza Event Grid w funkcjach. |
| [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy mogą przekazywać obrazy za poorednictwem aplikacji sieci Web do konta magazynu. Po utworzeniu obiektu blob magazynu Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła wiadomości z tematu Service Bus do aplikacji funkcji i aplikacji logiki. |

## <a name="event-hubs"></a>Event Hubs

Użyj Event Hubs, gdy rozwiązanie pobiera zdarzenia szybciej niż może przetworzyć zdarzenia. Aplikacja przetwarza zdarzenia z Event Hubs zgodnie z harmonogramem. Przetwarzanie zdarzenia można skalować w celu obsługi zdarzeń przychodzących.

Event Hubs może działać jako źródło zdarzenia lub procedura obsługi zdarzeń. W poniższym artykule przedstawiono sposób użycia Event Hubs jako programu obsługi programu.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure Event Hubs przy użyciu interfejsu wiersza polecenia platformy Azure i Event Grid](custom-event-to-eventhub.md) | Wysyła niestandardowe zdarzenie do centrum zdarzeń w celu przetworzenia przez aplikację. |
| [Szablon Menedżer zasobów: temat niestandardowy i Event Hubs punkt końcowy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżer zasobów, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do Event Hubs platformy Azure. |

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Użyj Połączenia hybrydowe Azure Relay, aby wysyłać zdarzenia do aplikacji znajdujących się w sieci przedsiębiorstwa i nie mają publicznie dostępnego punktu końcowego.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: wysyłanie zdarzeń do połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła niestandardowe zdarzenie do istniejącego połączenia hybrydowego w celu przetworzenia przez aplikację odbiornika. |

## <a name="logic-apps"></a>Logic Apps

Użyj Logic Apps, aby zautomatyzować procesy biznesowe w celu reagowania na zdarzenia.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian maszyny wirtualnej przy użyciu Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Samouczek: wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do centrum IoT. |
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła wiadomości z tematu Service Bus do aplikacji funkcji i aplikacji logiki. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Kolejki usługi Service Bus

Można kierować zdarzenia w Event Grid bezpośrednio do kolejek Service Bus do użycia w scenariuszach buforowania lub & poleceń w aplikacjach dla przedsiębiorstw.

W Azure Portal podczas tworzenia subskrypcji zdarzeń wybierz pozycję "Service Bus kolejki" jako typ punktu końcowego, a następnie kliknij pozycję "Wybierz punkt końcowy", aby wybrać kolejkę Service Bus.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Używanie interfejsu wiersza polecenia do dodawania procedury obsługi kolejki Service Bus

W przypadku interfejsu wiersza polecenia platformy Azure Poniższy przykład subskrybuje i łączy temat usługi Event Grid z kolejką Service Bus:

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

### <a name="service-bus-topics"></a>Tematy Service Bus

Zdarzenia w Event Grid można kierować bezpośrednio do tematów Service Bus, aby obsłużyć zdarzenia systemu Azure za pomocą tematów Service Bus lub w przypadku scenariuszy z obsługą poleceń &.

W Azure Portal podczas tworzenia subskrypcji zdarzeń wybierz pozycję "Service Bus temat" jako typ punktu końcowego, a następnie kliknij pozycję "Wybierz i punkt końcowy", aby wybrać temat Service Bus.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Używanie interfejsu wiersza polecenia w celu dodania procedury obsługi tematu Service Bus

W przypadku interfejsu wiersza polecenia platformy Azure Poniższy przykład subskrybuje i łączy temat usługi Event Grid z kolejką Service Bus:

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

Używaj magazynu kolejek do odbierania zdarzeń wymagających ściągnięcia. Możesz użyć magazynu kolejek, gdy masz długotrwały proces, który trwa zbyt długo. Wysyłając zdarzenia do magazynu kolejek, aplikacja może ściągać i przetwarzać zdarzenia zgodnie z własnym harmonogramem.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure queue storage przy użyciu interfejsu wiersza polecenia platformy Azure i Event Grid](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |

## <a name="webhooks"></a>Elementy webhook

Użyj elementów webhook dla dostosowywalnych punktów końcowych, które reagują na zdarzenia.

|Tytuł  |Opis  |
|---------|---------|
| Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą [interfejsu wiersza polecenia platformy Azure](custom-event-quickstart.md), [programu PowerShell](custom-event-quickstart-powershell.md)i [portalu](custom-event-quickstart-portal.md). | Pokazuje, jak wysyłać zdarzenia niestandardowe do elementu webhook. |
| Szybki Start: kierowanie zdarzeń usługi BLOB Storage do niestandardowego punktu końcowego sieci Web przy użyciu [interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)i [portalu](blob-event-quickstart-portal.md). | Pokazuje, jak wysyłać zdarzenia magazynu obiektów BLOB do elementu webhook. |
| [Szybki Start: wysyłanie zdarzeń rejestru kontenera](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń Container Registry. |
| [Przegląd: odbieranie zdarzeń do punktu końcowego HTTP](receive-events.md) | Opisuje sposób sprawdzania poprawności punktu końcowego HTTP w celu odbierania zdarzeń z subskrypcji zdarzeń oraz odbierania i deserializacji zdarzeń. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
