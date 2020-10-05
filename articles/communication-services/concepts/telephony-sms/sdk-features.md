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
ms.openlocfilehash: 95a7ae951edab8e75894b0e9aa85029a54a50acb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91664923"
---
# <a name="sms-client-library-overview"></a>Omówienie biblioteki klienta wiadomości SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

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
| Wywołanie PSTN      | Dodawanie możliwości wywoływania funkcji PSTN do numeru bezpłatnego z włączoną obsługą programu SMS (prywatna wersja zapoznawcza)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z ogólnymi [pojęciami programu SMS](../telephony-sms/concepts.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Planowanie rozwiązania programu SMS](../telephony-sms/plan-solution.md)
