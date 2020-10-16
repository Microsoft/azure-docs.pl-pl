---
title: Definicje metryk dla usługi komunikacyjnej platformy Azure
titleSuffix: An Azure Communication Services concept document
description: W tym dokumencie opisano definicje metryk dostępnych w Azure Portal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b8d74f86fc9c3d2cc020628f67c36d8d67973fff
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128654"
---
# <a name="metrics-overview"></a>Przegląd metryk

Usługi Azure Communication Services obecnie udostępniają metryki dla programów chat i SMS. Za pomocą [usługi Azure Eksplorator metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) można wykreślać własne wykresy, badać nienormalne wartości metryk i zrozumieć ruch związany z interfejsem API przy użyciu danych metryk wysyłanych przez czat i żądania SMS.

## <a name="where-to-find-metrics"></a>Gdzie można znaleźć metryki

Usługi chat i SMS w usłudze Azure Communications Services emitują metryki dla żądań interfejsu API. Te metryki można znaleźć w bloku metryki w ramach zasobu usług komunikacyjnych. Stałe pulpity nawigacyjne można także tworzyć przy użyciu bloku skoroszyty w ramach zasobu usług komunikacyjnych.

## <a name="metric-definitions"></a>Definicje metryk

Istnieją dwa typy żądań, które są reprezentowane w ramach metryk usług komunikacyjnych: **żądania interfejsu API rozmowy** i **żądania interfejsu API programu SMS**.

Metryki żądań interfejsu API rozmowy i SMS zawierają trzy wymiary, których można użyć do filtrowania danych metryk. Te wymiary mogą być agregowane przy użyciu `Count` typu agregacji i obsługują wszystkie standardowe serie czasowe agregacji platformy Azure, w tym `Sum` ,, `Average` `Min` i `Max` .

Aby uzyskać więcej informacji na temat obsługiwanych typów agregacji i agregacji szeregów czasowych, można znaleźć [Zaawansowane funkcje usługi Azure Eksplorator metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#changing-aggregation)

- **Operacja** — wszystkie operacje lub trasy, które mogą być wywoływane w bramie rozmowy ACS.
- **Kod stanu** — odpowiedź dotycząca kodu stanu wysłana po żądaniu.
- **StatusSubClass** — seria kodów stanu wysłana po odpowiedzi. 


### <a name="chat-api-request-metric-operations"></a>Operacje metryk żądań interfejsu API rozmowy

W metrykach żądań interfejsu API rozmowy dostępne są następujące operacje:

| Operacja/trasa    | Opis                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Pobiera komunikat o identyfikatorze wiadomości. |
| ListChatMessages     | Pobiera listę komunikatów rozmowy z wątku. |
| SendChatMessage      | Wysyła komunikat rozmowy do wątku. |
| UpdateChatMessage    | Aktualizuje komunikat rozmowy. |
| DeleteChatMessage    | Usuwa komunikat rozmowy. |
| GetChatThread        | Pobiera wątek rozmowy. |
| ListChatThreads      | Pobiera listę wątków rozmowy użytkownika. |
| UpdateChatThread     | Aktualizuje właściwości wątku rozmowy. |
| CreateChatThread     | Tworzy wątek rozmowy. |
| DeleteChatThread     | Usuwa wątek. |
| GetReadReceipts      | Pobiera potwierdzenia odczytu dla wątku. |
| SendReadReceipt      | Wysyła zdarzenie potwierdzenia odczytu do wątku w imieniu użytkownika. |
| SendTypingIndicator           | W imieniu użytkownika publikuje zdarzenie wpisywania do wątku. |
| ListChatThreadParticipants    | Pobiera członków wątku. |
| AddChatThreadParticipants     | Dodaje elementy członkowskie wątku do wątku. Jeśli członkowie już istnieją, zmiany nie są wykonywane. |
| RemoveChatThreadParticipant   | Usuń element członkowski z wątku. |

:::image type="content" source="./media/chat-metric.png" alt-text="Metryka żądania interfejsu API rozmowy.":::

Jeśli zostanie wysłane żądanie do operacji, która nie została rozpoznana, otrzymasz odpowiedź na wartość "zła trasa".

### <a name="sms-api-requests"></a>Żądania interfejsu API programu SMS

W metrykach żądań interfejsu API programu SMS są dostępne następujące operacje:

| Operacja/trasa    | Opis                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Wysyła wiadomość SMS. |
| SMSDeliveryReportsReceived     | Pobiera raporty dostarczania SMS |
| SMSMessagesReceived      | Pobiera wiadomości SMS. |


:::image type="content" source="./media/sms-metric.png" alt-text="Metryka żądania interfejsu API rozmowy.":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metrykach platformy danych](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)
