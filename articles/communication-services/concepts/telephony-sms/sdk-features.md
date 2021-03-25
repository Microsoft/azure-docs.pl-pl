---
title: Omówienie zestawu SDK programu SMS dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie zestawu SDK programu SMS i jego ofert.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25a5befab6ef141b059caaac7b1784d47f2f93cf
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108115"
---
# <a name="sms-sdk-overview"></a>Omówienie zestawu SDK programu SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Zestawy SDK SMS usługi Azure Communication Services umożliwiają dodawanie wiadomości SMS do aplikacji.

## <a name="sms-sdk-capabilities"></a>Możliwości zestawu SMS SDK

Poniższa lista przedstawia zbiór funkcji, które są obecnie dostępne w naszych zestawach SDK.

| Grupa funkcji | Możliwość                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Podstawowe możliwości | Wysyłanie i odbieranie wiadomości SMS </br> *Obsługiwane znaki emoji Unicode*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Otrzymywanie raportów o dostarczeniu dla wysłanych komunikatów                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Wszystkie zestawy znaków (obsługa języka/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Obsługa długich komunikatów (do 2048 znaków)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Autołączenie długich komunikatów                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Zdarzenia            | Użyj Event Grid, aby skonfigurować elementy webhook do odbierania komunikatów przychodzących i raportów dostarczania | ✔️   | ✔️    | ✔️    | ✔️      |
| Numer telefonu      | Numery Toll-Free                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Normatywn        | Obsługa Opt-Out                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorowanie        | Monitoruj użycie komunikatów wysłanych i odebranych                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Wywołanie PSTN      | Dodawanie możliwości wywoływania sieci PSTN do numeru bezpłatnego z włączoną obsługą programu SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z ogólnymi [pojęciami programu SMS](../telephony-sms/concepts.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Typy numerów telefonów w usłudze Azure Communications Services](../telephony-sms/plan-solution.md)
