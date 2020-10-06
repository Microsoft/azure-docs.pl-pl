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
ms.openlocfilehash: d40637b05a5d398124ee52dbca49d55b3112ec95
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758408"
---
# <a name="sms-client-library-overview"></a>Omówienie biblioteki klienta wiadomości SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

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
| Numer telefonu      | Bezpłatne numery telefonów                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Normatywn        | Rezygnacja z obsługi                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorowanie        | Monitoruj użycie komunikatów wysłanych i odebranych                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Wywołanie PSTN      | Dodawanie możliwości wywoływania sieci PSTN do numeru bezpłatnego z włączoną obsługą programu SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z ogólnymi [pojęciami programu SMS](../telephony-sms/concepts.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Planowanie rozwiązania programu SMS](../telephony-sms/plan-solution.md)
