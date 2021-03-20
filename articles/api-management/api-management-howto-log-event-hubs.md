---
title: Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management. Event Hubs to wysoce skalowalna usługa transferu danych.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072411"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management
Azure Event Hubs to wysoce skalowalna usługa transferu danych przychodzących, która może obsługiwać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Event Hubs działa jako "drzwi tylne" dla potoku zdarzeń, a po zebraniu danych do centrum zdarzeń można je przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart wsadowych/magazynowych. Usługa Event Hubs oddziela wytwarzanie strumienia zdarzeń od użycia tych zdarzeń, dzięki czemu odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z własnym harmonogramem.

Ten artykuł stanowi pomocnika [integracji API Management platformy Azure z Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) wideo i opisuje sposób rejestrowania zdarzeń API Management przy użyciu usługi Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia centrum zdarzeń i uzyskiwania parametrów połączenia, które są potrzebne do wysyłania i odbierania zdarzeń do i z centrum zdarzeń, zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Tworzenie rejestratora API Management
Teraz, gdy masz centrum zdarzeń, następnym krokiem jest skonfigurowanie [rejestratora](/rest/api/apimanagement/2019-12-01/logger) w usłudze API Management, dzięki czemu może on rejestrować zdarzenia w centrum zdarzeń.

Rejestratory API Management są konfigurowane przy użyciu [interfejsu API REST API Management](/rest/api/apimanagement/ApiManagementREST/API-Management-REST). Szczegółowe przykłady żądań można znaleźć w temacie [How to Create rejestrators](/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhub-policies"></a>Konfigurowanie zasad rejestrowania w usłudze eventhub

Po skonfigurowaniu rejestratora w API Management można skonfigurować zasady logowania do centrum danych w celu rejestrowania żądanych zdarzeń. Zasady log-to-eventhub można używać w sekcji zasady ruchu przychodzącego lub w sekcji zasady wychodzące.

1. Przejdź do swojego wystąpienia usługi APIM.
2. Wybierz kartę API.
3. Wybierz interfejs API, do którego chcesz dodać zasady. W tym przykładzie dodamy zasady do **interfejsu API ECHA** w **nieograniczonego** produktu.
4. Wybierz opcję **Wszystkie operacje**.
5. W górnej części ekranu wybierz kartę projektowanie.
6. W oknie przetwarzanie przychodzące lub wychodzące kliknij trójkąt (obok ołówka).
7. Wybierz Edytor kodu. Aby uzyskać więcej informacji, zobacz [jak ustawić lub edytować zasady](set-edit-policies.md).
8. Umieść kursor w `inbound` `outbound` sekcji lub zasad.
9. W oknie po prawej stronie wybierz pozycję dziennik **zasad zaawansowanych** w  >  **centrum EventHub**. Spowoduje to wstawienie `log-to-eventhub` szablonu instrukcji zasad.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Zamień na `logger-id` wartość użytą `{loggerId}` w adresie URL żądania, aby utworzyć rejestrator w poprzednim kroku.

Możesz użyć dowolnego wyrażenia, które zwraca ciąg jako wartość `log-to-eventhub` elementu. W tym przykładzie jest rejestrowany ciąg w formacie JSON zawierający datę i godzinę, nazwę usługi, identyfikator żądania, adres IP żądania i nazwę operacji.

Kliknij przycisk **Zapisz** , aby zapisać zaktualizowaną konfigurację zasad. Gdy tylko zostanie zapisany, zasady są aktywne, a zdarzenia są rejestrowane w wydzielonym centrum zdarzeń.

> [!NOTE]
> Maksymalny obsługiwany rozmiar komunikatu, który można wysłać do centrum zdarzeń z tych zasad API Management, to 200 kilobajtów (KB). Jeśli komunikat wysyłany do centrum zdarzeń jest większy niż 200 KB, zostanie automatycznie obcięty, a skrócony komunikat zostanie przetransferowany do centrów zdarzeń.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Podgląd logowania Event Hubs przy użyciu Azure Stream Analytics

Możesz wyświetlić podgląd dziennika w Event Hubs przy użyciu [zapytań Azure Stream Analytics](../event-hubs/process-data-azure-stream-analytics.md). 

1. W Azure Portal przejdź do centrum zdarzeń, do którego Rejestrator wysyła zdarzenia. 
2. W obszarze **funkcje** wybierz kartę **dane procesu** .
3. Na karcie **Włącz szczegółowe informacje w czasie rzeczywistym na podstawie zdarzeń** wybierz pozycję **Eksploruj**.
4. Powinien być w stanie wyświetlić podgląd dziennika na karcie **Podgląd danych wejściowych** . Jeśli wyświetlane dane nie są bieżące, wybierz pozycję **Odśwież** , aby wyświetlić najnowsze zdarzenia.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Przewodnik programowania Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji API Management i Event Hubs
  * [Odwołanie do jednostki rejestratora](/rest/api/apimanagement/2019-12-01/logger)
  * [Dokumentacja zasad logowania do centrum eventhub](./api-management-advanced-policies.md#log-to-eventhub)
  * [Monitoruj interfejsy API przy użyciu usługi Azure API Management, Event Hubs i Moesif](api-management-log-to-eventhub-sample.md)  
* Dowiedz się więcej [na temat integracji z usługą Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png