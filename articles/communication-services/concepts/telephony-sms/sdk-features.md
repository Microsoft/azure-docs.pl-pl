---
title: Omówienie biblioteki klienta programu SMS dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie biblioteki klienta programu SMS i jej ofert.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ec468f070b5dd418772aeea8760b5d0b270e093a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487512"
---
# <a name="sms-client-library-overview"></a>Omówienie biblioteki klienta wiadomości SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Za pomocą bibliotek klienckich SMS usług Azure Communication Services można dodawać wiadomości SMS do aplikacji.

## <a name="sms-client-library-capabilities"></a>Możliwości biblioteki klienta programu SMS

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w naszych bibliotekach klientów.

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
