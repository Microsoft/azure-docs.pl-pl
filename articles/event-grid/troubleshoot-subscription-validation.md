---
title: Azure Event Grid — weryfikacja subskrypcji w celu rozwiązywania problemów
description: W tym artykule opisano sposób rozwiązywania problemów z walidacją subskrypcji.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592944"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Rozwiązywanie problemów z walidacją subskrypcji Azure Event Grid
W trakcie tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki jak `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , oznacza to, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

- Wykonaj wpis HTTP w adresie URL elementu webhook za pomocą [przykładowej](webhook-event-delivery.md#validation-details) treści żądania SubscriptionValidationEvent przy użyciu elementu Poster lub zwinięcie lub podobnego narzędzia.
- Jeśli element webhook implementuje mechanizm uzgadniania synchronicznego walidacji, należy sprawdzić, czy ValidationCode jest zwracany jako część odpowiedzi.
- Jeśli element webhook implementuje mechanizm uzgadniania asynchronicznego sprawdzania poprawności, sprawdź, czy jest to wpis HTTP, który zwraca 200 OK.
- Jeśli element webhook jest zwracany `403 (Forbidden)` w odpowiedzi, sprawdź, czy element webhook znajduje się za zaporą aplikacji sieci Web lub Application Gateway platformy Azure. Jeśli tak jest, musisz wyłączyć te reguły zapory i ponownie wykonać POST protokołu HTTP:
    - 920300 (żądanie nie zawiera nagłówka Accept)
    - 942430 (ograniczone wykrycie anomalii znaku SQL (args): Przekroczono liczbę znaków specjalnych (12))
    - 920230 (wykryto wielokrotne kodowanie adresu URL)
    - 942130 (atak wstrzykiwania kodu SQL: wykryto tautology SQL).
    - 931130 (możliwe, że ataku zdalnego dołączania plików (RFI) = odwołuje się do domeny/linku

> [!IMPORTANT]
> Aby uzyskać szczegółowe informacje na temat walidacji punktów końcowych dla elementów webhook, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md).

W poniższych sekcjach pokazano, jak zweryfikować subskrypcje zdarzeń przy użyciu programu Poster i zwinięcie.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Weryfikowanie subskrypcji zdarzeń Event Grid przy użyciu programu Poster
Oto przykład użycia programu Poster do sprawdzania poprawności subskrypcji elementu webhook zdarzenia Event Grid: 

![Weryfikacja subskrypcji zdarzeń usługi Event Grid przy użyciu programu Poster](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Oto przykład **SubscriptionValidationEvent** JSON:

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

Aby dowiedzieć się więcej na temat sprawdzania poprawności zdarzeń Event Grid dla elementów webhook, zobacz [Walidacja punktu końcowego przy użyciu zdarzeń usługi Event Grid](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Weryfikowanie subskrypcji zdarzeń Event Grid przy użyciu programu zwinięcie 
Poniżej znajduje się przykładowa część polecenia służąca do sprawdzania poprawności subskrypcji elementu webhook zdarzenia Event Grid: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Weryfikowanie subskrypcji zdarzeń w chmurze za pomocą programu Poster
Oto przykład użycia programu Poster do sprawdzania poprawności subskrypcji elementu webhook zdarzenia w chmurze: 

![Weryfikacja subskrypcji zdarzeń w chmurze przy użyciu programu Poster](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Aby sprawdzić poprawność przy użyciu zdarzeń w chmurze, użyj metody **opcji http** . Aby dowiedzieć się więcej na temat sprawdzania poprawności zdarzeń w chmurze dla elementów webhook, zobacz [Walidacja punktu końcowego ze zdarzeniami w chmurze](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Rozwiązywanie problemów z walidacją subskrypcji zdarzeń

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
