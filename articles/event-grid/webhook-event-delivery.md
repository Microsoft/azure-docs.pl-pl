---
title: Dostarczanie zdarzeń elementu webhook
description: Ten artykuł zawiera opis dostarczania zdarzeń elementu webhook i walidacji punktu końcowego podczas korzystania z elementów webhook.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461242"
---
# <a name="webhook-event-delivery"></a>Dostarczanie zdarzeń elementu webhook
Elementy webhook to jeden z wielu sposobów odbierania zdarzeń z Azure Event Grid. Gdy nowe zdarzenie jest gotowe, usługa Event Grid ogłasza żądanie HTTP do skonfigurowanego punktu końcowego przy użyciu zdarzenia w treści żądania.

Podobnie jak w przypadku wielu innych usług, które obsługują elementy webhook, Event Grid wymaga potwierdzenia własności punktu końcowego elementu webhook przed rozpoczęciem dostarczania zdarzeń do tego punktu końcowego. To wymaganie uniemożliwia złośliwemu użytkownikowi przelanie punktu końcowego ze zdarzeniami. W przypadku korzystania z jednej z trzech usług platformy Azure wymienionych poniżej infrastruktura platformy Azure automatycznie obsługuje tę weryfikację:

- Azure Logic Apps za pomocą [łącznika Event Grid](/connectors/azureeventgrid/)
- Azure Automation za pośrednictwem [elementu webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions z [wyzwalaczem Event Grid](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Walidacja punktu końcowego ze zdarzeniami Event Grid
Jeśli używasz innego typu punktu końcowego, takiego jak oparty na wyzwalaczu HTTP funkcja platformy Azure, kod punktu końcowego musi uczestniczyć w uzgadnianiu walidacji z Event Grid. Event Grid obsługuje dwa sposoby walidacji subskrypcji.

1. **Uzgadnianie synchroniczne**: w momencie tworzenia subskrypcji zdarzeń Event Grid wysyła do punktu końcowego zdarzenie weryfikacji subskrypcji. Schemat tego zdarzenia jest podobny do dowolnego innego zdarzenia Event Grid. Część danych tego zdarzenia zawiera `validationCode` Właściwość. Aplikacja weryfikuje, czy żądanie weryfikacji dotyczy oczekiwanej subskrypcji zdarzeń, i zwraca kod weryfikacyjny w odpowiedzi synchronicznie. Ten mechanizm uzgadniania jest obsługiwany we wszystkich wersjach Event Grid.

2. **Uzgadnianie asynchroniczne**: w niektórych przypadkach nie można zwrócić ValidationCode w odpowiedzi synchronicznie. Na przykład w przypadku korzystania z usługi innej firmy (takiej jak [`Zapier`](https://zapier.com) lub [IFTTT](https://ifttt.com/)) nie można programowo odpowiedzieć na kod weryfikacyjny.

   Począwszy od wersji 2018-05-01-Preview, Event Grid obsługuje ręczne uzgadnianie walidacji. Jeśli tworzysz subskrypcję zdarzeń za pomocą zestawu SDK lub narzędzia korzystającego z interfejsu API w wersji 2018-05-01-Preview lub nowszej, Event Grid wysyła `validationUrl` Właściwość w części danych zdarzenia walidacji subskrypcji. Aby ukończyć uzgadnianie, Znajdź ten adres URL w danych zdarzenia i wykonaj żądanie GET. Możesz użyć klienta REST lub przeglądarki sieci Web.

   Podany adres URL jest prawidłowy przez **5 minut**. W tym czasie stan aprowizacji subskrypcji zdarzenia to `AwaitingManualAction` . Jeśli weryfikacja ręczna nie zostanie zakończona w ciągu 5 minut, stan aprowizacji zostanie ustawiony na `Failed` . Przed rozpoczęciem walidacji ręcznej należy utworzyć subskrypcję zdarzeń.

   Ten mechanizm uwierzytelniania wymaga również, aby punkt końcowy elementu webhook zwracał kod stanu HTTP 200, aby wiedział, że wpis dla zdarzenia sprawdzania poprawności został zaakceptowany, zanim będzie można go umieścić w trybie walidacji ręcznej. Innymi słowy, jeśli punkt końcowy zwraca 200, ale nie zwraca odpowiedzi walidacji synchronicznie, tryb jest przenoszony do trybu walidacji ręcznej. Jeśli w ciągu 5 minut znajduje się adres URL sprawdzania poprawności, uzgadnianie weryfikacji jest uznawane za pomyślne.

> [!NOTE]
> Sprawdzanie poprawności przy użyciu certyfikatów z podpisem własnym nie jest obsługiwane. Zamiast tego użyj podpisanego certyfikatu z komercyjnego urzędu certyfikacji.

### <a name="validation-details"></a>Szczegóły walidacji

- W momencie tworzenia/aktualizowania subskrypcji zdarzeń Event Grid ogłasza zdarzenie weryfikacji subskrypcji w docelowym punkcie końcowym.
- Zdarzenie zawiera wartość nagłówka "AEG-Event-Type: SubscriptionValidation".
- Treść zdarzenia ma ten sam schemat co inne zdarzenia Event Grid.
- Właściwość eventType zdarzenia ma wartość `Microsoft.EventGrid.SubscriptionValidationEvent` .
- Właściwość Data zdarzenia zawiera `validationCode` Właściwość z losowo generowanym ciągiem. Na przykład "validationCode: acb13...".
- Dane zdarzenia zawierają również `validationUrl` Właściwość z adresem URL służącą do ręcznego weryfikowania subskrypcji.
- Tablica zawiera tylko zdarzenie walidacji. Inne zdarzenia są wysyłane w oddzielnym żądaniu po ECHA kodu weryfikacyjnego.
- Zestawy SDK EventGrid dataplanes mają klasy odpowiadające danym zdarzenia weryfikacji subskrypcji i odpowiedzi na weryfikację subskrypcji.

Przykład SubscriptionValidationEvent pokazano w następującym przykładzie:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Aby udowodnić własność punktu końcowego, echo kod weryfikacyjny we właściwości validationResponse, jak pokazano w następującym przykładzie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Należy zwrócić kod stanu odpowiedzi HTTP 200. Akceptowany protokół HTTP 202 nie został rozpoznany jako prawidłowa odpowiedź na weryfikację subskrypcji Event Grid. Żądanie HTTP musi zakończyć się w ciągu 30 sekund. Jeśli operacja nie zakończy się w ciągu 30 sekund, operacja zostanie anulowana i może zostać ponowiona po 5 sekundach. Jeśli wszystkie próby zakończą się niepowodzeniem, będzie ono traktowane jako błąd uzgadniania walidacji.

Można też ręcznie zweryfikować subskrypcję, wysyłając żądanie GET do adresu URL walidacji. Subskrypcja zdarzeń pozostaje w stanie oczekiwania do momentu zweryfikowania. Adres URL walidacji używa portu 553. Jeśli reguły zapory blokują port 553, reguły mogą wymagać aktualizacji w celu pomyślnego uzgodnienia ręcznego.

Aby zapoznać się z przykładem obsługi uzgadniania subskrypcji, zobacz [przykład w języku C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego z CloudEvents v 1.0
Jeśli znasz już Event Grid, możesz mieć świadomość, Event Grid uzgadniania sprawdzania poprawności punktu końcowego w celu zapobiegania nadużyciom. CloudEvents v 1.0 implementuje własną [semantykę ochrony przed nadużyciami](webhook-event-delivery.md) przy użyciu metody http Options. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). W przypadku korzystania ze schematu CloudEvents dla danych wyjściowych Event Grid jest używany z ochroną nadużycia CloudEvents v 1.0 zamiast mechanizmu zdarzenia walidacji Event Grid.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym artykułem, aby dowiedzieć się, jak rozwiązywać problemy z walidacją subskrypcji zdarzeń: 

[Rozwiązywanie problemów z walidacją subskrypcji zdarzeń](troubleshoot-subscription-validation.md)
