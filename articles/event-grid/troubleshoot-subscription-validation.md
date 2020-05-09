---
title: Azure Event Grid — weryfikacja subskrypcji w celu rozwiązywania problemów
description: W tym artykule opisano sposób rozwiązywania problemów z walidacją subskrypcji.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630189"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Rozwiązywanie problemów z błędami Azure Event Grid
Ten przewodnik rozwiązywania problemów zawiera informacje dotyczące rozwiązywania problemów z walidacją subskrypcji zdarzeń. 


## <a name="troubleshoot-event-subscription-validation"></a>Rozwiązywanie problemów z walidacją subskrypcji zdarzeń

W trakcie tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`jak, oznacza to, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

- Wykonaj wpis HTTP w adresie URL elementu webhook za pomocą [przykładowej](webhook-event-delivery.md#validation-details) treści żądania SubscriptionValidationEvent przy użyciu elementu Poster lub zwinięcie lub podobnego narzędzia.
- Jeśli element webhook implementuje mechanizm uzgadniania synchronicznego walidacji, należy sprawdzić, czy ValidationCode jest zwracany jako część odpowiedzi.
- Jeśli element webhook implementuje mechanizm uzgadniania asynchronicznego walidacji, sprawdź, czy wpis HTTP jest zwracany 200 OK.
- Jeśli element webhook zwraca 403 (zabroniony) w odpowiedzi, sprawdź, czy element webhook znajduje się za usługą Azure Application Gateway lub zaporą aplikacji sieci Web. Jeśli tak jest, musisz wyłączyć te reguły zapory i ponownie wykonać POST protokołu HTTP:

  920300 (żądanie nie zawiera nagłówka Accept, możemy to naprawić)

  942430 (ograniczone wykrycie anomalii znaku SQL (args): Przekroczono liczbę znaków specjalnych (12))

  920230 (wykryto wielokrotne kodowanie adresu URL)

  942130 (atak wstrzykiwania kodu SQL: wykryto tautology SQL).

  931130 (możliwe, że ataku zdalnego dołączania plików (RFI) = odwołuje się do domeny/linku

> [!IMPORTANT]
> Aby uzyskać szczegółowe informacje na temat walidacji punktów końcowych dla elementów webhook, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Weryfikowanie subskrypcji zdarzeń usługi Event Grid przy użyciu programu Poster
Oto przykład użycia programu Poster do sprawdzania poprawności subskrypcji elementu webhook zdarzenia Event Grid: 

![Weryfikacja subskrypcji zdarzeń usługi Event Grid przy użyciu programu Poster](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Oto przykład SubscriptionValidationEvent JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Oto przykład pomyślnej odpowiedzi:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Aby dowiedzieć się więcej o walidacji zdarzeń w usłudze Event Grid dla elementów webhook, zobacz [Walidacja punktu końcowego przy użyciu zdarzeń usługi Event Grid](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="validate-cloud-event-subscription-using-postman"></a>Weryfikowanie subskrypcji zdarzeń w chmurze za pomocą programu Poster
Oto przykład użycia programu Poster do sprawdzania poprawności subskrypcji elementu webhook zdarzenia w chmurze: 

![Weryfikacja subskrypcji zdarzeń w chmurze przy użyciu programu Poster](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Aby sprawdzić poprawność przy użyciu zdarzeń w chmurze, użyj metody **opcji http** . Aby dowiedzieć się więcej na temat sprawdzania poprawności zdarzeń w chmurze dla elementów webhook, zobacz [Walidacja punktu końcowego ze zdarzeniami w chmurze](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Weryfikacja subskrypcji zdarzeń usługi Event Grid przy użyciu programu zwinięcie 
Poniżej znajduje się przykładowa część polecenia służąca do sprawdzania poprawności subskrypcji elementu webhook zdarzenia Event Grid: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
