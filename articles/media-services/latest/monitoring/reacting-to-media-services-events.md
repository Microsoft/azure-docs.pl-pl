---
title: Oddziałanie do Azure Media Services zdarzeń
description: W tym artykule opisano sposób korzystania z Azure Event Grid w celu subskrybowania Media Services zdarzeń.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9bed493ca37d21d0c3e5c289bb8c26607975bdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609722"
---
# <a name="handling-event-grid-events"></a>Obsługa zdarzeń usługi Event Grid

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Zdarzenia Media Services umożliwiają aplikacjom reagowanie na różne zdarzenia (na przykład zdarzenie zmiany stanu zadania) korzystające z nowoczesnych architektur bezserwerowych. Robi to bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pomocą [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)lub nawet do własnego elementu webhook, i płacisz tylko za to, czego używasz. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Dostępność zdarzeń Media Services jest powiązana z Event Grid [dostępnością](../../../event-grid/overview.md) i stanie się dostępna w innych regionach jako Event Grid.  

## <a name="media-services-events-and-schemas"></a>Media Services zdarzenia i schematy

Funkcja Event Grid używa [subskrypcji zdarzeń](../../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Zdarzenia Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft. Media.".

Aby uzyskać więcej informacji, zobacz [Media Services schematów zdarzeń](../media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia Media Services, powinny spełniać kilka zalecanych praktyk:

* Ponieważ można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie przyjmować zdarzeń z określonego źródła, ale w celu sprawdzenia, czy pochodzi ona z konta magazynu, którego oczekujesz.
* Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
* Ignoruj pola, które nie są zrozumiałe.  Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
* Użyj prefiksu "podmiot" i dopasowania sufiksu, aby ograniczyć zdarzenia do określonego zdarzenia.

> [!NOTE]
> Zdarzenia podlegają [Umowa dotycząca poziomu usług Event Grid (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Jeśli chcesz otrzymywać powiadomienia o zdarzeniach przy użyciu interfejsów API, zobacz przykłady użycia zdarzeń przy użyciu [zestawu .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) lub [zestawu Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie zdarzeń — Portal](../monitor-events-portal-how-to.md)
* [Monitorowanie zdarzeń — interfejs wiersza polecenia](../job-state-events-cli-how-to.md)
