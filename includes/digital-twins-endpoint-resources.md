---
author: baanders
description: plik dołączany dla zasobów, które są konieczne do tworzenia punktów końcowych usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054515"
---
### <a name="prerequisite-create-endpoint-resources"></a>Wymaganie wstępne: Tworzenie zasobów punktu końcowego

Aby połączyć punkt końcowy z usługą Azure Digital bliźniaczych reprezentacji, temat usługi Event Grid, centrum zdarzeń lub Service Bus tematu, który jest używany dla punktu końcowego, musi już istnieć.

Skorzystaj z poniższej tabeli, aby sprawdzić, jakie zasoby powinny zostać skonfigurowane przed utworzeniem punktu końcowego.

| Typ punktu końcowego | Wymagane zasoby (powiązane z instrukcjami tworzenia) |
| --- | --- |
| Event Grid punkt końcowy | [temat dotyczący siatki zdarzeń](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs punkt końcowy | [&nbsp;Przestrzeń nazw centrów zdarzeń &nbsp;](../articles/event-hubs/event-hubs-create.md)<br/><br/>[centrum zdarzeń](../articles/event-hubs/event-hubs-create.md)<br/><br/>Obowiązkowe [reguła autoryzacji](../articles/event-hubs/authorize-access-shared-access-signature.md) dla uwierzytelniania opartego na kluczach | 
| Service Bus punkt końcowy | [Service Bus przestrzeń nazw](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Temat Service Bus](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Obowiązkowe [reguła autoryzacji](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) dla uwierzytelniania opartego na kluczach|
