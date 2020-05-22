---
title: Azure Event Grid — weryfikacja subskrypcji w celu rozwiązywania problemów
description: W tym artykule opisano sposób rozwiązywania problemów z walidacją subskrypcji.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778712"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Rozwiązywanie problemów z walidacją subskrypcji Azure Event Grid
Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z walidacją subskrypcji zdarzeń. 

> [!IMPORTANT]
> Aby uzyskać szczegółowe informacje na temat walidacji punktów końcowych dla elementów webhook, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md).

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

## <a name="error-code-403"></a>Kod błędu: 403
Jeśli element webhook zwraca 403 (zabroniony) w odpowiedzi, sprawdź, czy element webhook znajduje się za usługą Azure Application Gateway lub zaporą aplikacji sieci Web. Jeśli tak jest, musisz wyłączyć następujące reguły zapory i ponownie wykonać POST protokołu HTTP:

  - 920300 (żądanie nie zawiera nagłówka Accept, możemy to naprawić)
  - 942430 (ograniczone wykrycie anomalii znaku SQL (args): Przekroczono liczbę znaków specjalnych (12))
  - 920230 (wykryto wielokrotne kodowanie adresu URL)
  - 942130 (atak wstrzykiwania kodu SQL: wykryto tautology SQL).
  - 931130 (możliwe, że ataku zdalnego dołączania plików (RFI) = odwołuje się do domeny/linku

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
