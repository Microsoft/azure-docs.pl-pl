---
title: Azure Event Hubs — zestawy SDK klienta | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące zestawów SDK klienta dla platformy Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/26/2020
ms.author: spelluru
ms.openlocfilehash: ffa0663f392c4dbf290a244e2c95422a8855debf
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267956"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs — zestawy SDK klienta
Ten artykuł zawiera następujące informacje dotyczące zestawów SDK obsługiwanych przez usługę Azure Event Hubs: 

- Lokalizacja pakietu, którego można używać w aplikacjach 
- Lokalizacja usługi GitHub, w której można znaleźć kod źródłowy, przykłady, plik Readme, dziennik zmian, zgłaszane problemy, a także zgłaszać nowe problemy 
- Linki do samouczków szybkiego startu 

## <a name="client-sdks"></a>Zestawy SDK klienta
W poniższej tabeli opisano wszystkie aktualnie dostępne klientów środowiska uruchomieniowego platformy Azure Event Hubs. Niektóre z tych bibliotek zawierają również ograniczoną funkcjonalność zarządzania, ale istnieją również pewne biblioteki przeznaczone do operacji zarządzania. Podstawowym fokusem tych bibliotek jest **wysyłanie i odbieranie komunikatów** z centrum zdarzeń.

| Język | Pakiet | Dokumentacja | 
| -------- | ------- | --------------- | 
| . NET Standard (**Najnowsza** i obsługa platformy .NET Core i .NET Framework) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Samouczek](get-started-dotnet-standard-send-v2.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Samouczek](get-started-dotnet-standard-send-v2.md)</li></ul> |
| . NET Standard (**Starsza wersja** i obsługuje zarówno .NET Core, jak i .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Samouczek](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Samouczek](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . .NET Framework (**stary**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Samouczek](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [Azure-Messaging — eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Samouczek](get-started-java-send-v2.md)</li></ul> |
|      | [Azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(starsza wersja)** | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Samouczek](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure — centrum eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Samouczek](get-started-python-send-v2.md)</li></ul> |
|        | [Azure-eventhub-checkpointstoreblob-AIO](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Samouczek](get-started-python-send-v2.md)</li></ul> |
| JavaScript | [Azure/Event-Hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Samouczek](get-started-node-send-v2.md)</li></ul> |
|            | [Azure/eventhubs — checkpointstore — obiekt BLOB](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Samouczek](get-started-node-send-v2.md)</li></ul> |
| Przejdź | [Azure-Event-Hubs — przejdź](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Samouczek](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-Hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Samouczek](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Zestawy SDK zarządzania
W poniższej tabeli wymieniono wszystkie aktualnie dostępne biblioteki specyficzne dla zarządzania. Żadna z tych bibliotek nie zawiera operacji środowiska uruchomieniowego i służy wyłącznie do **zarządzania jednostkami Event Hubs**.

| Język | Pakiet | Dokumentacja | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Lokalizacja usługi GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Samouczek](get-started-dotnet-standard-send-v2.md)</li></ul> |


## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
